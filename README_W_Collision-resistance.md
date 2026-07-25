# Deep Neural Cryptography: Implementation of SHA-1, SHA-2, and SHA-3 Family Hash Functions Using Deep Neural Networks

## Overview
This project investigates the capability of **Deep Neural Networks (DNNs)** to learn the deterministic, non-linear mappings of cryptographic hash functions. It focuses on the emulation of three major families: **SHA-1**, **SHA-2 (SHA-256)**, and **SHA-3 (SHA-3-256)**.

## Research Objective
The primary goal is to evaluate if a deep learning architecture can successfully learn to reproduce the bit-wise output of standard hash algorithms based on random string inputs. This study explores the boundary between statistical learning and cryptographic determinism.

## III. Proposed Methodology
This section delineates the systemic approach adopted to investigate the learning capabilities of Deep Neural Networks (DNNs) in the context of cryptographic hash function emulation. The methodology is divided into three primary phases: Data Synthesis, Input/Output Encoding, and Deep Multi-Layer Perceptron (DMLP) design.

### A. Data Synthesis and Ground Truth Generation
To evaluate the model, a synthetic dataset was constructed consisting of random alpha-numeric strings.
- **Input Space ($S$):** Random strings of fixed length $L=16$ were generated from a character set $\Sigma$ containing lowercase letters and digits ($|\Sigma| = 36$).
- **Target Functions ($H$):** For each input $s \in S$, the ground truth was computed using standard cryptographic implementations of SHA-1, SHA-256, and SHA-3-256.
- **Dataset Size:** A total of $N=1000$ samples were generated for each hash family to assess the model's ability to generalize or memorize bit-level mappings.

### B. Data Representation and Encoding
Neural networks require numerical tensors. Given the discrete nature of text and the bit-oriented nature of hashes, a specialized encoding scheme was implemented:
1.  **Input Encoding (One-Hot):** Each character in the input string is transformed into a $|\Sigma|$-dimensional binary vector. The total input vector $X$ is a flattened representation of size $L \times 36 = 576$ dimensions.
2.  **Output Encoding (Binary Vectorization):** The hexadecimal output of the hash functions is converted into a raw bit array.
    -   **SHA-1:** $y \in \{0,1\}^{160}$
    -   **SHA-256 / SHA-3-256:** $y \in \{0,1\}^{256}$

### C. Deep Neural Network Architecture
The proposed architecture is a Deep Multi-Layer Perceptron (DMLP) designed to handle multi-label binary classification, where each bit of the hash is treated as an independent target.

| Layer | Type | Configuration | Activation |
| :--- | :--- | :--- | :--- |
| 1 | Input | 576 Units | - |
| 2 | Dense (Hidden) | 512 Units | ReLU |
| 3 | Dense (Hidden) | 256 Units | ReLU |
| 4 | Output | 160 or 256 Units | Sigmoid |

### D. Training Strategy
The model was optimized using the **Adam** optimizer, chosen for its adaptive learning rate properties which are beneficial when navigating the highly non-linear loss landscapes of cryptographic functions. The objective function utilized was **Binary Cross-Entropy**, formulated as:

$$L(y, \hat{y}) = -\frac{1}{B} \sum_{i=1}^{B} [y_i \log(\hat{y}_i) + (1-y_i) \log(1-\hat{y}_i)]$$

where $B$ represents the number of bits in the hash. This approach allows the network to penalize deviations on a per-bit basis, facilitating the study of the 'Avalanche Effect' within the neural weights.

## Implementation & Results Analysis

### 1. Training Convergence (Loss Curves)
As shown in the generated visualization plots below, the **Binary Cross-Entropy Loss** for all three families (SHA-1, SHA-256, and SHA-3) remains high and flat throughout the training process.
- **Observation**: The loss curves do not show the typical 'elbow' or steep decline associated with successful learning.
- **Significance**: This indicates that the neural network is unable to find a differentiable pattern in the hash mapping, confirming the **Avalanche Effect** where a single bit change in input leads to a drastic, uncorrelated change in output.

### 2. Accuracy Comparison
| Hash Family | Bit-wise Accuracy | Interpretation |
| :--- | :--- | :--- |
| **SHA-1** | ~0.0% | Slightest correlation found, likely due to the smaller bit-space (160-bit). |
| **SHA-256** | ~0.5% | Near-random performance, showcasing strong non-linearity. |
| **SHA-3-256** | ~1.0% | Highest robustness; the sponge construction makes it invisible to MLP learning. |

### 3. Collision Resistance Demonstration
A key property of cryptographic hash functions is collision resistance. The demonstration involved generating 5000 random strings and checking if any produced identical hash outputs for SHA-1, SHA-256, and SHA-3_256. As expected for cryptographically secure hashes:
- **No collisions were found** for SHA-1, SHA-256, or SHA-3_256 within the generated sample set.
- This empirically confirms the practical impossibility of finding collisions for these functions with a reasonable number of inputs, highlighting their strength in maintaining data integrity and security.

<img width="1042" height="597" alt="image" src="https://github.com/user-attachments/assets/d8bd38a3-2f7a-408d-a00c-a07c354c5826" />


### 4. Empirical Performance Visualizations
Below are the three comparative visualizations mapped for your research results:

| Algorithm | Loss & Accuracy Visualization |
| :--- | :--- |
| **SHA-1** | <img width="1010" height="470" alt="image" src="https://github.com/user-attachments/assets/eceb7c96-6b72-47ac-b3c9-2e5d98a2ac29" />
|
| **SHA-256** | <img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/f5d99d30-9f3c-410d-a40b-33705a1bc12d" />
 |
| **SHA-3** | <img width="1010" height="470" alt="image" src="https://github.com/user-attachments/assets/4e3d3ae2-eee7-429c-a5d3-7ad34ae3b67c" />
 |

**Analysis for README:**
- **SHA-1**: Shows high volatility in training, reflecting the smaller 160-bit state space but maintaining high entropy.
- **SHA-256**: Shows a stable, high loss plateau, indicating the model cannot find a gradient to optimize the compression function.
- **SHA-3**: Demonstrates near-zero accuracy improvement, confirming that the sponge construction is highly resistant to standard multi-layer perceptron emulation.

## Conclusion
While the DNNs were successfully implemented and 'deep' in structure (utilizing 512-256 hidden layer configurations), the results prove that cryptographic hash functions are robust against simple machine learning emulation. This project serves as a foundational step for advanced **Deep Neural Cryptanalysis**, investigating how AI models interact with secure cryptographic primitives.

## Requirements
- TensorFlow / Keras
- NumPy
- Pandas
- Matplotlib
- Scikit-learn
