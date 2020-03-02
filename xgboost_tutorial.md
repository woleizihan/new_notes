# XGBoost Tutorial

## Hyperparameters

https://xgboost.readthedocs.io/en/latest/parameter.html

`learning_rate`: step size shrinkage used to prevent overfitting $[0,1]$

`max_depth`: determines how deeply each tree is allowed to grow during any boosting round.

`subsample`: percentage of samples used per tree. Low value can lead to underfitting.

`colsample_bytree`: percentage of features used per tree. High value can lead to overfitting.

`n_estimators`: number of trees you want to build. Same as `num_rounds`, `num_boost_rounds`

**Objective** (`objective`) 

- `reg:squarederror`: regression with squared loss

- `reg:squaredlogerror`
  $$
  \frac{1}{2} [\log(pred + 1) - \log(label +1)]^2
  $$
  

- `reg:logistic`

- `binary:logistic`

- `binary:hinge`

- `rank:pairwise`: LambdaMART

- `rank:ndcg`: LambdaMART

**Regularization**

- `gamma`: penalty $\gamma T$ with $T$ being the tree size
- `alpha`: $\alpha \|w\|_1$
- `lambda`: $\lambda \|w\|_2^2$

**CV** (`xgb.cv`)

- `num_boost_round`
- `metrics`
- `nfolds`

