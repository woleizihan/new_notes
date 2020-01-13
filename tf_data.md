# Tensorflow Data Notes

https://www.tensorflow.org/api_docs/python/tf/data/Dataset?version=stable

### Create `Dataset`

**From `list`**

```python
dataset = tf.data.Dataset.from_tensor_slices([1,2,3])
```

**From generators**

```python
dataset = tf.data.Dataset.from_generator(count, args=[25], output_types=tf.int32, output_shapes = (), )
```

**From lines in fies**

```python
dataset = tf.data.TextLineDataset(["file1.txt", "file2.txt"]
```

**From `TFRecord`**

```python
dataset = tf.data.TFRecordDataset(["file1.tfrecords", "file2.tfrecords"])
```

**From all files matching a pattern**

```python
dataset = tf.data.dataset.list_files("/path/*.txt")
```

**From `.csv`**

- Using pandas

  ```python
  df = pd.read_csv(csv_file, index_col=None)
  df_slices = tf.data.Dataset.from_tensor_slices(dict(df))
  ```

- Using `experimental.make_csv_dataset`

  ```python
  titanic_batches = tf.data.experimental.make_csv_dataset(
      titanic_file, batch_size=4,
      label_name="survived")
  
  for feature_batch, label_batch in titanic_batches.take(1):
    for key, value in feature_batch.items():
  ```

  - `select_columns` argument if only need a subset of columns

**zip**

We can add column to a dataset by using zip

```python
lines = tf.data.TextLineDataset(titanic_file)
counter = tf.data.experimental.Counter()
dataset = tf.data.Dataset.zip((counter, lines))
```

**`element_spec`**

`Dataset.element_spec` allows you to inspect the type of each element component



### Access element

**`take`**

```python
for element in dataset.take(2)
```

**`skip`**

`skip()` will skip a certain number of batches after each access 

```python
dataset.batch(2).skip(1)
```



### Transformations

**Element-wise map**

 ```python
dataset = dataset.map(lambda x: x*2)
 ```

**Apply a transformation function**

```python
dataset = dataset.apply(dataset_fn)
```

- `dataset_fn` is a function that takes one `Dataset` argument and returns a `Dataset`

**Transform to numpy iterator**

```python
for element in dataset.as_numpy_iterator()
```

- `as_numpy_iterator` will preserve the nested structure of dataset elements, i.e. rows will be individual samples

**External Python libraries**

We can use `tf.py_function(function, arg list, output type)` to wrap the external function before using `map`

```python
import scipy.ndimage as ndimage

def random_rotate_image(image):
  image = ndimage.rotate(image, np.random.uniform(-30, 30), reshape=False)
  return image

def tf_random_rotate_image(image, label):
  im_shape = image.shape
  [image,] = tf.py_function(random_rotate_image, [image], [tf.float32])
  image.set_shape(im_shape)
  return image, label

rot_ds = images_ds.map(tf_random_rotate_image)
```



### Batching

**Simple Batch **

```python
dataset = dataset.batch(3)
```

-  `drop_remainder = True/False`
- each batch is a list of rows 

**Padded Batch **

```python
dataset = dataset.padded_batch(3)
```



### Preprocessing

**Multiple epochs**

```python
dataset = dataset.repeat(3).batch(128)
```

**Shuffle**

```python
dataset = dataset.shuffle(buffer_size=100)
```

- `shuffle` maintains a fixed-size buffer and chooses the next element uniformly at random from the buffer

- `shuffle` doesn't signal the end of an epoch until the shuffle buffer is empty. 
  - `shuffle` before `repeat` will show every element of one epoch before moving to the next
  - `shuffle` after `repeat` will mix elements from different epochs

**Cleaning data**

See "Transformation" part



### Time Series Windowing

**Using `batch`**

```python
batches = range_ds.batch(10, drop_remainder=True)

def dense_1_step(batch):
  # Shift features and labels one step relative to each other.
  return batch[:-1], batch[1:]

predict_dense_1_step = batches.map(dense_1_step)

for features, label in predict_dense_1_step.take(3):
  print(features.numpy(), " => ", label.numpy())
```

To allow overlap between the features of one batch and the labels of another, we can construct them individually and `zip` together:

```python
feature_length = 10
label_length = 5

features = range_ds.batch(feature_length, drop_remainder=True)
labels = range_ds.batch(feature_length).skip(1).map(lambda labels: labels[:-5])

predict_5_steps = tf.data.Dataset.zip((features, labels))

for features, label in predict_5_steps.take(3):
  print(features.numpy(), " => ", label.numpy())
```



**Using `window`**

```python
window_size = 5
windows = range_ds.window(window_size, shift=1, stride=1)
```

- `shift` determines the shift between two windows
- `stride` controls the distance between two samples within a window
- `windows.take()` will return a dataset of datasets

To flatten the dataset, we can use `Dataset.flat_map()`

```python
 for x in windows.flat_map(lambda x: x).take(30):
   print(x.numpy(), end=' ')
```

Batching the flattened dataset using the `window_size` will give a dataset of windows

```python
def sub_to_batch(sub):
  return sub.batch(window_size, drop_remainder=True)

for example in windows.flat_map(sub_to_batch).take(5):
  print(example.numpy())
```



### Resampling

When working with a dataset that's very class-imbalanced, you may want to resample the dataset.

**`sample_from_datasets` method**

First filter the dataset by classes:

```python
negative_ds = (
  creditcard_ds
    .unbatch()
    .filter(lambda features, label: label==0)
    .repeat())
positive_ds = (
  creditcard_ds
    .unbatch()
    .filter(lambda features, label: label==1)
    .repeat())
```

Then we can sample from individual classes with a desired frequency

```python
balanced_ds = tf.data.experimental.sample_from_datasets(
    [negative_ds, positive_ds], [0.5, 0.5]).shuffle(100).batch(10)
```

**Rejection resampling (NOT STABLE)**

For the above method, we need one dataset per class. We can deal with this problem by using `experimental.rejection_resample`

```python
def class_func(features, label):
  return label

resampler = tf.data.experimental.rejection_resample(
    class_func, target_dist=[0.5, 0.5], initial_dist=fractions)

resample_ds = creditcard_ds.unbatch().apply(resampler).shuffle(100).batch(10)
balanced_ds = resample_ds.map(lambda extra_label, features_and_label: features_and_label)
```



### High-level APIs

**`tf.keras`**

```python
train, test = tf.keras.datasets.fashion_mnist.load_data()

images, labels = train
images = images/255.0
labels = labels.astype(np.int32)

fmnist_train_ds = tf.data.Dataset.from_tensor_slices((images, labels))
fmnist_train_ds = fmnist_train_ds.shuffle(5000).batch(32)

model = tf.keras.Sequential([
  tf.keras.layers.Flatten(),
  tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
              loss=tf.keras.losses.SparseCategoricalCrossentropy(), 
              metrics=['accuracy'])

model.fit(fmnist_train_ds, epochs=2)
loss, accuracy = model.evaluate(fmnist_train_ds)
print("Loss :", loss)
print("Accuracy :", accuracy)
```

For long/infinite datasets, use the argument `steps` to control the evaluation:

```python
loss, accuracy = model.evaluate(fmnist_train_ds.repeat(), steps=10)
```

For the `Model.predict`, the labels will be ignored if passed in `Dataset`:

```python
result = model.predict(fmnist_train_ds, steps = 10)
print(result.shape)
```