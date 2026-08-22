# Day 01 • Conditional Constructs in Verilog

> **"Good RTL is not just about describing hardware — it is about choosing the right way to express decisions."**

In **Module 5 — Day 1**, I explored **conditional constructs in Verilog**, focusing on how `if` and `case` statements describe decision-making logic in RTL. These constructs help translate functional requirements into clear and synthesizable hardware descriptions.

---

## Topics Covered

- [Introduction to `if` and `else if` conditions](#introduction-to-if-and-else-if-conditions)
- [Incomplete `if` — `incomp_if`](#incomplete-if--incomp_if)
- [Simulation and Synthesis of `if` statements](#simulation-and-synthesis-of-if-statements)
- [Introduction to `case` Conditions](#introduction-to-case-conditions)
- [Simulation and Synthesis of `case` conditions](#simulation-and-synthesis-of-case-conditions)
- [`comp_case` — Complete case with `default`](#comp_case--complete-case-with-default)
- [`partial_case_assign` — The Latch Experiment](#partial_case_assign--the-latch-experiment)
- [`bad_case` — Four Explicit Cases](#bad_case--four-explicit-cases)
- [Summary](#summary)

---

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

---

## Introduction to `case` Conditions

The **`case` statement** in Verilog is used to select one operation from multiple possible conditions based on the value of a control signal. It provides a clean and structured way to describe **MUXes, decoders, and other selection-based combinational logic**.

In this experiment, the `case` conditions determine which input is connected to the output `y`. When a matching condition is found, the corresponding output value is assigned.

> **`case` conditions turn multiple choices into clear hardware selection logic.**

---

## Incomplete `case` Statement — `incomp_case`

After exploring incomplete `if` conditions, I experimented with an **incomplete `case` statement** to understand how missing case conditions affect the synthesized hardware.

### RTL Code

<img width="900" height="582" alt="gvim_incomp_case" src="https://github.com/user-attachments/assets/0adf9a96-9711-4c68-93db-220bce4eb707" />

Here, sel is a 2-bit selection signal, so it can have four possible combinations:
```text
00
01
10
11
```
However, the case statement defines assignments only for:
```text
sel = 00 → y = i0
sel = 01 → y = i1
```
There are no assignments for:
```text
sel = 10
sel = 11
```
This makes the case statement incomplete.

### Simulation — GTKWave

The waveform shows the behavior of sel, the inputs, and the output y.

As sel changes through its different combinations:

<img width="900" height="582" alt="tb_incomp_case" src="https://github.com/user-attachments/assets/fddca18a-b0e3-49de-b81a-da2e255ec842" />

```text
sel = 00 → y follows i0
sel = 01 → y follows i1
sel = 10 → y holds its previous value
sel = 11 → y holds its previous value
```

The waveform therefore demonstrates that the output does not continuously respond to the inputs for every possible sel value.

When the case condition is not matched, there is no new assignment to y, so the previous output value is retained.

That retention behavior is the indication of storage, rather than pure combinational logic.

### Yosys Synthesis

The synthesized circuit clearly shows that the incomplete case has resulted in latch inference.

The circuit contains:

<img width="900" height="582" alt="incomp_case_show" src="https://github.com/user-attachments/assets/406058ed-7192-49c4-9459-f1a617ceb7ac" />

- **A MUX** for selecting between `i0` and `i1`
- **Logic generated from the `sel` conditions**
- **A latch** at the output
- **The final output `y`**

The important part of the synthesized design is the latch

---

## comp_case — Complete case with default

<img width="900" height="582" alt="gvim_comp_case" src="https://github.com/user-attachments/assets/a7fa4914-be17-47a4-9d2a-5b0eea80bcf3" />

Here sel is 2 bits, so it can have four possible combinations

The important point is the `default`.

Instead of separately writing:
```text
2'b10: y = i2;
2'b11: y = i2;
```
we use:
```text
default: y = i2;
```
So every possible sel value gets an assignment.

**There is no latch, because y receives a value for every possible condition.**

### Simulation waveform of comp_case

Your GTKWave screenshot is showing:

<img width="900" height="582" alt="tb_comp_case" src="https://github.com/user-attachments/assets/2b90284e-fe9b-4104-903a-1c6a08b743a7" />

- **Selector sequence:** `00 → 01 → 10 → 11 → 00 → ...`

- **When `sel = 00`:**
  - `y = i0`
  - `y` follows the `i0` waveform.

- **When `sel = 01`:**
  - `y = i1`
  - `y` follows the `i1` waveform.

- **When `sel = 10`:**
  - `y = i2`
  - `y` follows the `i2` waveform.

- **When `sel = 11`:**
  - The `default` condition is executed.
  - `y = i2`
  - Therefore, `y` continues to follow `i2`.

### Synthesized circuit of comp_case

Your Yosys diagram is particularly useful.

It contains:

<img width="900" height="582" alt="comp_case_show" src="https://github.com/user-attachments/assets/52fb83c8-52e5-417f-8c16-f476991a1f40" />

```text
sky130_fd_sc_hd__mux2i_1
sky130_fd_sc_hd__nand2_1
sky130_fd_sc_hd__o21ai_0
```

Yosys has taken your high-level case description and converted it into actual standard-cell hardware.

And importantly, there is no $DLATCH in this circuit.

That means comp_case description does not infer a latch.

---

## partial_case_assign — The Latch Experiment

Now this is the most important screenshot for understanding latch inference.

<img width="900" height="586" alt="gvim_partial_case" src="https://github.com/user-attachments/assets/d996196e-fb5d-42a7-a6a6-3e9eb8b800f1" />

- **When `sel = 00`:**
  - `x` is assigned:
    ```verilog
    x = i2;
    ```

- **When `sel = 01`:**
  - Nothing is assigned to `x`.
  - Therefore, `x` retains its previous value.

- **When `sel = 10` or `sel = 11`:**
  - `x` is assigned:
    ```verilog
    x = i1;
    ```
#### Why y does NOT get a latch

Notice that y is assigned in every case:
```text
00 → y = i0
01 → y = i1
default → y = i2
```
Therefore:
```text
y → combinational logic
x → latch + combinational logic
```
synthesized diagram proves this.

<img width="900" height="582" alt="partial_case_assign_show" src="https://github.com/user-attachments/assets/6847780b-3b2f-4570-8a16-8f9e5801c83e" />

we can actually see the $DLATCH_P_ only on the x path.

---

## bad_case — Four Explicit Cases

Now look at my last RTL:

<img width="900" height="582" alt="gvim_bad_case" src="https://github.com/user-attachments/assets/883b4540-1f35-4e01-a294-804ffa6ddbdf" />

Here every possible value of the 2-bit sel is explicitly covered.
```text
00 → i0
01 → i1
10 → i2
11 → i3
```
So the hardware is simply a 4:1 MUX.

### Yosys Synthesis

<img width="900" height="582" alt="bad_case_show" src="https://github.com/user-attachments/assets/2da3b2d1-836c-47f9-9680-1b16434e90dc" />

Since sel is 2 bits, there are exactly four possible combinations, and all four are explicitly covered.

#### Why is it called bad_case then?

There is an important subtlety here.

For this exact 2-bit sel, code is functionally complete:
```text
00
01
10
11
```
All four combinations are covered.

So it does not infer a latch merely because there is no default.

synthesis result confirms this: Yosys generated:
```text
sky130_fd_sc_hd__mux4_2
```
which is a 4:1 multiplexer.

So in this particular example, bad_case is not actually "bad" from a latch-inference perspective.

However, using a default is often safer when not all values are explicitly handled, especially when unknown/unexpected values need defined behavior.

### bad_case waveform

<img width="900" height="582" alt="tb_bad_case" src="https://github.com/user-attachments/assets/12da8b72-ad98-439b-ae15-b12987634f08" />

GTKWave shows that when `sel = 00`, the output `y` follows `i0`. When `sel = 01`, `y` follows `i1`. Similarly, for `sel = 10`, `y` follows `i2`, and for `sel = 11`, `y` follows `i3`. Thus, the waveform clearly verifies the expected **4:1 MUX behavior**, where the selector determines which input is passed to the output.

### Gate-Level Simulation of bad_case

final screenshot is the GLS waveform.

Notice the hierarchy:

<img width="900" height="582" alt="tb_bad_case_GLS" src="https://github.com/user-attachments/assets/03271de9-9e40-40e3-9c8a-5b8e1e871da8" />

These are synthesized gate-level instances.

Instead of simulating your original:
```text
case(sel)
```
we are now simulating the synthesized gate-level implementation.

And the output still behaves as:
```text
sel = 00 → y = i0
sel = 01 → y = i1
sel = 10 → y = i2
sel = 11 → y = i3
```
This is useful because it verifies that the synthesized hardware still implements the intended RTL behavior.

## Summary

In this experiment, I explored how **`case` conditions are translated into hardware during synthesis**. A complete `case` statement correctly implements selection logic such as a **4:1 MUX**, while incomplete assignments can cause **latch inference**. Using **Yosys**, I observed how the RTL description is converted into **SKY130 standard cells**, and with **GTKWave**, I verified that the synthesized logic produces the expected output for different `sel` values.

---

<div align="center">

### ⭐ Day 01 Complete

> **From `case` conditions to real gates — complete assignments keep the logic combinational, while incomplete paths can make hardware remember.**

</div>




