# Day 02 • Loops in Verilog — `for` & Generate

> **“Write the pattern once in RTL — let the loop replicate the hardware.”**

Welcome to **Day 02 of Module 5**, where I explored **loops in Verilog** and how they help describe repetitive operations and repeated hardware structures efficiently.

In this experiment, I studied the **`for` loop** and understood how it can be used to repeat procedural statements for a defined number of iterations. I also explored the **generate loop**, which allows hardware structures to be replicated during elaboration.

The key difference between them is that a **`for` loop** is commonly used for procedural operations, while a **generate loop** is used to create repeated hardware structures. Understanding this distinction helps in writing **clean, scalable, and synthesizable RTL designs**.

---

## What I Explored

- [Introduction to Loops](#introduction-to-loops)
- [`for` Loop](#for-loop)
- [Generate Loop](#generate-loop)
- [`for` Loop vs Generate Loop](#for-loop-vs-generate-loop)
- [Loop-Based Hardware Replication](#loop-based-hardware-replication)
- [Key Takeaways](#key-takeaways)

---

## Introduction to Loops

In Verilog, loops are used to repeat a set of statements efficiently instead of writing the same code multiple times. They are especially useful when designing hardware structures that contain repeated logic or when processing multiple bits of a signal.

### for Loop

A for loop repeats a block of procedural statements a specific number of times. It is commonly used inside always blocks for repetitive operations such as bit-by-bit processing.

### Generate Loop

A generate loop is used to create multiple hardware instances or repeated structures during elaboration. Unlike a procedural for loop, a generate loop describes hardware that is replicated in the final design.

In short:
for loop → repeats procedural operations
generate loop → replicates hardware structures

---

## MUX using `for` loop — Latch Inference


<img width="900" height="582" alt="gvim_mux_generate" src="https://github.com/user-attachments/assets/525e6df4-306f-44d1-9a2e-33dae6b07367" />


Look closely at the `always` block — **`y` is only assigned when `k == sel`**. There's no `else` and no default value given to `y` before the loop. That means:

- For 3 out of 4 loop iterations, nothing happens to `y`.
- Synthesis tools read this as *"y must hold its previous value when no condition matches"* — which is the literal definition of a **latch**.

### Gtkwave Waveform Shows


<img width="900" height="582" alt="tb_mux_generate" src="https://github.com/user-attachments/assets/e108ee23-d404-4f33-843e-aa676153689e" />


In the GTKWave simulation, `y` stays stuck at `0` for the whole run, even as `sel`, `i0`–`i3` toggle. This is the simulation-side symptom of the same bug: since `y` is a `reg` and never gets a default assignment, in behavioral simulation it just doesn't update — it's inferring latch-like "hold" behavior even at the RTL simulation level.

### Synthesis (Yosys `show`) 

The gate-level schematic shows:


<img width="900" height="582" alt="mux_generate_show" src="https://github.com/user-attachments/assets/6919b69e-524f-44b4-bbcd-21bb8bc254c0" />


- Yosys correctly recognizes the intended structure as a **4:1 mux** (`sky130_fd_sc_hd__mux4_2`) selecting between `i0..i3` using `sel[0]` and `sel[1]`.
- But right after the mux output `X`, there's a **`$_DLATCH_N_`** cell (D-latch) with `E` tied to a constant `1'0` net — a dead giveaway that the synthesizer inferred a latch to "remember" `y` for the case where no `if` condition was true.

---

## DEMUX using `case` statement


<img width="900" height="582" alt="gvim_demux_case and generate" src="https://github.com/user-attachments/assets/2103b37a-a74e-4213-8f02-09ac74fc77aa" />


Unlike the earlier `mux_generate` bug, this time **`y_int` is pre-loaded with `8'b0` before the `case`**. So every output bit has a defined value on every path — no "remember the old value" condition exists → **no latch inferred**.

### Waveform Check


<img width="900" height="582" alt="tb_demux_case" src="https://github.com/user-attachments/assets/564114d2-1388-4779-8df1-b2b4fb753bda" />


`sel` walks through `000 → 001 → 010 → ... → 110`, and exactly one output (`o0` through `o6`) toggles at a time, following `i`. This is exactly demux behavior: the input `i` is routed to whichever output line `sel` selects, and every other output stays `0`.

### Synthesis (Yosys `show`)


<img width="900" height="582" alt="demux_case_show" src="https://github.com/user-attachments/assets/55b19d79-08cb-4fb3-a7a0-fa725b1e6b1b" />


The gate-level netlist confirms clean **combinational logic** — a bank of `and4_1` / `and4b_1` / `nor4b_1` / `nor4bb_1` gates, one per output bit, decoding `sel` and gating `i`. **No `$_DLATCH_N_` cell anywhere** — proof the default assignment did its job.

## DEMUX using `for` (generate-style) loop

```verilog
module demux_generate (output o0, output o1, output o2, output o3,
                        output o4, output o5, output o6, output o7,
                        input [2:0] sel, input i);
reg [7:0] y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
    y_int = 8'b0;               // default assignment again
    for(k = 0; k < 8; k++) begin
        if(k == sel)
            y_int[k] = i;
    end
end
endmodule
```

Same idea as `demux_case`, but the 8-way selection is written as a `for` loop instead of spelling out all 8 `case` branches. Because `y_int` is still defaulted to `0` up front, this is functionally and structurally identical to the `case` version.

### Waveform Check


<img width="900" height="582" alt="tb_demux_generate" src="https://github.com/user-attachments/assets/4dd9d2c6-bef1-4b58-a8a4-83af85aa9f78" />


Identical behavior to `demux_case` — `sel` sweeps `000` through `110`, and the corresponding output toggles with `i` while the rest stay low.

### Synthesis (Yosys `show`)


<img width="900" height="582" alt="demux_generate_show" src="https://github.com/user-attachments/assets/7173068b-af35-4800-82a6-eb42f089329b" />


The netlist is (as expected) essentially the same decoder structure as the `case` version — `and4_1`, `and4b_1`, `nor4b_1`, `nor4bb_1` gates feeding `o0`–`o7`, packed into `y_int`. **This is the key takeaway: `case` and `for-loop` styles synthesize to the same hardware when written correctly (full assignment on every path).** The only real difference is coding style/readability, not the resulting logic.

---

## Ripple Carry Adder (RCA) using `generate`

<img width="900" height="582" alt="gvim_rca_fa1" src="https://github.com/user-attachments/assets/ca71b5c0-3c9b-4c04-b4af-0df0929b95e8" />

This builds an 8-bit adder out of 8 single-bit full adders (`fa`), chained together:
- **`u_fa_0`** handles bit 0, with carry-in hardwired to `1'b0` (no carry coming in at the LSB).
- **`genvar i` loop** (`i = 1` to `7`) instantiates `u_fa_1` for bits 1–7, where each stage's carry-in (`c`) is the previous stage's carry-out (`int_co[i-1]`) — this chaining is what makes it a **ripple carry adder**: the carry "ripples" from LSB to MSB, one full-adder delay at a time.
- The final `sum[8]` is the carry-out of the last (MSB) full adder — i.e., the 9th bit needed to represent the full sum of two 8-bit numbers without overflow.

### Waveform Simulation

<img width="900" height="582" alt="tb_rca_waveform" src="https://github.com/user-attachments/assets/f029aa96-c3f6-44b0-85b9-f0d87e85966d" />

From the GTKWave trace:
- `num1 = 0xCB` (203)
- `num2 = 0x5C` (92)
- `sum_out = 0x127` (295)

Check: `203 + 92 = 295 = 0x127` — matches exactly, confirming the RCA is functionally correct.

**Note on `genvar`/`generate`:** unlike the earlier `for` loops (which ran inside `always` blocks to *describe behavior*), this `generate...for` loop runs at **elaboration time** — it literally stamps out 7 separate `fa` instances in hardware, not a single reused block. That's the core difference between a `for` loop for *behavioral logic* 

## Key Takeaways

- **Always default your outputs** in `always @(*)` blocks (e.g. `y = 1'b0;` before the logic). Miss this → Yosys infers an unwanted latch (`$_DLATCH_N_`), like in `mux_generate.v`.
- **`case` vs `for-loop`** styles synthesize to the *same* hardware if every path is covered — `demux_case.v` and `demux_generate.v` produced identical netlists.
- **`for` in `always`** = describes behavior (one shared block of logic). **`generate...for` with `genvar`** = physically stamps out multiple hardware instances — that's how the RCA chains 8 full adders together.
- **Always cross-check**: waveform (sim) + Yosys `show` (synthesis). Sim shows the symptom (stuck signal), the schematic shows the cause (a latch).
- **RCA correctness confirmed by math**: `0xCB + 0x5C = 0x127` matched the waveform.

---



