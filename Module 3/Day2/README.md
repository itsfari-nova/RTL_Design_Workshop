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
  
---
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
```
## DFF Constant Optimization — `dff_const1` & `dff_const2`

## 1. `dff_const1` — Constant D Input with Reset

<img width="900" height="582" alt="gvim_dff_const1,const2" src="https://github.com/user-attachments/assets/d93d2daa-d21c-4b09-8ecc-5be403eea63c" />

- The DFF is triggered by the **positive edge of `clk`**.
- It has an **asynchronous active-high reset**.
- When `reset = 1`, the output `q` becomes **0**.
- When `reset = 0`, the next **positive clock edge** makes `q = 1`.
- The **D input is effectively a constant `1`**.
- Therefore, although the D input is constant, the flip-flop **cannot be completely removed** because the asynchronous reset still controls the output.

## Synthesized Hardware

After synthesis, Yosys mapped the design to a SKY130 flip-flop:

<img width="900" height="582" alt="dff_const1_show" src="https://github.com/user-attachments/assets/0aad242a-5392-4158-a391-8a15eb8f6ec8" />

The synthesized circuit contains:

- **D Flip-Flop (DFF)**
- **Constant `1`** connected to the **D input**
- **Reset logic** for the asynchronous reset
- **Clock connection** for positive-edge triggering
- **Output `q`**

## GTKWave Simulation `dff_const1`

The GTKWave simulation shows:

<img width="1920" height="940" alt="tb_dff_const1" src="https://github.com/user-attachments/assets/f7525ec4-31a2-4eca-9e0e-ad4fcc27384f" />

- `clk` continuously toggling.
- `reset` changing during the simulation.
- `q` responding according to the reset condition and clock edge.
- After reset is released, `q` becomes `1` on the appropriate clock edge.

> **This confirms the expected sequential behavior of the RTL.**

## 2. `dff_const2` — Output Always Constant

```verilog
module dff_const2(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)
begin
    if(reset)
        q <= 1'b1;
    else
        q <= 1'b1;
end

endmodule
```
Therefore:

reset = 1  →  q = 1
reset = 0  →  q = 1

The value of clk and reset does not actually affect the final value of q.

So, the DFF is unnecessary and can be completely removed during synthesis.

## Optimization of `dff_const2`

Yosys recognizes that `q` is always constant:

<img width="900" height="582" alt="dff_const2_show" src="https://github.com/user-attachments/assets/86db2399-378c-4cbf-a69d-9780095a2ed2" />

```text
q = 1'b1
```
The synthesized design no longer requires a DFF.

The clock and reset become unused because neither signal can change the value of q.

## GTKWave Simulation `dff_const2`

The GTKWave simulation shows:

<img width="900" height="582" alt="tb_dff_const2" src="https://github.com/user-attachments/assets/a73e7bb6-3525-478e-a3fc-1670e859154f" />

The `dff_const2` waveform shows that:

- `clk` continues toggling.
- `reset` changes during the simulation.
- `q` remains at logic **`1`** throughout the simulation.

This demonstrates that the output does not depend on the sequential elements anymore.

> Even though the clock and reset signals are active, `q` remains constant at `1`, allowing the DFF to be removed during synthesis.
