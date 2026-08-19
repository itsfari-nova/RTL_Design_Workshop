# Day 05 • Various Flop Coding Styles

> **"A flip-flop doesn't just remember a bit — it decides exactly when that bit is allowed to change."** 

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
- [Key Learnings](#-key-learnings)

---

## What is a Glitch?

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

## Flip-Flop Fundamentals

A **flip-flop** is a sequential logic element capable of storing **one bit of information**.

Unlike combinational logic, where the output depends only on the current inputs, a flip-flop has **memory**.

## Synchronous Reset

A **synchronous reset** is a reset signal that operates only in synchronization with the clock. Even when the reset signal becomes active, the flip-flop does not immediately change its output. Instead, it waits for the specified active clock edge.

When the clock edge arrives, the flip-flop checks the reset condition. If reset is active, the stored output is forced to `0`; otherwise, the flip-flop captures the data input.

This makes synchronous reset completely dependent on the clock.

### Important Characteristics

- Reset is evaluated at the active clock edge.
- The output does not change immediately when reset becomes active.
- Reset behavior is synchronized with normal sequential operation.

> **A synchronous reset waits for the clock before changing the stored state.**

## Asynchronous Reset

An **asynchronous reset** operates independently of the clock. When the reset signal becomes active, the flip-flop can immediately force its output to `0`, without waiting for the next clock edge.

This provides a direct mechanism for placing a sequential circuit into a known state.

Because the reset does not depend on the clock, it can be used when a circuit needs to be initialized or forced into a safe state immediately.

### Important Characteristics

- Reset does not require a clock edge.
- The output can change as soon as the reset becomes active.
- It provides immediate control over the stored state.

> **An asynchronous reset does not wait for the clock; it can directly control the stored state.**

## Asynchronous Set

An **asynchronous set** is similar to an asynchronous reset, but instead of forcing the output to `0`, it forces the output to `1`.

When the Set signal becomes active, the flip-flop can immediately enter the logic-high state without waiting for a clock edge.

This is useful when a circuit needs to be placed directly into a known high state.

### Important Characteristics

- Set operates independently of the clock.
- The output can be forced to `1` immediately.
- No clock edge is required for the set operation.
- It provides direct control over the stored state.
- Standard-cell libraries may provide dedicated flip-flops with asynchronous set capability.

> **Asynchronous Set directly forces the stored output to logic `1`.**

# Exploring Flip-Flop Set & Reset Behavior

The following experiments helped me understand how **synchronous reset, asynchronous reset, and asynchronous set** behave at both the simulation and synthesized-hardware levels.

---

## Synchronous Reset — Simulation

The first waveform demonstrates the behavior of a **synchronous reset**.

Here, the reset signal does not immediately change the output. The flip-flop waits for the active **clock edge** before applying the reset condition.

The waveform shows the relationship between:

- `clk` — clock signal
- `reset` — synchronous reset
- `d` — data input
- `q` — flip-flop output

The important observation is that the reset operation is **controlled by the clock**.

<img width="900" height="482" alt="sync_reset_show" src="https://github.com/user-attachments/assets/f960ead9-95a4-4153-bd52-0506af1b68b7" />

> **Synchronous Reset → Reset is applied at the clock edge.**

<img width="900" height="482" alt="syncres_reset_waveform" src="https://github.com/user-attachments/assets/b9ed5add-3a51-4c77-b29e-57b474fb9ce0" />

---

## Asynchronous Reset — Simulation

The second waveform demonstrates an **asynchronous reset**.

The first diagram represents the synthesized implementation of an **asynchronous-reset D flip-flop**.

The main storage element is:

`sky130_fd_sc_hd__dfrtp_1`

This is a SKY130 flip-flop cell that provides a dedicated **reset capability**.

The important signals are:

- `clk` → Clock input
- `d` → Data input
- `async_reset` → Asynchronous reset control
- `q` → Stored output

The `async_reset` signal first passes through the inverter cell:

`sky130_fd_sc_hd__clkinv_1`

The resulting signal is then connected to the **RESET_B** input of the flip-flop.

The `_B` in `RESET_B` indicates that this control is **active-low**.

So, the synthesized structure shows that the asynchronous reset is implemented using a **dedicated reset pin of the flip-flop**, rather than being inserted into the normal data path.

The waveform helps visualize that the reset operation is **independent of the clock**.

<img width="900" height="482" alt="asyncres_show" src="https://github.com/user-attachments/assets/ea380cbd-5dee-4997-9640-fd9b10b12145" />

> **Asynchronous Reset → Reset can act immediately.**

<img width="900" height="482" alt="asyncres_waveform" src="https://github.com/user-attachments/assets/7e66ec8e-e56c-4ba0-b09f-6b8f5626e8da" />

---

## Asynchronous Set — Simulation

The third waveform demonstrates an **asynchronous set**.

When the set signal becomes active, the flip-flop output is forced to logic `1` independently of the clock.
The main sequential cell is:

sky130_fd_sc_hd__dfstp_2

This cell contains a dedicated SET_B control input.

The important signals shown are:

clk → Clock
d → Data
q → Feedback/output
async_set → Asynchronous set control

The async_set signal is processed through additional logic before reaching the flip-flop's set control.

The diagram also shows the feedback connection involving q, which is used by the synthesized logic to implement the required behavior.

The SET_B input provides direct control over the state of the flip-flop.

This experiment shows the opposite action of asynchronous reset:

```text
Asynchronous Reset → Q = 0
Asynchronous Set   → Q = 1
```
<img width="900" height="482" alt="async_set_show" src="https://github.com/user-attachments/assets/77af49fd-d04a-4749-ad92-e4764b4e278b" />

> **Asynchronous Set → The output can be forced high without waiting for the clock.**

<img width="900" height="482" alt="async_set_waveform" src="https://github.com/user-attachments/assets/47b53b9b-e9cd-46ad-89c7-1fbbdb734ef6" />

# Differences Between Synchronous Reset, Asynchronous Reset & Asynchronous Set

| Feature | Synchronous Reset | Asynchronous Reset | Asynchronous Set |
|---|---|---|---|
| **Control Signal** | `sync_reset` | `async_reset` | `async_set` |
| **Effect on Q** | Forces `Q = 0` | Forces `Q = 0` | Forces `Q = 1` |
| **Clock Required?** | ✅ Yes | ❌ No | ❌ No |
| **When does it act?** | At the active clock edge | Immediately when reset is active | Immediately when set is active |
| **Control Path** | Through combinational logic to `D` | Dedicated reset path | Dedicated set path |
| **Dedicated Control Pin** | ❌ No | ✅ `RESET_B` | ✅ `SET_B` |
| **Data Path Affected?** | ✅ Yes | ❌ No | ❌ No |
| **Purpose** | Clock-controlled reset | Immediate reset | Immediate initialization to `1` |
| **Behavior** | Clock-dependent | Clock-independent | Clock-independent |

## Key Learnings

- Understood **Sync & Async Set/Reset**.
- Learned the importance of **clock and timing**.
- Verified flip-flop behavior using **GTKWave**.
- Explored **Yosys → SKY130 cell mapping**.

---
<div align="center">

### ⭐ Day 05 Complete

> **"From storing a bit to understanding the hardware behind it."** 

</div>
