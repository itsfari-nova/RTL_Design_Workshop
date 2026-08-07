# Day 01 • My First Step into RTL Design Workshop

> "Every complex processor begins with a simple logic gate."

Welcome to **Day 01** of my RTL Design journey! 👋  
In this session, I explored how a digital circuit is described using **Verilog**, verified through **simulation**, and transformed into hardware using **synthesis**.

---

## Explore This Repository

- [ Concepts learned](#-concepts-learned)
- [ Toolchain Setup](#️-toolchain-setup)
- [ Experiment](#-experiment)
- [ Simulation Flow](#-simulation-flow)
- [ Results](#-results)
- [ Understanding the Verilog Design](#-Understanding-the-Verilog-Design)
- [ Summary](#-Summary)

---
# Concepts Learned

On the first day of my RTL Design journey, I explored the fundamental concepts that form the basis of digital hardware design.These concepts serve as the building blocks for all future RTL design projects.


## Simulator

One of the first concepts I learned was the role of a **simulator**. A simulator is a software tool that executes Verilog code and predicts how a digital circuit will behave without requiring any physical hardware. Instead of implementing the design directly on a chip, I can first verify its functionality in a virtual environment.

## Design

I learned that the **design** is the actual Verilog module that describes the functionality of a digital circuit. It defines the inputs, outputs, and the logic required to perform a specific operation. The design represents the hardware behavior using Verilog statements instead of physical gates.

<img width="700" height="382" alt="image" src="https://github.com/user-attachments/assets/4701f6c5-e452-44d2-b886-15e49bf5f33b" />

## Testbench

Another important concept I understood was the **testbench**. A testbench is a separate Verilog module created specifically for verification purposes. Unlike the design, it does not represent hardware. Instead, it generates different input combinations, applies them to the design, and allows me to observe the resulting outputs.

<img width="700" height="382" alt="image" src="https://github.com/user-attachments/assets/fd18b73f-889d-437d-8d92-60eb08bf9a9c" />

## Getting Started with Icarus Verilog

I also learned about **Icarus Verilog**, an open-source Verilog compiler and simulator widely used for digital design verification. It provides an easy way to compile Verilog source files, execute simulations, and generate waveform files that can later be analyzed using GTKWave.

<img width="700" height="482" alt="image" src="https://github.com/user-attachments/assets/f21e607f-f5d8-4f0d-bf76-884510c52478" />

---
# Toolchain Setup

I set up my simulation environment by installing Icarus Verilog and GTKWave using the following commands:

```bash
sudo apt install gtkwave
iverilog
```
After installation, I verified both tools were correctly installed using:

---
# Experiment

## Good_Mux RTL Simulation

Every successful chip begins with a successful simulation.

The good_mux design is compiled together with its testbench, executed in the simulator, and observed for correct output behavior. This forms the first checkpoint in the RTL design flow.

```bash
iverilog good_mux.v tb_good_mux.v
./a.out
```

<img width="1569" height="930" alt="image" src="https://github.com/user-attachments/assets/553f0f93-1e38-491b-bb9b-92bccd1da7ca" /> 

The simulation produces a **VCD waveform file**, enabling detailed inspection of signal transitions over time. This serves as a visual confirmation that the RTL design operates according to the expected multiplexer logic.

### 🔍 Open in GTKWave

```bash
gtkwave tb_good_mux.vcd
```
<img width="1569" height="930" alt="image" src="https://github.com/user-attachments/assets/2baef662-11dd-49da-a697-8508a6d14cbe" />

**Why use GTKWave?**
-  Visualize signal transitions
-  Debug RTL behavior
- ✅ Verify functional correctness
- ⚡ Analyze timing relationships

---
## Understanding the Verilog Design
2:1 Multiplexer Implementation (good_mux.v)
```
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

| Signal | Type | Description |
|--------|------|-------------|
| `i0` | Input | First data input. Selected when `sel = 0` |
| `i1` | Input | Second data input. Selected when `sel = 1` |
| `sel` | Input | Selection control signal that decides the output path |
| `y` | Output | Final output signal carrying the selected input data |

---

## Selection Operation

```
             sel
              |
              ↓

        +-------------+
 i0 --->|             |
        |   2:1 MUX   |-----> y
 i1 --->|             |
        +-------------+

```

### Output Logic

| Select (`sel`) | Output (`y`) |
|----------------|--------------|
| 0 | `y = i0` |
| 1 | `y = i1` |

When:

- `sel = 0` → The first input `i0` is passed to the output.
- `sel = 1` → The second input `i1` is passed to the output.
---

# Day 01 Summary

Day 01 marked the beginning of my RTL Design journey, where I explored the fundamentals of digital hardware development using **Verilog HDL**.

The session introduced the complete RTL workflow — from describing a circuit using code, verifying its behavior through simulation, analyzing waveforms, and understanding how designs move closer to real hardware implementation.

I explored the role of **design modules, testbenches, simulators, and waveform viewers** while setting up the RTL environment using **Icarus Verilog** and **GTKWave**.

The first experiment focused on a **2:1 Multiplexer**, where I understood how input selection logic is described in Verilog and verified through simulation. This helped build a strong foundation in writing RTL code and analyzing hardware behavior.

---
<div align="center">

### ⭐ Day 01 Complete

*"Small circuits today, smarter chips tomorrow."*

</div>

