# Day 07 • Combinational Logic Optimizations

> *"Good hardware isn't just designed — it's optimized."*

Welcome to **Day 07** of my RTL Design & Synthesis journey!

Today, I explored how synthesis tools optimize digital circuits to reduce **area, improve performance, and eliminate unnecessary logic**.

---

## Topics Covered

- Introduction to Optimizations
- Combinational Logic Optimization
- Constant Propagation
- Boolean Logic Minimization
- Optimization of Multiple Modules
- Yosys Optimization Commands

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

---

### Multiple Module Optimization

When a design contains multiple modules, synthesis can optimize them more efficiently after flattening.

```bash
flatten
opt_clean -purge
```

Flattening converts the hierarchical design into a single-level representation, allowing the optimizer to remove redundant logic across modules.

---

### Optimization in Yosys

After synthesis, Yosys can remove redundant logic using:

```bash
opt_clean -purge
```

This command removes unused cells and wires from the synthesized design.

---

---

## Optimization Flow

```text
RTL Design
     │
     ▼
Synthesis
     │
     ▼
Constant Propagation
     │
     ▼
Boolean Simplification
     │
     ▼
Redundant Logic Removal
     │
     ▼
Optimized Netlist
```

---

## Key Learnings

- Constant values can simplify entire logic networks.
- Boolean algebra reduces gate count.
- Yosys automatically removes unnecessary hardware.
- Flattening improves optimization in hierarchical designs.
- Registers that do not affect outputs are eliminated.

---

<div align="center">

### ⭐ Day 07 Complete

*"Every unnecessary gate removed is another step toward smarter hardware."* 

</div>
