# Understanding Machine Learning

## Chapter 2: A Gentle Start

### A Formal Model

The **input** to a learner model has access to the following:

- Domain set
- Label set
- Training data

The **output** of a learner model will be a prediction rule $h: \mathcal{X} \rightarrow \mathcal{Y}$

The **underlying data** is generated by a tuple $(\mathcal{D},f)$:

- The distribution of $x$ is given by $\mathcal{D}$
- The label $y = f(x)$

**Measure of success** is defined by 
$$
L_{D,f}(h) = P(h(x)\neq f(x)) = D(\{x: h(x)\neq f(x)\})
$$

### Empirical Risk Minimization

We don't know the underlying distribution $\mathcal{D}$ as well as $f$. A useful notion of error can is the **empirical error/training error**:
$$
L_S(h) = \frac{|\{h(x_i) \neq y_i\}|}{|S|}
$$
Seeking a predictor that minimizes $L_S$ might lead to **overfitting**

### Empirical Risk Minimization with Inductive Bias

We can specify a **hypothesis space** $\mathcal{H}$ to select the predictors $h$ from. The $ERM_\mathcal{H}$ learner uses ERM rule to choose a predictor $h \in \mathcal{H}$. 

By restricting the learner to choosing a predictor form $\mathcal{H}$, we bias it toward a particular set of predictors and such restrictions are often called an **inductive bias**.

**Definition** (Realizability) A hypothesis space $\mathcal{H}$ is realizable if 
$$
\exists \hspace{5px} h^* \in \mathcal{H}, \hspace{5px} L_{(D,f)}(h^*) = 0
$$
**Confidence parameter:** $\delta$, the probability of getting a nonrepresentative sample

**Accuracy parameter:** $\epsilon$, if $L_{D,f}(h_S) > \epsilon$,  we interpret the predictor as a failure



**Corollary** If $\mathcal{H}$ is a finite hypothesis space with realizability assumption. For a sample $S$ with size 
$$
m \geq \frac{\log(|\mathcal{H}|/\delta)}{\epsilon}
$$
We have that for every ERM hypothesis $h_S$, 
$$
L_{(D,f)}(h_S) \leq \epsilon
$$
Learningwith probability $1-\delta$.



## Chapter 3: A Formal Learning Model

### PAC Learning

**Definition** (PAC Learnability) A hypo space $\mathcal{H}$ is PAC learnable if there exists a function $m_\mathcal{H}: (0,1)^2 \rightarrow \mathbb{N}$ and a learning algorithm such that

1. for every $\delta, \epsilon$ and $(D,f)$
2. the learning algorithm on a training set with size $m \geq m_\mathcal{H}(\delta, \epsilon)$ will give a predictor $h$ 
3. $L_{(D,f)}(h) \leq \epsilon$ with probability at least $1-\delta$

$m_\mathcal{H}$ is called the **sample complexity**.

**Corollary** Every finite hypo space is PAC learnable with sample complexity
$$
m_\mathcal{H}(\delta, \epsilon) \leq [\frac{\log(|\mathcal{H}|/\delta)}{\epsilon}]
$$

### A More General Learning Model

#### Releasing the realizability assumption - Agnostic PAC learning

- We relax the distribution to a joint distribution on $\mathcal{X} \times \mathcal{Y}$

- $L_D(h) = D(\{(x,y): h(x)\neq y\})$

**Definition** (Agnostic PAC Learnability)      A hypo space $\mathcal{H}$ is agnostic PAC learnable if there exists a complexity function $m_\mathcal{H}$ and a learning algorithm that given a sample with size $m \geq m_\mathcal{H}$, the algorithm returns a $h$ such that
$$
L_D(h) \leq min_{h^\prime \in \mathcal{H}}L_D(h^\prime) + \epsilon
$$


#### Generalized Loss Functions

A generalized loss function is given by $l: \mathcal{H} \times Z \rightarrow \mathbb{R}_+$.

**Risk function: ** $L_D(h) = E_{z \sim D}[l(h,z)]$

**Empirical risk: ** $L_s(h) = \frac{\sum_{z \in S} l(h)}{|S|}$

Some common $l$:

- 0-1 loss
- Square loss



**Definition** (Agnostic PAC Learnability for General Loss Functions) Replace the loss function by a general loss function



## Chapter 4: Learning via Uniform Convergence

### Uniform Convergence is Sufficient for Learnability

**Definition** ($\epsilon$-representative sample)      $S$ is $\epsilon$-representative if 
$$
\forall h \in \mathcal{H}, \hspace{5px} |L_S(h) - L_D(h)| \leq \epsilon
$$


**Lemma**     $\epsilon$-representative implies agnostic PAC learnability:

If $S$ is $\epsilon / 2$ - representative, then any $h \in argmin \hspace{5px} L_S(h)$ satisfies
$$
L_D(h_S) \leq \min_{h \in \mathcal{H}} L_D(h) + \epsilon
$$


**Definition** (Uniform Convergence)      A hypo class $\mathcal{H}$ has **uniform convergence property** if there exists a complexity function $m_\mathcal{H}^{UC}$ such that for any sample $S$ with size $m \geq m_\mathcal{H}^{UC}(\delta, \epsilon)$, it is $\epsilon$-representative with probability at least $1-\delta$



**Corollary**      Uniform convergence implies agnositc PAC learnable with complexity function
$$
m_\mathcal{H}(\delta, \epsilon) \leq m_\mathcal{H}^{UC}(\delta, \epsilon/2)
$$
Furthermore, the ERM paradigm is a successful agnostic PAC learner for $\mathcal{H}$



### Finite Classes Are Agnostic PAC Learnable

This can be proved by using the law of large numbers

**Corollary**      Let $\mathcal{H}$ finite, then it's agnostic PAC learnable with
$$
m_\mathcal{H}^{UC}(\delta, \epsilon) \leq \frac{\log(2|\mathcal{H}/\delta|)}{2\epsilon^2}
$$
for loss function $l: \mathcal{H} \times Z \rightarrow [0,1]$.