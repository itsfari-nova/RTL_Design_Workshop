# Day 02 • RTL to Netlist — Synthesis with Yosys

> **"RTL describes the behavior. Synthesis transforms that behavior into hardware."**

Welcome to **Day 02** of my RTL Design journey! 👋

In this session, I explored what happens after writing and simulating RTL. I learned how **Verilog RTL is converted into a gate-level netlist using synthesis**, how **Yosys** performs synthesis, and how a **`.lib` standard-cell library** influences the implementation.

As a hands-on experiment, I synthesized the **`good_mux` 2:1 multiplexer** from Day 01 and generated its synthesized **netlist**.

---

## Explore This Repository

- [Concepts Learned](#-concepts-learned)
- [RTL Design](#-rtl-design)
- [What is Synthesis](#️-what-is-synthesis)
- [Yosys](#️-yosys)
- [.lib — Standard Cell Library](#-lib--standard-cell-library)
- [Cell Flavors](#-cell-flavors)
- [Good MUX Synthesis](#-good-mux-synthesis)
- [Generated Netlist](#-generated-netlist)
- [Results](#-results)
- [Key Takeaways](#-key-takeaways)

---

# Concepts Learned

Day 02 focused on understanding the transition from **RTL description to hardware implementation**.

## RTL Design

**RTL (Register Transfer Level)** is a behavioral representation of the required digital hardware.

At RTL, the designer describes the functionality of the circuit rather than manually specifying every individual logic gate.

RTL can describe:

- Inputs and outputs
- Combinational logic
- Sequential logic
- Registers
- Clock signals
- Reset signals
- Data transfer

## Yosys — The Synthesis Tool

**Yosys** is an open-source synthesis framework used to process Verilog RTL and convert it into a synthesized hardware representation or netlist.

Instead of manually converting RTL into individual logic gates, Yosys analyzes the design and performs synthesis automatically.

During synthesis, Yosys can:

- Read Verilog RTL
- Analyze the design hierarchy
- Elaborate RTL constructs
- Perform logic optimization
- Simplify Boolean logic
- Convert RTL structures into logic
- Perform technology mapping
- Generate a synthesized netlist

For my experiment, I used Yosys to synthesize the `good_mux` design that I created and simulated earlier.

The important concept I learned is:

> **RTL describes the intended behavior, while synthesis determines a hardware implementation of that behavior.**

---

##  .lib — Standard Cell Library

A **`.lib` file**, commonly called a Liberty file, contains information about standard cells available for synthesis and implementation.

A standard-cell library can contain cells such as:

- AND gates
- OR gates
- NOT gates
- NAND gates
- NOR gates
- Buffers
- Multiplexers
- Flip-flops

However, a library does not necessarily contain only one implementation of each logic function.

---

## Different Cell Flavors?

One important concept I learned was that **the same logical function can have multiple cell flavors**.

For example, a 2-input AND gate may have different implementations:

```text
2-Input AND

├── Slow
├── Medium
└── Fast
```
---

# Synthesis

I opened **Yosys** in the terminal and loaded my Verilog RTL design. Then I ran the synthesis commands to convert the RTL code into a **gate-level netlist**.

```bash
yosys
read_verilog good_mux.v
synth -top good_mux
write_verilog good_mux_netlist.v
```
<img width="920" height="582" alt="terminal_yosys_commands" src="https://github.com/user-attachments/assets/d91a92db-6b00-4298-b6cc-abc87e9268d7" />

---
### Graphical View of Synthesis

After synthesis, I used Yosys to generate a **graphical representation of the synthesized circuit**. This view helped me visually understand how my RTL code was converted into logic gates and connected together.

```yosys
show
```
<img width="920" height="582" alt="virtual_4" src="https://github.com/user-attachments/assets/cc360628-3355-424e-b9d8-a5de08530928" />

The graphical output gives a clear view of the internal logic structure of the **2:1 multiplexer**, making it easier to see how the input, select signal, and output are connected after synthesis.

### Exploring the Netlist

I inspected the generated file to understand what Yosys actually produced:

<img width="920" height="582" alt="Virtual5" src="https://github.com/user-attachments/assets/259829a3-1dfd-4728-bc9f-c9ebc1ee0880" />

### What Makes the Netlist Interesting?

The netlist gives me a different perspective of the same design:


| 📝 RTL View | 🔧 Netlist View |
|---|---|
| Describes behavior | Describes structure |
| Easy for humans to write | Closer to hardware implementation |
| Uses `if`, `always`, and signals | Uses logic cells and connections |
| Focuses on functionality | Focuses on hardware realization |

The synthesized netlist was generated as `good_mux_netlist.v`. I then checked the generated netlist to make sure the RTL design was correctly converted into hardware-level logic.

# Key Takeaways

- **RTL to Hardware:** I learned that RTL is a behavioral description of the circuit, while synthesis converts this description into a structure that can be implemented as hardware.
- **Understanding Yosys:** I explored **Yosys** as a synthesis tool and learned how it reads Verilog RTL, analyzes the design, optimizes the logic, and generates a synthesized netlist.
- **Role of `.lib`:** I learned that a **`.lib` standard-cell library** contains information about available standard cells and their characteristics, which is important during technology mapping.
- **Cell Flavors:** I understood that the same logic function can have different cell implementations, providing different trade-offs between **Power, Performance, and Area (PPA)**.
- **Netlist Exploration:** I generated and inspected `good_mux_netlist.v` to understand how my original 2:1 MUX RTL was represented after synthesis.
- **Graphical Representation:** The Yosys graphical view helped me visualize the internal logic structure and understand how signals and logic elements are connected.

