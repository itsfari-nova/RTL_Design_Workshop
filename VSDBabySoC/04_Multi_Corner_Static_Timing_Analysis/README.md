# Multi-Corner Static Timing Analysis (STA)

### Comprehensive Setup and Hold Timing Evaluation Across 18 SKY130 HD PVT Corners

<p align="center">
  <img src="https://img.shields.io/badge/Technology-SKY130%20HD-blue?style=for-the-badge" alt="SKY130 HD">
  <img src="https://img.shields.io/badge/Analysis-OpenSTA-orange?style=for-the-badge" alt="OpenSTA">
  <img src="https://img.shields.io/badge/PVT%20Corners-18-green?style=for-the-badge" alt="18 PVT corners">
  <img src="https://img.shields.io/badge/Timing-Setup%20%26%20Hold-informational?style=for-the-badge" alt="Setup and hold analysis">
</p>

## Table of Contents

- [Project Overview](#-project-overview)
- [Introduction to STA](#-introduction-to-static-timing-analysis)
- [Design Files and Libraries](#-design-files-and-timing-libraries)
- [Multi-Corner PVT Configuration](#-multi-corner-pvt-configuration)
- [Timing Results](#-multi-corner-timing-results)
- [Analysis Methodology](#-sta-execution-methodology)
- [Setup and Hold Evaluation](#-setup-and-hold-timing-evaluation)
- [WNS and TNS Analysis](#-wns-and-tns-analysis)
- [Timing Visualization](#-timing-visualization)
- [Screenshots and Evidence](#-screenshots-and-evidence)
- [Key Findings](#-key-findings)
- [Conclusion](#-conclusion)
- [Tools and Technologies](#-tools-and-technologies)

---

## Project Overview

This project focuses on performing **multi-corner Static Timing Analysis (STA)** on the synthesized VSDBabySoC design using OpenSTA and SKY130 HD standard-cell timing libraries.

The design is evaluated under **18 process, voltage, and temperature (PVT) timing conditions** to study the behavior of minimum-delay and maximum-delay paths. The analysis produces timing reports and comparison plots covering minimum slack, maximum slack, Worst Negative Slack (WNS), and Total Negative Slack (TNS).

By comparing results across operating corners, this work identifies conditions that satisfy timing requirements and highlights corners that require further setup-timing investigation.

### 📋 Project Details

| Parameter | Description |
|---|---|
| Project | VSDBabySoC Multi-Corner STA |
| Top-level module | `vsdbabysoc` |
| Assessment date | 19 September 2026 |
| Timing engine | OpenSTA |
| Standard-cell technology | SKY130 HD |
| Number of PVT corners | 18 |
| Analysis category | Static Timing Analysis |
| Timing checks | Setup and hold |
| Evaluation metrics | Minimum slack, maximum slack, WNS and TNS |

## Introduction to Static Timing Analysis

Static Timing Analysis is a fundamental digital-design verification technique that evaluates signal propagation delays and timing requirements without relying on functional input-vector simulation.

STA examines timing paths through sequential and combinational logic and checks whether data reaches its destination within the required timing window.

In this project, OpenSTA analyzes the synthesized VSDBabySoC netlist using the specified timing constraints and characterized SKY130 HD libraries. Multiple PVT corners are evaluated to understand how changes in process characteristics, supply voltage, and temperature influence circuit timing.

The resulting reports help identify timing-critical operating conditions and provide a basis for further investigation and optimization.

## 🗂️ Design Files and Timing Libraries

The following files and directories form part of the reported analysis setup.

| Component | File or directory |
|---|---|
| Synthesized netlist | `/home/vsduser/OpenSTA/BabySoC_Simulation/src/vsdbabysoc.synth.v` |
| SDC constraints | `/home/vsduser/Desktop/VSDBabySoC/src/sdc/vsdbabysoc_synthesis.sdc` |
| PLL Liberty library | `/home/vsduser/Desktop/VSDBabySoC/src/lib/avsdpll.lib` |
| DAC Liberty library | `/home/vsduser/Desktop/VSDBabySoC/src/lib/avsddac.lib` |
| SKY130 HD timing libraries | `/home/vsduser/Desktop/skywater-pdk-libs-sky130_fd_sc_hd/timing/` |

### 1. Synthesized Netlist

**File:** `vsdbabysoc.synth.v`

The synthesized netlist describes the mapped digital design and serves as the circuit representation analyzed by the STA tool.

### 2. Timing Constraints

**File:** `vsdbabysoc_synthesis.sdc`

The Synopsys Design Constraints file defines the timing requirements used during analysis, including applicable clock and path constraints.

### 3. PLL and DAC Liberty Models

**Files:** `avsdpll.lib` and `avsddac.lib`

These Liberty files provide the timing models associated with the PLL and DAC components included in the design setup.

### 4. SKY130 HD Standard-Cell Libraries

The SKY130 HD timing-library directory contains characterized cell timing data for supported operating conditions. The appropriate library models are used to evaluate circuit delays at the configured PVT corners.

## Multi-Corner PVT Configuration

A process-voltage-temperature corner describes a particular combination of manufacturing-process characteristics, supply voltage, and operating temperature.

The corner designations used in this analysis are:

- **FF — Fast-Fast:** Represents fast modeled transistor process behavior.
- **SS — Slow-Slow:** Represents slow modeled transistor process behavior.
- **TT — Typical-Typical:** Represents typical modeled transistor process behavior.

Supply voltage and temperature further influence the delay characteristics of standard cells and the overall timing behavior of a circuit.

### Complete List of 18 Timing Corners

| No. | Corner | Process | Temperature | Voltage |
|---:|---|---|---:|---:|
| 1 | `ff_100C_1v65` | FF | 100°C | 1.65 V |
| 2 | `ff_100C_1v95` | FF | 100°C | 1.95 V |
| 3 | `ff_n40C_1v56` | FF | -40°C | 1.56 V |
| 4 | `ff_n40C_1v65` | FF | -40°C | 1.65 V |
| 5 | `ff_n40C_1v76` | FF | -40°C | 1.76 V |
| 6 | `ff_n40C_1v95` | FF | -40°C | 1.95 V |
| 7 | `ff_n40C_1v95_ccsnoise` | FF + CCS noise | -40°C | 1.95 V |
| 8 | `ss_100C_1v40` | SS | 100°C | 1.40 V |
| 9 | `ss_100C_1v60` | SS | 100°C | 1.60 V |
| 10 | `ss_n40C_1v28` | SS | -40°C | 1.28 V |
| 11 | `ss_n40C_1v35` | SS | -40°C | 1.35 V |
| 12 | `ss_n40C_1v40` | SS | -40°C | 1.40 V |
| 13 | `ss_n40C_1v44` | SS | -40°C | 1.44 V |
| 14 | `ss_n40C_1v60` | SS | -40°C | 1.60 V |
| 15 | `ss_n40C_1v60_ccsnoise` | SS + CCS noise | -40°C | 1.60 V |
| 16 | `ss_n40C_1v76` | SS | -40°C | 1.76 V |
| 17 | `tt_025C_1v80` | TT | 25°C | 1.80 V |
| 18 | `tt_100C_1v80` | TT | 100°C | 1.80 V |

### Why Multi-Corner Analysis Matters

Timing performance is not identical under every operating condition. Evaluating several corners helps determine whether a design satisfies its timing requirements across the selected range of conditions.

In the reported results, all listed corners pass the hold checks. The listed FF and TT corners also pass setup checks, whereas the listed SS corners exhibit negative maximum slack and require further investigation.

## Multi-Corner Timing Results

The following table summarizes the minimum slack, maximum slack, and reported setup/hold status for each corner.

| Timing corner | Min slack (ns) | Max slack (ns) | Hold status | Setup status |
|---|---:|---:|---|---|
| `ff_100C_1v65` | 0.25 | 4.05 | MET | MET |
| `ff_100C_1v95` | 0.20 | 5.57 | MET | MET |
| `ff_n40C_1v56` | 0.29 | 2.28 | MET | MET |
| `ff_n40C_1v65` | 0.26 | 3.41 | MET | MET |
| `ff_n40C_1v76` | 0.22 | 4.43 | MET | MET |
| `ff_n40C_1v95` | 0.19 | 5.59 | MET | MET |
| `ff_n40C_1v95_ccsnoise` | 0.19 | 5.59 | MET | MET |
| `ss_100C_1v40` | 0.91 | -17.12 | MET | VIOLATED |
| `ss_100C_1v60` | 0.64 | -7.87 | MET | VIOLATED |
| `ss_n40C_1v28` | 1.83 | -62.56 | MET | VIOLATED |
| `ss_n40C_1v35` | 1.35 | -39.70 | MET | VIOLATED |
| `ss_n40C_1v40` | 1.12 | -29.69 | MET | VIOLATED |
| `ss_n40C_1v44` | 0.99 | -23.91 | MET | VIOLATED |
| `ss_n40C_1v60` | 0.66 | -10.61 | MET | VIOLATED |
| `ss_n40C_1v60_ccsnoise` | 0.66 | -10.61 | MET | VIOLATED |
| `ss_n40C_1v76` | 0.50 | -4.38 | MET | VIOLATED |
| `tt_025C_1v80` | 0.31 | 1.94 | MET | MET |
| `tt_100C_1v80` | 0.31 | 2.09 | MET | MET |

### Initial Interpretation

- All 18 listed corners have positive minimum slack and are reported as passing hold checks.
- The listed FF and TT corners have positive maximum slack and pass the reported setup checks.
- All listed SS corners have negative maximum slack and are reported as failing setup checks.
- The most negative maximum slack occurs at `ss_n40C_1v28`.

*Note: The values and status labels above reproduce the supplied report. Their interpretation depends on the constraints, path coverage, and timing-report definitions used in the analysis.*

## STA Execution Methodology

The multi-corner analysis follows the workflow described in the supplied report.

1. **Load timing libraries** — Read the relevant SKY130 HD Liberty files and the PLL/DAC timing models.
2. **Read the synthesized netlist** — Load the mapped VSDBabySoC circuit representation.
3. **Apply timing constraints** — Read the SDC file to establish the timing requirements.
4. **Run timing analysis** — Evaluate minimum-delay and maximum-delay paths for each configured corner.
5. **Generate reports** — Save corner-specific timing results for later inspection.
6. **Extract metrics** — Collect minimum slack, maximum slack, WNS, and TNS.
7. **Organize report outputs** — Archive generated timing reports for comparison.
8. **Visualize the results** — Generate plots to compare timing behavior across the 18 corners.

This process creates a consistent basis for examining timing differences across the selected operating conditions.

## Setup and Hold Timing Evaluation

### Hold Timing — Minimum-Delay Analysis

Hold analysis verifies that data does not arrive too early relative to the active clock edge. It evaluates minimum-delay paths and checks whether the required hold interval is maintained.

The reported minimum-slack values are positive for all 18 corners.

| Hold metric | Reported result |
|---|---|
| Lowest minimum slack | 0.19 ns |
| Corners with the lowest value | `ff_n40C_1v95`, `ff_n40C_1v95_ccsnoise` |
| Highest minimum slack | 1.83 ns |
| Corner with the highest value | `ss_n40C_1v28` |
| Reported hold status | MET at all listed corners |

These results indicate non-negative slack for the hold paths covered by the supplied reports.

### Setup Timing — Maximum-Delay Analysis

Setup analysis checks whether data reaches its destination early enough to satisfy the setup requirement before the relevant clock edge.

The reported maximum-slack values show a clear difference between the process-corner groups.

| Setup metric | Reported result |
|---|---|
| Most negative maximum slack | -62.56 ns |
| Critical corner | `ss_n40C_1v28` |
| Setup status at the critical corner | VIOLATED |
| Other affected corners | All other listed SS corners |
| Listed FF and TT corners | MET |

The negative maximum slack at the SS corners indicates that the reported setup requirements are not satisfied under those conditions. Critical-path reports and constraint checks are needed to investigate the cause and identify suitable optimization measures.

## WNS and TNS Analysis

### Worst Negative Slack (WNS)

WNS represents the worst slack among the relevant timing paths in an analysis. A negative WNS indicates that at least one path violates its timing requirement.

The report identifies the following result:

| Metric | Reported value |
|---|---|
| Worst setup slack | -62.56 ns |
| Critical PVT corner | `ss_n40C_1v28` |
| Timing result | Setup violation |

The WNS comparison plot highlights `ss_n40C_1v28` as the corner with the most severe reported setup result. This corner should be prioritized when investigating the timing paths and applied constraints.

### Total Negative Slack (TNS)

TNS aggregates negative slack across the violating paths included in the timing analysis. It helps indicate the overall extent of setup-timing violations rather than describing only the single worst path.

| Metric | Reported value |
|---|---|
| Most negative reported TNS | Approximately -13,555.8 ns |
| Critical PVT corner | `ss_n40C_1v28` |
| Interpretation | Significant aggregate setup-timing violations are reported |

The TNS plot shows its most negative value at `ss_n40C_1v28`. Further examination of the critical paths, timing constraints, and report coverage is necessary before deciding which design or constraint changes are appropriate.

## Timing Visualization

The generated plots provide a visual summary of the reported timing metrics across the configured PVT corners.

### WNS Across the 18 Corners

The WNS plot compares the worst setup slack for each operating condition. The lowest reported value is approximately **-62.6 ns** at `ss_n40C_1v28`.

### TNS Across the 18 Corners

The TNS plot compares the accumulated negative slack at each corner. The most negative reported value is approximately **-13,555.8 ns**, also at `ss_n40C_1v28`.

### WNS Compared with Minimum Hold Slack

The combined timing plot contrasts the setup results with minimum hold slack. The reported hold slack remains positive across all listed corners, while negative setup slack appears at the SS corners.

Together, these plots make it easier to identify timing-critical conditions and compare the reported behavior across the full corner set.

## Screenshots and Evidence

The following figures document the STA environment, timing constraints, library configuration, report-generation process, and extracted timing results.

### Figure 1 — OpenSTA Timing Path Report

Displays an example timing path with arrival time, required time, and slack information.

<img width="448" height="471" alt="OpenSTA timing path analysis" src="https://github.com/user-attachments/assets/9aab51f9-cf10-45a8-b3aa-fd61ba1e232b" />

### Figure 2 — SDC Timing Constraints

Shows the clock and timing constraints supplied to the VSDBabySoC analysis flow.

<img width="443" height="472" alt="SDC timing constraint file" src="https://github.com/user-attachments/assets/ffa73d90-c1d2-4ffc-b1c9-946a331ec709" />

### Figure 3 — Multi-Library OpenSTA Script

Illustrates the script used to configure timing libraries, load the synthesized netlist, and apply SDC constraints.

<img width="502" height="474" alt="OpenSTA multi-library script" src="https://github.com/user-attachments/assets/937fa352-8786-4207-92f9-41988af283bb" />

### Figure 4 — SKY130 HD Timing Library Directory

Shows the characterized standard-cell timing libraries used in the multi-corner setup.

<img width="715" height="474" alt="SKY130 HD timing library files" src="https://github.com/user-attachments/assets/63b85cbc-d32b-40c1-b68b-c1c6dad346af" />

### Figure 5 — Multi-Corner Report Generation

Documents the automated generation of timing reports for the configured corners.

<img width="780" height="438" alt="Multi-corner STA report generation" src="https://github.com/user-attachments/assets/53ff68f6-d4e0-4d40-8f23-29607628034f" />

### Figure 6 — Liberty Timing-Model Inspection

Shows inspection of Liberty library pin definitions and timing-model information.

<img width="780" height="440" alt="Liberty timing model inspection" src="https://github.com/user-attachments/assets/aea8d6d6-408d-4f5d-b31f-031785746616" />

### Figure 7 — Hold Timing Report

Displays a minimum-delay timing path with positive slack and a reported MET result.

<img width="780" height="438" alt="Hold timing analysis" src="https://github.com/user-attachments/assets/b66da847-b7e1-42f9-8772-6e85c429be93" />

### Figure 8 — Setup Timing Report

Shows a maximum-delay timing path used to evaluate setup timing and calculate slack.

<img width="780" height="439" alt="Setup timing analysis" src="https://github.com/user-attachments/assets/4eb70b0a-0b89-4a16-913c-891c36bc3954" />

### Figure 9 — Slack Extraction Across 18 Corners

Documents the extraction of corner-specific slack values from the generated reports.

<img width="780" height="439" alt="Slack extraction across timing corners" src="https://github.com/user-attachments/assets/b33257d2-db32-4d0d-8db4-1acfdf49ddcd" />

### Figure 10 — Report Archiving and Plot Generation

Shows the timing-report archive and generation of the associated timing plots.

<img width="780" height="439" alt="STA report archiving and plot generation" src="https://github.com/user-attachments/assets/7354fdf1-bd05-4ff1-991c-c34f06192f76" />

### Figure 11 — Automated WNS and TNS Extraction

Shows the process used to extract WNS and TNS values across the configured corners.

<img width="780" height="441" alt="Automated WNS and TNS extraction" src="https://github.com/user-attachments/assets/bf92b4d2-70bd-4a8e-90ef-022cc3346136" />

### Figure 12 — Extracted Timing Metrics

Displays extracted WNS and TNS values for comparison between the reported fast and slow corners.

<img width="567" height="472" alt="Extracted WNS and TNS values" src="https://github.com/user-attachments/assets/f9e9b2ee-3151-4c3a-b6cb-8a487e249be3" />

### Figure 13 — Completion of Multi-Corner Extraction

Documents the completion of timing-metric extraction for the 18-corner set.

<img width="780" height="430" alt="Completion of multi-corner extraction" src="https://github.com/user-attachments/assets/3d3af7fd-7fcf-4d10-8bf8-ea676873a78d" />

### Figure 14 — Timing Quality Plots

Shows the plots used to compare WNS, TNS, and minimum hold slack across operating conditions.

<img width="780" height="439" alt="Timing quality plots" src="https://github.com/user-attachments/assets/f5637ae0-e1ef-46ac-bc5c-186bf9520c52" />

### Figure 15 — Worst Setup Slack Across 18 Corners

Highlights the reported worst setup slack of approximately -62.6 ns at `ss_n40C_1v28`.

<img width="780" height="439" alt="Worst setup slack across 18 corners" src="https://github.com/user-attachments/assets/63f74498-f768-403b-a7a1-28ff9f546318" />

### Figure 16 — Total Negative Slack Across 18 Corners

Shows the reported TNS comparison, with the lowest value of approximately -13,555.8 ns at `ss_n40C_1v28`.

<img width="780" height="443" alt="Total negative slack across 18 corners" src="https://github.com/user-attachments/assets/e5873992-f00c-418e-a5aa-093ad4ca6412" />

### Figure 17 — WNS and Minimum Hold Slack Comparison

Compares the setup and hold metrics, showing positive reported minimum hold slack and negative setup slack at the affected SS corners.

<img width="780" height="439" alt="WNS and minimum hold slack comparison" src="https://github.com/user-attachments/assets/c09b6624-8ef4-4d77-9b19-26c2383cbdd6" />

## Key Findings

The reported multi-corner timing analysis leads to the following observations:

1. All 18 listed corners have positive minimum slack, and their reported hold status is MET.
2. The listed FF and TT corners show positive maximum slack and pass the reported setup checks.
3. Every listed SS corner exhibits negative maximum slack, indicating setup-timing violations.
4. The worst reported setup slack is **-62.56 ns** at `ss_n40C_1v28`.
5. The most negative reported TNS is approximately **-13,555.8 ns** at the same corner.
6. The timing plots clearly distinguish the reported setup behavior from the positive hold-slack results.
7. Multi-corner analysis identifies the operating conditions that need closer timing investigation.
8. The generated reports provide a foundation for reviewing critical paths, validating constraints, and planning timing optimization.

## Conclusion

The VSDBabySoC design was evaluated using OpenSTA and SKY130 HD timing libraries across **18 PVT corners**. The analysis covered minimum-delay and maximum-delay paths and produced corner-specific reports, extracted timing metrics, and comparison plots.

According to the supplied results, all listed hold checks pass, with minimum slack remaining positive across the evaluated corners. The setup results, however, reveal violations at all listed SS corners.

The most critical reported condition is `ss_n40C_1v28`, with a worst setup slack of **-62.56 ns** and a TNS of approximately **-13,555.8 ns**.

These findings demonstrate how multi-corner STA can expose timing weaknesses that may not be visible when examining only a single operating condition. The affected paths and constraints should be investigated further to identify the causes of the setup violations and determine suitable corrective actions.

**Final assessment:** The reported hold checks pass across the 18 listed corners, while setup timing requires further investigation at the affected SS corners.

## Tools and Technologies

- **OpenSTA** — Static timing analysis and timing-report generation.
- **SKY130 HD** — Standard-cell timing libraries.
- **VSDBabySoC** — Design under evaluation.
- **Verilog synthesized netlist** — Circuit representation used for timing analysis.
- **SDC** — Timing constraints.
- **Liberty files** — Characterized timing models for standard cells and design components.
- **Multi-corner run scripts** — Automated analysis across the configured PVT conditions.
- **Timing reports** — Corner-specific setup and hold results.
- **WNS/TNS extraction** — Quantitative comparison of timing performance.
- **Timing plots** — Visual analysis of results across operating corners.

---

<p align="center">
  <b>“Accurate timing analysis is a key step toward reliable digital design.”</b>
  <br>
  <i>VSDBabySoC | OpenSTA | SKY130 HD | Multi-Corner STA</i>
</p>
