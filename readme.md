# Third paper

The third paper is after the [Efficient Implementations of CRAFT Cipher For Internet of Things](https://github.com/jiahaoxiang2000/FirstPaper) and [Low-Latency Implementation of Bitsliced SPN-Cipher on IoT Processors](https://github.com/jiahaoxiang2000/SecondPaper). The First paper is hardware implement, the second paper is software implement, and the third paper is the combination of hardware and software implement.

## background survey

Considering the particular difficulties of combining hardware and software implementations, designing both from scratch would consume too much time.
Instead, we can leverage existing heterogeneous hardware to accelerate the software implementation of the cipher, which may offer a more efficient approach.

We aim to use GPU units to accelerate the software implementation of the cipher.
GPU units are heterogeneous hardware components particularly well-suited for parallel computing.
Additionally, Quantum cryptography is gaining significant popularity in 2025 and shows an upward trend. Therefore, we utilized GPU units to accelerate the software implementation of the Quantum cryptography hash signature, known as SLH-DSA.

For more details, please refer to the [background folder](./background/)

## TODO

_Tue Apr 8 16:04:41 CST 2025_

- [ ] add the metic/view to explain our job, now like use the Primitive Operation Parallel Efficient (POPE) ref issue https://github.com/jiahaoxiang2000/ThirdPaper/issues/2
  - [ ] improve required: need do more experiment, find the writing angle. _priority 2_
- [ ] finish the experiment section, add the data for figure and table, ref issue https://github.com/jiahaoxiang2000/ThirdPaper/issues/4 _priority 0_
  - [ ] we only need to add the FLP component experiment.
  - [ ] here we also need to reflect the FLP component. on the future, we also need to clean the code for only have our experiment code.

## experiment platform

we all GPU code is on the [sphincs-plus](https://github.com/jiahaoxiang2000/sphincs-plus), (maybe we call the SLH-DSA more great).
