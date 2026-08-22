# Day 01 • Conditional Constructs in Verilog

> **"Good RTL is not just about describing hardware — it is about choosing the right way to express decisions."**

In **Module 5 — Day 1**, I explored **conditional constructs in Verilog**, focusing on how `if` and `case` statements describe decision-making logic in RTL. These constructs help translate functional requirements into clear and synthesizable hardware descriptions.

---
## Topics Covered

- Introduction to `if and `else if` conditions
- Incomplete `if` — `incomp_if`
- Simulation and Synthesis of `if` statements
- **`case` Construct**

## Introduction to `if` and `else` Conditions

In Verilog, **`if` and `else if` conditions** are used to describe decision-making behavior in RTL. They allow the designer to specify how an output should respond to different input conditions.

An **`if` statement** checks a condition and executes the corresponding assignment when that condition is true. If the condition is false and no `else` is provided, the output is left unassigned for that condition. In combinational logic, this can cause the synthesis tool to infer a **latch**, because the output must retain its previous value.

An **`if-else` statement** provides two possible paths. When the `if` condition is true, one assignment is performed; otherwise, the `else` assignment is performed. This ensures that the output receives a value for both possible conditions and is commonly used to describe selection logic such as a **multiplexer**.

An **`else if` statement** is used when there are multiple conditions that need to be checked. The conditions are evaluated in sequence, starting from the first `if`. If the first condition is false, the next `else if` condition is checked. This continues until a true condition is found. Therefore, an `if-else if` chain naturally creates **priority-based logic**, where the earlier conditions have higher priority.

### Why Do We Use `if` Conditions?

We use `if` statements when the hardware needs to **make a choice between different conditions**.

For example:

```verilog
if (sel)
    y = i1;
else
    y = i0;
```
Here:
```text
When sel = 1 → y gets i1
When sel = 0 → y gets i0
```
So, the if-else construct describes conditional data selection, which synthesis tools can convert into hardware such as MUXes, gates, or storage elements, depending on how the RTL is written.

#### In RTL Design

if conditions are commonly used for:

🔹 Selecting between different data inputs
🔹 Controlling operations based on enable signals
🔹 Describing priority-based logic
🔹 Implementing control logic
🔹 Describing sequential behavior inside clocked blocks

## Incomplete `if` — `incomp_if`

### RTL Code

<img width="900" height="582" alt="gvim_incomp_if2" src="https://github.com/user-attachments/assets/23a2e435-6f2e-410b-b30e-638958666f33" />

#### What is happening?

Here, y is assigned a value only when i0 = 1.

When:

```text
i0 = 1 → y = i1
i0 = 0 → y is not assigned
```
### Simulation — incomp_if

The GTKWave simulation shows the behavior of the incomplete if.

---

<img width="900" height="582" alt="tb_incomp_if" src="https://github.com/user-attachments/assets/410f3231-7792-4fe8-8838-2a7d976f2481" />

---
When i0 = 1, y follows i1.
When i0 = 0, y does not receive a new value.
Instead, y retains its previous value.

This is the key indication of storage behavior.
There is no else statement telling the hardware what y should become when i0 = 0.

Therefore, the previous value of y must be retained.
```text
if i0 = 0 → no assignment → retain previous y
```
### Yosys Synthesis — incomp_if

The Yosys synthesized design shows:

<img width="900" height="582" alt="incomp_if_show" src="https://github.com/user-attachments/assets/a88981dd-e89e-4baa-9242-2bb2e16d76a9" />

Yosys inferred:

```text
$DLATCH_P
```

The synthesized latch is enabled by i0.

i0 = 1 → latch is transparent → y can follow i1

i0 = 0 → latch holds its previous value

So the hardware generated from the RTL is effectively a level-sensitive latch.

## Incomplete if-else if — incomp_if2

The second experiment uses multiple conditions:

---

<img width="900" height="582" alt="gvim_incomp_if_2" src="https://github.com/user-attachments/assets/902a07f8-73b7-42a9-9ccf-dcd83afdf712" />

---

The intended behavior is:
```text
if i0 = 1 → y = i1
else if i2 = 1 → y = i3
```
But what happens when:
```text
i0 = 0
i2 = 0
```
There is no assignment to y.

### Simulation — incomp_if2

The GTKWave waveform confirms the storage behavior.

<img width="900" height="582" alt="tb_incomp_if2" src="https://github.com/user-attachments/assets/fb997086-8e66-4261-aba4-858ff72594df" />

The important case is:
```text
i0 = 0
i2 = 0
```
Since neither condition executes, y keeps its previous value.

### Yosys Synthesis — incomp_if2

The synthesized circuit contains:

<img width="900" height="582" alt="incomp_if2_show" src="https://github.com/user-attachments/assets/2075845f-110f-467d-8cd3-326300073347" />

Yosys maps the logic to:
```text
sky130_fd_sc_hd__mux2_1
sky130_fd_sc_hd__nor2_1
$DLATCH_N
```
#### What the synthesized circuit tells us

The MUX selects between:
```text
i1 → when i0 = 1
i3 → when i0 = 0
```
The NOR gate generates the latch enable:
```text
Enable = ~(i0 | i2)
```
This is the hardware consequence of leaving the assignment incomplete.



