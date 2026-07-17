# Scaling book

[Scaling book link](https://jax-ml.github.io/scaling-book/)

## Part 3. Sharded Matmuls

[Section link](https://jax-ml.github.io/scaling-book/sharding/)

[Collective operations](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/usage/collectives.html) from nvidia.

- An AllReduce is **twice** expensive as an AllGather. AllReduce can be expressed as a composition of two other primitives: A **ReduceScatter** and an **AllGather**.

- AllToAlls are typically required to rearrange sharded layouts between different regions of a sharded computation that don’t have compatible layout schemes. AllToAll actually **cheaper** then an AllGather by a factor of **1/4**.
