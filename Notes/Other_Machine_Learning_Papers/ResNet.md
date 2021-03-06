# Basic info
- Title: Deep Residual Learning for Image Recognition
- Author: Kaiming He, Xiangyu Zhang, Shaoqing Ren & Jian Sun
- Affiliation: Microsoft Research
- Publication status: 
- Short name: ResNet
- Year: 2015

# Score
- Idea: 5
- Usability: 5
- Presentation: 5
- Overall: 5

# Contributions
## Problem addressed / Motivation
- When deeper networks start converging, a degradation problem is exposed.
- With an increase in network depth, the accuracy gets saturated and then degrades rapidly.

## Idea / Observation / Contribution
- Utilises skip connections or short-cuts to jump over some layers.
- The skipping of layers allows for the avoidance of the vanishing gradient problem, by reusing activations from a previous layer until the adjacent layer learns its weights.
- These short-cut connections use residual functions i.e. f(x) = H(x) - x. If identity mapping is the optimal formulation, the learned weights should drive f(x) to 0 (and they observe that this is a suitable preconditioning as most residual function responses are small).

<p align="center">
  <img src="https://neurohive.io/wp-content/uploads/2019/01/resnet-e1548261477164.png" width=500>
</p>

## Formulation / Solver / Implementation
- During training, the weights adapt to mute the upstream layer, and amplify the previously-skipped layer. 
- In the simplest case, only the weights for the adjacent layer's connection adapted, with no explicit weights for the upstream layer.
- Skipping effectively simplifies the network, using fewer layers in the initial training stages.
- This speeds learning by reducing the impact of the vanishing gradient, as there are fewer layers to propagate through.
- The network then gradually stores the skipped layers as it learns the *feature space*.
- Towards the end of training, when all the layers are expanded, it stays closer to the manifold and thus learns faster.
- A neural network without residual parts explores more of the feature space, but this makes it more vulnerable to perturbations that cause it to leave the manifold, and necessitates extra training data to recover.

## Useful info / tips
- *ResNets* have single-layer skips.
- *HighwayNets* use an additional weight matrix to learn skip weights.
- *DenseNets* have several parallel skips.
- The idea of shortcut connections to force blocks to learn residual functions preconditioned on identity mapping is neat, and more so because it doesn't require additional parameters.

# Evaluation
## Dataset
- ImageNet

## Metrics
- The image was resized with its shorter side randomly sampled in [256,480] for scale augmentation.

## Results
<p align="center">
  <img src="https://neurohive.io/wp-content/uploads/2019/01/resnet-validation-570x355.png">
</p>

- ResNets are easier to optimise, but the *plain* network (that simply stack layers) shows higher training error when the depth increases.
- ResNets can easily gain accuracy from greatly increased depth, producing results which are better than previous networks.

# Resource
## Paper
https://arxiv.org/abs/1512.03385
