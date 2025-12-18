MQ_supporting_ATS — Shared-Buffer Multi-Queue Switch Architecture Based on ATS

Project Overview
- ZYNQ/Vivado design with 4 RGMII ports and an internal `64-bit` data path.
- Core logic implements ATS (Asynchronous Traffic Shaping): per-frame eligible time is computed via a `token bucket` and `group eligibility time`, then scheduled with strict priority under a shared buffer.
- The codebase covers MAC adaptation for RX/TX, input arbitration, Ethernet parsing and MAC address lookup, shared buffer and queue management, frame eligibility calculation, and egress shaping.

