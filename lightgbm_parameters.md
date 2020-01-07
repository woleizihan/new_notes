# LightGBM Parameters

**TODO**

- [ ] What's "lambda rank"

- [ ] Math expressions for evaluation metrics
- [ ] What's "number of bins for feature bucket"
- [ ] Understand tree learner

- [ ] Physical core or virtual core used
- [ ] How is bagging used
- [ ] What is "sum Hessian" for one leaf



## Training Parameters

### IO

**Training data** (`data`)

**Validation data** (`valid_data`)

**Use two round loading** (`use_two_round_loading`, `true` or `false`, use this when data is bigger than memory size)

**Save data to binary file** (`is_save_binary_file`, `true` if save data to binary file and application will auto load data from binary file)

**Output model file** (`output_model`)

**Frequency for metric output** (`metric_freq`)

**Output metric for training data** (`is_training_metric`, `true` or `false`)



### Model Parameters

**Task type** (`task`)

- `train`, `predict`

**Boosting type** (`boost`)

- `gbdt`

**Application type** (`objective`)

- `bainry`: binary classification
- `regression`: regression task
- `lambdarank`: lambda rank task

**Evaluation metrics** (`metric`, support multiple metrics delimited by `,`)

- `l1`, `l2` (default for regression)
- `ndcg` (default for landbdarank)
- `auc`
- `binary_logloss` (default for binary)
- `binary_error`

**Number of bins for feature bucket** (`max_bin`, `255` is recommended)

**Number of trees** (`num_trees`, this is the same as `num_boost_round` in `lgd.train`)

**Learning rate** (`learning_rate`)

**Force splits** (`forced_splits`)Number of leaves for one tree** (`num_leaves`)

**Type of tree learner** (`tree_learner`)

- `serial`: single machine version
- `feature`: use feature parallel to train
- `data`: use data parallel to train
- `voting`: use voting based parallel to train

**Size of random sub-sample** (`feature_fraction`)

**Bagging** (`bagging_freq`)

**Bagging fraction** (`bagging_fraction`)

**Minimal number data for one leaf** (`min_data_in_leaf`, this helps to deal with overfitting)

**Minimal sum Hessians for one leaf** (`min_sum_hession_in_leaf`, this helps to deal with overfitting)

**Force splits** (`forced_splits`, `.json` file)



### Performance and Parallel Computing

**Number of cores for multi-threading** (`num_threads`, one thread will use one core)

**Save memory and faster speed for sparse feature** (`is_enable_sparse`)

**Number of machines in parallel training** (`num_machines`)

**Local listening port in parallel training** (`local_listen_port`)

**Machine list file for parallel training** (`machine_list_file`)



## Predict Parameters

**Task type** (`task`)

**Data** (`data`)

**Trained model file** (`input_model`)

