This repository contains the FPGA implementation and evaluation framework of the
**Multiple Queues (MQ)** architecture proposed in our paper:

> **Designing Multiple Queues to Support Asynchronous Traffic Shaping in Shared-Buffer TSN Switches**  
> *(submitted to IEEE TCAD)*

The MQ architecture implements ATS shaped queues as metadata-driven logical queues
on top of a shared-buffer switch architecture.

**Key design features include:**
- Virtual queues implemented using lightweight head/tail pointers  
- Shared metadata memory storing linked-list queue nodes  
- A centralized Multiple Queues Manager for enqueue and dequeue control  
- No per-queue payload FIFOs  
- Full compatibility with IEEE 802.1Qcr ATS semantics  

## License

This project is released under the **MIT License**.

