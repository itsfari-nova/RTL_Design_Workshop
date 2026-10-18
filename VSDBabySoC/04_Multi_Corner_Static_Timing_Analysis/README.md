# Multi-Corner Static Timing Analysis(STA)

### Setup-timing sign-off study of the synthesized `vsdbabysoc` netlist across all 16 SKY130 HD PVT corners

![Technology](https://img.shields.io/badge/Technology-SKY130%20HD-blue)
![Tool](https://img.shields.io/badge/STA%20Engine-OpenSTA-orange)
![Corners](https://img.shields.io/badge/PVT%20Corners-16-green)
![Clock](https://img.shields.io/badge/Clock-11%20ns-informational)
![Result](https://img.shields.io/badge/Setup-8%20Pass%20%7C%208%20Fail-red)

---

## Table of Contents

1. [Project Summary](#1--project-summary)
2. [Headline Results](#2--headline-results)
3. [Inputs & Environment](#3--inputs--environment)
4. [Understanding PVT Corners](#4--understanding-pvt-corners)
5. [The 16 Corners Analysed](#5--the-16-corners-analysed)
6. [Analysis Flow](#6--analysis-flow)
7. [Results — Worst Setup Path per Corner](#7--results--worst-setup-path-per-corner)
8. [WNS & TNS Analysis](#8--wns--tns-analysis)
9. [Key Findings](#9--key-findings)
10. [Per-Corner Terminal Evidence](#10--per-corner-terminal-evidence)
11. [Scope, Assumptions & Limitations](#11--scope-assumptions--limitations)
12. [How to Reproduce](#12--how-to-reproduce)
13. [Conclusion](#13--conclusion)
14. [Tools & Technologies](#14--tools--technologies)

---

## 1. Project Summary

This repository documents a **multi-corner Static Timing Analysis (STA)** of the synthesized **VSDBabySoC** design. The gate-level netlist was timed with **OpenSTA** against every characterized **sky130_fd_sc_hd** Liberty file (16 process-voltage-temperature combinations), using a single **11 ns** clock constraint.

For each corner the worst setup (max-delay) path was captured, and the results were consolidated in an Excel workbook that computes **WNS**, **TNS** and a pass/fail status, and plots both metrics across the corner set.

**Goal:** discover *where* the design stops meeting its clock, *why*, and *by how much* — before any physical-design effort is spent.

### Project at a glance

| Parameter | Detail |
|---|---|
| Design | VSDBabySoC |
| Top module | `vsdbabysoc` |
| Netlist | `vsdbabysoc.synth.v` (post-synthesis) |
| Constraints | `vsdbabysoc_synthesis.sdc` — clock `clk`, period **11.00 ns** |
| STA tool | OpenSTA (`sta`, driven by `run.tcl`) |
| Std-cell library | `sky130_fd_sc_hd` — 16 Liberty corners |
| Analog macros | `avsdpll.lib`, `avsddac.lib` (same in every run) |
| Check performed | Setup / max-delay (worst path per corner) |
| Metrics | Slack, WNS, TNS, approx. minimum period |

---

## 2. Headline Results

| Metric | Value |
|---|---|
| Corners analysed | **16** |
| Corners meeting setup | **8** (all 6 FF + both TT) |
| Corners violating setup | **8** (all SS) |
| Best slack | **+5.59 ns** @ `ff_n40C_1v95` |
| Worst slack / WNS | **−62.56 ns** @ `ss_n40C_1v28` |
| Worst TNS | **−13,957.59 ns** @ `ss_n40C_1v28` *(see [note](#11--scope-assumptions--limitations))* |
| Mildest failing corner | `ss_n40C_1v76` — −4.38 ns (needs ≈ 15.4 ns period) |
| Nominal corner `tt_025C_1v80` | **+1.94 ns** — MET |

> **Bottom line:** the design closes at 11 ns under fast and typical silicon, but fails every slow-silicon corner. The failure is driven by one long **ripple-carry adder chain** whose delay grows sharply as supply voltage drops.

---

## 3. Inputs & Environment

| Item | Source |
|---|---|
| Synthesized netlist | `vsdbabysoc.synth.v` |
| SDC constraints | `sdc/vsdbabysoc_synthesis.sdc` |
| PLL timing model | `avsdpll.lib` |
| DAC timing model | `avsddac.lib` |
| SKY130 HD timing libraries | [`efabless/skywater-pdk-libs-sky130_fd_sc_hd`](https://github.com/efabless/skywater-pdk-libs-sky130_fd_sc_hd) → `timing/` |
| Result workbook | `sky130_WNS_TNS_analysis.xlsx` |

---

## 4. Understanding PVT Corners

A **corner** is one specific combination of **P**rocess, **V**oltage and **T**emperature at which the standard cells were characterized. Since silicon must work across manufacturing spread and operating conditions, timing is verified at the extremes instead of only at the average.

Library naming convention: `sky130_fd_sc_hd__<process>_<temp>_<voltage>.lib`
→ e.g. `ss_n40C_1v28` = slow-slow process, −40 °C, 1.28 V.

| Field | Values | What it means |
|---|---|---|
| **Process** | `tt` | Typical NMOS/PMOS — nominal reference behaviour |
| | `ss` | Slow-slow — highest Vth, lowest drive current → **longest delays** (setup-critical) |
| | `ff` | Fast-fast — lowest Vth, highest drive → **shortest delays** (hold-critical, peak power) |
| **Temperature** | `n40C`, `025C`, `100C` | −40 °C, 25 °C, 100 °C (`n` = negative) |
| **Voltage** | `1v28` … `1v95` | Supply in volts; `1v80` is the nominal core supply |

### Temperature inversion — visible in this data

At low supply voltage, gate overdrive (VDD − Vth) is small and Vth rises when cold, so **cold silicon can be slower than hot silicon**. The results confirm this:

| Voltage | Hot (100 °C) slack | Cold (−40 °C) slack |
|---|---|---|
| 1.40 V (SS) | −17.12 ns | **−29.69 ns** (worse) |
| 1.60 V (SS) | −7.87 ns | **−10.61 ns** (worse) |

At 1.95 V (FF) hot and cold converge: path arrival is 5.39 ns vs 5.37 ns.

---

## 5. 🗂️ The 16 Corners Analysed

| # | Corner | Process | Temp | VDD (V) | vs 1.80 V | Role |
|--:|---|---|--:|--:|--:|---|
| 1 | `ff_100C_1v65` | Fast-Fast | 100 °C | 1.65 | −8.3 % | Hold / min-delay, power |
| 2 | `ff_100C_1v95` | Fast-Fast | 100 °C | 1.95 | +8.3 % | Hold / min-delay, power |
| 3 | `ff_n40C_1v56` | Fast-Fast | −40 °C | 1.56 | −13.3 % | Hold / min-delay, power |
| 4 | `ff_n40C_1v65` | Fast-Fast | −40 °C | 1.65 | −8.3 % | Hold / min-delay, power |
| 5 | `ff_n40C_1v76` | Fast-Fast | −40 °C | 1.76 | −2.2 % | Hold / min-delay, power |
| 6 | `ff_n40C_1v95` | Fast-Fast | −40 °C | 1.95 | +8.3 % | Hold / min-delay, power |
| 7 | `ss_100C_1v40` | Slow-Slow | 100 °C | 1.40 | −22.2 % | Setup / max-delay |
| 8 | `ss_100C_1v60` | Slow-Slow | 100 °C | 1.60 | −11.1 % | Setup / max-delay |
| 9 | `ss_n40C_1v28` | Slow-Slow | −40 °C | 1.28 | −28.9 % | Setup / max-delay |
| 10 | `ss_n40C_1v35` | Slow-Slow | −40 °C | 1.35 | −25.0 % | Setup / max-delay |
| 11 | `ss_n40C_1v40` | Slow-Slow | −40 °C | 1.40 | −22.2 % | Setup / max-delay |
| 12 | `ss_n40C_1v44` | Slow-Slow | −40 °C | 1.44 | −20.0 % | Setup / max-delay |
| 13 | `ss_n40C_1v60` | Slow-Slow | −40 °C | 1.60 | −11.1 % | Setup / max-delay |
| 14 | `ss_n40C_1v76` | Slow-Slow | −40 °C | 1.76 | −2.2 % | Setup / max-delay |
| 15 | `tt_025C_1v80` | Typical | 25 °C | 1.80 | 0 % | Nominal reference |
| 16 | `tt_100C_1v80` | Typical | 100 °C | 1.80 | 0 % | Nominal reference |

> 💡A conventional 1.8 V design is normally signed off at roughly **1.62 – 1.98 V**. The 1.28 – 1.44 V corners probe extreme low-power / brown-out behaviour and are informative, but sit far outside a typical supply specification.

---

## 6. Analysis Flow

The **same `run.tcl`** was executed for every corner. Only the standard-cell Liberty file was swapped; the PLL and DAC macro libraries stayed constant.

<div align="center">

![run.tcl used for the tt_025C_1v80 run](images/01_run_tcl_setup.jpg)

*`run.tcl` as used for the `tt_025C_1v80` run*

</div>

| Step | Command | Purpose |
|---|---|---|
| 1 | `read_liberty -min / -max` | Load timing libraries (std-cells, PLL, DAC) |
| 2 | `read_verilog` + `link_design vsdbabysoc` | Read the gate-level netlist and bind cells to the library |
| 3 | `read_sdc` | Apply clock and I/O constraints (11 ns period) |
| 4 | `report_checks -fields [list … capacitance slew fanout]` | Print the worst path with Fanout, Cap, Slew, Delay, Time |
| 5 | `report_worst_slack`, `report_tns` | Confirm WNS / TNS (shown for the first corner) |
| 6 | Excel workbook | Collate results, compute WNS / TNS / status, draw charts |

---

## 7. Results — Worst Setup Path per Corner

| # | Corner | Endpoint register¹ | Arrival (ns) | Lib setup (ns) | **Slack (ns)** | Status | Approx. min period² (ns) |
|--:|---|---|--:|--:|--:|:--:|--:|
| 1 | `ff_100C_1v65` | `Xreg_value_a4_reg[16][30]` | 6.88 | 0.07 | **+4.05** | ✅ MET | 6.95 |
| 2 | `ff_100C_1v95` | `Xreg_value_a4_reg[16][30]` | 5.39 | 0.04 | **+5.57** | ✅ MET | 5.43 |
| 3 | `ff_n40C_1v56` | `Xreg_value_a4_reg[16][31]` | 8.64 | 0.08 | **+2.28** | ✅ MET | 8.72 |
| 4 | `ff_n40C_1v65` | `Xreg_value_a4_reg[16][31]` | 7.52 | 0.07 | **+3.41** | ✅ MET | 7.59 |
| 5 | `ff_n40C_1v76` | `Xreg_value_a4_reg[1][31]` | 6.52 | 0.05 | **+4.43** | ✅ MET | 6.57 |
| 6 | `ff_n40C_1v95` | `Xreg_value_a4_reg[16][31]` | 5.37 | 0.04 | **+5.59** | ✅ MET | 5.41 |
| 7 | `ss_100C_1v40` | `Xreg_value_a4_reg[27][30]` | 27.54 | 0.58 | **−17.12** | ❌ VIOLATED | 28.12 |
| 8 | `ss_100C_1v60` | `Xreg_value_a4_reg[27][30]` | 18.51 | 0.36 | **−7.87** | ❌ VIOLATED | 18.87 |
| 9 | `ss_n40C_1v28` | `Xreg_value_a4_reg[27][30]` | 71.70 | 1.86 | **−62.56** | ❌ VIOLATED | 73.56 |
| 10 | `ss_n40C_1v35` | `Xreg_value_a4_reg[27][30]` | 49.47 | 1.23 | **−39.70** | ❌ VIOLATED | 50.70 |
| 11 | `ss_n40C_1v40` | `Xreg_value_a4_reg[27][30]` | 39.74 | 0.96 | **−29.69** | ❌ VIOLATED | 40.70 |
| 12 | `ss_n40C_1v44` | `Xreg_value_a4_reg[27][30]` | 34.10 | 0.80 | **−23.91** | ❌ VIOLATED | 34.90 |
| 13 | `ss_n40C_1v60` | `Xreg_value_a4_reg[27][30]` | 21.15 | 0.46 | **−10.61** | ❌ VIOLATED | 21.61 |
| 14 | `ss_n40C_1v76` | `Xreg_value_a4_reg[27][30]` | 15.07 | 0.31 | **−4.38** | ❌ VIOLATED | 15.38 |
| 15 | `tt_025C_1v80` | `Xreg_value_a4_reg[27][30]` | 8.90 | 0.16 | **+1.94** | ✅ MET | 9.06 |
| 16 | `tt_100C_1v80` | `Xreg_value_a4_reg[27][30]` | 8.81 | 0.10 | **+2.09** | ✅ MET | 8.91 |

<sub>¹ Endpoints are prefixed `core/CPU_` in the reports. &nbsp; ² Approx. min period = data arrival + library setup of the reported path only, with an ideal clock — a quick indicator, **not** a full f<sub>max</sub> analysis.</sub>

---

## 8. WNS & TNS Analysis

| Term | Definition used here |
|---|---|
| **Setup slack** | Required time − arrival time. Positive = met, negative = violated |
| **WNS** | Worst negative slack at a corner; plotted as `MIN(0, worst slack)` so passing corners show 0 |
| **TNS** | Sum of negative slack over all failing endpoints at a corner; 0 when nothing fails |

### Consolidated WNS / TNS

| Corner | VDD (V) | Temp | WNS (ns) | TNS (ns) | Failing endpoints (≥)³ |
|---|--:|--:|--:|--:|--:|
| `ss_100C_1v40` | 1.40 | 100 °C | −17.12 | −2,599.51 | 152 |
| `ss_100C_1v60` | 1.60 | 100 °C | −7.87 | −892.70 | 114 |
| `ss_n40C_1v28` | 1.28 | −40 °C | **−62.56** | **−13,957.59** | **224** |
| `ss_n40C_1v35` | 1.35 | −40 °C | −39.70 | −7,993.79 | 202 |
| `ss_n40C_1v40` | 1.40 | −40 °C | −29.69 | −5,466.48 | 185 |
| `ss_n40C_1v44` | 1.44 | −40 °C | −23.91 | −4,107.19 | 172 |
| `ss_n40C_1v60` | 1.60 | −40 °C | −10.61 | −1,355.80 | 128 |
| `ss_n40C_1v76` | 1.76 | −40 °C | −4.38 | −367.93 | 85 |
| All 6 FF + 2 TT corners | — | — | 0.00 | 0.00 | 0 |

<sub>³ Lower bound = ⌈ \|TNS\| / \|WNS\| ⌉, because no endpoint can be worse than the WNS.</sub>

### Excel workbook — `sky130_WNS_TNS_analysis.xlsx`

Sheets: `Read_Me` · `WNS_TNS_Table` · `WNS_Plot` · `TNS_Plot` · `Graph_Data` · `Graphs`

**Sheet `WNS_TNS_Table`** — one row per corner, formulas for WNS / TNS / Status and a summary block (16 analysed, 8 MET, 8 VIOLATED):

![WNS_TNS_Table sheet](images/02_wns_tns_table.jpg)

**Sheet `Graph_Data`** — chart source, every cell linked back to the table so edits propagate automatically:

![Graph_Data sheet](images/03_graph_data.jpg)

**Sheet `Graphs`** — TNS (left) and WNS (right) across the 16 corners:

![TNS and WNS charts](images/04_wns_tns_charts.jpg)

> Both curves share the same shape. WNS is a single path (−62.56 ns) whereas TNS accumulates every failing endpoint (−13,957.59 ns), so it is orders of magnitude larger.

---

## 9. Key Findings

1. **Clean pass/fail split.** All 6 FF and both TT corners meet 11 ns (slack **+1.94 → +5.59 ns**); all 8 SS corners violate it (**−4.38 → −62.56 ns**).
2. **Voltage dominates.** Along the `ss_n40C` series slack climbs steadily with supply: −62.56 (1.28 V) → −39.70 → −29.69 → −23.91 → −10.61 → −4.38 ns (1.76 V). The `ff_n40C` series improves from +2.28 ns (1.56 V) to +5.59 ns (1.95 V).
3. **Large process spread.** The reported path takes 5.37 ns (`ff_n40C_1v95`), 8.90 ns (`tt_025C_1v80`) and 71.70 ns (`ss_n40C_1v28`) — the slowest corner is ≈ 8× typical and ≈ 13× the fastest. *(Endpoints differ slightly between corners, so treat ratios as indicative.)*
4. **Temperature inversion confirmed.** Cold is slower at low VDD; the hot/cold gap shrinks near 1.95 V and is only ≈ 0.09 ns at TT.
5. **Setup time scales too.** Capture-flop setup grows from 0.04 ns (fastest) to 1.86 ns (`ss_n40C_1v28`), so part of the loss comes from the flop itself, not just the combinational chain.
6. **One structural culprit.** Across corners the critical path is the **ripple-carry chain of full adders** (`sky130_fd_sc_hd__fa_1` COUT cells) launched from `CPU_is_addi_a3_reg`. The corner simply rescales that chain's delay. The clock is ideal (0.00 ns) in every run.
7. **Practical reading.** To close even the mildest SS corner (`ss_n40C_1v76`) this path needs ≈ **15.4 ns**; options are relaxing the clock or restructuring the adder / ALU path. Whether the very-low-voltage corners must be met depends on the supply range the design is specified for.

---

## 10. Per-Corner Terminal Evidence

Each screenshot shows the tail of the OpenSTA `report_checks` output — data arrival, required time and slack. Expand a group to view them.

<details>
<summary><b>🟢 Fast-Fast corners (6) — all MET</b></summary>

#### 1 · `ff_100C_1v65` — +4.05 ns (MET)
Fast silicon, 100 °C, supply 8.3 % below nominal. Fast but hot and slightly under-volted.
![ff_100C_1v65](images/corner_01_ff_100C_1v65.jpg)

#### 2 · `ff_100C_1v95` — +5.57 ns (MET)
Fast silicon, 100 °C, supply 8.3 % above nominal. Shortest gate delays with peak power and leakage — the classic hold, IR-drop and electromigration stress point.
![ff_100C_1v95](images/corner_02_ff_100C_1v95.jpg)

#### 3 · `ff_n40C_1v56` — +2.28 ns (MET)
Fast silicon, cold, 13 % under-voltage. Slowest of the FF set (8.64 ns) because of temperature inversion.
![ff_n40C_1v56](images/corner_03_ff_n40C_1v56.jpg)

#### 4 · `ff_n40C_1v65` — +3.41 ns (MET)
Fast silicon, cold, 8.3 % under-voltage. Shows how quickly delay recovers as VDD rises.
![ff_n40C_1v65](images/corner_04_ff_n40C_1v65.jpg)

#### 5 · `ff_n40C_1v76` — +4.43 ns (MET)
Fast silicon, cold, nearly nominal supply — realistic for a tightly regulated 1.8 V rail.
![ff_n40C_1v76](images/corner_05_ff_n40C_1v76.jpg)

#### 6 · `ff_n40C_1v95` — +5.59 ns (MET) 🏅 best slack
Fast silicon, cold, 8.3 % over-voltage. Shortest path in the sweep (5.37 ns).
![ff_n40C_1v95](images/corner_06_ff_n40C_1v95.jpg)

</details>

<details>
<summary><b>🔴 Slow-Slow corners (8) — all VIOLATED</b></summary>

#### 7 · `ss_100C_1v40` — −17.12 ns (VIOLATED)
Slow silicon, hot, 22 % under-voltage — a deep low-power operating point.
![ss_100C_1v40](images/corner_07_ss_100C_1v40.jpg)

#### 8 · `ss_100C_1v60` — −7.87 ns (VIOLATED)
Slow silicon, hot, 11 % under-voltage — the textbook worst-case-hot setup corner for a 1.8 V rail with a −10 % tolerance.
![ss_100C_1v60](images/corner_08_ss_100C_1v60.jpg)

#### 9 · `ss_n40C_1v28` — −62.56 ns (VIOLATED) ⚠️ worst corner
Slow silicon, cold, 29 % under-voltage. Near-threshold behaviour, 71.70 ns arrival, well outside a normal ±10 % supply range.
![ss_n40C_1v28](images/corner_09_ss_n40C_1v28.jpg)

#### 10 · `ss_n40C_1v35` — −39.70 ns (VIOLATED)
Slow silicon, cold, 25 % under-voltage. Small gate overdrive plus raised Vth at cold keeps delay extreme.
![ss_n40C_1v35](images/corner_10_ss_n40C_1v35.jpg)

#### 11 · `ss_n40C_1v40` — −29.69 ns (VIOLATED)
Same voltage as corner 7 but cold — a clear demonstration of temperature inversion (−29.69 vs −17.12 ns).
![ss_n40C_1v40](images/corner_11_ss_n40C_1v40.jpg)

#### 12 · `ss_n40C_1v44` — −23.91 ns (VIOLATED)
Slow silicon, cold, 20 % under-voltage; delays remain far above the TT / FF runs.
![ss_n40C_1v44](images/corner_12_ss_n40C_1v44.jpg)

#### 13 · `ss_n40C_1v60` — −10.61 ns (VIOLATED)
Same voltage as corner 8 but cold; slightly slower than the hot case (−10.61 vs −7.87 ns). A realistic slow-cold setup corner.
![ss_n40C_1v60](images/corner_13_ss_n40C_1v60.jpg)

#### 14 · `ss_n40C_1v76` — −4.38 ns (VIOLATED)
Mildest SS corner: slow and cold but almost nominal voltage. Still fails 11 ns by 4.38 ns.
![ss_n40C_1v76](images/corner_14_ss_n40C_1v76.jpg)

</details>

<details>
<summary><b>🔵 Typical-Typical corners (2) — both MET</b></summary>

#### 15 · `tt_025C_1v80` — +1.94 ns (MET)
Typical process, 25 °C, nominal 1.80 V — the reference corner used in `run.tcl`.
![tt_025C_1v80](images/corner_15_tt_025C_1v80.jpg)

#### 16 · `tt_100C_1v80` — +2.09 ns (MET)
Typical process, 100 °C, nominal supply. Marginally faster than at 25 °C (8.81 vs 8.90 ns): temperature dependence is weak at nominal VDD.
![tt_100C_1v80](images/corner_16_tt_100C_1v80.jpg)

</details>

---

## 11. Scope, Assumptions & Limitations

| Topic | Note |
|---|---|
| **Setup only** | `report_checks` defaults to the max (setup) path. **Hold was not analysed** in these runs; add `report_checks -path_delay min` to cover it (most relevant at FF corners). |
| **Single library for min and max** | Each run loads the same corner for `-min` and `-max`. A full sign-off run would pair an FF library (min) with an SS library (max) in one session. |
| **Ideal clock** | Clock network delay and reconvergence pessimism are 0.00 ns because no clock tree exists at synthesis. Post-CTS results will differ. |
| **TNS values need confirmation** | TNS = 0 for FF/TT follows directly from the screenshots. The eight SS TNS values in the workbook were entered from a reference sheet, not read from the screenshots — confirm each with `report_tns -max` before final submission. The failing-endpoint counts in §8 depend on them. |
| **Tcl warning 168** | `unknown field delay_time` is harmless — `delay` and `time` columns print by default. Remove them from the `-fields` list to silence it. |
| **Screenshot labelling** | Three screenshots were named by corner and the rest numerically; they were mapped to corners by library run order, which is consistent with the data (slack rises monotonically with VDD in each series). |
| **Scrolled captures** | Screenshots show the tail of each path report, so the earliest cells are cut off; arrival, required time and slack are all visible. |

---

## 12. How to Reproduce

```tcl
# run.tcl — swap only the first two lines for each corner
read_liberty -min /path/to/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max /path/to/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_liberty -min /path/to/lib/avsdpll.lib
read_liberty -max /path/to/lib/avsdpll.lib
read_liberty -min /path/to/lib/avsddac.lib
read_liberty -max /path/to/lib/avsddac.lib

read_verilog /path/to/vsdbabysoc.synth.v
link_design vsdbabysoc
read_sdc /path/to/sdc/vsdbabysoc_synthesis.sdc

report_checks -fields {capacitance slew fanout}
report_worst_slack -max
report_tns
```

```bash
sta -no_splash run.tcl
```

Repeat for each of the 16 `.lib` files, record the slack, arrival and setup values, and update the workbook.

---

## 13. Conclusion

Multi-corner STA of the synthesized VSDBabySoC netlist shows that the design **meets its 11 ns clock at every fast and typical corner** (8 of 16) and **fails at every slow corner** (8 of 16). The worst case is `ss_n40C_1v28` with **WNS −62.56 ns**, and slack improves steadily as supply rises, reaching −4.38 ns at `ss_n40C_1v76`.

The limiting structure is the same everywhere — a ripple-carry adder chain feeding the register file — so closing timing at slow corners means either **relaxing the clock** (≈ 15.4 ns for the mildest SS corner) or **restructuring the adder / ALU path** (carry-lookahead, carry-select or pipelining). Hold analysis, a proper paired min/max run, and post-CTS timing remain as the next steps.

---

## 14. Tools & Technologies

- **OpenSTA** — static timing engine
- **SkyWater SKY130 `sky130_fd_sc_hd`** — 16 Liberty timing corners
- **Synthesized VSDBabySoC netlist** and **SDC constraints**
- **`avsdpll.lib` / `avsddac.lib`** — analog macro timing models
- **Tcl** — `run.tcl` run script
- **Microsoft Excel** — WNS / TNS computation and plotting

---

<div align="center">

*Prepared by **Shaik Farida** — VSD Multi-Corner STA Study*

</div>

