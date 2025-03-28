# background

here we will introduce the background of the third paper. include following parts:

- [background](#background)
  - [motivation](#motivation)
    - [Other implementations](#other-implementations)
  - [related work](#related-work)
    - [GPU Implementations of SPHINCS+](#gpu-implementations-of-sphincs)
      - [Sun et al. (2020)](#sun-et-al-2020)
      - [Kim et al. - Parallel Implementation of SPHINCS+ With GPUs](#kim-et-al---parallel-implementation-of-sphincs-with-gpus)
      - [GRASP - GPU-based paRallel Accelerated SPHINCS+](#grasp---gpu-based-parallel-accelerated-sphincs)
      - [CUSPX - CUDA SPHINCS+](#cuspx---cuda-sphincs)

## motivation

For more popular projects, here we want to do the Post-Quantum Cryptography [PQC](https://csrc.nist.gov/pqc-standardization) project, which specify algorithms derived from CRYSTALS-Dilithium, CRYSTALS-KYBER and SPHINCS+, were published August 13, 2024.

Here we want to optimize the SPHINCS+ scheme, which is not digital signature algorithm, like the SHA-256. SPHINCS+ is a stateless hash-based signature scheme, which is a post-quantum secure digital signature algorithm. Here is the [3rd round submission package](https://sphincs.org/data/sphincs+-round3-submission-nist.zip) for the NIST PQC project.

### Other implementations

- [nvidia cupqc](https://developer.nvidia.com/cupqc) published on the 2024-10-03. Not open code source, only supported by the x86 library.

## related work

| **Paper Title**                                                         | **Description**                                                                                                              | **Key Contributions**                                                                                                   | **Link**                                                                                                              |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Parallel Implementation of SPHINCS+ With GPUs                           | This paper explores parallel and optimized implementations of SPHINCS+ on NVIDIA GPU architectures.                          | Identifies parallelizable parts of SPHINCS+; achieves performance improvements across Pascal, Turing, and Ampere GPUs.  | [IEEE Xplore](https://ieeexplore.ieee.org/document/10461494/?utm_source=chatgpt.com)                                  |
| CUSPX: Efficient GPU Implementations of Post-Quantum Signature SPHINCS+ | The authors introduce CUSPX, a large-scale parallel implementation of SPHINCS+ on GPUs.                                      | Optimizes SPHINCS+ for scalability and efficiency, achieving significant improvements on large-scale GPU architectures. | [IEEE Computer Society](https://www.computer.org/csdl/journal/tc/5555/01/10677363/209otdi2Xi8?utm_source=chatgpt.com) |
| Accelerating Hash-based PQC Performance on GPU Parallel                 | Discusses GPU-accelerated implementations of SPHINCS+, with an emphasis on improving throughput of signing and verification. | Utilizes CUDA optimizations to enhance performance, with a focus on both signing and verification processes.            | [ePrint](https://eprint.iacr.org/2024/1030.pdf?utm_source=chatgpt.com)                                                |

- [Intel-Co-Develops-One-of-Three-New-Post-Quantum-Crypto-Standards](https://community.intel.com/t5/Blogs/Tech-Innovation/Cloud/Intel-Co-Develops-One-of-Three-New-Post-Quantum-Crypto-Standards/post/1638099) Post.

### GPU Implementations of SPHINCS+

Several research efforts have focused on optimizing SPHINCS+ using GPU parallelization:

#### Sun et al. (2020)

- Developed parallelization techniques for MSS, HORST, and WOTS+
- Achieved 5,152 signatures per second on GTX 1080
- Throughput scaled up to 27,052 ops/sec using multiple GPUs
- Did not implement parallelization across hypertree layers

#### Kim et al. - Parallel Implementation of SPHINCS+ With GPUs

- Implemented parallel methods for FORS, WOTS+, MSS, and hypertree computation
- Optimized critical operations in SPHINCS+ by leveraging warp-based execution and efficient memory access
- Focused on parallelizing MSS leaf node generation, identified as the major bottleneck (~89% of execution time)
- Used separate CUDA kernels for different components, requiring global memory exchanges

#### GRASP - GPU-based paRallel Accelerated SPHINCS+

- Implemented an adaptable parallelization strategy with throughput vs. latency optimization
- Introduced kernel fusion technology to consolidate all SPHINCS+ submodules into a single CUDA kernel
- Eliminated overhead from inter-kernel data exchanges
- Achieved throughput improvements of 1.37× to 3.45× compared to previous GPU implementations
- Provided flexible thread configuration options optimized for different security levels
- Implemented both high-throughput and low-latency modes

#### CUSPX - CUDA SPHINCS+

- First large-scale parallel implementation capable of running on 10,000+ cores
- Introduced a novel three-level parallelism framework:
  1. Inter-tree parallelism (multiple Merkle trees in parallel)
  2. Inter-node parallelism (nodes at same layer processed in parallel)
  3. Intra-node parallelism (parallel computation within leaf nodes)
- Developed parallel Merkle tree construction algorithms for arbitrary parallel scales
- Implemented both subtree-first and node-first parallel schemes with different tradeoffs
- Added a fourth level for hybrid parallelism (multiple tasks in parallel)
- Achieved signature generation latency of 0.67ms, demonstrating 5,105× speedup over single-thread implementation
- Incorporated various load-balancing strategies for WOTS+ and FORS trees
