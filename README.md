# DESIGN-AND-IMPLEMENTATION-OF-HIGH-PERFORMANCE-AND-LOW-POWER-RISC-V-PROCESSOR
Modern embedded systems and IoT platforms need
processors that give high performance while maintaining low
power consumption and high resource efficiency. This work
presents the design and implementation of high performance
and low power 32-bit RISC-V processor 4-stage pipelined for
non-load and 5-stage for load operations, also extending its
capabilities as system on chip design. The micro-architecture
has optimized functional units including Baugh Wooley signed
multiplier and 32-bit radix 16 divider. Functional verification of
all ISA components, hazard detection, forwarding mechanisms,
and branch prediction ensures architectural correctness. The
processor is implemented on a Nexys 4 DDR Artix-7 FPGA with
a resource requirement of 7415 lookup tables (LUTs) and 3401
flip flops (FFs) and performance is evaluated using Dhrystone 2.1
and CoreMark benchmarks. The design achieves a performance
of 1.77 DMIPS/MHz and 3.07 CoreMark/MHz. Different power
optimizations are added to the design to reduce dynamic power.
The physical design of the core using commercial tools shows
that the design can go a maximum of 173.8 MHz in UMC
55nm technology with 0.095179 mm^2 area. Under different PVT
conditions the measured dynamic power is 20.86 µW/MHz in
worst case corner and 24.59 µW/MHz in typical corner and 32.85
µW/MHz in best case corner. The core is extended as an SoC
with an MMIOperipheral bridge that integrates IR sensor, PWM
motor control, GPIO, UART, seven-segment display, timers, and
support for interrupt controller, enabling a complete embedded
platform implemented in FPGA.
