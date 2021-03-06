# Basic info
- Title: Hierarchical Graph Representation Learning with Differentiable Pooling
- Author: Rex Ying, Jiaxuan You, Christopher Morris, Xiang Ren, William L. Hamilton & Jure Leskovec
- Affiliation: Stanford University, TU Dortmund University & University of Southern California
- Publication status: NIPS 2018
- Short name: DiffPool
- Year: 2018

# Score
- Idea: 4
- Usability: 4
- Presentation: 5
- Overall: 4

# Contributions
## Problem addressed / Motivation
- Current GNN methods do not learn **hierarchical** representations of graphs.
- This is very problematic for graph classification where the goal is to predict the label associated with an entire graph.
- Normally, embeddings are generated for all the nodes in the graph and then the node embeddings are *globally pooled* together. Not using hierarchical structure.
- Example: A organic molecule involve local molecular structure (e.g. individual atoms and their direct bonds) and coarse-grained structure of the molecular graph (e.g. groups of atoms and bonds representing functional units in a molecule).

## Idea / Observation / Contribution
- Propose DIFFPOOL that is a differentiable graph pooling module.
- This can generate hierarchical representations of graphs and can be combined with various GNN architectures in an *end-to-end* fashion.
- Learns a differentiable [soft cluster assignment](#Terms) for nodes at each layer of a deep GNN. 
- This maps nodes to a set of clusters, which form the coarsened input for the next GNN layer. 

<p align="center">
  <img src="https://github.com/AndrewColligan/Paper-Reading-Notes/blob/master/Notes/Imgs/diffpool.png" width = 700>
</p>

## Formulation / Solver / Implementation
- At each hierarchical layer, a GNN model is run to obtain embeddings of nodes. 
- The learnt embeddings are used to cluster nodes together.
- Another GNN layer is then run on the coarser graph.
- This process is repeated for each layer of the network.
- The final output representation is used to classify the graph.

<p align="center">
  <img src="https://github.com/AndrewColligan/Paper-Reading-Notes/blob/master/Notes/Imgs/diffpool_fig_2.PNG" width = 900>
</p>

#### Pooling with an Assignment Matrix
- Learned cluster assignment matrix at layer l is <a href="https://www.codecogs.com/eqnedit.php?latex=S^{(l)}\in&space;\mathbb{R}^{^{n_{l}}\times&space;n_{l&plus;1}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?S^{(l)}\in&space;\mathbb{R}^{^{n_{l}}\times&space;n_{l&plus;1}}" title="S^{(l)}\in \mathbb{R}^{^{n_{l}}\times n_{l+1}}" /></a> where n is the number of nodes.
- Each row of S<sup>(l)</sup> corresponds to one of the n<sub>l</sub> nodes (or clusters) at layer l,
and each column of S<sup>(l)</sup> corresponds to one of the n<sub>l+1</sub> clusters at the next layer l + 1. 
- Intuitively, S<sup>(l)</sup> provides a soft assignment of each node at layer l to a cluster in the next coarsened layer l + 1.
<p align="center"><a href="https://www.codecogs.com/eqnedit.php?latex=X^{l&plus;1}=S^{(l)T}Z(l)\in&space;\mathbb{R}^{n^{_{l&plus;1}}\times&space;d}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?X^{l&plus;1}=S^{(l)T}Z(l)\in&space;\mathbb{R}^{n^{_{l&plus;1}}\times&space;d}" title="X^{l+1}=S^{(l)T}Z(l)\in \mathbb{R}^{n^{_{l+1}}\times d}" /></a></p>
<p align="center"><a href="https://www.codecogs.com/eqnedit.php?latex=A^{l&plus;1}=S^{(l)T}A(l)S^{(l)}\in&space;\mathbb{R}^{n^{_{l&plus;1}}\times&space;n^{_{l&plus;1}}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A^{l&plus;1}=S^{(l)T}A(l)S^{(l)}\in&space;\mathbb{R}^{n^{_{l&plus;1}}\times&space;n^{_{l&plus;1}}}" title="A^{l+1}=S^{(l)T}A(l)S^{(l)}\in \mathbb{R}^{n^{_{l+1}}\times n^{_{l+1}}}" /></a></p>

#### Learning the Assignment Matrix
- Learn S<sup>(l)</sup> and Z<sup>(l)</sup> with two separate GNNs applied over the same input.
- Z<sup>(l)</sup> represents new embeddings for the input nodes at this layer.
<p align="center">
  <a href="https://www.codecogs.com/eqnedit.php?latex=Z^{l}=GNN^{_{l,embed}}(A^{^{(l)}},X^{^{(l)}})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?Z^{l}=GNN^{_{l,embed}}(A^{^{(l)}},X^{^{(l)}})" title="Z^{l}=GNN^{_{l,embed}}(A^{^{(l)}},X^{^{(l)}})" /></a>
</p>

- S<sup>(l)</sup> represents probabalistic assignments of the input nodes to n<sub>l+1</sub> clusters.
- Output dimension n<sub>l+1</sub> is a hyperparameter.
<p align="center"><a href="https://www.codecogs.com/eqnedit.php?latex=S^{l}=softmax(GNN^{_{l,pool}}(A^{^{(l)}},X^{^{(l)}}))" target="_blank"><img src="https://latex.codecogs.com/gif.latex?S^{l}=softmax(GNN^{_{l,pool}}(A^{^{(l)}},X^{^{(l)}}))" title="S^{l}=softmax(GNN^{_{l,pool}}(A^{^{(l)}},X^{^{(l)}}))" /></a></p>

#### Full Graph Representation
At the penultimate layer L − 1 of a deep GNN model using DIFFPOOL, the assignment matrix S<sup>(L−1)</sup> is set to be a vector of 1’s, such that all nodes at the final layer L are assigned to a single cluster, generating a final embedding vector corresponding to the entire graph.

#### Permutation Invariance
For graph classification, the pooling layer should be invariant under node permutations. For this to be true the component GNNs must be permutation invariant.

#### Auxiliary Link Prediction Object and Entropy Regularization
- Difficult to train the pooling GNN using only gradient signal from the graph classification task.
- Due to non-convex optimization problem.
- They train pooling GNN with an auxiliary link prediction task.
- Encodes the intuition that nearby nodes should be pooled.
- The output cluster assignment for each node should be close to a one-hot vector.
- To ensure the membership of each cluster or subgraph is clearly defined.
- To achieve this the entropy of each cluster assignment is regularized.

# Evaluation
## Dataset
- ENZYMES
- PROTEINS
- D&D
- REDDIT-MULTI-12K
- COLLAB

## Metrics
- Built upon [GRAPHSAGE](https://cs.stanford.edu/people/jure/pubs/graphsage-nips17.pdf) architecture.
- When using 2 DIFFPOOL layer architecture, number of clusters is set to 25% of the number of nodes before applying DIFFPOOL.
- When using 1 DIFFPOOL layer architecture, number of clusters is set to 10% of the number of nodes before applying DIFFPOOL.
- Batch normalization.
- L<sub>2</sub> normalization.
- 3,000 epochs with early stopping.

## Results
<p align="center">
  <img src="https://github.com/AndrewColligan/Paper-Reading-Notes/blob/master/Notes/Imgs/diffpool_results.PNG" width = 700>
</p>


# Resource
## Paper
https://arxiv.org/abs/1806.08804

## Source code
- [Orignal](https://github.com/RexYing/diffpool)
- [PyTorch](https://github.com/dmlc/dgl/tree/master/examples/pytorch/diffpool)

## Questions


## Build upon


## Paper connections
- Hierarchical Graph
- Graph Neural Networks

## Software & Hardware


## Terms
**Hard Clustering** 
- Grouping data items such that each item is only assigned to one cluster.

**Soft Cluster**
- Grouping the data items such that an item can exist in multiple clusters. 
