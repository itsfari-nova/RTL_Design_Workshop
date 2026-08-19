# Day 03 • Inside the `.lib` — Exploring Standard Cells 🔬

> **"Today I moved one step closer to the silicon — from describing logic to understanding the cells that build it."**

Welcome to **Day 03** of my RTL Design journey! 👋

After learning about synthesis and netlists, today I explored another important layer of the digital design flow — the **`.lib` file**.

I worked with the **SKY130 standard-cell library** and opened the library files to understand what information is actually available for a standard cell. Instead of seeing a gate only as a logical function, I started looking at it from a more practical hardware perspective — including its **power, voltage, timing, and other cell characteristics**.

---

## Explore This Repository

- [Entering the Standard-Cell Library](#-entering-the-standard-cell-library)
- [Exploring the `.lib` File](#-exploring-the-lib-file)
- [From Logic to Real Cells](#-from-logic-to-real-cells)
- [Summary](#-Summary)

---

# Entering the Standard-Cell Library

I started by navigating to the **SKY130 standard-cell library** from the terminal.

```bash
gvim ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

```
<img width="900" height="582" alt="standard_lib" src="https://github.com/user-attachments/assets/b52401bd-55e5-42db-9d10-cdc60cccd3c1" />

---
## Power •  Voltage •  Timing

While exploring the `.lib` file, I came across three important aspects of a standard cell: **power, voltage, and timing**. **Power** represents the energy consumed by a cell while it operates, which is important for designing power-efficient circuits. **Voltage** represents the electrical supply condition under which the cell is characterized and operates correctly. **Timing** describes how quickly a cell responds to changes at its inputs and produces the corresponding output. Together, these characteristics help describe the practical behavior of a standard cell beyond its basic logic function and are important when evaluating the **performance, power, and reliability** of a digital design.

# From Logic to a Real Cell

I opened and examined the **AND0 cell** to understand how a simple logic function is represented as a real standard cell. At the RTL level, an AND gate can be described simply as `Y = A & B`, but inside the standard-cell library, the **AND0 cell** contains much more information about its actual hardware characteristics. I explored its cell definition, pins, logic function, and the associated **power, voltage, and timing information**. This helped me understand that a real standard cell is not just a Boolean operation — it is a characterized hardware building block with electrical and timing properties that are important during synthesis and implementation.

```text
        A ───────┐
                 │
                 ├──── AND0 ──── Y
                 │
        B ───────┘

              AND0 Cell
                  │
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      Power     Voltage    Timing
        │         │         │
        └─────────┼─────────┘
                  ↓
          Real Cell Behaviour
```
<img width="900" height="582" alt="and0" src="https://github.com/user-attachments/assets/7529da0f-f1e8-456f-ae02-4b179f83580a" />

# Summary

Day 03 gave me a practical introduction to **`.lib` files and standard-cell libraries**. I explored the SKY130 library using GVim and opened the `sky130_fd_sc_hd__tt_025C_1v80.lib` file to understand what information is available inside a real cell library. I explored the **AND cell** and learned that a standard cell is more than just a logic function — it also has important characteristics related to **power, voltage, and timing**. This helped me connect the simple logic I write in RTL with the real hardware cells used during synthesis and implementation. Overall, today helped me take one more step from **logical design toward understanding real hardware.** 

---

<div align="center">

### ⭐ Day 03 Complete

> *"The logic was simple. The cell behind it was a whole new story."* 

</div>

