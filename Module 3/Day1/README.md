# Day 01 • Combinational Logic Optimizations

> *"Good hardware isn't just designed — it's optimized."*

Today, I explored how synthesis tools optimize digital circuits to reduce **area, improve performance, and eliminate unnecessary logic**.

---

## Topics Covered

- [Introduction to Optimizations](#-introduction-to-optimizations)
- [Combinational Logic Optimization](#-combinational-logic-optimization)
- [Constant Propagation](#-constant-propagation)
- [Boolean Logic Minimization](#-boolean-logic-minimization)
- [Optimization of Multiple Modules](#-optimization-of-multiple-modules)
- [Key Learnings](#-key-learnings)

---
## Introduction

Optimization is the process of improving a digital circuit to achieve better performance while using fewer hardware resources.

During **RTL synthesis**, optimization techniques are applied to simplify the logic and generate an efficient **gate-level implementation**. The synthesis tool analyzes the RTL code, removes unnecessary logic, and produces an optimized netlist without changing the original functionality of the design.

### Objectives of Optimization

- Reduce the number of logic gates
- Minimize chip area
- Lower power consumption
- Improve circuit speed
- Reduce propagation delay

---
## Combinational Logic Optimization

Combinational logic circuits do not contain memory elements. Their outputs depend only on the current inputs.

The synthesis tool analyzes the logic and removes unnecessary hardware while preserving the same functionality.

### Constant Propagation

Constant propagation is an optimization technique in which constant values are propagated through the circuit to simplify the logic.

**Example:**

```text
y = ((a & b) | c)'
```

If `b = 0`,

```text
y = ((a & 0) | c)'
  = (0 | c)'
  = c'
```

The entire expression is reduced to a single inverter.

---

### Boolean Logic Minimization

Boolean algebra can simplify large expressions into much smaller circuits.

**Example:**

```verilog
assign y = a ? (b ? c : (c ? a : 0)) : (~c);
```

After simplification:

```text
y = a XNOR c
```

A complex expression is reduced to a single gate.

---

### Optimization in Yosys

After synthesis, Yosys can remove redundant logic using:

```bash
opt_clean -purge
```

This command removes unused cells and wires from the synthesized design.

## Exploring Combinational Logic Optimization

In this lab, I used **Yosys** inside **VirtualBox** to analyze how different combinational logic expressions are optimized during synthesis.

The generated circuits were visualized using the **`show`** command in Yosys, which displayed the synthesized netlists in **Dot Viewer**.

## Optimization 1 (`opt_check`)

<img width="900" height="582" alt="opt_check_show" src="https://github.com/user-attachments/assets/1b920bde-cf15-442d-befa-858308667028" />

The synthesized circuit was optimized into a **2-input AND gate**.

### Observation

- Inputs **`a`** and **`b`** are connected to the AND gate.
- The output **`y`** is generated only when both inputs are equal to **1**.
- The synthesis tool simplified the original RTL description and mapped it to a standard cell from the **SKY130 library**.

### Optimized Logic

```text
y = a & b
```

---

## Optimization 2 (`opt_check2`)

<img width="900" height="582" alt="opt_check2_show" src="https://github.com/user-attachments/assets/f21ec320-5ae8-4515-82dd-1283ea746b1b" />

The synthesized circuit was optimized into a **2-input OR gate**.

### Observation

- Inputs **`a`** and **`b`** are connected to the OR gate.
- The output **`y`** becomes **1** whenever at least one input is **1**.
- Yosys recognized the logic expression and directly mapped it to an optimized OR gate.

### Optimized Logic

```text
y = a | b
```

---

## Optimization 3 (`opt_check3`)

<img width="900" height="582" alt="opt_check3_show" src="https://github.com/user-attachments/assets/eb0081ba-f3d8-4390-a4ea-c2e1d1f01124" />

The synthesized circuit was optimized into a **3-input AND gate**.

### Observation

- Inputs **`a`**, **`b`**, and **`c`** are connected to a single AND gate.
- The output **`y`** becomes **1** only when all three inputs are **1**.
- Instead of creating multiple 2-input AND gates, the synthesis tool selected a **single 3-input standard cell**, reducing hardware complexity.

### Optimized Logic

```text
y = a & b & c
```

---

### Multiple Module Optimization

When a design contains multiple modules, synthesis can optimize them more efficiently after flattening.

<img width="900" height="582" alt="multiple_module_opt_show" src="https://github.com/user-attachments/assets/ebb01538-caaa-4d94-87c8-32a2495ea3ab" />

```bash
flatten
opt_clean -purge
```

multiple_modules_opt helps demonstrate that synthesis is not simply a direct translation of every Verilog statement into gates.

multiple_modules_opt shows how Yosys can analyze a multi-module RTL design and optimize its logic while maintaining the same intended functionality.

---

## Key Learnings

- Constant values can simplify entire logic networks.
- Boolean algebra reduces gate count.
- Yosys automatically removes unnecessary hardware.
- Different RTL descriptions can produce efficient hardware implementations.
- Logic optimization reduces unnecessary hardware while preserving functionality.

---

<div align="center">

### ⭐ Day 01 Complete

*"Every unnecessary gate removed is another step toward smarter hardware."* 

</div>
