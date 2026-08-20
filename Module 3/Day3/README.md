# Day 03 • Sequential Optimizations for Unused Outputs 

> **"If the output never sees it, synthesis has no reason to keep it."**

In **Day 03**, I explored **sequential logic optimization for unused outputs** using **Yosys synthesis**.

The experiment focuses on a **3-bit counter** and demonstrates how synthesis identifies sequential logic that does not contribute to the required output and optimizes it.

---
## What I Explored

- [**Unused Outputs & Unused Sequential Logic**](#unused-outputs-and-unused-sequential-logic)
- [**Optimization of Counter Logic**](#optimization-of-counter-logic)
- [**Output Observability**](#output-observability)
- [**Effect of Output Logic on Optimization**](#effect-of-output-logic-on-optimization)
- [**Summary**](#summary)
---

## Sequential Optimizations for Unused Outputs

Sequential optimization for unused outputs is the process of **identifying and removing sequential logic that does not contribute to any required output of the design**.

In RTL, we may have registers, flip-flops, counters, or state variables that are updated on every clock cycle but whose values are **never used by the output logic**. Although these elements are present in the Verilog code, they may not be necessary in the actual hardware.

During synthesis, **Yosys analyzes the connectivity and observability of sequential logic**. If a register or its associated logic has no effect on any observable output, Yosys can optimize it away from the synthesized netlist.

## Counter Output Uses Only One Bit

### RTL Code

<img width="900" height="582" alt="gvim_counter_opt" src="https://github.com/user-attachments/assets/18dfaea1-3384-4320-98ad-54ecc804d5b5" />

Understanding the RTL

The design contains a 3-bit counter:

```text
count[2:0]
```
The counter starts from:
```text
000
```
and increments on every rising edge of clk.

The asynchronous reset forces the counter back to zero:
```text
if (reset)
    count <= 3'b000;
```
However, the output is connected only to:
```text
assign q = count[0];
```
This means that although the RTL declares three counter bits, only count[0] can affect the external output.
```text
Counter Behavior
count = 000 → q = 0
count = 001 → q = 1
count = 010 → q = 0
count = 011 → q = 1
count = 100 → q = 0
count = 101 → q = 1
```
### Yosys Optimization

<img width="900" height="582" alt="counter_opt_show" src="https://github.com/user-attachments/assets/b92f6895-0f0f-4453-8592-e3f734b4e00e" />

Only count[0] is connected to the external output.

The upper bits count[2] and count[1] do not directly contribute to the observable output.

The count diamond is a 2:1 mux construct showing bit-select/route logic (2:1 - 1:0 and 1:0 - 2:1 labels are just Yosys's bit-index annotations for the mux inputs/outputs).

Output q comes directly off one of the mux branches.

Therefore, Yosys can analyze their observability and eliminate sequential logic that is not required to preserve the required output behavior.

## Output Uses the Complete Counter

The RTL was then modified so that the output depends on the entire 3-bit counter.

<img width="900" height="582" alt="gvim_counter_opt2_using3bit" src="https://github.com/user-attachments/assets/5c0605b6-4a84-49dc-977a-10cd6fdbc69a" />

The output now checks whether the complete counter value is equal to:
```text
3'b100
```
Therefore:
```text
count = 000 → q = 0
count = 001 → q = 0
count = 010 → q = 0
count = 011 → q = 0
count = 100 → q = 1
count = 101 → q = 0
...
```
The output is asserted only when:

```text
count = 100
```
### Optimization Is Different Here

q = count[0];

Only one bit was required.

But now:

q = (count[2:0] == 3'b100);

Every counter bit participates in determining the output.

Therefore, removing count[2] or count[1] would change the functionality.

## Summary

- A **3-bit counter** is implemented in the RTL.
- Only **`count[0]`** is connected to the output `q`.
- The upper bits **`count[2]` and `count[1]`** do not affect the observable output.
- Yosys analyzes this **output observability** during synthesis.
- Unnecessary sequential logic can be **eliminated or simplified**.
- This demonstrates how synthesis can produce **optimized hardware from seemingly larger RTL**.
---
<div align="center">

### ⭐ Day 03 Complete 

> **The RTL describes the possibility — synthesis keeps only what the hardware actually needs.**

</div>
