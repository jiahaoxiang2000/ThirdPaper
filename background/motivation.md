# Motivation

For more popular projects, here we want to do the Post-Quantum Cryptography [PQC](https://csrc.nist.gov/pqc-standardization) project, which specify algorithms derived from CRYSTALS-Dilithium, CRYSTALS-KYBER and SPHINCS+, were published August 13, 2024.

Here we want to optimize the SPHINCS+ scheme, which is not digital signature algorithm, like the SHA-256. SPHINCS+ is a stateless hash-based signature scheme, which is a post-quantum secure digital signature algorithm. Here is the [3rd round submission package](https://sphincs.org/data/sphincs+-round3-submission-nist.zip) for the NIST PQC project.

## related work

| **Paper Title**                                                         | **Description**                                                                                                              | **Key Contributions**                                                                                                   | **Link**                                                                                                              |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Parallel Implementation of SPHINCS+ With GPUs                           | This paper explores parallel and optimized implementations of SPHINCS+ on NVIDIA GPU architectures.                          | Identifies parallelizable parts of SPHINCS+; achieves performance improvements across Pascal, Turing, and Ampere GPUs.  | [IEEE Xplore](https://ieeexplore.ieee.org/document/10461494/?utm_source=chatgpt.com)                                  |
| CUSPX: Efficient GPU Implementations of Post-Quantum Signature SPHINCS+ | The authors introduce CUSPX, a large-scale parallel implementation of SPHINCS+ on GPUs.                                      | Optimizes SPHINCS+ for scalability and efficiency, achieving significant improvements on large-scale GPU architectures. | [IEEE Computer Society](https://www.computer.org/csdl/journal/tc/5555/01/10677363/209otdi2Xi8?utm_source=chatgpt.com) |
| Accelerating Hash-based PQC Performance on GPU Parallel                 | Discusses GPU-accelerated implementations of SPHINCS+, with an emphasis on improving throughput of signing and verification. | Utilizes CUDA optimizations to enhance performance, with a focus on both signing and verification processes.            | [ePrint](https://eprint.iacr.org/2024/1030.pdf?utm_source=chatgpt.com)                                                |

## Other implementations

- [nvidia cupqc](https://developer.nvidia.com/cupqc) published on the 2024-10-03. Not open code source, only supported by the x86 library.
