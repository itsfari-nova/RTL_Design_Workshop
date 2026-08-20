# Day 02 • Sequential Logic Optimizations 

> **"Good RTL is not only about functionality — it is about describing hardware that can be optimized efficiently."** ⚙️✨

On **Day 02** of Module 3, I explored **Sequential Logic Optimizations** using Yosys synthesis.

The main focus was to understand how synthesis tools analyze **D Flip-Flops (DFFs)**, constant signals, redundant logic and how they simplify the final hardware implementation.

---
## Topics Covered

- [Introduction to Sequential Logic](#-introduction-to-sequential-logic)
- [DFF Constant Optimization (`dff_const`)](#-dff-constant-optimization-dff_const)
- [Yosys Synthesis & Optimization](#-yosys-synthesis--optimization)
- [Key Takeways](#-key-takeaways) 
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

### Synthesized Hardware

After synthesis, Yosys mapped the design to a SKY130 flip-flop:

<img width="900" height="582" alt="dff_const1_show" src="https://github.com/user-attachments/assets/0aad242a-5392-4158-a391-8a15eb8f6ec8" />

The synthesized circuit contains:

- **D Flip-Flop (DFF)**
- **Constant `1`** connected to the **D input**
- **Reset logic** for the asynchronous reset
- **Clock connection** for positive-edge triggering
- **Output `q`**

### GTKWave Simulation `dff_const1`

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

### Optimization of `dff_const2`

Yosys recognizes that `q` is always constant:

<img width="900" height="582" alt="dff_const2_show" src="https://github.com/user-attachments/assets/86db2399-378c-4cbf-a69d-9780095a2ed2" />

```text
q = 1'b1
```
The synthesized design no longer requires a DFF.

The clock and reset become unused because neither signal can change the value of q.

### GTKWave Simulation `dff_const2`

The GTKWave simulation shows:

<img width="900" height="582" alt="tb_dff_const2" src="https://github.com/user-attachments/assets/a73e7bb6-3525-478e-a3fc-1670e859154f" />

The `dff_const2` waveform shows that:

- `clk` continues toggling.
- `reset` changes during the simulation.
- `q` remains at logic **`1`** throughout the simulation.

This demonstrates that the output does not depend on the sequential elements anymore.

> Even though the clock and reset signals are active, `q` remains constant at `1`, allowing the DFF to be removed during synthesis.

## 3. `dff_const3` 

In `dff_const3`, I explored a design containing **two sequential elements**, `q` and `q1`, with an asynchronous reset.

<img width="900" height="582" alt="gvim_dff_cont3" src="https://github.com/user-attachments/assets/d58205fd-e793-4b49-9405-3e1f3d2a0969" />

The design contains two sequential signals:

- `q1` is set to `0` when `reset = 1`.
- `q` is set to `1` when `reset = 1`.
- When `reset = 0`, `q1` becomes `1` on the next positive clock edge.
- `q` receives the **previous value of `q1`** because non-blocking assignments are used.

Therefore, after reset is released, the output changes through the following sequence:

### Synthesized Netlist

After synthesis using Yosys, the design is mapped to two SKY130 flip-flop cells.

<img width="900" height="582" alt="dff_const3_show" src="https://github.com/user-attachments/assets/367b431e-c2fb-40c7-866b-f41298eaaa87" />

- One **DFF** for `q1`
- One **DFF** for `q`
- Constant `1'b1` connected to the **D input** of the first DFF
- `q1` connected to the **D input** of the second DFF
- **Reset control logic**
- **Clock connections**

Unlike `dff_const2`, the sequential elements **cannot be completely removed**, because the output `q` depends on the **stored state of `q1`**.

### GTKWave Simulation `dff_const3`

The GTKWave simulation shows:

<img width="900" height="582" alt="tb_dff_const3" src="https://github.com/user-attachments/assets/2f1f8b0d-2642-44bd-b661-d8f6d31b27a6" />

- `clk` continuously toggling.
- `reset` initially active and later released.
- `q1` changes from `0` to `1` after the reset is released.
- `q` temporarily changes to `0` and then becomes `1`.

## 4.DFF Constant Optimizations — `dff_const4` & `dff_const5`

<img width="900" height="582" alt="gvim_dff_const4,const5" src="https://github.com/user-attachments/assets/f1e08155-6699-4706-b336-e702b1f5c03c" />

Here we can observe that:
```text
reset = 1  →  q1 = 1
reset = 0  →  q1 = 1
```
Therefore, q1 is actually a constant value:
```text
q1 = 1
```
Since q eventually receives the value of q1, q also becomes:
```text
q = 1
```
So the sequential logic is unnecessary because the outputs can be replaced by constant logic.

### Synthesized Design

The synthesized design shows that Yosys recognizes the **constant behavior** and removes the **unnecessary sequential logic**.

<img width="900" height="582" alt="dff_const4_show" src="https://github.com/user-attachments/assets/c674197d-682a-47bc-b56b-abed456bf094" />

There is no need for the original DFF structure because the outputs do not depend on the clock for their final value.

### Waveform Observation

The waveform confirms the optimization behavior:

<img width="900" height="582" alt="tb_dff_const4" src="https://github.com/user-attachments/assets/3aa46629-b818-43b6-911a-2a728c7847de" />

- `clk` continues toggling throughout the simulation.
- `reset` changes during the simulation.
- `q1` remains at logic **`1`**.
- `q` also remains at logic **`1`**.
- The output does not depend on the clock after optimization.

### Output Behavior

```text
reset = 1  →  q1 = 1, q = 1

reset = 0  →  q1 = 1, q = 1
```
## `dff_const5`

In `dff_const5`, the reset condition assigns both signals to `0`, while during normal operation `q1` becomes `1` and `q` follows `q1`.

```verilog
if(reset)
begin
    q  <= 1'b0;
    q1 <= 1'b0;
end
else
begin
    q1 <= 1'b1;
    q  <= q1;
end
```
Here, q1 does not have the same constant value in both conditions.

```text
reset = 1  →  q1 = 0
reset = 0  →  q1 = 1
```
Therefore, q1 depends on the reset condition.

Also:

```text
q = q1
```

So the value of q depends on the sequential behavior of q1.

### Synthesized Design

The synthesized design contains two DFFs connected in sequence.

<img width="900" height="582" alt="dff_const5_show" src="https://github.com/user-attachments/assets/14f77305-b7b4-46ee-b677-ba77192bf6a7" />

The first flip-flop generates q1, and the second flip-flop stores the value of q1.

Because q depends on the previous sequential value of q1, the DFF structure cannot simply be removed.

### Waveform Observation

The dff_const5 waveform shows:

<img width="900" height="582" alt="tb_dff_cont5" src="https://github.com/user-attachments/assets/293afaca-57ab-4c55-b0a5-cae15f3caed8" />

1. `clk` continues toggling.
2. `reset` is initially active.
3. During reset, `q` remains at **`0`**.
4. After reset is released, `q` changes to **`1`**.
5. The output transition occurs according to the **sequential behavior of the flip-flops**.

## Key Takeaways

- **Constant propagation** allows Yosys to identify signals that always evaluate to a fixed value.
- In `dff_const1`, the D input is constant, but the **asynchronous reset keeps the DFF necessary**.
- In `dff_const2`, both reset and normal-operation branches assign `q = 1`, so the **entire DFF can be removed**.
- `dff_const3` demonstrates that **sequential dependencies matter** — `q` depends on the previous value of `q1`, so the DFFs must be preserved.
- In `dff_const4`, `q1` is constant in both reset and normal operation, allowing Yosys to **eliminate the unnecessary sequential logic**.
- `dff_const5` shows that when a signal changes based on reset, it **cannot be treated as a constant**.
- The key lesson is that **small changes in RTL can lead to significantly different synthesized hardware**.

---
<div align="center">

### ⭐ Day02 Complete

> **"Every bit has a purpose. Every flip-flop has a story. Synthesis decides what stays."** 

</div>
