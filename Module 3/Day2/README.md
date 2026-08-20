# Day 02 • Sequential Logic Optimizations & Unused Outputs

> *“Good RTL is not only about functionality — it is about describing hardware that can be optimized efficiently.”* ⚙️✨

On **Day 02**, I explored **Sequential Logic Optimizations** and **Unused Output Optimizations** using Yosys synthesis.

The main focus was to understand how synthesis tools analyze **D Flip-Flops (DFFs)**, constant signals, redundant logic, and unused outputs, and how they simplify the final hardware implementation.

---
## Topics Covered

- [Introduction to Sequential Logic](#-introduction-to-sequential-logic)
- [DFF Constant Optimization (`dff_const`)](#-dff-constant-optimization-dff_const)
- [Unused Output Optimization](#-unused-output-optimization)
- [Yosys Synthesis & Optimization](#-yosys-synthesis--optimization)

## Introduction to Sequential Logic

**Sequential logic** is digital logic where the output depends on both the **current input** and the **previously stored state**.

Unlike combinational logic, sequential circuits contain **memory elements**, mainly **flip-flops**.

### Common Sequential Elements

- D Flip-Flop
- JK Flip-Flop
- T Flip-Flop
- Registers
- Counters
- Shift Registers

A **D Flip-Flop** stores the value of the `D` input at the active clock edge.

```text
        D
        │
        ▼
   ┌─────────┐
CLK│   DFF   │───► Q
 ─►│         │
   └─────────┘

