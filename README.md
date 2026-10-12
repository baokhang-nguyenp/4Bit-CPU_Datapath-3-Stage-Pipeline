# 4-Bit 3-Stage Pipelined CPU in Verilog HDL

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Language: Verilog HDL](https://img.shields.io/badge/Language-Verilog-blue.svg)]()
[![Toolchain: Xilinx Vivado](https://img.shields.io/badge/Toolchain-Vivado%202026.1-orange.svg)]()
[![Target FPGA: Xilinx Virtex-7](https://img.shields.io/badge/Target-Virtex--7%20(xc7vx485tffg1157--1)-success.svg)]()

A complete hardware implementation of a **Custom 4-Bit Pipelined RISC CPU** featuring Harvard architecture, a 3-stage execution pipeline, dynamic control hazard resolution, and hardware FPGA synthesis validation.

---

##  Core Architectural Features

1. **Strict Harvard Architecture:**
   - Independent Instruction Memory (16x12 ROM) and Data Memory (16x4 RAM).
   - Enables simultaneous instruction fetch and data read/write transactions within the same clock cycle.
2. **3-Stage Pipeline Hierarchy:**
   - **Stage 1 (IF):** Instruction Fetch via 4-bit Program Counter (PC) and PC Multiplexer, buffered by the `IF/ID (IR)` register.
   - **Stage 2 (ID):** Instruction Decode, bit-field extraction (Opcode, Rd, Rs, Imm), and combinational control generation.
   - **Stage 3 (EX):** Execute, Memory Access, and Write-Back arbitration.
3. **Advanced Datapath Refinements:**
   - **Dual-Stage Synchronous Pipeline Flush:** Instantly resolves control hazards by flushing IF/ID to NOP (`12'h000`) and clearing ID/EX control strobes with an optimal 1-cycle penalty.
   - **Flexible OP MUX:** Dynamically selects between register operand `qb` and immediate constant `imm` for ALU input B.
   - **Dedicated Write-Back MUX:** Cleanly arbitrates between `alu_out` and memory `read_data` to drive the unified register write bus (`wd`).
   - **Synchronous FLAGS Register:** Latches Zero (`alu_z`) and Carry (`alu_c`) flags at `posedge clk` to eliminate combinational glitches during conditional branching (`JZ`).

---

##  FPGA Synthesis & Hardware Utilization

Synthesized successfully using **Xilinx Vivado** targeting the **Virtex-7 FPGA (`xc7vx485tffg1157-1`)**:

| Resource Category | Used | Available | Utilization (%) |
| :--- | :---: | :---: | :---: |
| **Slice LUTs** | 53 | 303,600 | < 1% |
| **Slice Registers (FFs)** | 50 | 607,200 | < 1% |
| **Bonded IOBs** | 28 | 600 | 4.67% |
| **BUFGCTRL** | 1 | 32 | 3.13% |

---

##  Repository Structure

```text
4Bit-Pipelined-CPU/
│
├── rtl/                  # Verilog source files
│   ├── cpu_top.v         # Top-level module integrating datapath & control
│   ├── alu.v             # 4-bit Arithmetic Logic Unit
│   ├── control_unit.v    # Instruction decoder & control signal generator
│   └── register_file.v   # 4x4-bit Register File (R0-R3)
│
├── sim/                  # Simulation testbenches & waveforms
│   └── tb_cpu.v          # Comprehensive testbench verifying sequential & branch execution
│
├── docs/                 # Documentation, schematics, and presentation assets
│   └── vivado_utilization.png
│
├── .gitignore            # Excludes Vivado temporary logs and cache (.Xil/, *.jou)
└── README.md             # Project documentation
