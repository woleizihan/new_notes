# Element of Statistical Learning
# Overview of Supervised Learning
## Least Squares and Nearest Neighbors

### Least Squares
- $\hat{Y} = \hat{\beta_0} + \sum X_j\hat{\beta}_j$
- $RSS(\beta) = \sum (y_i - x_i^T\beta)^2 = (\bm{y}-\bm{X}\beta)^T(\bm{y}-\bm{X}\beta)$
- $\hat{\beta} = (\bm{X}^T\bm{X})^{-1}\bm{X}^T\bm{y}$

### Nearest Neighbor
- $\hat{Y}(x) = \frac{1}{k} \sum_{x_i \in N_k(x)} y_i$

## Statistical Decision Theory
We have a joint distribution $P(X,Y)$ and we seek a function $f(X)$ that predicts $Y$ given values of the input $X$. We select $f$ by using a loss function $L(Y,f(X))$:
$$L(Y,f(X)) = E(Y-f(X))^2 = E_XE_{Y|X}([Y-f(X)]^2 | X).$$
Our desired $f$ is an approximation of 
$$f(x) = E(Y|X=x).$$
- Linear regression: $f(x) \approx x^t\beta$
- Nearest Neighbor: $f(x) = Avg(y_i | x_i \in N_k(x))$

## Local Methods in High Dimensions
### Curse of dimensionality
- Need to pick a large region in high dimesion
- All sample points are close to an edge of the sample

### Bias-variance decomposition
Let $T$ denote the training sample,
$$MSE(x_0) =  E_T[f(x_0) - \hat{y}_0]^2 = E_T[\hat{y}_0-E_T(\hat{y}_0)]^2 + [E_T(\hat{y_0} - f(x_0))]^2$$
- The first term represents the variantion in the predicted values $\hat{y}_0$ in terms of training sample
- The second term represents the bias of the predicted value from the true value

## Statistical Models, Supervised Learning and Function Approximation
Our gola is to find a usefual approximation $\hat{f}$ to the function $f = E(Y|X=x)$.
### A Statistical Model for $P(X,Y)$
Fit a statistical model that describes the joint distribution:
$$Y = f(X) + \epsilon.$$
We use the training sample pairs $(x_i, y_i)$.
### Function Approximation
 We speculate a certain form for $f$ and then fit the parameters. We typically use a basis expansion:
 $$f_\theta(x) = \sum h_k(x)\theta_k$$
where $\theta_k$ are constants.
- Linear: $h_k$ linear functions
- Polynomial: $h_k$ polynomials of inputs ($x_1x_2$...)
- Sigmoid: $h_k(x) = \frac{1}{1+\exp(-x^t\beta_k)}$

To find the "best" coefficients, we also need to specify a target or loss fucntion.
- RSS
- Maximum likelihood: $L(\theta) = \sum_i \log P_\theta(y_i)$

## Classes of Restricted Estimators
### Roughness Penalty
We can use a roughness penalty to obtain fittings that are more smooth:
$$PRSS = RSS + \lambda pJ(f)$$
with $J$ to be something like:
$$J(f) = \int [f^{\prime\prime}(x)]^2dx$$
### Kernel Methods and Local Regression
We can weight different terms in RSS by their distance from our current input:
$$RSS(f,x_0) = \sum K_\lambda(x_0,x_i)(y_i - f(x_0))^2.$$
One common choice is the Guassian kernel:
$$K_\lambda(x,y) = \frac{1}{\lambda}\exp[-\frac{|x-y|^2}{2\lambda}]$$
### Basis functions
- Linear
- Splines
- Radial: $\exp(-|x-\mu|^2/2\lambda)$
- NN: $\sigma(\alpha_m^Tx + b_m)$

# Linear Methods for Regression
## Linear Regression 
$$f(X) = \beta_0 + \sum X_j\beta_j$$
The variables $X_j$ can be
- quantitative inputs
- transformations of quantitative inputs
- dummy coding for categorical inputs
- interactions between inputs ($X_3 = X_1 X_2$)

Obtain the parameters $\beta_j$ by minimizing the least square:
$$RSS(\beta) = (\bm{y} - \bm{X}\beta)^T(\bm{y} - \bm{X}\beta)$$
- $\hat{\beta} = \bm{(X^TX)^{-1}X^Ty}$
- $\hat{y} = X\hat{\beta}$
- Geometric interpretation: consider the columns of $X$. We choose $\hat{\beta}$ such that $y - \hat{y}$ is othorgonal ot the subspace spanned by the columns of $X$
- $\hat{\beta} \sim N(\beta, (\bm{Xp}^T\bm{X})^{-1}\sigma^2)$
- $(N-p-1)\hat{\sigma}^2 \sim \sigma^2\chi^2_{N-p-1}$

To test the significance of groups of coefficients, we can use the $F$ statistics:
$$F = \frac{(RSS_0-RSS_1)/(p_1-p_0)}{RSS_1/(N-p_1-1)}$$
where 1 corresponds to the bigger model while 0 corresponds to smaller model.

### The Gauss-Markov Theorem
For any other unbised estimate $\theta$ of the parameter $\beta$, we have
$$V(\hat{\beta}) \leq V(\theta)$$
Notice this is the unconditional variance, not the variance condition on the training samples.

### Multiple Regression

Algorithm (Regression by Successive Orthogonalization)
  1. Initialize $z_0 = x_0 =1$
  2. For each feature: Regress $x_j$ on $z_0,...z_{j-1}$ to produce coefficients $\hat{\gamma}_{lj}$ and residual vector $z_j$
  3. Regress $\rm{y}$ on the residual $z_p$ to give the estimate $\hat{\beta}_p$

Matrix representation:
1. $X = Z\Gamma$ with $\rm{\Gamma}$ upper triangular
2. Standarize $Z$ by letting $Q = ZD^{-1}$, $R = D\Gamma$
3. Then $Q$ gives an orthogonal basis for the col space of $X$
4. We can regress $Q^Ty$ against $R$ 
5. Obtain $\hat{\beta} = R^{-1}Qy$ and $\hat{y} = QQ^Ty$

### Multiple Outputs
We can also have multiple columns of output $Y$:
$$Y = XB + E.$$
$Y:N \times K$, $X:N \times p$, $B:p \times K$.
A straightforward generalization of the ploss function is:
$$RSS(B) = \sum_k\sum_i (y_{ik} - f_k(x_i)) = tr[(Y-XB)^T(Y-XB)]$$
We can also have correlated errors and our loss function will take the form
$$RSS(B;\Sigma) = \sum_i (y_i - f(x_i))^T\Sigma^{-1}(y_i-f(x_i)).$$

## Subset Selection
- Best subset: $2^p$
- Forward: start with intercept and then sequentially add in features
- Backward: start with full model and sequentially remove features

## Shrinkage Methods
### Ridge
Add $L^2$ penalty to the loss function:
$$RSS(\beta) + \lambda\|\beta\|_2^2$$
equivalent to adding a constraint:
$$\|\beta\|_2^2 \leq t.$$
The resulting coefficient takes the form
$$\hat{\beta}^{ridge} = (X^TX + \lambda I)^{-1}X^Ty$$
With SVD, we can see that the difference between linear regression and ridge regression:
- Linear: $X\hat{\beta}^{ls} = UU^Ty$
- Ridge: $X\hat{\beta}^{ridge} = \sum_j u_j \frac{d_j^2}{d_j^2+\lambda}u_j^Ty$
- Effective degrees of freedom: $df(\lambda) = \sum_j \frac{d_j^2}{d_j^2+\lambda}$

### Lasso
Add $L^1$ penalty to the loss function:
$$RSS(\beta) + \lambda \|\beta\|_1.$$
Equivalent to the constraint:
$$\|\beta\|_1 \leq t.$$

### Least Angle Regression
Algorithm (Least Angle Regression)
1. Standarize the predictors. Initialize $\bm{r} = \bm{y} - \bar{\bm{y}}$, $\beta_i = 0$
2. Find the predictor $\bm{x}_j$ most correlated with $\bm{r}$
3. Move $\beta_j$ from $0$ towards the least regression coefficient $\langle x_j,r \rangle$, until some other competitor $\bm{x}_k$ has as much correlation with the current residual as does $\bm{x}_j$
4. Move $\beta_j$ and $\beta_k$ towards the least sqaure coefficients, until $\bm{x}_l$ has as much correlation with the current residual
5. Continue this step unitl all predictors have been entered

Algorithm (LAR: Lasso Modification)

4. If a non-zero coefficient hits zero, drop its variable from the active set of variables and recompute the current joint least squares direction

### Degrees of freedom for LAR and Lasso
We cna define the degrees of freedom of any fitted vector $\hat{y}$ by
$$df(\hat{y}) = \frac{1}{\sigma^2} \sum_i Cov(y_i, \hat{y}_i)$$
For Lasso and LAR, the degrees of freedom is exactly $k$.

## Methods Using Derived Input Directions
### Principal Components Regression
Consider the SVD of $X$:
$$X = UDV^T.$$
The columns of the $Z = UD$ are called the principal components of $X$ with $z_m = Xv_m$. We can regress $\bm{y}$ against $Z$:
$$\hat{\bm{y}}_{(M)}^{prc} = \bar{y}\bm{1} + \sum \hat{\theta}_m \bm{z}_m.$$
We can recover the coefficient for $\bm{x}$ by 
$$\hat{\beta}^{pcr}(M) = \sum_m \hat{\theta}_mv_m.$$

### Parital Least Squares
Also construct the new set of features from the $x_1,...,x_p$. However PLS uses the target $y$ to supervise the process.

Algorithm (Partial Least Squares)
1. Standarize inputs. Initialize $\hat{y}^{(0)} = \bar{y}\bm{1}$ and $\bm{x}_j^{(0)} = \bm{x}_j$
2. For $m=1,...,p$
   1. $z_m = \sum_j \hat{\phi}_{mj}x_j^{(m-1)}$ where $\hat{\phi}_{mj} = \langle x_j^{(m-1)}, y \rangle$
   2. $\hat{\theta}_j = \langle z_m, y \rangle / \langle z_m, z_m \rangle$
   3. $\hat{y}^{(m)} = \hat{y}^{(m-1)}+\hat{\theta}_mz_m$
   4. Orthogonalize each $x_j^{(m-1)}$ with respect to $z_m$
3. Output the sequence of fitted vectors $\{\hat{\bm{y}}^{(m)}\}$. The corresponding coefficients can be recovered form the sequence of PLS transformations.

PLS seeks directions that have high variance and have high correlation with the response. In particular, the principal component solves the problem
$$max_\alpha V(X\alpha)$$
$$\|\alpha\| = 1, \alpha^TSv_l = 0$$
for $l=1,...,m-1$.

On the other hand, the PLS direction solves
$$max_\alpha Corr^2(y,X\alpha)V(X\alpha)$$
$$\|\alpha\|=1, \alpha^TS\hat{\phi}_l = 0$$

## More on Path Algorithms

### Incremental Forward Stagewise
Algorithm (Incremental Forward Stagewise Regression)
1. Start with residual $r=y$ and all coefficients $0$
2. Find predictor $x_j$ most correlated with $r$
3. update $\beta_j = \beta_j + \delta_j$ and $r = r - \delta_j x_j$
4. Repeat until residuals are uncorrelated with all the predictors

Algorithm (Least Angle Regression)
4. Find the new direction by solving the conbstrained least squares problem
   $$min \|r - X_Ab\|_2^2, b_js_j\geq 0$$
   where $s_j = sign(\langle x_j, r \rangle)$.

### Piecewise-Linear 
If we add a penalty to the loss function:
$$R(\beta) + \lambda J(\lambda).$$
To make the solution $\beta(\lambda)$ piecewise constant, it suffices to have
- $R(\beta)$ quadratic or piecewise-quadratic as a function of $\beta$
- $J$ is piecewise linear in $\beta$

### Danzig Selector
Minimize the loss function 
$$\|X^T(y-X\beta)\|_\infty$$
subject to 
$$\|\beta\|_1 \leq t.$$

# Linear Methods for Classification
## Linear regression of an indicator matrix
If there are $K$ classes, we can form $Y_k = 1$ if $G=k$ and $0$ otherwise. We try to fit with features $X$ and target $Y$:
$$\hat{Y} = X(X^TX)^{-1}X^TY.$$
Rationale: 
$$E(Y_k | X=x) = P(G=k | X=x) = f_k(x)$$
and we assume $f_k$ is a linear function of $x$. 

## Linear discriminant analysis
- For each $k$ we associate a discriminant functions $\delta_k(x)$
- Classify $x$ to the $k$ with biggest discriminant function $\delta_k(x)$
- Consider again the conditional distribution $P(G|X)$ and assume
  $$P(G=k | X=x) = \frac{f_k(x)\pi_k}{ \sum_l f_l(x)\pi_l}$$
- $f_k(x)$ represents the unconditional joint distribution $P(G=k, X=x)$
- Common models
  - Linear/quadratic discriminant analysis: Gaussian
  - Mixture of Gaussians
  - Nonparametric density estimate
  - Naive Bayes

LDA:
- $f_k = \frac{1}{(2\pi)^{p/2}|\Sigma_k|^{1/2}}\exp(-\frac{1}{2}(x-\mu_k)^T\Sigma_k^{-1}(x-\mu_k))$
- Same $\Sigma_k$ for all $k$
- $\delta_k(x) = x^T\Sigma^{-1}\mu_k - \frac{1}{2}\mu_k^T\Sigma^{-1}\mu_k + \log \pi_k$
- Estimates for parameters:
  - $\hat{\pi}_k = N_k/N$
  - $\hat{\mu}_k = \sum_{g_i=k} x_i/N_k$
  - $\hat{\Sigma} = \sum_k \sum_{g_i=k} (x_i-\hat{\mu}_k)(x_i-\hat{\mu}_k)^T/(N-K)$

QDA:
- Same $f_k$
- Different $\Sigma_k$ for each class
- $\delta_k(x) = -\frac{1}{2}\log |\Sigma_k| - \frac{1}{2}(x-\mu_k)^T\Sigma_k^{-1}(x-\mu_k) + \log \pi_k$
- Estimates for parameters:
  - $\hat{\pi}_k$ : same
  - $\hat{\mu}_k$ : same
  - $\hat{\Sigma}_k = \sum_{g_i=k} (x_i-\hat{\mu}_k)(x_i-\hat{\mu}_k)^T/(N_k-1)$

Regularized DA:
Compromise between LDA and QDA, which allows us to shrink the separate covariances of QDA toward a common covariance as in LDA
$$\hat{\Sigma}_k(\alpha) = \alpha \hat{\Sigma}_k + (1-\alpha)\hat{\Sigma}.$$

Can do futher and shrink LDA covariances towards identity matrix. 
$$\hat{\Sigma}(\gamma) = \gamma \hat{\Sigma} + (1-\gamma)\hat{\sigma}^2I.$$

Computations:
Consider the eigenvalue decomposition of $\Sigma_k = U_kD_kU_k^T$. We can use this to compute the discriminatn function:
- $(x-\mu_k)^T\Sigma_k (x-\mu_k) = [U_k^T(x-\mu_k)]^TD_k^{-1}[U_k^T(x-\mu_k)]$
- $\log |\Sigma_k| = \sum_l \log d_{kl}$

So for the LDA, we can 
- First compute the eigenvalue decomposition and then transform $X_* = D^{-1/2}U^TX$. The covariance matrix of $X_*$ will be identity. 
- Then classify to the closest class cnetroid in the transformed space, modulo the effect of the class prior distribution $\pi_k$

Dimension reduction:
- Project onto the subspace of centroid
- Do a straightforward PCA on the covariance matrix can consider the principal component subspace

## Logistic Regression
Model the posterior probabilities in the form:
$$\log \frac{P(G=i | X=x)}{P(G=K|X=x)} = \beta_{i0} + \beta_i^Tx.$$
The resulting distribution takes the form
$$P(G=i|X=x) = \frac{\exp(\beta_{i0}+\beta_i^Tx)}{1 + \sum_l \exp(\beta_{l0}+\beta_l^Tx)}$$
and
$$P(G=i|X=x) = \frac{1}{1 + \sum_l \exp(\beta_{l0}+\beta_l^Tx)}$$

### Fitting
Consider a two classes case encoded by $0,1$. The parameters are fitted by max likelihood
$$l(\beta) = \sum_i \{y_i\log p(x_i;\beta) + (1-y_i)\log(1-p(x_i;\beta))\}$$
Then we can use gradient descent to find the max

### Logistic vs LDA
- Logistic more general because it only models the conditional distribution $P(G|X)$
- LDA instead requires us to fit the parameters by maximizing the full log-likelihood of the joint density

## Separating Hyperplanes
A hyperplane is defined by
$$\beta_0 + \beta^Tx = 0.$$
- unit normal vector: $\beta / \|\beta \|$
- signed distance: $\frac{1}{\|\beta\|}(\beta^Tx + \beta_0)$

### Optimal separating hyperplanes
Suppose we have two classes encoded by $\pm 1$, consider the optimization problem:
$$\max_{|\beta\|=1} M$$
$$y_i(x_i^T\beta + \beta_0) \geq M$$
$M$ represents the margin, i.e. what's the closest distance training data is away from the hyperplane. We can also fix $M$ and try to minimize the norm of $\beta$:
$$ min \frac{1}{2}\|\beta\|^2$$
$$ y_i(x_i^T\beta + \beta_0) \geq 1.$$
Try to solve this optimization problem will show some interesting properties:
$$L_P = \frac{1}{2}\|\beta\|^2 - \sum \alpha_i[y_i(x_i^T\beta + \beta_0) - 1]$$
Take derivative:
- $\beta = \sum \alpha_i y_i x_i$
- $\sum \alpha_iy_i = 0$


Substitute:
$$L_D = \sum \alpha_i - \frac{1}{2}\sum_i\sum_j \alpha_i\alpha_jy_iy_jx_i^Tx_j$$
$$\alpha_i \geq 0$$
$$\alpha_i[y_i(x_i^T\beta + \beta_0) - 1] = 0.$$
Then $\alpha_i = 0$ if the point is not on the margin. Therefore, only points that are on the margin contribute to the fitted value of $\beta$.

# Basis Expansions and Regularization
If we choose a basis $h_m \mathbb{R}^p \rightarrow \mathbb{R}$, we then can expand
$$f(X) = \sum \beta_m h_m(X)$$
Common choices for the basis
- $X_m$: linear model
- $X_iX_j$: polynomimal model
- $\log(X_j)$, $\sqrt{X_j}$ or other nonlinear transformations
- $\bm{1}(L_m \leq X_i \leq R_m)$ indicator for a region of $X_i$

Fit the expansion
- Restriction method: decide before-hand to limit the class of functions
- Selection method: adaptively scan the dictionary and include only basis functions that contribute significantly to the fit of the model
- Regularizatoin method: use the entire dictionary but add penalties to restrict the coefficients

## Piecewise polynomial and splines
Basis takes the form
$h_1(X) = 1$, $h_2(X) = X$, $h_3(X) = (X-\xi_1)_+$, $h_4(X) = (X-\xi_2)_+.$
To increase the smoothness of basis, we increase the order of the local polynomial. 
- Cubic spline: require continuous first and second derivatives at the knots.
- Natural cubic spline: add additional constraints that function is linear beyond the boundary knots. This frees up 4 degrees of freedom which can be used to fit the interior better

## Filtering and feature extraction
Basis matrix $H$ is $p \times M$. Each column is given by the value of $h_m$ at points $X_i$, $i=1,...,p$. Then given a new point $x \in \mathbb{R}^p$, we can consider the transformed features $x^* = H^Tx$ where the coordinates of $x^*$ can be viewed as coefficient of the basis functions $h_m$. Then we can fit models on $x^*$

## Smoothing splines
This is a spline method that avoids knots selection. We seek the $C^2$ function that minimizes teh penalized residual sum of squares:
$$RSS(f) + \lambda \int {f^{\prime\prime}(t)}^2 dt$$
The result is a natural cubic spline with knots at each unique value $x_i$. Let $N$ denote the $N \times N$ basis matrix for the splines. Then the criterion reduces to 
$$RSS(\theta, \lambda) = (y-N\theta)^T(y-N\theta) + \lambda \theta^T\Omega_N\theta.$$
The solution is a generalized ridge coefficient
$$\hat{\theta} = (N^TN + \lambda\Omega_N)^{-1}N^Ty.$$

## Automatic selection of the smoothing parameters
Fitting splines requires several parameters:
- degree of the splines
- number of knots
- placement of knots
Smoothing splines:
- the penalty parameter $\lambda$

Choosing the parameters
- degree of freedom: F-tests, residual plots and other more subjective criteria

Bias-Variance tradeoff
We have $\hat{f} = S_\lambda y$ and therefore
- $Cov(\hat{f}) = S_\lambda Cov(y) S_\lambda^T = S_\lambda S_\lambda^T$
- $Bias(\hat{f}) = f - S_\lambda f$ 

## Nonparametric logistic regression
Instead of assuming the log odd is linear, we try to fit it using splines:
$$\log \frac{P(Y=1 | X=x)}{P(Y=0 | X=x)} = f(x)$$
and then construct the penalized log-likelihood:
$$l(f;\lambda) = l(f) - \frac{1}{2}\lambda \int \{f^{\prime\prime}(t)\}^2dt.$$
Expand $f$ in terms of a basis for natural splines:
$$f(x) = \sum_N \theta_j N_j(x)$$
 we can use Newton's method to maximize the penalized log likelihood.

## Multidimensional splines
A spline $\mathbb{R}^p \rightarrow \mathbb{R}$ is just the product of one dimensional splines on each of its coordinates

# Kernel Smoothing Methods
We can achieve flexibility in estimating the regression function $f(X)$ over the domain by fitting a different but simple model separately at each query point $x_0$.

## One-dimensional kernel smoothers
Consider the KNN avg estimates:
$$\hat{f}(x) = Avg(y_i | x_i \in N_k(x_)).$$
The resulting estimator $\hat{f}$ is not smooth with respect to $x$. To make it smooth, we gradually decrease the effect of each $y_i$ as $x_i$ moves away from $x$ as opposed to a hard cutoff:
$$\hat{f}(x) = \frac{\sum K_\lambda(x_i,x)y_i)}{\sum K_\lambda(x_i, x)}.$$
with $K_\lambda(x_i,x)$ gradually decreases to $0$ as $|x_i - x|$ increasesf.
One example is the Epanechnikov quadratic kernel:
- $K_\lambda(x_0,x) = D(\frac{|x-x_0|}{\lambda})$
- $D(t) = \frac{3}{4}(1-t^2)$ for $|t| \leq 1$

Details:
- $\lambda$ determines the width of the local neighborhood. Large $\lambda$ leads to low variance but high bias.
- issues arise when there are ties in $x_i$. With most techniques, one can simple reduce the data set by taking avg at the tied values of $X$ and give them additional weight when computing the average
- for nearest neighbors, we can choose neighborhoods with a constant total weight $k$

### Local linear regression
For each query point $x_0$, we run a linear regression locally within a neighborhood of $x_0$:
$$\min \sum_i K_\lambda(x_0,x_i) [y_i - \alpha(x_0) - \beta(x_0)x_i]^2$$
The estimate is then given by $\hat{f}(x_0) = \alpha(x_0) + \beta(x_0) x_0$. Let $W(x_0)$ be the $N \times N$ diagonal matrix given by $K_\lambda(x_0, x_i)$, we can use the extended feature $X = (1, X_1, ..., X_p)$ and $\beta = (\alpha, \beta)$. The least square coefficient is given by 
$$\beta(x_0) = (X^TW(x_0)X)^{-1}X^TW(x_0)y.$$
Our estimate is then given by 
$$\hat{f}(x_0) = (1,x_0^T) \beta(x_0) = \sum l_i(x_0)y_i.$$

Automatic kernel carpentry:
- Local inear regression automatically modifies the kernel to correct the bias exactly to first order
- $E\hat{f}(x_0) - f(x_0) \sim |x_0 - x_i|^2$

### Local polynomial regression
Pretty much the same. Just replace the linear regression by polynomial regression:
$$\min \sum K_\lambda(x_0, x_i) [y_i - \alpha(x_0) - \sum \beta_j(x_0)x_i^j]$$
Collected wisdom:
- Local linear fits can help bias dramatically at the boundaries at a modest cost in variance
- Local quadratic fits tend to be most helpful in reducing bias due to curvature in the interior of the domain
- Asymptotic analysis suggest that local polynomials of odd degree dominate those of even dgree. This is largely due to the fact that asymptotically the MSE is dominated by boundary effects

## Selecting the width of the kernel
In each of the kernels, $\lambda$ is a parameter that controls its width:
- Epanechnikov: $\lambda$ radius of the support 
- Gaussian: $\lambda$ is thte standard deviation
- KNN: $\lambda$ is the number $k$ of nearest neighbors

Use leave-out CV or k-fold

## Local regression in $\mathbb{R}^p$
Replace absolute value by norm and use inner product as opposed to just product:
$$\min \sum K_N(x_0, x_i)(y_i - b(x_i)^T\beta(x_0))$$

## Structured local regression models in $\mathbb{R}^p$
In high dimesion problems, local regression does not help us much because 
- The neighborhood has to be big to reduce variance
- All points are close to the border of the neighborhood so bias will also be big 

We have to make some structural assumptions about the model.

### Structured kernels
One line of approach is to modify the kernel. We can give different weight to different coordinates:
$$K_{\lambda, A}(x_0, x) = D(\frac{(x-x_0)^TA(x-x_0)}{\lambda})$$

### Structured regression functions
We are trying to fit $E(Y|X) = f(X_1,...,X_p)$ and it's natural to consider ANOVA decomposition of the form
$$f(X_1,...,X_p)  = \alpha + \sum g_j(X_j) + \sum g_{kl}(X_k, X_l) + ...$$

We can use backfitting to fit the coefficients, i.e., fix all the coefficients and fit one coefficient, repeat until convergence.

## Local likelihood and other models
We can do the similar local estimate by maximizing a localized version of log likelihood:
$$l(\beta(x_0)) = \sum K_\lambda(x_0, x_i)l(y_i, x_i^T\beta(x_0))$$
This can be made more general by replacing the linear model by a more general model.
Can also apply to autoregressive models to account for time difference with current

## Kernel density esitmation and classification
Kernel density estimation is an unsupervised learning procudure. It also leads naturally to a simple family of procedures for nonparametric classification.

### Kernel Density Estimation
Suppose we have a random sample $x_1,...,x_N$ from a pdf $f_X(x)$ and we wish to estimate $f_X$ at a point $x_0$. A natural local estimate has the form
$$\hat{f}_X(x_0) = \frac{x_i \in N(x_0)}{N\lambda}$$
where the neighborhood has width $\lambda$. This estimte again is not smooth with respect to $x$ and we can smooth it using
$$\hat{f}_X(x_0) = \frac{1}{N\lambda}\sum K_\lambda(x_0, x_i).$$
We can also view this as a convolution. Let $\hat{F}(x_i) = 1/N$, we have
$$\hat{f}_X(x) = (\hat{F} * K_\lambda)(x).$$

### Kernel density classification
Once we have fit the nonparametric density estimates $\hat{f}_j(X)$ separately for each class, we can use Bayes to estimate the conditional probability:
$$\hat{P}(G=j|X=x) = \frac{\hat{\pi}_j\hat{f}_j(x)}{\sum \hat{\pi}_k \hat{f}_k(x)}$$

### Naive Bayes classifier
We assume that given a class $j$, the features $X_k$ are independent:
$$f_j(X) = \prod f_{jk}(X_k)$$
- each $f_{jk}$ can be estimated separately using one-dimensional kernel density
- if $X_j$ is discrete, then an appropriate histogram estimate can be used

## Mixture models for density estimation and classification
The mixture model can be viewed as one kind of kernel method:
$$f(x) = \sum \alpha_m \phi(x; \mu_m, \Sigma_m)$$
with mixing proportions $\sum \alpha_m = 1$ and $\phi$ represents the guassian distribution.
Parameters are fit using EM algo. Special cases
- if the covaraince matrices are constrained to be scalar, then this has the form of a radial basis expansion
- if in addition all covariance matrices are identical and we let $M \rightarrow N$, then the max likelihood estimate approaches teh kernel density estimate.

## Model Assessment and Selection
- Model selection: estimating the performance of different models in order to choose the best one
- Model assessment: having chosen a final model, estimating its prediction error on new data

### The bias-variance decomposition
$$Err(x_0) = Irred + Bias^2(\hat{f}(x_0)) + V(\hat{f}(x_0))$$

### Optimism of the training error rate
- most models effectively estimate the expected error condition on the training data as opposed to the unconditional error
- typically, the training error $\frac{1}{N}\sum L(y_i, \hat{f}(x_i))$ will be less than test error

### The effective number of parameters
The concept of "number of parameters" can be generalized, especially in models where regularization is used in the fitting. Suppose the prediction is given by
$$\hat{y} = Sy$$
for $S$ $N \times N$ matrix depending on inputs $X$ but not on $y$.
- effective number of parameters:
  $$df(S) = trace(S).$$
- If $S$ is a orthogonal projection, then this is the dimension of the range

### The Bayesian approach and BIC
- BIC is applicable in settings where the fitting is carried out by maximization of log-likelihood.
- BIC is defined by
  $$BIC = -2 \cdot loglik + (\log N)d$$
  - $N$: number of samples
  - $d$: effective degree of freedom
- choosing the model with minimum BIC is equivalent to choosing the mdoel with largest posteriro probability

### Cross-validation
K-Fold CV
- split the data into $K$ roughly equal-sized parts
- fit the model $K$ times using every fold except one
- $$CV(\hat{f}) = \frac{1}{N} \sum L(y_i, \hat{f}^{-k(i)}(x_i))$$
- the CV error is computed using the fitted estimator from the fold the sample is not in

### Bootstrap methods
- bootstrap sampling: draw with replacement $B$ datasets with the same sample size $N$
- we can estimate any aspect of the distribution of $Z$ 
  - $S(Z)$ any quantity computed from the data $Z$, we can estimate its variance by
    $$\hat{V}(S(Z)) = \frac{1}{B-1} \sum (S(Z^{*b})-\hat{S}^*)^2$$
- we can also estimate the prediction error by
  $$\hat{Err}_{boot} = \frac{1}{B}\frac{1}{N}\sum_{b,i}L(y_i, \hat{f}^{*b}(x_i))$$
- a better way is to compute error only for samples out of the bootstrap sample
  $$\hat{Err}^{(1)} = \frac{1}{N}\sum_i \frac{1}{|C^{-i}|}\sum_{b \in C^{-i}}L(y_i, \hat{f}^{*b}(x_i))$$
- however, a bootstrap sample on average contains only $0.632N$ distinct samples, so its bias will roughly behave liek that of twofold coross-validation
  $$\hat{E}^{.632} = 0.368\hat{err} + 0.632\hat{Err}^{(1)}$$