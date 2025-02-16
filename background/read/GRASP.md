# GRASP: Accelerating Hash-based PQC Performance on GPU Parallel Architecture

## Abstract

- **Context:** SPHINCS+, a leading post-quantum digital signature algorithm, is recognized by NIST but has performance limitations in real-world applications.
- **Proposal:** Introduction of GRASP (GPU-based paRallel Accelerated SPHINCS+) to address these performance issues.
- **Approach:**
  - Leverages GPU parallelization via CUDA.
  - Implements an adaptable parallelization strategy focusing on critical sections in the signing and verification processes.
  - Applies bottom-up optimizations, including memory access improvements and hypertree computation enhancements.
  - Utilizes kernel fusion technology.
- **Results:** Achieves throughput improvements between 1.37× to 3.45× compared to current GPU-based approaches and outperforms the NIST reference implementation by over three orders of magnitude.

## Introduction

- **SPHINCS+ Characteristics:**

  - SPHINCS+ is highlighted as the only hash-based digital signature algorithm, offering relatively short public and private keys which simplify key management.
  - Despite its advantages, SPHINCS+ suffers from significant performance issues, with signing and verification being substantially slower (approximately 150× and 50×, respectively) than alternatives like Dilithium.

- **Performance Challenges & Optimizations:**

  - To enable SPHINCS+ for practical applications (e.g., TLS, DNSSEC), performance improvements—especially for signing—are essential.
  - GPUs, with their highly parallel architectures, are explored as a solution, leveraging NVIDIA’s CUDA platform to optimize cryptographic computations.
  - The text discusses GPU use in accelerating various cryptographic operations, including blockchain enhancements, TLS acceleration in cloud services, and more.

- **Context of Post-Quantum Cryptography:**
  - The introduction emphasizes the vulnerability of traditional public-key schemes (RSA, ECC) to quantum attacks.
  - It outlines the ongoing efforts by NIST to standardize quantum-safe algorithms, presenting a backdrop to the research and improvements proposed for SPHINCS+.

### Related Work

- **GPU-Based SPHINCS+ Implementations:**

  - **Sun et al. [20]:**
    - Developed parallelization techniques for MSS, HORST, and WOTS+.
    - Achieved throughput ranging from 5152 ops/sec (GTX 1080) up to 27052 ops/sec with multiple GPUs.
  - **Kim et al. [21]:**
    - Implemented parallel methods for FORS, WOTS+, MSS, and hypertree computation.
    - Demonstrated significant throughput improvements for signature generation and verification on an RTX 3090.
    - Noted inefficiencies due to the use of multiple CUDA kernels.

- **FPGA-Based Approaches:**

  - **Berthet et al. [22]:**
    - Optimized digital signature operations of WOTS+ and FORS on FPGA.
    - Achieved moderate signature generation and verification times using resource-constrained hardware (Xilinx FPGA).
  - **Amiet et al. [23]:**
    - Focused on accelerating SHA-3 and SHAKE functions on FPGA platforms.
    - Reported very fast signature generation times with optimized FPGA implementations.

- **Broader GPU Accelerations in PQC:**
  - **Zhao et al. [24]:**
    - Optimized the Dilithium algorithm by adjusting batch sizes for better GPU thread efficiency.
    - Noted significant computation time reductions on various GPU models.
  - **Shen et al. [25]:**
    - Extended GPU optimizations to include both Dilithium and hash algorithms.
    - Employed resource utilization analysis (using tools like Nsight) to achieve large throughput improvements, particularly on high-end GPU (RTX 4090).

### Contributions

- **Performance Gap & Motivation:**  
  SPHINCS+ suffers from significant performance limitations compared to other post-quantum schemes, hindering its practical use despite advantages like shorter key lengths. GPUs offer high parallelism ideal for cryptographic acceleration, yet their full computational potential for SPHINCS+ remains underutilized.

- **Main Contributions:**

  - **Parallelization Analysis & Adaptability:**  
    An in-depth examination of SPHINCS+ signing and verification processes to identify bottlenecks. GRASP proposes adaptable parallelization strategies that allow tuning of GPU thread configurations based on throughput and latency needs.

  - **CUDA-Based Bottom-Up Optimization:**  
    Leveraging CUDA for detailed optimization:

    - Enhancing memory access patterns to reduce latency.
    - Refining hypertree computation to lower the number of required WOTS+ signatures.
    - Utilizing kernel fusion techniques to consolidate computational steps, reducing kernel launch overhead.

  - **Extensive Experimental Validation:**  
    Demonstrates significant performance gains on RTX 4090:
    - **Signature Generation:** Throughput improvements of 1.37× to 1.79× at various security levels; achieves low-latency generation with up to 5.5× throughput reduction and nearly 9.4× lower latency compared to prior work.
    - **Signature Verification:** Throughput boost of 1.53× to 3.45×.
    - Overall, the GPU-optimized implementation surpasses the NIST reference by over three orders of magnitude.

## Optimized implementation

### Analysis of Parallel Strategies for SPHINCS+ Signature Generation

- **Throughput vs. Latency Trade-off:**

  - Assigning one task per thread maximizes throughput by fully utilizing GPU cores.
  - However, low computational intensity per thread leads to high latency.

- **FORS Subtree Computation:**

  - FORS consists of k independent subtrees that can be processed in parallel.
  - Using multiple threads per subtree (t > 1) causes inefficient node merging as the number of merging operations decreases per tree layer, coupled with the overhead of multiple synchronizations.
  - For maximum throughput, it is optimal to set t = 1 per subtree; for lower latency, a higher t might be considered.

- **Hypertree Strategy (WOTS+ and MSS Trees):**
  - In WOTS+, while each block can theoretically be processed by an individual thread, the subsequent compression step (merging public keys) is single-threaded, leading to thread wastage.
  - Similarly, processing each layer of the MSS tree in parallel suffers from the same inefficiencies when t > 1.
  - Thus, to maximize throughput in hypertree computations, setting t = 1 is preferred.

Overall, the analysis illustrates that while parallelizing tasks can reduce processing latency, choosing the appropriate number of threads is crucial to avoid resource wastage and maintain high throughput.

### The SPHINCS+ Signing Implementation to Maximize Throughput

- **Differences from Prior Work:**

  - Previous methods (e.g., Kim et al. [21]) used multiple CUDA kernels, dedicating one per SPHINCS+ submodule.
  - This modular approach required exchanging intermediate results via global memory, which incurs high latency.

- **Proposed Technique – Kernel Fusion:**

  - Consolidates the computations of all SPHINCS+ submodules into a single CUDA kernel.
  - Eliminates the overhead associated with inter-kernel data exchanges.

- **Parallel Computing Strategy:**

  - **CUDA Grid Configuration:**
    - A CUDA grid is organized into blocks with multiple threads, allowing for the simultaneous processing of multiple signatures.
    - Each signature is generated by a designated thread configuration that is used consistently across all submodules.
  - **Optimal Thread Configuration:**
    - Based on analysis, each submodule (hypertree layers, FORS subtrees) is assigned one thread to minimize wastage.
    - The thread count “t” is chosen such that multiples of t closely match the number of hypertree layers (d) and FORS subtrees (k).
    - For security levels 1 and 3 (with d = 22 and k = 33), using t = 11 threads allows efficient rounds of computation with no wastage.
    - At security level 5 (with d = 17 and k = 35), an 18-thread configuration is used, though it introduces minimal thread wastage.

- **Advantage of the Approach:**
  - Achieves high throughput while keeping latency within an acceptable range.
  - Focuses on the hypertree, the most time-consuming component, for optimized thread allocation.

### The SPHINCS+ Signing Implementation to Minimize Latency

- **Objective:**  
  Rather than focusing on reducing thread wastage, this section targets minimizing latency by maximizing parallelism during signature generation.

- **Hypertree Parallelization:**

  - The hypertree computation consists of two main components:
    - **MSS Tree Computation:**
      - Includes the heavy generation of Merkle tree leaves (WOTS+ key pair generation) and the relatively lighter authentication path computation.
      - Uses a thread configuration (Table IV) that assigns d·l·2^h_d threads for simultaneous WOTS+ key pair generation across all layers.
    - **WOTS+ Signature Generation:**
      - Though less demanding, it is parallelized similarly to ensure low latency.
  - The strategy ensures that all Merkle tree leaves are computed within a single round.

- **FORS Parallelization:**

  - FORS contains k independent subtrees.
  - All leaf nodes can be processed simultaneously if enough threads are available.
  - The authentication path for FORS is computed by grouping 16 threads per subtree within one block for efficient intra-block synchronization.

- **Key Takeaways:**
  - A high degree of parallelism is exploited in both hypertree and FORS computations to reduce overall latency.
  - The detailed thread configurations (as shown in Table IV) ensure that even the most time-consuming steps (like WOTS+ key pair generation) complete rapidly.
  - The approach balances massive parallel thread usage for heavy computations with controlled synchronization for lighter tasks, leading to a low-latency signing process.

### The SPHINCS+ Signature Verification Implementation for Maximizing Throughput

- **Differences from Signature Generation:**

  - During verification, the public key is derived from the signature and message rather than generating all leaf nodes as in signing.
  - Since leaf node generation constitutes ~90% of signing time, verification is inherently more efficient.

- **Feasibility of Parallelization:**

  - The verification process for Merkle trees involves sequential steps (e.g., computing the root from the authentication path), limiting parallelism.
  - In hypertree structures, while the computation of leaf node public keys can be somewhat parallelized using multiple threads, the sequential nature of node merging and inter-layer dependencies precludes effective parallelism.
  - FORS verification allows for inter-subtree parallelism since its k subtrees are independent, but intra-subtree computation remains largely serial.

- **Proposed Parallel Strategy:**
  - The complete verification is implemented within a single CUDA kernel.
  - Each signature verification is handled by a single thread to maximize throughput.
  - Although using one thread increases latency compared to a multi-threaded approach, the overall latency remains acceptable given that verification is much faster than signing.
