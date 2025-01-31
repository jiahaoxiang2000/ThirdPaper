# CUSPX: Efficient GPU Implementations of Post-Quantum Signature SPHINCS+

## Abstract

- We introduce CUSPX (CUDA SPHINCS+), the first large-scale **parallel implementation** of SPHINCS+ capable of running across 10,000 cores.
  - CUSPX leverages a novel three-level parallelism framework, applying it to _algorithmic parallelism_, _data parallelism_, and _hybrid parallelism_.
  - CUSPX introduces parallel Merkle tree construction algorithms for arbitrary parallel scales and several load-balancing solutions, further enhancing performance.
- CUSPX achieves a single task’s signature generation latency of **0.67 ms**, demonstrating a 5,105× speedup over a single-thread version and an 18.50× speedup over the previous fastest implementation.

## 1. Introduction

Nowadays, the National Institute of Standards and Technology (NIST) has selected three DSAs for standardization : CRYSTALS-Dilithium, Falcon, and SPHINCS+.
Also, a KEM is selected for standardization: CRYSTALS-KYBER. According to the NIST’s recent presentation, the standardization of these algorithms is expected to be finalized in 2024.
Compared to the other three lattice-based algorithms, SPHINCS+ can offer more conservative security but with poorer performance. Therefore, it is necessary to optimizes the performance of $SPHINCS+$.

> **SPHINCS+** is the PQC stander until 2025.

### 1.1 Motivation

The reported maximum throughput of SPHINCS+-128s-simple is 80.65 signatures per second on an FPGA implementation, corresponding to 12.40 ms per signature and 14,285.71 verifications per second, or 0.07 ms per verification.

### 1.2 Challenges and Contributions

Our design faces three challenges.

- Large-scale parallel designs: Efficiently parallelizing $SPHINCS+$ on GPUs, with their many cores and poor single-core performance, is a challenging task.
- Hybrid parallel designs: Hybrid parallelism requires a trade-off between latency and throughput, which further complicates the design of parallel schemes.
- GPU efficient implementation: It is a challenge to make $SPHINCS+$ fully utilizes the hardware resources on the GPU.

> the challenges not close related to the $SPHINCS+$ algorithm itself. It is the all parallel computing challenges.
