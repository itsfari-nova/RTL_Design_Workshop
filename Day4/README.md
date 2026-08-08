# Day 05 • Hierarchical vs Flat Synthesis & Multi-Module RTL

> **"One module shows the logic. Multiple modules reveal the architecture."** 🔗

Welcome to **Day 05** of my RTL Design journey! 👋

Today, I explored **multi-module RTL designs**, learned how to trace **sub-modules**, and understood how hierarchy connects different blocks together.

I also executed **hierarchical and flat synthesis** to see how synthesis tools handle and transform the design structure. 

## Explore This Repository

- [Multiple Module Design](#-multiple-module-design)
- [Hierarchical vs Flat Synthesis](#️-hierarchical-vs-flat-synthesis)
- [Understanding Sub-Modules](#-understanding-sub-modules)
- [Synthesis Experiment](#️-synthesis-experiment)
- [Exploring the Synthesized Design](#-exploring-the-synthesized-design)
- [Summary](#-summary)

## Multiple Modules

As RTL designs become larger, writing the entire design inside a single module can make the code difficult to understand and maintain.

Instead, the design can be divided into **multiple smaller modules**, with each module handling a specific function.

```text
                    TOP MODULE
                        │
              ┌─────────┴─────────┐
              │                   │
          MODULE A             MODULE B
              │
              ▼
         SUB-MODULE
```
## Synthesizing the Multiple-Module Design

After understanding the module hierarchy, I synthesized the complete design using **Yosys**.

The RTL files were read together, the **top module** was identified, and the hierarchy between the top module and its sub-modules was analyzed during synthesis.

This helped me observe how multiple RTL modules are processed together and transformed into a **synthesized netlist**.

### 🔍 Synthesized Design

The synthesized design was explored to verify the module hierarchy and understand how the different blocks are represented after synthesis.

<img width="900" height="500" alt="sub_multiples_modules" src="https://github.com/user-attachments/assets/20e57736-01dc-47d8-a1eb-3a7115252828" />

> **From multiple RTL modules → hierarchy → synthesized netlist.** 🔗

The image below shows the **synthesized multi-module design**, where the top module and its connected sub-modules can be observed after synthesis.

<img width="900" height="500" alt="show_multiples_modules" src="https://github.com/user-attachments/assets/63cdb32b-8a10-4d9c-8773-7a9ed0ec9329" />

## Exploring Multiple Modules with GVim

I used **GVim** to open and explore the Verilog files containing the multiple modules.

This helped me understand how the **top module connects with its sub-modules** and how the complete design is structured.

After analyzing the modules, I executed the synthesis flow and obtained the **synthesized output**.

<img width="700" height="482" alt="gvim_multiple_modules" src="https://github.com/user-attachments/assets/8a3150ce-274a-4ca3-ac47-434ab69c761f" />

# Hierarchical vs Flat Synthesis

After synthesizing the multiple-module design, I explored two different approaches — **hierarchical synthesis** and **flat synthesis**.

### Hierarchical Synthesis

In hierarchical synthesis, the original structure of the design is maintained. The **top module, sub-modules, and their relationships** remain identifiable after synthesis.

This approach makes it easier to understand the design structure, trace signals between modules, and analyze individual blocks of a larger design.

```text
Multiple RTL Modules
        ↓
   Top Module
        ↓
  Sub-Modules
        ↓
Synthesis with Hierarchy
        ↓
Hierarchical Netlist

```

### Flat Synthesis

In flat synthesis, the hierarchy is removed by **flattening the design**. The logic from the sub-modules is combined into the top-level design, allowing the synthesis tool to view the design as a more unified logic structure.

```text
Multiple RTL Modules
        ↓
   Top Module
        ↓
   Flatten Design
        ↓
Combine Module Logic
        ↓
    Flat Netlist
```

<img width="900" height="582" alt="flat_multiple_module" src="https://github.com/user-attachments/assets/93486c03-4627-4e7b-b950-3725471ec54c" />

This can provide more freedom for **optimization across module boundaries**, although the original module structure becomes less visible.

### My Observation

I executed both hierarchical and flat synthesis on the same multi-module RTL design and examined the commands, generated code, and synthesis results.

Comparing both outputs helped me understand the practical difference between **preserving module hierarchy** and **flattening the complete design**.

<img width="900" height="582" alt="hier vs flat_modules" src="https://github.com/user-attachments/assets/87b4ea68-3961-45a8-a408-734ead67e910" />

> **Hierarchical synthesis keeps the design organized; flat synthesis gives the tool a broader view for optimization.**

## Understanding Sub-Modules

A **sub-module** is a smaller Verilog module that is instantiated inside another module. It performs a specific function and becomes part of the larger design.

### How Sub-Modules Work

The top module does not need to contain the internal logic of every block. Instead, it connects different sub-modules through their input and output ports.

```text
Input Signals
     │
     ▼
┌─────────────┐
│ TOP MODULE  │
└──────┬──────┘
       │
       ├──────────────► Sub-Module A
       │                       │
       │                       ▼
       └──────────────► Sub-Module B
                               │
                               ▼
                         Output Signals
```

After synthesizing my sub-module, I observed that the RTL logic was reduced to **a single AND gate**.

The AND gate has **two inputs and one output**. Its output becomes `1` only when **both inputs are `1`**.

<img width="900" height="482" alt="show_sub_module1" src="https://github.com/user-attachments/assets/8d962c3a-01c1-4793-8b62-960520dd0f9d" />

# Day 05 Summary

Today, I explored **multiple RTL modules, sub-modules, and module hierarchy** using GVim and Yosys. I synthesized the design and observed how the RTL was transformed into logic gates.

I also executed **hierarchical and flat synthesis** side by side and compared how the design structure changes when hierarchy is preserved or flattened.

The most interesting observation was seeing a simple sub-module reduce to a **single AND gate** after synthesis — connecting the RTL description directly to the hardware it represents.

<div align="center">

### ⭐ Day 05 Complete

> **“From a module on the screen to a gate in hardware — the hierarchy finally came alive.”** 

</div>
