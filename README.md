# Day 01 • My First Step into RTL Design

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
- [ What I Learned](#-what-i-learned)
- [ Next Challenge](#-next-challenge)

---
# Concepts Learned

On the first day of my RTL Design journey, I explored the fundamental concepts that form the basis of digital hardware design.These concepts serve as the building blocks for all future RTL design projects.


## Simulator

One of the first concepts I learned was the role of a **simulator**. A simulator is a software tool that executes Verilog code and predicts how a digital circuit will behave without requiring any physical hardware. Instead of implementing the design directly on a chip, I can first verify its functionality in a virtual environment.

## Design

I learned that the **design** is the actual Verilog module that describes the functionality of a digital circuit. It defines the inputs, outputs, and the logic required to perform a specific operation. The design represents the hardware behavior using Verilog statements instead of physical gates.

<img width="900" height="482" alt="image" src="https://github.com/user-attachments/assets/4701f6c5-e452-44d2-b886-15e49bf5f33b" />

## Testbench

Another important concept I understood was the **testbench**. A testbench is a separate Verilog module created specifically for verification purposes. Unlike the design, it does not represent hardware. Instead, it generates different input combinations, applies them to the design, and allows me to observe the resulting outputs.

<img width="900" height="482" alt="image" src="https://github.com/user-attachments/assets/fd18b73f-889d-437d-8d92-60eb08bf9a9c" />

## Getting Started with Icarus Verilog

I also learned about **Icarus Verilog**, an open-source Verilog compiler and simulator widely used for digital design verification. It provides an easy way to compile Verilog source files, execute simulations, and generate waveform files that can later be analyzed using GTKWave.

<img width="900" height="482" alt="image" src="https://github.com/user-attachments/assets/f21e607f-f5d8-4f0d-bf76-884510c52478" />

---
# Toolchain Setup

I set up my simulation environment by installing Icarus Verilog and GTKWave using the following commands:

```bash
sudo apt install gtkwave
iverilog
```
After installation, I verified both tools were correctly installed using:

# Experiment

## Good_Mux RTL Simulation

Every successful chip begins with a successful simulation.

The good_mux design is compiled together with its testbench, executed in the simulator, and observed for correct output behavior. This forms the first checkpoint in the RTL design flow.

```bash
iverilog good_mux.v tb_good_mux.v
./a.out
```



---

## 🌟 Why Simulation Matters

Simulation allows us to verify that the RTL behaves exactly as intended before moving to synthesis.

### Benefits

- ✔ Detects design bugs early
- ✔ Saves hardware development cost
- ✔ Verifies functional correctness
- ✔ Simplifies debugging
- ✔ Improves design reliability

> **Simulation first, synthesis next — a fundamental rule in digital design.**

This version is more visually appealing, uses diagrams, callouts, and concise explanations, making your GitHub README stand out while remaining easy to read.

---

<div align="center">

### ⭐ Day 01 Complete

*"Small circuits today, smarter chips tomorrow."*

</div>

