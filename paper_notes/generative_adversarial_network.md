# Unsuperviesd Representation Learning with Deep Convolutional Generative Adversarial Networks

**Authors:** Alec Radford, Luke Metz Soumith Chintalan

**Link:** https://arxiv.org/abs/1511.06434

## Intro

- Propose and evaluate a set of constraints on Convolutional GANs that maek them stable to train in most settings
- Use trained discriminators for image classification tasks
- Visualize the filters learned by GANs

## Related Work

- Unsupervised learnings: 
  - heirarchical clustering of image patches
  - auto-encoders
  - deep belief networks
- Generating natural images
  - Non-parametric: do matching from a database of existing images, often matching patches of images
  - Parametric: variational sampling approach, iterative forward diffusion process, GANs

## Approach and Model Architecture

Adopt and modify three recently demonstrated changes to the CNN architectures:

- The all convolutional net which replaces deterministic spatial pooling functions with strided convolution, allowing the network to learn its own spatial downsampling. This is used in the generator, allowing it to learn its own spatial upsampling, and discriminator
- Trend towards eliminating fully connected layers on top of ocnvolutional features
- Batch normalization which stabilizes learning by normalizing th input to each unit to have zero mean and unit variance.

Activations:

- Generator: Relu except Tanh for output layer

- Discriminator: Leaky Relu

## Details of Training

- Mini-batch SGD: `lr = 0.0002, beta=0.5` and `batch_size=128`
- Weights initialized from `normal(0, 0.02)`
- LeakyRelu: `alpha=0.2` for stable training

![Screenshot from 2019-11-24 10-45-45](/home/angli/Pictures/Screenshots/Screenshot from 2019-11-24 10-45-45.png)

![deep_convolutional_generative_adversarial_network](/home/angli/Pictures/deep_convolutional_generative_adversarial_network.png)

More details:

- Training loss: binary cross entropy
- Discriminator loss will be the sum of two different losses: one for the real images and one for the fake images
- Two gradient tapes: one for discriminator and one for generator

