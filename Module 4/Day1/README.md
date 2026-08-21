# Day 01 • Gate level Simulation(GLS)

> **“RTL describes the intention — GLS reveals how that intention behaves after synthesis.”**

Welcome to **Day 1 of Module 4**, where I started exploring **Gate-Level Synthesis (GLS)** and how an RTL design is transformed into a synthesized gate-level representation.

In this experiment, I worked with a **2:1 MUX using the Verilog ternary operator** and explored how the same design moves from **RTL to synthesized netlist and then to Gate-Level Simulation (GLS)**.

I also understood how GLS can be used to check whether the **synthesized hardware behaves consistently with the original RTL design**.

---

## Topics Covered

- [Introduction to Gate-Level Simulation](#introduction-to-gate-level-simulation)
- [GLS Using Iverilog](#gls-using-iverilog)
- [Ternary Operator Based MUX](#ternary-operator-based-mux)
- [Generating the Gate-Level Netlist](#generating-the-gate-level-netlist)
- [Key Takeaways](#key-takeaways)
---
## Introduction to GLS

**Gate-Level Simulation (GLS)** is the process of simulating the **synthesized gate-level netlist** instead of the original RTL code.

In this module, I explore how **Verilog coding styles, synthesis, and simulation** are connected. The focus is on understanding how RTL is transformed into hardware and how coding styles can sometimes cause differences between **RTL simulation and synthesized hardware behavior**.

GLS helps verify that the synthesized design behaves as expected and helps identify issues such as **synthesis-simulation mismatches** and problems caused by incorrect use of Verilog statements.

## GLS Using Iverilog
---
<img width="900" height="582" alt="Screenshot 2026-08-20 214525" src="https://github.com/user-attachments/assets/b934a5e2-b7b1-411f-8e44-8d62ff16b349" />
---

### Components of the Flow

- **Design**  
  The original RTL design that represents the intended hardware functionality.

- **Gate-Level Verilog Model**  
  The synthesized gate-level netlist generated from the RTL. It represents the hardware after synthesis.

- **Testbench**  
  Provides input stimulus to the gate-level design and checks its behavior.

- **IVERILOG**  
  Compiles and simulates the **gate-level Verilog model along with the testbench**.

- **VCD File**  
  IVERILOG generates a **Value Change Dump (VCD)** file containing signal transitions during simulation.

- **GTKWave**  
  The VCD file is opened in GTKWave to visualize and analyze the **gate-level waveforms**.

### Timing Validation

If the **gate-level models are delay annotated**, GLS can also be used for **timing validation**.

This helps verify not only the functionality of the synthesized design but also its behavior with respect to **propagation delays and timing**.

## Ternary Operator MUX & Gate-Level Simulation

<img width="900" height="582" alt="gvim_ternary_operator_bg" src="https://github.com/user-attachments/assets/0571d7a8-81c7-4354-b238-8a68dc7874ac" />
Here, the ternary operator:

sel ? i1 : i0

means:
```text
When sel = 1, i1 is selected.
When sel = 0, i0 is selected.
```
This provides a compact way of describing the functionality of a 2:1 multiplexer.

### Comparison with the Good MUX

I also compared the ternary-operator MUX with the earlier good_mux implementation.

Ternary Operator MUX
```text
assign y = sel ? i1 : i0;
Good MUX
always @(*)
begin
    if(sel)
        y <= i1;
    else
        y <= i0;
end
```
Both describe the same 2:1 MUX functionality, but the ternary operator gives a more compact combinational RTL description.

### Synthesized Design — show

The Yosys show command was used to visualize the synthesized design.

The generated schematic shows:

<img width="900" height="582" alt="ternary_operator_show" src="https://github.com/user-attachments/assets/4268b789-9749-4026-982b-509ea98c9716" />

So, instead of manually describing gates, the synthesis tool recognized the MUX functionality and mapped it to an appropriate standard cell.

## RTL Waveform — GTKWave

The RTL simulation waveform was viewed using GTKWave.

<img width="900" height="582" alt="tb_ternary_operator" src="https://github.com/user-attachments/assets/25f6c186-f632-4c7d-94a5-8828338ab944" />

The waveform contains:

i0 — first MUX input
i1 — second MUX input
sel — select signal
y — MUX output

The output follows the MUX rule:
```text
sel = 0  →  y = i0
sel = 1  →  y = i1
```
By observing the waveform, I verified that the RTL implementation produces the expected MUX output for changing input and select signals.

## Generating the Gate-Level Netlist

After synthesis, the gate-level netlist was generated using Yosys.

The synthesized netlist contains the technology-mapped SKY130 cell instead of the original high-level ternary operator.

The terminal output also shows the synthesis result:
```text
sky130_fd_sc_hd__mux2_1 cells: 1
input signals: 3
output signals: 1
```
This confirms that the design was mapped to one SKY130 2:1 MUX cell with three inputs and one output.

## GLS Waveform — GTKWave

Finally, I opened the generated VCD file in GTKWave and observed the gate-level simulation waveform.

<img width="900" height="582" alt="tb_ternary_op_GLS" src="https://github.com/user-attachments/assets/4e013133-aa4c-429b-9f5d-73bf0dc49a92" />

Unlike the RTL waveform, the GLS waveform also shows internal synthesized signals such as:
```text
_0_
_1_
_2_
_3_
```
These are internal nets created during synthesis.

The important signals are still:
```text
i0
i1
sel
y
```
The output y follows the expected MUX behavior.
```text
sel = 0  →  y = i0
sel = 1  →  y = i1
```
Therefore, the synthesized gate-level implementation produces the expected functional behavior.

## Key Takeaways

- Explored **Gate-Level Simulation (GLS)** and its importance in verifying synthesized hardware.
- Implemented a **2:1 MUX using the Verilog ternary operator**.
- Understood how a simple RTL description is transformed into a **gate-level netlist**.
- Used **Yosys** for synthesis and observed the mapping to the **SKY130 `mux2_1` standard cell**.
- Used **IVERILOG** to simulate both the RTL design and the synthesized gate-level design.
- Generated and analyzed **VCD files** using **GTKWave**.
- Compared **RTL simulation and GLS waveforms** to verify functional consistency.
- Understood that GLS can provide additional confidence that **synthesis has preserved the intended RTL functionality**.
- Learned that **delay-annotated gate-level models** can also be used for timing validation.

---

<div align="center">

### ⭐ Day 03 Complete 

> **From RTL Intent to Gate-Level Reality — GLS closes the gap between design and hardware.**

</div>

