readme_content = """# High-Performance and Low-Power RV32IM RISC-V Processor & SoC

![RISC-V](https://img.shields.io/badge/RISC--V-RV32IM-blue.svg)
![FPGA](https://img.shields.io/badge/FPGA-Xilinx%20Artix--7-orange.svg)
![ASIC](https://img.shields.io/badge/ASIC-UMC%2055nm-green.svg)
![Status](https://img.shields.io/badge/Status-Silicon%20Proven%2FVerified-success.svg)

## Overview

This repository contains the RTL design, synthesis scripts, and documentation for a custom **32-bit RISC-V Processor** implementing the base integer instruction set with multiplication and division extensions (**RV32IM**). Designed to strike a rigorous balance between computational throughput and energy efficiency, the core is optimized for modern embedded systems and IoT platforms.

The processor features an adaptive pipeline architecture (4-stage for non-load, 5-stage for load operations) and integrates aggressive dynamic power optimization strategies, including RTL clock gating and operand isolation. It has been thoroughly verified and synthesized on both **Nexys 4 DDR Artix-7 FPGA** and as an **ASIC targeting UMC 55nm standard cells**.

---

## 🔬 Core Micro-architecture

The micro-architecture is engineered for enhanced instructions-per-cycle (IPC) and strict power economy.

### Pipeline Design
- **Adaptive Staging**: Uses a 4-stage pipeline (Fetch, Decode, Execute, Writeback) for non-load operations to eliminate memory-access delays, and dynamically extends to a 5-stage pipeline (adding a Memory stage) for load operations.
- **Branch Prediction**: Features a 2-bit dynamic branch predictor with a branch history table (States: Strongly Not Taken, Weakly Not Taken, Weakly Taken, Strongly Taken) to drastically minimize pipeline stalls. Unconditional jumps (JAL) execute in a single cycle.

### Execution Unit (IE Stage)
- **ALU, Shifter, Comparator**: Combinational logic units executing in a single cycle.
- **Hardware Multiplier**: Incorporates a **33x33 Baugh-Wooley Signed Multiplier** providing optimal area-power-timing trade-offs. The computation completes in 3 cycles using partial product alignment and optimized parallel adders (levels 1-3).
- **Hardware Divider**: Features a 32-bit **Radix-16 Divider** driven by a 4-stage FSM (IDLE, PRECOMP, COMPUTE, DONE). Completes division in 9 cycles utilizing a compare-subtractor array and one-hot encoding for maximal quotient digit selection.

---

## 🔋 Power Optimization Techniques

Dynamic power consumption ($P_{dynamic} = \\alpha CV^2f$) is minimized fundamentally by reducing switching activity ($\\alpha$) through targeted RTL techniques.

1. **Intelligent Clock Gating**: Integrated Clock Gating (ICG) cells are utilized extensively. Modules like the Control and Status Register (CSR) block and the Divider FSM are clock-gated when inactive, preventing parasitic power drain.
2. **Operand Isolation (OI)**: AND-based clamping-to-zero technique is deployed at the inputs of major combinational blocks (ALU, Multiplier, Divider). When pipeline stalls occur or functional units are unselected by the decoder, input transit propagation is structurally halted, saving substantial switching power.

---

## 📊 Implementation Results & Benchmarks

### 1. ASIC Physical Design (UMC 55nm)
The core underwent complete RTL-to-GDSII flow using Cadence Genus (Synthesis) and Cadence Innovus (Physical Design).

| Target Node | VDD | Temp | Fmax | Area | Dynamic Power | Leakage Power |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **UMC 55nm (TC)** | 1.20 V | 25°C | 100 MHz* | 0.095 mm² | **24.59 µW/MHz** | 0.0401 mW |
| **UMC 55nm (WC)** | 1.08 V | 125°C | 100 MHz* | 0.095 mm² | 20.86 µW/MHz | 0.1405 mW |
| **UMC 55nm (BC)** | 1.32 V | -40°C | 100 MHz* | 0.095 mm² | 32.17 µW/MHz | 0.0673 mW |

*\*Note: Timing analysis indicates the core can operate up to a maximum frequency of **173.8 MHz** under typical conditions.*

### 2. FPGA Prototyping (Nexys 4 DDR Artix-7)
- **Resource Utilization**: 7415 LUTs | 3401 Flip-Flops (FFs)
- **Cache**: 128 KB Instruction Cache | 64 KB Data Cache (Synthesized in Block RAM)
- **Operating Frequency**: 100 MHz (Timing Met)

### 3. Benchmark Performance
Performance evaluated using standard embedded test suites compiled via RISC-V GNU Toolchain:
* **Dhrystone 2.1**: `1.77 DMIPS/MHz`
* **CoreMark**: `3.07 CoreMark/MHz`

---

## 💻 System-on-Chip (SoC) Ecosystem

The processor core is wrapped into a fully functional SoC, demonstrating its capacity to handle real-time embedded workloads. The memory-mapped I/O (MMIO) subsystem completely eliminates the need for special I/O opcodes.

**SoC Peripherals:**
- **MMIO Bridge**: Low-latency arbiter for dataflow between core and peripherals.
- **Timers**: 32-bit hardware countdown timer with programmable prescaler.
- **UART TX**: Serial console link for benchmark outputs and host communication.
- **GPIO Bank**: Mapped to Artix-7 pins; controls a 7-segment display and external modules.
- **Real-Time Integration**: Successfully verified using an external IR sensor and L298N Motor Driver. The SoC reacts to sensor interrupts in real-time by manipulating PWM signals to control DC motor states.

---

## 📁 Repository Structure
*(Assumed/Template structure based on standard project conventions)*
