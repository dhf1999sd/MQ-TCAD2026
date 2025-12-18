


This repository contains the FPGA implementation and evaluation framework of
the Multiple Queues (MQ) architecture proposed in our paper:

> **Designing Multiple Queues to Support Asynchronous Traffic Shaping in Shared-Buffer TSN Switches**
> (submitted to IEEE TCAD)
> MQ implements ATS shaped queues as metadata-driven logical queues on top of a
> shared-buffer switch architecture.
> Key design features:
>
> - Virtual queues implemented using head/tail pointers
> - Shared Metadata Memory storing linked-list nodes
> - Centralized Multiple Queues Manager controlling enqueue/dequeue
> - No per-queue payload FIFOs
> - Compatible with IEEE 802.1Qcr ATS semantics

## License

This project is released under the MIT License (or BSD-3-Clause).
