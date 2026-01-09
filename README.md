# MRAM AXI Slave Controller – Architectural Evaluation Fork

## Overview

This repository is an independent fork of the original **mram-axi-slave-controller** project.  
It is used for **early-stage architectural evaluation of MRAM-based AXI slave designs** in modern SoCs, with a focus on latency, bandwidth, and transaction-level trade-offs derived directly from RTL parameters.

Rather than re-implementing functionality, this fork extends the original work with **cycle-level performance analysis, architectural documentation, and reproducible evaluation examples** that support engineering decision-making prior to full-system simulation or silicon implementation.

---

## Why this fork exists

During SoC architecture definition, engineers frequently need to compare memory options and configuration points under realistic assumptions, before verification environments or physical measurements are available.

This fork exists to support that process by:
- Translating RTL parameters into cycle-level latency and bandwidth estimates
- Making AXI transaction behaviour and MRAM timing effects explicit
- Providing a lightweight reference for evaluating MRAM suitability in AI, control, or embedded workloads

The emphasis is on **architectural reasoning**, not educational walkthroughs or end-user tutorials.

---

## What is evaluated

This fork focuses on evaluating the following architectural trade-offs:

- **MRAM read latency vs. effective throughput**  
  Impact of MRAM read latency (`READ_LAT`) on sustained AXI burst bandwidth.

- **Write delay vs. bus utilisation**  
  Effect of write delay configuration on write bandwidth and transaction overlap.

- **AXI bus width and burst structure**  
  How data width and burst composition influence payload efficiency.

- **Protocol overhead vs. usable bandwidth**  
  Difference between raw AXI bandwidth and effective payload bandwidth once timing and protocol costs are accounted for.

All calculations are derived from RTL-level assumptions rather than abstract models.

---

## Intended users

This repository is intended for engineering audiences involved in SoC development, including:

- **SoC architects**  
  Evaluating MRAM-based subsystems against alternative memory architectures during early design exploration.

- **Design verification (DV) engineers**  
  Understanding expected performance envelopes and corner cases prior to full testbench development.

- **System and firmware engineers**  
  Reasoning about realistic memory access characteristics when planning boot flows, DMA usage, or accelerator workloads.

It is not intended as a beginner tutorial or a complete verification environment.

---

## Performance summary (example)

Example configuration:
- Clock: 100 MHz
- AXI data width: 64-bit
- MRAM read latency: `READ_LAT = 2`
- MRAM write delay: `write_delay_config = 8`

Estimated results:
- Write bandwidth ≈ 80 MB/s  
- Read bandwidth ≈ 267 MB/s  

A 4-beat write + 4-beat read transaction transfers 64 bytes in approximately 52 cycles (~520 ns), resulting in an effective payload bandwidth of ~123 MB/s.

Detailed derivations and example calculations are provided in `Performance.md`.

---

## Repository contents

- `axi_mram_slave.sv`  
  Reference RTL for an AMBA-compatible MRAM AXI slave controller.

- `Performance.md`  
  Cycle-level performance estimation and example calculations.

- `soc_rtl/`  
  SoC-level architectural block diagrams.

- `MRAM based AI SOC architecture.docx`  
  Architectural context and system-level integration considerations.

- `What is MRAM and Why It Matters in SoC Design.docx`  
  Background material supporting architectural decision-making.

---

## Scope and limitations

This repository is intended for **early-stage architectural evaluation**.  
It does not replace:
- Full RTL simulation
- Power sign-off or silicon measurement
- Formal performance validation

Instead, it provides a transparent and reproducible way to narrow design choices and identify potential risks early in the SoC design cycle.

---

## Related technical article

Designing an AMBA-Compatible MRAM AXI Slave Controller for Modern SoCs  
https://medium.com/@ace.lin0121/designing-an-amba-compatible-mram-axi-slave-controller-for-modern-socs-3cade20bce41
