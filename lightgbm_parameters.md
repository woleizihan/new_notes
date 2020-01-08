# LightGBM Parameters

**TODO**

- [x] What's "lambda rank"
- [x] Math expressions for evaluation metrics
- [x] What's "number of bins for feature bucket"
- [x] Understand tree learner
  - https://zhanpengfang.github.io/418home.html
  - Different learner improves performance in parallel computing mode
- [x] Physical core or virtual core used
- [x] How is bagging used
  - http://www.ccs.neu.edu/home/vip/teach/MLcourse/4_boosting/materials/bagging_lmbamart_jforests.pdf
  - Bagging can be used as a variance reduction method
- [x] What is "sum Hessian" for one leaf
  - Sum of second order derivatives of the loss with respect to predictions



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

- `ndcg` (discounted cumulative gain, default for landbdarank)
  $$
  DCG_p = \sum_{i=1}^p \frac{rel_i}{\log_2(i+1)},
  $$
  where $rel_i$ is the graded relevance of the result at position $i$.

- `auc` (area under the ROC curve)
  $$
  TPR = \frac{TP}{TP + FN}, \hspace{5px} FPR = \frac{FP}{FP + TN}
  $$

  - ROC curve plots TPR vs FPR at different classification thresholds. Lowering the classification threshold classifies more item as positive, thus increasing both FP and TP.
  - The bigger the area, the faster TPR grows compared to FPR

- `binary_logloss` (default for binary)
  $$
  -\frac{1}{m}\sum_i y_i \log h(x_i) + (1-y_i)\log(1-h(x_i))
  $$
  

- `binary_error`
  $$
  \frac{1}{m} \sum_i \textbf{1}(y_i \neq h(x_i))
  $$
  

**Number of bins for feature bucket** (`max_bin`, `255` is recommended)

This is used to turn continuous features into discrete bins.

**Number of trees** (`num_trees`, this is the same as `num_boost_round` in `lgd.train`)

**Learning rate** (`learning_rate`)

**Force splits** (`forced_splits`)Number of leaves for one tree** (`num_leaves`)

**Type of tree learner** (`tree_learner`)

- `serial`: single machine version
- `feature`: use feature parallel to train
  - For each level, parallel with respect to features. This avoids sorting the instances of the node by the feature value
- `data`: use data parallel to train
- `voting`: use voting based parallel to train

**Size of random sub-sample** (`feature_fraction`)

**Bagging** (`bagging_freq`)

**Bagging fraction** (`bagging_fraction`)

**Minimal number data for one leaf** (`min_data_in_leaf`, this helps to deal with overfitting)

**Minimal sum Hessians for one leaf** (`min_sum_hession_in_leaf`, this helps to deal with overfitting)

This is the sum of the second derivatives of loss with respect to predictions:
$$
\sum_i\frac{\partial^2 L(y_i, \hat{y}_i)}{\partial \hat{y}_i^2}
$$
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

