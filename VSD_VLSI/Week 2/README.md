# WEEK 02 - VLSI Design Flow

### Sessions 02 & 03 | From Idea → Silicon 

This week was all about understanding **how a VLSI chip is born** — starting from a simple specification and ending as a real fabricated chip.

### The Journey of a Chip

```text
💡 Specification
       ↓
🏗️ Architecture
       ↓
💻 RTL Design
       ↓
🔬 Simulation
       ↓
⚙️ Synthesis
       ↓
🔗 Gate-Level Netlist
       ↓
📐 Physical Design
       ↓
✅ Verification
       ↓
🏭 Fabrication
       ↓
🧪 Testing
       ↓
💎 SILICON CHIP
```

## Pre-Synthesis Simulation

In Session 02, I worked on **pre-synthesis simulation** and analyzed the generated waveforms using **GTKWave**.

The RTL design was simulated before synthesis to verify its functional behavior.

---

## GTKWave Analysis

After running the pre-synthesis simulation, I opened the generated VCD file in **GTKWave**.

The waveform contains important signals such as:

- `CLK`
- `reset`
- `OUT`
- `RV_TO_DAC[9:0]`
- `RV_TO_DAC[9]` to `RV_TO_DAC[0]`

### Observed Waveform

The `CLK` signal continuously toggles and acts as the timing reference.

The `reset` signal initializes the design.

The `OUT` signal shows the resulting output waveform.

The `RV_TO_DAC[9:0]` signal represents a **10-bit digital signal** whose individual bits can also be observed separately.

---


<img width="900" height="582" alt="pre_synth_dataformat" src="https://github.com/user-attachments/assets/5b01ec21-84d3-473f-8a7b-da7cf987c2f5" />


<img width="900" height="582" alt="pre_synth_waveform1" src="https://github.com/user-attachments/assets/59743468-a72e-4045-9c62-08e07f9bb4b8" />

## What I Learned

- 🔹 Learned how to perform **pre-synthesis simulation**.
- 🔹 Learned to open and analyze **VCD waveforms using GTKWave**.
- 🔹 Learned how to add and observe **internal signals**.
- 🔹 Understood how a **10-bit bus** can be viewed as individual bits.
- 🔹 Learned to change the **data representation format** of signals.
- 🔹 Observed the relationship between **digital signal transitions and the output waveform**.
