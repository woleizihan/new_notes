# Dimension Reduction
## Basis Expansions and Regularization
- Piecewise polynomials and splines
- Smoothing splines
  - penalty on the smoothness of the fitted spline
- Wavelet

## Unsupervised Methods
- Self organizing map
  - constrained K-means inwhich the prototypes are encouraged to lie in a one or two-dimensional manifold in the feature space
- Principal components, curves and surface
- Kernel principal components
  - instead of computing the components that maximize the variance in $X$, compute the components that maximaize the derived samples $h(X)$
- Sparse principal components
  - instead of a hard cutoff, say first $m$ principal components, use a Lasso penalty to force sparse components
- Independent component analysis
  - find the orthogonal matrix $A$ such that components of $A^TX$ are independent
  - differential entropy We can simply run the above to compute ICA

## High-dimensional Problems
- Diagonal LDA
  - assume the covariance matrix is diagonal
- Nearest shrunken centroids
  - shrink classwise mean toward the overall mean for each feature separately and cutoff below a threashold
  - therefore for a feature that is pretty much the same across different classes, it will be dropped
- Regularized discriminant analysis
  - shrink the covariance matrix towards its diagonal
- Supervised principal components
  - seek linear combinations that has both high variance and significant correlation with the outcome
  - rank features by their correlation with the outcome, then apply PCA to the $m$ most significant features

# Classification
## Linear Methods
- Linear regression of an indicator matrix
- Linear/quadratic discriminant analysis
- Regularized discriminant analysis
  - shrink within class covariance towards global covariance
  - shrink towards identity matrix multiplied by variance
  - reduced rank: eigenvalue decomposition
- Logistic regression
- Separating hyperplane
  
## Kernel Smoothing Methods
- Kernel density estimate
  - modify KNN by assigning different weight to training samples according to a Kernel
  - results in a smoothed NN algo
- Mixture models
  - the distribution is a weighted sum of Gaussians
  - fitted by max likelihood

## Tree Base Methods
- Classification tree
  - loss: misclassification, Gini, cross-entropy

## Boosting
- AdaBoost
  - forward stage wise method with exponential loss

## Support Vector Machines and Flexible Discriminants
- Support vector classifier
  - fix the scale of coefficients and maximize the margin
  - soft margin
- Kernel SVM
  - apply SVM to transformed feature vectors
  - Kernels: polynomial, radial, neural network
- Flexible discriminant analysis
  - performing LDA using linear regression on derived responses
  - LDA can be performed by a sequence of linear regressions, followed by classification to the closest clss centroid in the space of fit
  - we can replace linear regression by more flexible regression
- Penalized discriminant analysis
  - penalize the enlarged set of predictors
- Mixture discriminant analysis

## Prototype Methods and Nearest-Neighbors
- K-mean clustering
  - in each iteration, assign training points to the nearest centroid and then update the centroids by mean of the samples in each class
- Learning vector quantization
  - initialize one prototype for each class
  - assisgn each sample to the closest prototype
  - update prototypes
    - correct prediction: move towards the training point
    - wrong predction: move away from teh training point
  - decrease learning rate until convergence
- KNN
- Adaptive NN
  - stretch the neighborhood in the directions for which the class probabilities don't change much
  - good for high dimensional problems

## Unsupervised Methods
- Cluster analysis
  - proximity matrices
  - dissimilarities based on attributes
  - object dissimilarity
- Hierarchial clustering
  - agglomerative: start with one cluster for each point and then merge
  - divisive: start with all points in a single cluster and then divide
- Spectral clustering
  - compute the graph Laplacian from a adjacency matrix
  - spectral clustering finds the eigenvectors corresponding to the smallest eigenvalues
  - pairs of points with large adjacencies have coordinates close together

## Random Forests
- Random forest
  - grow a random-forest tree for each bootstrap sample
    - random subset of all features
  - majority vote for classfication

# Prediction
## Linear Methods
- Lasso
- Ridge
- Subset selection
- Least angle regression
- Derived input directions
  - Principal components regression
  - Partial least square

## Kernel Smoothing Methods
- Local linear regression
  - train model locally on the inquiry point and each training sample contribution is modified by a Kernel
- Structured local regression
  - assign different weights to different features of a sample

## Tree Based Methods
- Regression trees
  - choose each node/region in sample space one by one

## Random forests
- Random forest
  - use average over each tree for regression

# Boosting
- Forward stagewise additive model
  - add simple function to the model at each stage
  - pick the simple function and it's weight by minimizing a loss function

# Model Selection
- Subset selection
- BIC
- K-Fold CV
- Bootstrap

# General Utility
- Stochastic gradient descent
- Newton's method
- EM algorithm
  - iterative approach to fit max likelihood
- MCMC
  - iterative approach to sample from the posterior
  - Gibbs: sample from conditional distribution until the joint distribution doesn't change
- Bagging
  - train and average model on each bootstrap sample
  - stacking: assign weights to trained model by minimizing the RSS from the weighted average
- Stochastic searcfig.h
  - instead of avefig.raging the models trained on bootstrap, select the one tfig.hat best fits the data
- Boostingfig.
  - sequentially afig.pply the weak classification algorithm to repeatedly mofig.dified version of the data
  - then output wefig.ighted average of the weak classifier at each stepfig.
  - control the sifig.ze of the simple model at each stage as well as the numbfig.er of iterations
- Gradient Boostinfig.g
  - steepest descent is only computed on training samples 
  - induce a trea at each iteration to approximate the negative gradient

# Visualization
## Types
- Lineplot
- Barplot
- Heatmap
  - annot
- Scatterplot
  - regplot
  - hue
  - lmplot
  - swarmplot
- Histogram
  - kde plot
  - 2d kde: jointplot
  - color-coded

## Choose
- Trends:
  - `sns.lineplot`
- Relationship:
  - `sns.barplot`
  - `sns.heatmap`
  - `sns.scatterplot`
  - `sns.regplot`
  - `sns.lmplot`
  - `sns.swarmplot`
- Distribution:
  - `sns.distplot`
  - `sns.kdeplot`
  - `sns.jointplotfig.`