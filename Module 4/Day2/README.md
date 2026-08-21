# Day 02 • Synthesis–Simulation Mismatch, Blocking & Non-Blocking

> **“RTL may look correct in simulation — but the coding style determines how faithfully it becomes hardware.”**

Welcome to **Day 02 of Module 4**, where I explored the relationship between **RTL simulation and synthesized hardware**, with a focus on **synthesis–simulation mismatch** and Verilog assignment statements.

In this experiment, I studied the difference between **Blocking (`=`)** and **Non-Blocking (`<=`) assignments**, how they behave during simulation, and how the choice of assignment can influence the interpretation of sequential and combinational logic.

I also explored the **caveats of using blocking statements in sequential logic** and understood why proper Verilog coding practices are important for ensuring that **simulation behavior, synthesis results, and intended hardware remain consistent**.

---
## What I Explored

- **Synthesis–Simulation Mismatch**
- **Blocking Assignments (`=`)**
- **Non-Blocking Assignments (`<=`)**
- **Caveats with Blocking Statements**
- **Simulation Mismatch (bad_mux)**
- **RTL vs. GLS Mismatch: Blocking Assignment Caveat (blocking_caveat)**
- **Key Takeaways**

## 1. Synthesis–Simulation Mismatch

Synthesis–simulation mismatch occurs when the behavior observed during **RTL simulation** does not match the behavior of the **synthesized hardware**.

This can happen because of incorrect RTL coding styles, incomplete assignments, improper use of blocking/non-blocking statements, or simulation constructs that do not represent real hardware.

> **💡 Simulation shows what the RTL does; synthesis reveals what hardware is actually built.**

---
## 2. Blocking Assignments (`=`)

A **blocking assignment** updates the left-hand side immediately within the procedural block.

```verilog
a = b;
c = a;
```
Here, c receives the new value of a because the first statement completes before the next statement executes.

Blocking assignments are commonly used when describing combinational logic.

## 3. Non-Blocking Assignments (<=)

A non-blocking assignment schedules the update of the left-hand side rather than updating it immediately.

```text
a <= b;
c <= a;
```

Both assignments use the values that existed before the clocked block updates them. This behavior closely models how multiple registers update simultaneously in real sequential hardware.

Non-blocking assignments are therefore commonly used for flip-flops, registers, and other sequential logic.

> **"💡 Non-blocking assignments help RTL model simultaneous state updates in clocked hardware."**

## 4. Caveats with Blocking Statements

Using blocking assignments in sequential logic can create unexpected simulation behavior because later statements may immediately see values assigned by earlier statements.

For example, changing the order of blocking assignments can change the simulation result even when the intended hardware structure appears similar.

Therefore:

Use blocking (=) mainly for combinational logic.
Use non-blocking (<=) mainly for sequential logic.
Avoid careless mixing of assignment types in the same procedural block.
"**
> **"✨ The right assignment style keeps RTL behavior predictable — from simulation to synthesized hardware."**
> **💡 Simulation shows what the RTL does; synthesis reveals what hardware is actually built.**

## Simulation Mismatch (bad_mux)

This experiment demonstrates a Simulation vs. Synthesis Mismatch (RTL vs. GLS mismatch) caused by an incomplete sensitivity list in an asynchronous combinational block.

### 1. Verilog Implementation (bad_mux.v)

<img width="900" height="582" alt="gvim_bad_mux" src="https://github.com/user-attachments/assets/d0d3dfea-9704-41b8-9681-bacdc908ac35" />

In bad_mux.v, the always block is triggered only on changes to the sel signal, omitting inputs i0 and i1 from the sensitivity list.

RTL Behavioral Modeling: The output y only updates when sel toggles. If sel remains static and i0 or i1 changes, y retains its previous state (behaving like a latch during RTL simulation).

Synthesized Hardware: Logic synthesis tools interpret a 2-to-1 multiplexer combinational logic irrespective of the missing inputs in always @(...). Synthesis infers pure combinational multiplexer hardware (y = sel ? i1 : i0), ignoring the incomplete sensitivity list artifact.

### Simulation Results

#### Pre-Synthesis (RTL Simulation)

<img width="900" height="582" alt="tb_bad_mux" src="https://github.com/user-attachments/assets/fc96ed66-918d-48b3-b736-3676728e9b19" />

---
Behavior: When i0 or i1 changes while sel remains constant, output y does not update.

Observation: As shown in the GTKWave waveform below, y holds its value despite changes in input streams because the always block is never triggered without a transition on sel.

---
#### Post-Synthesis (Gate-Level Simulation - GLS)

<img width="900" height="582" alt="tb_bad_mux_GLS" src="https://github.com/user-attachments/assets/4684f809-ad54-4895-a077-9be28f58be8d" />

---
Behavior: The synthesized netlist uses standard combinational logic gates. Output y updates immediately whenever i0, i1, or sel changes.

Observation: The GLS GTKWave waveform reflects true multiplexer hardware behavior, showing transitions on y as soon as inputs change.

---

## RTL vs. GLS Mismatch: Blocking Assignment Caveat (blocking_caveat)

This module demonstrates a classical RTL vs. Gate-Level Simulation (GLS) Mismatch caused by improper ordering of blocking assignments (=) inside an evaluation block.

### Verilog Code Analysis (blocking_caveat.v)

<img width="900" height="562" alt="gvim_blocking_caveat" src="https://github.com/user-attachments/assets/5149aedf-5128-457c-a6a6-c574d3eb9c64" />

Line 4 (d = x & c;): $d$ is evaluated using the previous (old) value of $x$.

Line 5 (x = a | b;): $x$ is updated after $d$ has already been computed.

Behavioral Impact: During RTL simulation, $d$ always uses $x$'s stale value from the previous evaluation trigger, introducing a 1-cycle/evaluation-event delay for changes in $a$ or $b$.

## Waveform Comparison

### RTL Simulation — GTKWave

<img width="900" height="582" alt="tb_blocking_caveat" src="https://github.com/user-attachments/assets/31de7317-a9c2-4bd2-afb4-b056c0d019aa" />

- When **`a` or `b` changes**, `x` updates only after `d` is evaluated.
- Therefore, **`d` temporarily shows the old value of `x`**.
- `d` gets the correct value only after the **next evaluation event**.
- This creates a visible **delay/mismatch** in the RTL waveform.

### Gate-Level Simulation — GTKWave

<img width="900" height="582" alt="tb_blocking_caveat_GLS" src="https://github.com/user-attachments/assets/2ad596ab-cd35-45b2-ab68-af846d6f5d0b" />

- The synthesized gate directly implements:
  **`d = (a | b) & c`**
- When **`a`**, **`b`**, or **`c`** changes, `d` responds according to the actual gate logic.
- There is **no RTL statement-order dependency**.
- The GLS waveform therefore shows the **optimized hardware behavior**.

## Key Takeaways

- **Blocking assignments (`=`)** execute statements sequentially within the same procedural block.
- Statement order can cause **simulation-only dependencies** and unexpected RTL behavior.
- RTL simulation may use a **stale intermediate value**, while synthesis can optimize the logic differently.
- **Yosys synthesis** focuses on the actual combinational logic rather than the procedural statement order.
- **GLS** helps verify whether the synthesized hardware behavior matches the intended RTL behavior.
- Careful RTL coding is essential to avoid **Synthesis–Simulation Mismatch**.

---

<div align="center">

> **"From RTL to gates — understanding the mismatch between simulation and synthesis."**

</div>
