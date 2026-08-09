# DAY 05 • Various Flop Coding Styles

> **"A flip-flop doesn't just remember a bit — it decides exactly when that bit is allowed to change."** ⚡

Welcome to **Day 05** of my RTL Design journey!

Today, I explored **Flip-Flops**, one of the fundamental building blocks of sequential digital logic.  
The focus was on understanding **synchronous reset, asynchronous reset, asynchronous set**, their simulation behavior, and how RTL descriptions are mapped to **SKY130 standard cells** during synthesis.

---

## Explore This Repository

- [Flip-Flop Fundamentals](#-flip-flop-fundamentals)
- [Synchronous Reset](#️-synchronous-reset)
- [Asynchronous Reset](#-asynchronous-reset)
- [Asynchronous Set](#-asynchronous-set)
- [Set vs Reset](#-set-vs-reset)
- [Simulation & Waveforms](#-simulation--waveforms)
- [RTL to Standard Cells](#-rtl-to-standard-cells)
- [Synthesized Structures](#-synthesized-structures)
- [Key Learnings](#-key-learnings)

---

What is a Glitch?

A glitch is a short, unwanted change in a digital signal caused by different logic paths having different propagation delays.

Ideally, we may expect:

```text
Input changes
     │
     ▼
 Logic settles
     │
     ▼
Output changes once
```

But because gates do not respond instantaneously, the output can briefly change to an incorrect value before settling.

Example

Suppose the expected output is:


```text
0 ─────────────────────── 1
```

A glitch might look like:

```text
0 ─────────────────┐ ┌─── 1
                   └─┘
                  glitch
```
The unwanted short pulse is the glitch.

# Flip-Flop Fundamentals

A **flip-flop** is a sequential logic element capable of storing **one bit of information**.

Unlike combinational logic, where the output depends only on the current inputs, a flip-flop has **memory**.

For a basic D Flip-Flop:

```text
             ┌──────────────┐
       D ───►│              │
             │   D FLIP     │───► Q
     CLK ───►│   FLOP       │
             │              │
             └──────────────┘
```
