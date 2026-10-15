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
SE203511_NguyenPhucBaoKhang_3StagePipelineCPU/
│
├── 1_SourceCode/
│   ├── 1_RTL/                  # Verilog source files
│   │   ├── alu.v               # 4-bit Arithmetic Logic Unit
│   │   ├── control_unit.v      # Instruction decoder & control signal generator
│   │   ├── cpu_top.v           # Top-level module integrating datapath & control
│   │   ├── data_memory.v       
│   │   ├── flag_register.v     
│   │   ├── instruction_memory.v
│   │   ├── instruction_register.v
│   │   ├── mux.v               
│   │   ├── pipeline_register.v 
│   │   ├── program_counter.v   
│   │   └── register_file.v     # Register File
│   │
│   ├── 2_Testbench/            # Simulation testbenches
│   │   ├── tb_alu.v            
│   │   ├── tb_control_unit.v   
│   │   ├── tb_cpu.v            # Comprehensive testbench
│   │   └── tb_register_file.v  
│   │
│   └── 3_Mem/                  # Memory initialization files
│       ├── prog_arith.mem      
│       ├── prog_jump.mem       
│       ├── prog_logic.mem      
│       └── program.mem         
│
├── 2_Documents/                # Documentation and schematics
│   ├── 4Bit CPU datapath.pptx  
│   └── Screenshots.docx        
│
└── 3_VivadoProject/            # Vivado project files
    ├── 4Bit_3Stage_Pipeline.srcs/
    └── 4Bit_3Stage_Pipeline.xpr
