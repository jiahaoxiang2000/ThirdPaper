# background

here we will introduce the background of the third paper. include following parts:

- [background](#background)
  - [motivation](#motivation)
  - [background survey](#background-survey)

## motivation

the motivation details see the [motivation](./motivation.md) part.

## background survey

the hardware and software combination to implement the cipher. One way to design the specific hardware is to use the FPGA. Then design specific software instruction set, to implement the cipher.
[RISC-V instruction set extensions for lightweight symmetric cryptography](https://tches.iacr.org/index.php/TCHES/article/view/9951) CHES-2023 paper.
We want to use the GPU to implement, because those are more widely used, and have more powerful computation ability.
like the [Efficient implementation of AES-CTR and AES-ECB on GPUs with applications for high-speed FrodoKEM and exhaustive key search](https://ieeexplore.ieee.org/abstract/document/9749135/) Transactions on Circuits and Systems 2022 paper.
