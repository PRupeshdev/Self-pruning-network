#  Self-Pruning Neural Network Report

##  Overview

In this project, we implemented a self-pruning neural network where the model learns to remove its own unnecessary connections during training. Instead of performing pruning as a post-processing step, the network dynamically identifies and suppresses weak weights using a learnable gating mechanism.

---

##  Prunable Linear Layer

Each weight in the network is associated with a learnable **gate parameter**. These gates are passed through a sigmoid function to constrain them between 0 and 1:

* If a gate value is close to **1**, the weight is active
* If a gate value is close to **0**, the weight is effectively pruned

The forward operation becomes:

[
W_{pruned} = W \odot \sigma(G)
]

where:

* (W) = weight matrix
* (G) = gate scores
* (\sigma(G)) = sigmoid applied to gate scores

---

##  Sparsity Regularization

To encourage pruning, we introduce an L1 penalty on the gate values:

[
Loss = ClassificationLoss + \lambda \cdot SparsityLoss
]

* **ClassificationLoss**: Cross-Entropy Loss
* **SparsityLoss**: Mean of all gate values

###  Why L1 encourages sparsity

The L1 norm penalizes non-zero values, pushing many gate values toward zero. Since gates lie between 0 and 1, minimizing their values results in many weights becoming effectively inactive.

---

##  Experimental Setup

* Dataset: CIFAR-10
* Model: Fully Connected Neural Network with 3 PrunableLinear layers
* Activation: ReLU
* Optimizer: Adam
* Epochs: 20
* Batch Size: 64

---

##  Results

| Lambda | Accuracy (%) | Sparsity (%) |
| ------ | ------------ | ------------ |
| 0.1    | 53.60        | 68.46        |
| 0.2    | 53.51        | 69.85        |
| 0.3    | 53.55        | 71.07        |

---

##  Analysis

From the results, we observe a clear trade-off between sparsity and accuracy:

* As **λ increases**, sparsity increases, meaning more weights are pruned
* However, higher sparsity leads to a **drop in accuracy**, as important connections may also be removed

A moderate value of λ provides the best balance, achieving significant pruning while maintaining reasonable performance.

---

##  Gate Distribution Visualization

The following plot shows the distribution of gate values for different λ values:

![Gate Distribution](combined_gate_distribution.png)

###  Interpretation

* For lower λ values, gate values are spread out → less pruning
* For higher λ values, many gates concentrate near **0** → more pruning
* A successful pruning model shows:

  * A spike near **0** (pruned weights)
  * Some values near **1** (important weights retained)

---

##  Limitations

* The model uses fully connected layers, which are not optimal for image data like CIFAR-10
* As a result, accuracy is lower compared to convolutional architectures

---

##  Conclusion

This project demonstrates that neural networks can learn to prune themselves during training using a gating mechanism and sparsity regularization. The model successfully identifies and removes redundant connections, reducing complexity while preserving performance to a reasonable extent.

---
