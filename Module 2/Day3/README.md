# Day 06 • Interesting Optimizations

> ### **“Good RTL describes the function. Smart RTL lets synthesis discover the simplest hardware.”**

Welcome to **Day 06** of my RTL Design & Synthesis journey!

Today, I explored an interesting topic in **RTL synthesis — Optimization**.

The main focus was to understand how **Yosys analyzes arithmetic operations written in Verilog and transforms them into efficient hardware representations**.

---

## What I Explored Today

- Multiplication by a constant
- Multiplication of a signal by itself
- RTL representation
- Yosys optimization
- Synthesized logic visualization
- Netlist exploration
- Summary
---

# Understanding Optimization

In RTL design, we describe **what the circuit should do**.

The synthesis tool then analyzes the RTL and determines **how that functionality can be implemented efficiently in hardware**.

For example:

```text
RTL Expression
      ↓
   Synthesis
      ↓
  Optimization
      ↓
Efficient Hardware
```
### Multiplication by Constant — mul2

The first experiment was based on multiplying a 3-bit input by the constant value 2.

Verilog RTL

<img width="900" height="482" alt="RTL_mult_code" src="https://github.com/user-attachments/assets/1a1cfb07-a6e0-4f63-8dd4-f3f8fa77683b" />

The RTL contains:
```
a × 2
```

In binary arithmetic, multiplying by 2 is equivalent to shifting the value left by one position:

```
a × 2  =  a << 1
```
For example:
```
a = 101₂

101₂ × 2 = 1010₂
```
Therefore, a general multiplier is not necessary for this particular constant multiplication. The synthesis tool can recognize this relationship and implement the operation using simpler hardware.

<img width="900" height="482" alt="mul2_show" src="https://github.com/user-attachments/assets/ac83245a-b736-4783-8e9a-b13536a93be4" />

This shows how multiplication by a constant can be reduced to simple bit-level operations.

### Multiplication of a Signal — mult8

The second experiment involved multiplying the input signal by itself.
Verilog RTL
```
y = a × a
```
The input a is 3 bits wide.
A 3-bit unsigned number can represent:
```
0 → 7
```
The maximum possible multiplication is:
```
7 × 7 = 49
```
The binary representation of 49 is:
```
49 = 110001₂
```
which requires 6 bits.
Therefore:
3-bit × 3-bit → 6-bit result
Hence the output is declared as:
```
output [5:0] y
```
<img width="900" height="482" alt="mult8_show" src="https://github.com/user-attachments/assets/642ff6d0-c50d-4398-83fc-ba39a1c8393f" />

### Netlist Exploration

After synthesis, I explored the generated netlist to understand how the RTL is represented after processing.

The explored netlist contains:

<img width="1004" height="529" alt="mult8_netlist" src="https://github.com/user-attachments/assets/46098554-1893-4b08-ab08-67b2ced04f8d" />

This helped me understand the transformation:
```
RTL Code
   ↓
Yosys Processing
   ↓
Optimization
   ↓
Synthesized Representation
   ↓
Hardware Structure
```
# Summary

RTL provides a clear description of hardware functionality, while synthesis tools such as **Yosys** analyze the RTL and transform it into efficient hardware implementations. Through this experiment, I learned how **constant multiplication can be optimized**, such as multiplying by `2` being equivalent to a left shift by one bit. I also understood that a **3-bit × 3-bit multiplication can require a 6-bit output**. Exploring the synthesized schematic and netlist helped me connect the RTL code with its underlying hardware structure and understand how **optimization can reduce unnecessary hardware complexity**.

---
<div align="center">

### ⭐ Day 06 Complete

> **"From a simple equation to a smarter circuit — optimization makes the difference."** 

</div>
