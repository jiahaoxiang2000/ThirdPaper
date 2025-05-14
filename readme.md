# Third paper

The third paper is after the [Efficient Implementations of CRAFT Cipher For Internet of Things](https://github.com/jiahaoxiang2000/FirstPaper) and [Low-Latency Implementation of Bitsliced SPN-Cipher on IoT Processors](https://github.com/jiahaoxiang2000/SecondPaper). The First paper is hardware implement, the second paper is software implement, and the third paper is the combination of hardware and software implement.

## Submission

You can check the current submission status on the [status check page](https://ieee.atyponrex.com/submission/dashboard). The submission system has moved from _manuscriptcentral_ to _atyponrex_, so please use the new platform for updates.

**TCAS-II** (IEEE Trans. Circuits Syst. II Express Briefs)

- Submitted to TCAS-II on Thu May 8, 2025, 05:34:03 PM CST.
- Rejected by TCAS-II on Wed May 14 08:35:48 AM HKT 2025.

## TODO

_Wed May 14 08:39:10 AM HKT 2025_

- [ ] find the new journal for letter type paper or change the paper to a regular paper. If on the letter type have three months on review, but the regular paper have six months on review. The times is our main problem.

## Background survey

Considering the particular difficulties of combining hardware and software implementations, designing both from scratch would consume too much time.
Instead, we can leverage existing heterogeneous hardware to accelerate the software implementation of the cipher, which may offer a more efficient approach.

We aim to use GPU units to accelerate the software implementation of the cipher.
GPU units are heterogeneous hardware components particularly well-suited for parallel computing.
Additionally, Quantum cryptography is gaining significant popularity in 2025 and shows an upward trend. Therefore, we utilized GPU units to accelerate the software implementation of the Quantum cryptography hash signature, known as SLH-DSA.

For more details, please refer to the [background folder](./background/)

## experiment platform

we all GPU code is on the [sphincs-plus](https://github.com/jiahaoxiang2000/sphincs-plus), (maybe we call the SLH-DSA more great).
