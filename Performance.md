# Fork Performance Documentation
## MRAM AXI Slave Controller

This document is provided as **fork-specific performance data** for the public repository.
It explains **what was implemented**, **how performance is derived**, and provides **example numerical results**
that can be independently recomputed from RTL parameters.

---

## What This Fork Demonstrates

This fork contains a **reference AXI-based SoC subsystem** composed of:

- 2× AXI Masters (MCU-style + DMA-style behavioral models)
- 2× AXI Slaves (MRAM AXI slave + SRAM model)
- A lightweight 2M2S AXI NoC
- An AXI-to-MRAM slave controller with:
  - Programmable write pacing (`write_delay_config`)
  - Deterministic MRAM read latency modeling
- A synthesizable MRAM macro behavioral model

The goal of this fork is to provide:
- A **verifiable RTL reference** for MRAM-backed AXI slaves
- **Cycle-level performance transparency** without requiring proprietary simulators
- Public, timestamped design evidence suitable for technical reference

---

## Performance Assumptions (Default Testbench)

| Item | Value |
|-----|------|
| Clock frequency | 100 MHz |
| Clock period | 10 ns |
| AXI data width | 64-bit (8 bytes/beat) |
| Write burst length | 4 beats |
| Read burst length | 4 beats |
| MRAM write delay | `write_delay_config = 8` cycles |
| MRAM read latency | `READ_LAT = 2` cycles |

---

## Write Path Performance (AXI → MRAM)

The MRAM AXI slave controller inserts a programmable delay between write beats.

### Formula
- Cycles per write beat ≈ `D + 2`
- Write bandwidth:
  - `BW_write = W / (D + 2) * fclk`

Where:
- `W` = bytes per beat
- `D` = write_delay_config
- `fclk` = clock frequency

### Example Calculation
- `D = 8`, `W = 8 bytes`
- Cycles per beat ≈ `10 cycles`
- Bandwidth ≈ `8 / 10 * 100 MHz = 80 MB/s`

4-beat write burst:
- Cycles ≈ `4 * 10 = 40 cycles`
- Time ≈ `400 ns`
- Payload = `32 bytes`

---

## Read Path Performance (MRAM → AXI)

The MRAM macro model returns read data after a fixed latency.

### Formula (Non-pipelined read issue)
- Cycles per read beat ≈ `L + 1`
- Read bandwidth:
  - `BW_read = W / (L + 1) * fclk`

### Example Calculation
- `L = 2`, `W = 8 bytes`
- Cycles per beat ≈ `3 cycles`
- Bandwidth ≈ `8 / 3 * 100 MHz ≈ 267 MB/s`

4-beat read burst:
- Cycles ≈ `12 cycles`
- Time ≈ `120 ns`
- Payload = `32 bytes`

---

## End-to-End MCU Transaction Example

MCU performs:
- One 4-beat write (32 B)
- One 4-beat read (32 B)

Total payload = `64 bytes`

| Component | Cycles |
|---------|-------|
| Write data phase | ~40 |
| Read data phase | ~12 |
| **Total** | **~52 cycles** |

- Total time ≈ `520 ns`
- Effective payload bandwidth ≈ `64 B / 520 ns ≈ 123 MB/s`

---

## Notes

- The AXI NoC is primarily combinational and does not add pipeline latency
- DMA traffic targets a different slave and does not contend with MRAM
- Performance is dominated by:
  - MRAM write pacing (`write_delay_config`)
  - MRAM read latency (`READ_LAT`)

All numbers above can be trivially recomputed by modifying `D`, `L`, or `fclk`.

---

## README Snippet (Copy & Paste)

```md
### Performance Summary

This fork includes cycle-level performance estimates derived directly from RTL parameters.

- Clock: 100 MHz, AXI 64-bit
- MRAM write delay: write_delay_config = 8
- MRAM read latency: READ_LAT = 2

Write bandwidth ≈ 80 MB/s  
Read bandwidth ≈ 267 MB/s  

A 4-beat write + 4-beat read MCU transaction transfers 64 bytes in ~52 cycles
(~520 ns), resulting in an effective payload bandwidth of ~123 MB/s.

Detailed derivations and example calculations are provided in
`Fork_Performance.md`.
```

---

End of document.
