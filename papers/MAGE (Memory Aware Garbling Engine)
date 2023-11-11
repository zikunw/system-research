MAGE: Nearly Zero-Cost Virtual Memory for Secure Computation

Code: https://github.com/ucbrise/mage

Secure Computation requires heavy memory foot print to achieve their cryptographic purpose. Most of the heavy memory footprint is swapped by OS to secondary storage. (Which requires I/O and really slow in general)

MAGE suggests a way to execute secure computation efficiently when it does not fit in memory.

Key insight: most secure computation algorithm's obliviousness allows us to calculate the access pattern in advance and use this information to manage memory more efficiently than OS paging.

MAGE optimizes storage bandwidth by evicting pages using MIN, and optimizes latency via prefetching and asynchronous eviction.

Written in C++ and support two SC protocols:
1. Garbled circuits, a type of SMPC
2. CKKS, a type of HE
