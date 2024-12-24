# background

here we will introduce the background of the third paper. include following parts:

- [motivation](#motivation)
- [background survey](#background-survey)

## motivation

the motivation details see the [motivation](./motivation.ipynb) part.

## background survey

the hardware and software combination to implement the cipher. One way to design the specific hardware is to use the FPGA. Then design specific software instruction set, to implement the cipher.
[RISC-V instruction set extensions for lightweight symmetric cryptography](https://tches.iacr.org/index.php/TCHES/article/view/9951) CHES-2023 paper.
We want to use the GPU to implement, because those are more widely used, and have more powerful computation ability.
like the [Efficient implementation of AES-CTR and AES-ECB on GPUs with applications for high-speed FrodoKEM and exhaustive key search](https://ieeexplore.ieee.org/abstract/document/9749135/) Transactions on Circuits and Systems 2022 paper.

Here is the related work on the GPU implementation of the cipher. The table shows the paper name, publication year, publication origin, and main results.

| paper name                                                                                                       | publication year | publication origin                                              | main results                                                         |
| ---------------------------------------------------------------------------------------------------------------- | ---------------- | --------------------------------------------------------------- | -------------------------------------------------------------------- |
| Speed Record of AES-CTR and AES-ECB Bit-Sliced Implementation on GPUs                                            | 2024             | IEEE EMBEDDED SYSTEMS LETTERS                                   | Showed high-throughput GPU-based encryption                          |
| Optimized GPU Implementation and Performance Analysis of HC Series of Stream Ciphers                             | 2012             | Information Security and Cryptology                             | Achieved faster stream cipher encryption on GPU                      |
| High Throughput Implementation of Post-quantum Key Encapsulation and Decapsulation on GPU for IoT Applications   | 2021             | IEEE Transactions on Services Computing                         | Demonstrated efficient post-quantum cryptography solutions on GPU    |
| Fast AES Implementation–A High-Throughput Bitsliced Approach                                                     | 2019             | IEEE Transactions on parallel and distributed systems           | Proposed bitsliced method for significant AES performance gains      |
| Elastic MSM–A Fast Elastic and ModularPreprocessing Technique for Multi-ScalarMultiplication Algorithm on GPUs   | 2024             | Cryptology ePrint Archive                                       | Improved multi-scalar multiplication speeds with GPU acceleration    |
| Efficient Implementation of AES-CTR and AES-ECB on GPUs With Applications for High-Speed FrodoKEM and Key Search | 2022             | IEEE Transactions on Circuits and Systems II: Express Briefs    | Showed AES on GPUs delivering high performance for lattice-based KEM |
| ACE-HoT–Accelerating an Extreme Amount of Symmetric Cipher Evaluations for High-Order Avalanche Tests            | 2023             | International Conference on Cryptology and Information Security | Introduced a GPU-based approach to efficiently run avalanche tests   |
