# Day 07 • Combinational and Sequential Optimizations

> *"Good hardware isn't just designed — it's optimized."* 

Today, I explored how synthesis tools optimize digital circuits to reduce **area, improve performance, and eliminate unnecessary logic**.

---

## Topics Covered

- Combinational Logic Optimization
- Sequential Logic Optimization
- Constant Propagation
- Boolean Logic Minimization
- Optimization of Multiple Modules
- Optimization of Unused Sequential Logic
- Yosys Optimization Commands

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

## Sequential Logic Optimization

Sequential circuits contain memory elements such as flip-flops.

Unlike combinational optimization, sequential optimization considers both the current inputs and the stored states.

---

### Sequential Constant Propagation

If a flip-flop receives a constant input, the synthesis tool checks whether the flip-flop is still required.

In some cases, the flip-flop can be removed.

In other cases, the flip-flop must remain because it still affects the circuit's behavior.

---

### Optimization of Unused Outputs

If certain registers do not contribute to the final output, the synthesis tool removes them.

**Example:**

A 3-bit counter might initially appear to require three flip-flops.

After synthesis, the optimizer may determine that only one bit actually affects the output.

As a result:

```text
3 Flip-Flops → 1 Flip-Flop
```

Unused logic is automatically eliminated.

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
