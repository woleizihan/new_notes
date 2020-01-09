# Learning from Heterogeneous Sources via Gradient Boosting Consensus

## Intro

- Heterogeneous learning: collectively use any available multiple heterogeneous data sources in torder to build effective learning models
- Minimize the empirical loss with two constraints
  - There should be consensus among the predictions of overlapping instances from different data source
  - Connected instances in graph datasets may have similar predictions
- Objective function is solved by stochastic gradient boosting trees
- Datasets:
  - Each data source can have its own feature sets
  - Each data source can have its own set of instances. Some instances might be missing from certain sources
  - There can be multiple network datasets
- General objective function:
  - If there are overlapping instances, the predictions of the same instance should be similar even when learning from different data sources
  - The predictions of connected data should be similar
  - Predictions models are judiciously combined with different weights to generate a global prediction model
- Gradient boosting trees:
  - Deep-ensemble: new trees are generated from all data sources
  - Network-friendly: can take graph relational features together with vector-based features
  - Robust: use weighting strategy to emphasize informative data sources and deemphasize noisy ones.

## Related Work

- Multi-view learning
- Consensus learning: perform learning on each heterogeneous feature spaces independently and summarize the results via ensemble
- Collaborative filtering: find a latent factor tahn connects all data sources and propogate information through the latent factor
- Collective classification: predict the class label from a network. Combine supervision knowledge from tranditional vector-based feature vectors as well as linkage information from the network

## Problem Formulation

- $x_i^{(j)} \in \mathbb{R}^{d_j}$: the i-th data in the j-th source, column vector
- $G_g = <V_g, E_g>$: the g-th graph with vertices and edges

## Gradient boosting Consensus

### The GBC framework

- Loss function:

  $$\mathcal{L}_{loss} = \sum_i w_i \sum_{x \in \mathcal{T}}L(f_i(x), y)$$

- Same instance in different datasets:

  - $\mathcal{C}(f,w) = \sum_i w_i \sum_{x \in U_i}L(f_i(x), E(f(x)))$: where $U_i$ is the set of instances in the i-th dataset
  - $E(f(x)) = \sum_{i, x \in U_i} w_if(x)$ 
  - $\sum_i w_i = 1$

-  Connected data:

  - $\mathcal{G}(f,w) = \sum_g w_g \sum_{x \in U_i}L(f_i(x), \tilde{E}_i(f(x)))$ 
  - $\tilde{E}_i(f(x)) = \sum_g \frac{\hat{w}_g}{|\{(z,x) \in E_g\}|} \sum_{(z,x)\in E_g}f_i(z)$ 
  - $\sum \hat{w}_g = 1$ 

- Combined objective function: $\mathcal{L} = \mathcal{L}_{loss} + \lambda_0 \mathcal{C}(f,w) + \lambda_1 \mathcal{G}(f, w, \hat{w})$

### Training

Use stochastic gradient descent to solve the optimization problem.

$$f(x) \leftarrow f(x) - \rho \frac{\partial \mathcal{L}}{\partial f(x)}$$ 

#### $L^2$ loss function

$$\frac{\partial \mathcal{L}}{\partial f(x)} = w_i (\sum_{x}(f_i(x) - y) + \lambda_0 \sum_{x}(f_i(x) - E) + \lambda_1 \sum_{x}(f_i(x)-\tilde{E}_i))$$  

- [ ] How to update $\lambda_i$? Also just gradient descent?

#### Logistic loss function

$$\frac{\partial \mathcal{L}}{\partial f(x)} = w_i(\sum_x \frac{-y \exp(-yf_i(x))}{1+\exp(-yf_i(x))} + \lambda_0 \sum_x \frac{-E(f(x))\exp(-E f_i(x))}{1 + \exp(-Ef_i(x))} + \lambda_i \sum_x \frac{-\tilde{E}_i \exp(-\tilde{E}_i f_i(x))}{1 + \exp(-\tilde{E}_i f_i(x))})$$ 

#### Other loss functions

- [ ] $L^1 $
- [ ] Elastic net $\alpha L^2 + (1-\alpha) L^1$
- [ ] Learn the loss function: neural nets

### Weight Learning

- Design the weights by looking at the empirical loss of the model trained from the data source
- If a data source induces large loss, its weight should be low
- Softmax on the total loss of each model
- Weights are also updated at each step

$$w_i = \exp (- \sum_{x \in U_i} L(f_i(x), y))/z$$ 

$$w_g = \exp (- \frac{1}{c} \sum_{(u,v) \in E_g} \sum_i L(f_i(u), f_i(v))) / z$$ 

## Experiments

