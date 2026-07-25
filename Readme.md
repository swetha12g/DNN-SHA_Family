# Deep Neural Cryptography: Implementation of SHA-1, SHA-2, and SHA-3 Family Hash Functions Using Deep Neural Networks

<img width="1536" height="1024" alt="ChatGPT Image Jul 24, 2026, 12_54_16 AM" src="https://github.com/user-attachments/assets/d437b70a-d863-4220-9654-b61060f22138" />

## Overview
This project (https://colab.research.google.com/drive/15EwJ9I518CMAhpUe0irbqAFcPa8FLJK4?usp=drive_link) investigates the capability of Deep Neural Networks (DNNs) to approximate the deterministic, highly non-linear mappings of cryptographic hash functions. It focuses on three major hash families: SHA-1, SHA-2 (SHA-256), and SHA-3 (SHA-3-256). Rather than replacing these algorithms, the objective is to experimentally evaluate whether a feed-forward DNN can learn their complex input-output relationships.

## Research Objective
The primary goal is to evaluate whether a deep learning architecture can learn the bit-wise output mapping of standard cryptographic hash algorithms from randomly generated input strings. This study explores the limitations of statistical learning when applied to deterministic cryptographic functions characterized by strong diffusion and avalanche properties.

## Methodology
The implementation follows a rigorous data-science pipeline:
1. **Data Generation**: 1000 random alpha-numeric strings are generated as inputs.
2. **Ground Truth Generation**: Actual hashes are computed using Python's `hashlib` to serve as labels.
3. **Encoding**:
    * **Input**: One-Hot Encoding (converting characters to binary vectors).
    * **Output**: Binary Vectorization (converting hex hashes to bit arrays).
4. **Deep Neural Network Architecture**:
    The model is identified as a **Deep** Neural Network due to its multiple hidden layers.
    * **Layer 1 (Input)**: Dimensionality based on encoded string length.
    * **Layer 2 (Dense)**: 512 neurons with ReLU activation.
    * **Layer 3 (Dense)**: 256 neurons with ReLU activation.
    * **Layer 4 (Output)**: Dense layer with Sigmoid activation (one neuron per hash bit).

## Implementation & Results

### SHA-256 Emulation
* **Status**: Successfully Trained.
* **Observation**: Although the model completed training successfully, the bit-wise prediction accuracy remained close to random guessing (~50%), with only marginal reduction in Binary Cross-Entropy loss. This demonstrates that the avalanche property of SHA-256 prevents a standard DNN from learning meaningful statistical relationships. This demonstrates the high complexity of the SHA-2 family's avalanche effect, making it resistant to simple neural emulation.

### SHA-1 Emulation
* **Status**: Successfully Trained.
* **Observation**: The model successfully completed training; however, the bit-wise prediction accuracy remained approximately at the random baseline (~50%). Despite the smaller 160-bit output space, the DNN was unable to learn meaningful input-output mappings. While higher than SHA-256, it still highlights that the mapping is highly non-linear and difficult for a DNN to memorize with small datasets.

### SHA-3 (SHA-3-256) Emulation
* **Status**: Successfully Trained.
* **Observation**: This family uses the Keccak sponge construction. Similar to SHA-256, the bit-wise prediction accuracy remained near the random baseline (~50%). The Keccak sponge construction effectively eliminates exploitable statistical patterns, making approximation by a conventional feed-forward DNN extremely difficult. 

## Conclusion
While the Deep Neural Networks were successfully implemented and trained using a 512–256 hidden-layer architecture, the experimental results demonstrate that they were unable to learn meaningful approximations of SHA-1, SHA-256, or SHA-3-256 beyond random bit-wise prediction. These findings reinforce the robustness of modern cryptographic hash functions and highlight the limitations of conventional deep learning models when applied to deterministic cryptographic primitives.

## Requirements
- TensorFlow / Keras
- NumPy
- Pandas
- Matplotlib
- Scikit-learn


# III. Proposed Methodology

This section delineates the systemic approach adopted to investigate the learning capabilities of Deep Neural Networks (DNNs) in the context of cryptographic hash function emulation. The methodology is divided into three primary phases: Data Synthesis, Input/Output Encoding, and Deep Multi-Layer Perceptron (DMLP) design.

## A. Data Synthesis and Ground Truth Generation
To evaluate the model, a synthetic dataset was constructed consisting of random alpha-numeric strings. 
- **Input Space ($S$):** Random strings of fixed length $L=16$ were generated from a character set $\Sigma$ containing lowercase letters and digits ($|\Sigma| = 36$).
- **Target Functions ($H$):** For each input $s \in S$, the ground truth was computed using standard cryptographic implementations of SHA-1, SHA-256, and SHA-3-256.
- **Dataset Size:** A total of $N=1000$ samples were generated for each hash family to assess the model's ability to generalize or memorize bit-level mappings.

## B. Data Representation and Encoding
Neural networks require numerical tensors. Given the discrete nature of text and the bit-oriented nature of hashes, a specialized encoding scheme was implemented:
1. **Input Encoding (One-Hot):** Each character in the input string is transformed into a $|\Sigma|$-dimensional binary vector. The total input vector $X$ is a flattened representation of size $L \times 36 = 576$ dimensions.
2. **Output Encoding (Binary Vectorization):** The hexadecimal output of the hash functions is converted into a raw bit array. 
    - **SHA-1:** $y \in \{0,1\}^{160}$
    - **SHA-256 / SHA-3-256:** $y \in \{0,1\}^{256}$

## C. Deep Neural Network Architecture
The proposed architecture is a Deep Multi-Layer Perceptron (DMLP) designed to handle multi-label binary classification, where each bit of the hash is treated as an independent target.

| Layer | Type | Configuration | Activation |
| :--- | :--- | :--- | :--- |
| 1 | Input | 576 Units | - |
| 2 | Dense (Hidden) | 512 Units | ReLU |
| 3 | Dense (Hidden) | 256 Units | ReLU |
| 4 | Output | 160 or 256 Units | Sigmoid |

## D. Training Strategy
The model was optimized using the **Adam** optimizer, chosen for its adaptive learning rate properties which are beneficial when navigating the highly non-linear loss landscapes of cryptographic functions. The objective function utilized was **Binary Cross-Entropy**, formulated as:

$$L(y, \hat{y}) = -\frac{1}{B} \sum_{i=1}^{B} [y_i \log(\hat{y}_i) + (1-y_i) \log(1-\hat{y}_i)]$$

where $B$ represents the number of bits in the hash.This approach penalizes prediction errors independently for each output bit. Due to the avalanche effect exhibited by cryptographic hash functions, even a one-bit change in the input produces widespread and unpredictable changes in the output, making the mapping extremely difficult for a conventional DNN to learn.

#### 4. Empirical Performance Visualizations

**A. SHA-1 Training Performance**
<img width="1010" height="470" alt="image" src="https://github.com/user-attachments/assets/9e49ec3a-f7a8-4b09-a4d6-7d00b26442ce" />

*The SHA-1 training curves exhibit only marginal reductions in loss, while the bit-wise prediction accuracy remains close to the random baseline (~50%). These observations indicate that the DMLP fails to learn meaningful representations of the highly non-linear mapping implemented by SHA-1.

**B. SHA-2 (SHA-256) Training Performance**
<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/ca6f8755-e02c-4799-aab6-be975853e642" />

*Observations: The SHA-256 model demonstrates consistently high Binary Cross-Entropy loss and bit-wise prediction accuracy close to random guessing (~50%). This behavior reflects the effectiveness of SHA-256's compression function and avalanche property in preventing statistical approximation by conventional feed-forward neural networks.

**C. SHA-3 (SHA-3-256) Training Performance**
<img width="1010" height="470" alt="image" src="https://github.com/user-attachments/assets/6f5147fc-13dc-4c49-93a7-12fb8adb970c" />

*The SHA-3 training metrics similarly show minimal improvement throughout training, with prediction accuracy remaining near the random baseline (~50%). These results illustrate the robustness of the Keccak sponge construction against approximation using standard DNN architectures. This visually confirms that the Keccak sponge construction effectively masks any statistical patterns that a standard feed-forward neural network might exploit.*

### Comparative Analysis of Training Convergence

The following plot compares the Binary Cross-Entropy loss across SHA-1, SHA-256, and SHA-3 models.The absence of significant loss reduction across all three experiments demonstrates that the proposed Deep Multi-Layer Perceptron is unable to learn meaningful approximations of these cryptographic hash functions. This experimentally reinforces the strong diffusion, non-linearity, and avalanche characteristics that underpin the security of modern hash algorithms.

plt.figure(figsize=(10, 6))

<img width="855" height="547" alt="image" src="https://github.com/user-attachments/assets/972d95a7-b1c6-45c9-9b88-fa4c624f2324" />



