# Design and Implementation of High Performance and Low Power RISC-V Processor

![RISC-V](https://img.shields.io/badge/RISC--V-RV32IM-blue.svg) ![FPGA](https://img.shields.io/badge/FPGA-Artix--7-orange.svg) ![ASIC](https://img.shields.io/badge/ASIC-UMC%2055nm-green.svg)

##  Abstract
* Modern embedded systems and IoT platforms need processors that give high performance while maintaining low power consumption and high resource efficiency. This work presents the design and implementation of a high performance and low power 32-bit RISC-V processor with 4-stage pipeline for non-load operations and 5-stage pipeline for load operations, also extending the processor as system on chip design to ensure that it can handle real-time data efficiently. The micro-architecture has optimized functional units including Baugh Wooley signed multiplier and 32-bit radix 16 divider. Functional verification of all ISA components, hazard detection, forwarding mechanisms, and branch prediction ensures architectural correctness. The processor is implemented on a Nexys 4 DDR Artix-7 FPGA with a resource requirement of 7415 lookup tables (LUTs) and 3401 flip flops (FFs) and performance is evaluated using Dhrystone 2.1 and CoreMark benchmarks. The design achieves a performance of 1.77 DMIPS/MHz and 3.07 CoreMark/MHz. Different power optimizations are added to the design to reduce dynamic power. The ASIC physical design of the core using Cadence EDA tools shows that the design can reach a maximum of 173.8 MHz in UMC 55nm technology with 0.095179 mm² area. Under different PVT conditions the measured dynamic power is 20.86 μW/MHz in worst case corner and 24.59 µW/MHz in typical corner and 32.17 µW/MHz in best case corner. Also, timing analysis is performed to ensure that the design has no timing violations under these cases. The core is extended as an SoC with an MMIO peripheral bridge that integrates IR sensor, PWM motor control, GPIO, UART, seven-segment display, timers, and support for interrupt controller, enabling a complete embedded platform implemented in FPGA.

---

##  Introduction
* This work consists of three main sections FPGA implementation, ASIC physical design, and system on chip (SoC) design around the processor core.
* Open source instruction set architecture (ISA) like RISC-V provides a free and flexible platform for implementing custom processor designs made for specific applications while maintaining required design parameters.

---

##  Micro-Architecture
* The core supports RISC-V ISA base integer instruction set (RV321) with multiplication and division extension.
* For non-load instructions the 4-stage pipeline is used since there is no need for separate memory access.
* For load instructions, 5-stage pipeline is used since the data memory access and return path take a full cycle.
* The IF stage starts in the branch predictor block, it predicts the next program counter (PC) using 2-bit branch history table.
* We have used the 33x33 Baugh Wooley signed multiplier which gives the best trade-off between area, power and timing better than other multipliers.
* The divider algorithm used is a 32-bit radix-16 divider which is a four stage FSM.

---

##  Power Optimization Methods
* RTL clock gating is a commonly used power optimization technique.
* The power saving is mainly due to the considerable reduction of switching activity in the logic since when the clock is not active, the unnecessary transitions do not occur.
* Operand isolation (OI) is another power reduction method in which the inputs of combinational block are gated when output is not used.
* The AND based (clamping to 0) operand isolation is used as it provides the best trade-off between power saving and area overhead.

---

##  System on Chip Design
* The core is extended as an SoC design to do real-time operations in FPGA, that contains CPU core, memory hierarchy, MMIO peripherals, timer, UART and support for interrupt controller.
* In memory mapped I/O method the input and output of the peripherals are directly mapped to the memory space of the processor.
* The Nexys 4 DDR Artix-7 FPGA's external pins are designed as GPIO pins for the SoC, making it possible to connect any peripheral devices.

---

##  Results

### A. FPGA Design of Processor Core
* The synthesis and implementation of the design are done using Xilinx Vivado on NEXYS 4 DDR Artix-7 FPGA.
* Instruction cache of size 128 KB and 64 KB data cache are realised in the block RAM of FPGA.
* The FPGA implementation results show that the core uses 7415 LUTs and 3401 FFs and the timing is met for the core to work properly in 100MHz.
* The core achieves a performance of 1.77 DMIPS/MHz and 3.07 CoreMark/MHz.

### B. ASIC Design of Processor Core
* The ASIC design of the core is implemented using Cadence EDA tools
* Cadence Genus is used for the RTL synthesis of the core and the physical design (floorplanning, placement, clock-tree synthesis, routing, and post-route optimization) is done using the Cadence Innovus tool.
* The physical design targets the UMC 55nm standard cell library with different PVT conditions; worst case (WC: 1.08V and 125°C), typical case (TC: 1.20V, 25°C) and best case (BC: 1.32V, -40°C).
* The core occupies an area of 0.095179 mm².
* The design operates at 100 MHz clock frequency with a positive slack of 4.250 ns, so the clock frequency can go up to a maximum of 173.8 MHz.

#### Table I: Power Report of Different PVT Corners

| Technology Node | Voltage (V) | Temperature (°C) | Power (mW) | Power (W/MHz) |
| :--- | :--- | :--- | :--- | :--- |
| UMC 55nm (WC) | 1.08 | 125 | 2.226 | 22.26 |
| UMC 55nm (TC) | 1.20 | 25 | 2.499 | 24.99 |
| UMC 55nm (BC) | 1.32 | -40 | 3.285 | 32.85 |

#### Table II: Dynamic Power of Different PVT Corners

| Corner | Internal (mW) | Switching (mW) | Leakage (mW) | Dynamic (mW) | Dynamic (W) | Dynamic (W/MHz) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| WC | 1.498 | 0.5878 | 0.1405 | 2.0858 | 2085.8 | 20.86 |
| TC | 1.899 | 0.5602 | 0.0401 | 2.4592 | 2459.2 | 24.59 |
| BC | 2.588 | 0.6293 | 0.06732 | 3.2173 | 3217.3 | 32.17 |

* In the typical case, the typical case (TC) library represents the nominal PVT, VDD is 1.20V, and the temperature is 25°C.
* The dynamic power obtained from the typical case is 24.59 W/MHz.
* In the worst case condition, the worst case (WC) library has lower voltage and higher temperature, VDD is 1.08V and temperature is 125°C so the logic is slower and often increases the leakage power.
* The dynamic power obtained from the worst case is 20.86 µW/MHz with a leakage of 0.1405 mW and the timing is met with positive setup slack of 0.642 ns and no max-delay (setup) violations.
* The best case (BC) uses higher voltage and colder temperature where VDD is 1.32V and temperature is -40°C so cells switch faster and dynamic power rises proportionally with VDD.
* The dynamic power obtained from the best case is 32.17 W/MHz with a leakage of 0.06732 mW and the hold timing is met for the logic with a positive hold slack of 0.045 ns and no hold violations.

### C. SoC Design around Processor Core
* RV32IM SoC was implemented on FPGA with hardware timer and support for interrupt controller.
* To show the real-time operation of the processor, IR sensor motor control experiment is designed.
* So, when an object is within the IR sensor's detectable range, the sensor detects the obstacle and the seven segment display turns from 0 to 1, and at the same time motor stops rotating.
* This quick response demonstrates the capability of the processor core to handle real-time data.

##  Authors

| Author | Department | Institution | Location | Email |
| :--- | :--- | :--- | :--- | :--- |
| **Abhinav K R** | Dept. ECE | Government Engineering College Thrissur | Kerala, India | abhinav.24m142@gectcr.ac.in |
| **Dr. A R Jayan** | Dept. ECE | Government Engineering College Thrissur | Kerala, India | arjayan@gectcr.ac.in |
