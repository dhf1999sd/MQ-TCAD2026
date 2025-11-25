MQ_supporting_ATS — Shared-Buffer Multi-Queue Switch Architecture Based on ATS

Project Overview
- ZYNQ/Vivado design with 4 RGMII ports and an internal `64-bit` data path.
- Core logic implements ATS (Asynchronous Traffic Shaping): per-frame eligible time is computed via a `token bucket` and `group eligibility time`, then scheduled with strict priority under a shared buffer.
- The codebase covers MAC adaptation for RX/TX, input arbitration, Ethernet parsing and MAC address lookup, shared buffer and queue management, frame eligibility calculation, and egress shaping.

Features
- `ATS Shaping`: `frame_eligbility_calculator` + `token_bucket` + `flow_entry_manager` compute frame eligible time and discard decision.
- `Shared Buffer`: `shared_buffer_core` manages multiport shared SRAM and free queue pointers (`multi_user_fq`).
- `Queue Management`: `switch_qm` maintains per-flow/per-priority queues with `comparator`, `priority_arbiter`, and `dequeue_process` for authorized dequeue.
- `Multi-Port IO`: `master_mac_transmit_rx_tx` and `slave_mac_transmit_rx_tx` adapt 4×RGMII links.
- `Egress Shaping`: `switch_post_top`/`switch_post` provide AXI-Stream conversion and backpressure control.

Directory (Core Files)
- `top.v`: main top-level wiring 4×RGMII, clocks/resets, and switching core.
- `shared_buffer_core.v`: shared buffer core managing enqueue/dequeue and multiport datapath.
- `switch_qm.v`: queue management for strict-priority dequeue and pointer maintenance.
- `frame_eligbility_calculator.v`: ATS frame eligible time and discard.
- `token_bucket.v`: token bucket (CBS/CIR).
- `flow_entry_manager.v`: shaping parameter match/update by `flow_id`/`group_id`.
- `priority_arbiter.v`: strict priority arbitration.
- `comparator.v`: minimum eligible-time selection per priority.
- `dequeue_process.v`: authorized dequeue and new head pointer generation.
- `multi_user_fq.v`: multi-user free queue.
- `local_clock.v`: local timestamp.
- `mac_addr_lut.v`: MAC address lookup and port mapping.
- `input_arbiter.v`: 4-way input arbitration and merge.
- `switch_post_top.v` / `switch_post.v`: egress shaping and AXI-Stream aggregation.
- Likely required memory/queue components: `block_ram_w64`, `dpsram_w4_d512`, `fifo_ft_w64_d512`, `fifo_ft_w16_d64`, `fifo_output_w20`, `ptr_fifo`, `sram_w16_d512`, etc.

Top-Level and Datapath
- `top.v` (`e:\github_file\MQ_supporting_ATS\top.v:13`):
  - Instantiates `clk_wiz_0` (Vivado IP) outputting `125MHz/200MHz/15.625MHz` clocks (`e:\github_file\MQ_supporting_ATS\top.v:238`).
  - Port 1 uses `master_mac_transmit_rx_tx`; ports 2–4 use `slave_mac_transmit_rx_tx` (`e:\github_file\MQ_supporting_ATS\top.v:252`, `287`, `321`, `352`).
  - Four inputs are merged by `input_arbiter` into a single data/control channel (`e:\github_file\MQ_supporting_ATS\top.v:381`).
  - Ethernet parsing via `ethernet_parser` extracts `MAC/ethertype/vlan_id/src_port/hash` (module not included in repo, `e:\github_file\MQ_supporting_ATS\top.v:415`).
  - `mac_addr_lut` outputs lookup result and port mapping (`e:\github_file\MQ_supporting_ATS\top.v:436`).
  - Switching core `shared_buffer_switch_top` handles shared-buffer forwarding (top not included in repo, `e:\github_file\MQ_supporting_ATS\top.v:455`).
- `shared_buffer_core.v` (`e:\github_file\MQ_supporting_ATS\shared_buffer_core.v:12`):
  - Enqueue: pointer/data FIFOs write shared SRAM, generate metadata, distribute to per-port queues.
  - Dequeue: poll ready ports, read SRAM, update per-port load counters and free pointers (RR mechanism).
  - ATS: per-port `frame_eligbility_calculator` instances (`e:\github_file\MQ_supporting_ATS\shared_buffer_core.v:778`) and `local_clock` (`e:\github_file\MQ_supporting_ATS\shared_buffer_core.v:837`).
- `switch_qm.v`:
  - Maintains `NUM_FLOW_QUEUES × DEPTH_FLOW_QUEUES` eligible-time queues (`e:\github_file\MQ_supporting_ATS\switch_qm.v:79`).
  - Compares minimum eligible time and arbitrates by strict priority (`e:\github_file\MQ_supporting_ATS\switch_qm.v:154`, `233`).
  - `dequeue_process` generates new head pointer and output metadata (`e:\github_file\MQ_supporting_ATS\switch_qm.v:198`).

Environment and Dependencies
- Recommended toolchain: `Vivado 2023.2`; target platform: ZYNQ (per file headers).
- Required IP/external modules:
  - `clk_wiz_0` (Vivado Clocking Wizard IP).
  - `ethernet_parser` (Ethernet parser; provide or replace).
  - `shared_buffer_switch_top` (switching top; provide).
  - RAM/FIFO components (see “Directory” above).

Quick Start (Synthesis)
- Create a new Vivado project and add repository `.v` sources and constraints.
- Instantiate/generate `clk_wiz_0` IP and connect to `top.v`.
- Add missing modules (`ethernet_parser`, `shared_buffer_switch_top`, RAM/FIFO/IP components).
- Set `top` as the top-level, run synthesis/implementation, generate bitstream, and program the board.

Simulation and Testing
- The repository does not include testbenches/scripts.
- Recommended testbenches:
  - `top`: drive ports, stimulate Ethernet frames, verify lookup/forwarding.
  - `shared_buffer_core`: enqueue/dequeue timing and shared SRAM consistency.
  - `switch_qm`: eligible-time comparison, strict-priority arbitration, and dequeue correctness.
  - `frame_eligbility_calculator`: token bucket and discard decisions under different parameters.

License and Citation
- Choose an appropriate license (e.g., `MIT`, `Apache-2.0`, `GPL-3.0`) and add it to the repository.
- If this code accompanies a paper/article, include title, authors, and link for citation and acknowledgment.

Contributing
- Contributions via `fork` → `feature branch` → `Pull Request` are welcome:
  - Ensure simulation/synthesis pass before submission and describe impact.
  - Provide minimal reproducible tests and waveforms/resource reports when possible.

Acknowledgements
- Author: `Wenxue Wu` (per headers across files).
- Thanks to the community for advancing TSN/ATS research and engineering practice.
