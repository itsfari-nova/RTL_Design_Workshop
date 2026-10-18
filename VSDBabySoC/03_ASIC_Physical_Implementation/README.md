# ASIC Physical Design Implementation and Multi-Corner Timing Analysis

### VSDBabySoC | SkyWater SKY130HD | OpenROAD Flow

<p align="center">
  <img src="https://img.shields.io/badge/Technology-SKY130HD-blue?style=for-the-badge" alt="SKY130HD">
  <img src="https://img.shields.io/badge/Physical%20Design-OpenROAD-orange?style=for-the-badge" alt="OpenROAD">
  <img src="https://img.shields.io/badge/PVT%20Analysis-16%20Corners-green?style=for-the-badge" alt="16 PVT corners">
  <img src="https://img.shields.io/badge/Timing-Setup%20Optimization%20Required-red?style=for-the-badge" alt="Setup timing optimization required">
</p>

## Table of Contents

- [Project Overview](#-project-overview)
- [Project Objectives](#-project-objectives)
- [Design Architecture and Macro Integration](#-design-architecture-and-macro-integration)
- [Technology Files and Design Inputs](#-technology-files-and-design-inputs)
- [ASIC Physical Design Fundamentals](#-asic-physical-design-fundamentals)
- [Environment Setup](#-environment-setup)
- [Implementation Commands](#-physical-design-implementation-commands)
- [Static Timing Analysis](#-static-timing-analysis)
- [Multi-Corner Timing Evaluation](#-multi-corner-timing-evaluation)
- [Implementation Evidence](#-physical-design-evidence)
- [Debugging and Verification](#-debugging-and-verification)
- [Results Summary](#-results-summary)
- [Conclusion](#-conclusion)

---

## Overview

This project explores the **ASIC physical design implementation of VSDBabySoC** using the OpenROAD-flow-scripts framework and the SkyWater SKY130HD process design kit.

The workflow covers technology mapping, floorplan preparation, tapcell insertion, power distribution network generation, standard-cell placement, clock-tree synthesis, and routing-related analysis. OpenROAD's physical-design environment is used to inspect the evolving layout, design connectivity, implementation logs, and timing information.

A major component of the project is **multi-corner Static Timing Analysis (STA)** across 16 process, voltage, and temperature (PVT) conditions. Setup and hold slack are compared to identify timing margins and determine which operating conditions require further investigation.

The project combines physical implementation with timing analysis to develop an understanding of how placement, clock distribution, interconnect parasitics, and operating conditions influence ASIC performance.

### Project Specifications

| Parameter | Details |
|---|---|
| Design under implementation | VSDBabySoC |
| Technology platform | SkyWater SKY130HD |
| Physical-design framework | OpenROAD-flow-scripts |
| Working directory | `~/Desktop/OpenROAD-flow-scripts/flow` |
| Design configuration | `designs/sky130hd/vsdbabysoc/config.mk` |
| Timing methodology | Static Timing Analysis |
| Operating-condition analysis | 16 PVT corners |
| Physical-design activities | Synthesis, floorplanning, placement, CTS and routing-related analysis |
| Timing metrics | Setup slack, hold slack, WNS and TNS |
| Primary objective | Evaluate implementation progress and identify timing-critical conditions |

## Objectives

The main objectives of this work are:

1. Explore the ASIC implementation flow using OpenROAD-flow-scripts.
2. Configure the SKY130HD platform and VSDBabySoC design environment.
3. Generate the synthesized netlist and implementation databases.
4. Understand floorplan definition, macro positioning, and physical design constraints.
5. Examine tapcell insertion and power distribution network preparation.
6. Investigate global placement and detailed placement results.
7. Study clock-tree synthesis and its influence on timing.
8. Inspect routing geometry, connectivity, and interconnect-related analysis.
9. Evaluate setup and hold timing across 16 PVT corners.
10. Compare timing margins across different operating conditions.
11. Identify implementation issues and corners that require further optimization.
12. Document the flow using screenshots, terminal output, timing plots, and implementation reports.

## Design Architecture and Macro Integration

The VSDBabySoC source hierarchy includes the following design files:

```text
vsdbabysoc.v
rvmyth.v
avsdpll.v
avsddac.v
```

These files represent the top-level SoC and its associated processor and analog-related modules. The design configuration must correctly identify the source files, libraries, macro models, and constraints required by the flow.

### SoC Integration Considerations

Successful integration requires consistency between logical connectivity and physical implementation data.

Important requirements include:

- Correct RTL hierarchy and top-module configuration.
- Valid standard-cell and macro timing models.
- Compatible physical abstracts and technology files.
- Appropriate placement constraints for hard macros.
- Correct signal, clock, power, and ground connectivity.
- Valid timing constraints for the required operating conditions.

### PLL and DAC Hard Macros

The PLL and DAC are handled as hard macros in the physical-design setup. Their physical dimensions, pin locations, orientation, and placement restrictions influence the surrounding layout.

The OpenROAD GUI shows the DAC macro with locked placement. The implementation logs also contain warnings indicating that some PLL supply pins are not connected to a net.

These warnings require investigation against the intended power-pin mapping and macro integration setup. Visible macro placement alone does not establish complete electrical connectivity.

## Technology Files and Design Inputs

The project uses the SKY130HD standard-cell platform. The supplied screenshots identify the following typical-corner Liberty file:

```text
sky130_fd_sc_hd__tt_025C_1v80.lib
```

This library corresponds to the typical process, 25°C, and 1.80 V operating condition.

### Important Design Files

| File format | Role in the implementation flow |
|---|---|
| Verilog/SystemVerilog | Describes circuit functionality and hierarchy |
| Liberty (`.lib`) | Provides cell timing and electrical characteristics |
| LEF | Defines physical cell geometry, pins, and macro abstracts |
| SDC | Specifies clock definitions and timing constraints |
| ODB | Stores physical design geometry and connectivity |
| Parasitic data | Represents estimated or extracted interconnect resistance and capacitance |
| Reports | Summarize timing, placement, routing, and verification results |
| Logs | Record tool execution, warnings, errors, and stage progress |

The correct combination of libraries, technology data, macro models, constraints, and design databases is essential for meaningful physical implementation and timing analysis.

## ASIC Physical Design Fundamentals

### 1. RTL Synthesis and Technology Mapping

Synthesis translates the design description into a gate-level netlist mapped to cells supported by the target technology.

The process includes RTL elaboration, hierarchy resolution, logic optimization, and standard-cell mapping.

The supplied terminal evidence shows synthesis-related activity, including the loading of the SKY130HD typical-corner library and the generation of database and constraint artifacts.

These outputs provide the starting point for subsequent physical implementation.

### 2. Floorplan Definition

Floorplanning establishes the physical organization of the design.

It defines the die and core regions, available placement area, macro locations, and physical constraints needed by downstream stages.

The DAC macro is visible in the supplied floorplan screenshots. Its location and orientation affect routing-channel availability, pin accessibility, and congestion around the macro.

### 3. Tapcell Insertion

Tapcells provide well and substrate-tie connections required by the target semiconductor technology.

The tapcell stage prepares the standard-cell layout to meet the corresponding physical implementation requirements.

### 4. Power Distribution Network Generation

The power distribution network provides physical supply paths for power and ground connections.

The OpenROAD GUI shows power/ground metal structures, including the `VSS` ground net. The loaded database identifies this net as routed.

This is evidence of visible power/ground geometry, but it does not independently verify every supply connection or establish power-integrity sign-off. The PLL supply-pin warnings must be resolved or explained separately.

### 5. Global Placement

Global placement estimates standard-cell locations while optimizing objectives such as wirelength and timing.

The reported placement output includes instance information, core dimensions, utilization, and other placement statistics.

An initial global-placement attempt ended with a Make error. That run should be considered unsuccessful until the underlying error is identified. Subsequent evidence shows progress through detailed placement and clock-tree synthesis.

### 6. Detailed Placement and Legalization

Detailed placement adjusts cell locations to satisfy physical placement rules.

Typical checks include:

- Placement-region compliance.
- Standard-cell row alignment.
- Site alignment.
- Prevention of cell overlap.

The supplied detailed-placement log reports zero issues in the displayed wrong-region, row-alignment, site-alignment, and overlap checks.

These results support successful legalization checks at that stage. They do not, by themselves, demonstrate successful detailed routing or final physical verification.

### 7. Clock-Tree Synthesis

Clock-tree synthesis constructs a clock distribution network that delivers the clock to sequential elements while managing factors such as insertion delay and skew.

The supplied CTS log reports:

| Parameter | Reported observation |
|---|---|
| Clock net | `CLK` |
| Clock sinks | 11,444 |
| Setup violations at the displayed CTS final stage | None reported |
| Hold violations at the displayed CTS final stage | None reported |

These results describe the displayed CTS stage. They must not be interpreted as proof of timing closure after routing or across all PVT corners.

Clock skew, transition limits, routing parasitics, and subsequent implementation changes can affect final timing.

### 8. Global Routing and Interconnect Analysis

Global routing assigns approximate routing paths to signal nets while considering available routing resources and congestion.

The supplied GUI shows routing-related geometry, with `net258` marked as `ROUTED` in the loaded database.

The displayed scripting console includes:

```tcl
grt::have_routes
estimate_parasitics -global_routing
sta::find_timing
sta::find_requireds
```

These commands are associated with route inspection, global-routing parasitic estimation, and timing-analysis operations in the shown session.

The status of a single net does not establish successful routing of the complete design.

### 9. Detailed Routing and Physical Verification

Detailed routing creates physical wire and via geometry for signal connections.

A complete implementation should be checked using the relevant routing logs, completion reports, design-rule checks, and other required verification outputs.

The supplied evidence shows routing-related geometry and analysis, but does not establish a successful complete detailed-routing run or DRC/LVS sign-off. These results must be confirmed separately.

### 10. Parasitic-Aware Timing Evaluation

Interconnect resistance and capacitance contribute to signal propagation delay. For this reason, timing analysis must use parasitic information appropriate to the implementation stage.

The timing result depends on:

- Standard-cell timing libraries.
- PVT operating conditions.
- Clock and I/O constraints.
- The loaded design database.
- Interconnect RC assumptions or extracted parasitics.
- The physical-design stage being evaluated.

Results from a floorplan or CTS database should not be presented as final post-route timing results.

## Environment Setup

The workspace shown in the report is:

```bash
cd ~/Desktop/OpenROAD-flow-scripts/flow
```

Confirm the current directory and available files:

```bash
pwd
ls
```

Inspect the VSDBabySoC design directory:

```bash
ls designs/sky130hd/vsdbabysoc/
```

Review the design configuration:

```bash
sed -n '1,240p' designs/sky130hd/vsdbabysoc/config.mk
```

Check the available flow targets:

```bash
make help
```

Set the design configuration variable:

```bash
export DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk
```

The commands supported by the flow depend on the installed OpenROAD-flow-scripts version.

## Physical Design Implementation Commands

Run each stage separately and inspect its output before proceeding to the next stage.

### Synthesis

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```

Generates the synthesized design representation and associated implementation artifacts.

### Floorplanning

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```

The valid target shown here is `floorplan`, not `floorplane`. The supplied evidence includes an earlier failed command caused by the misspelled target.

### Placement

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```

Runs the placement stage and its associated optimization steps.

### Clock-Tree Synthesis

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```

Builds the clock distribution network for the sequential elements.

### Global Routing

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk global_route
```

Runs global routing if the target is supported by the installed checkout. Use `make help` to verify the available target names.

### Routing

```bash
make DESIGN_CONFIG=$DESIGN_CONFIG route
```

Use this command only when `route` is available in the installed flow version. Check the logs to determine which routing stages actually completed.

### Inspect Generated Databases

```bash
find results/sky130hd/vsdbabysoc -maxdepth 3 -type f | sort
```

Inspect the base-stage results:

```bash
find results/sky130hd/vsdbabysoc/base -maxdepth 1 -type f | sort
```

### Inspect Reports and Logs

```bash
find reports/sky130hd/vsdbabysoc/base -type f | sort
```

```bash
find logs/sky130hd/vsdbabysoc -maxdepth 3 -type f | sort
```

Search for common implementation issues:

```bash
grep -RniE 'error|warning|slack|violation|global route|detailed route' \
logs/sky130hd/vsdbabysoc
```

For connectivity or failure messages:

```bash
grep -RniE 'error|failed|violation|unconnected' \
logs/sky130hd/vsdbabysoc/base
```

When a stage fails, identify the first meaningful error in the full log instead of relying solely on the final Make error message.

## Static Timing Analysis

Static Timing Analysis evaluates whether signal propagation delays satisfy the timing constraints for the loaded design. It analyzes timing paths without requiring functional simulation vectors.

### Setup Analysis

Setup checks verify that data reaches the destination early enough before the relevant active clock edge.

Negative setup slack indicates that a path fails its required timing limit.

### Hold Analysis

Hold checks verify that data remains stable for the required interval after the relevant active clock edge.

Negative hold slack indicates a hold timing violation, while positive hold slack indicates that the analyzed hold requirement is satisfied.

### Timing Reporting Commands

The supplied workflow includes the following timing-report commands:

```tcl
report_checks -path_delay max \
  -format full_clock_expanded \
  -fields {slew cap input_pin net} \
  -group_path_count 10

report_checks -path_delay min \
  -format full_clock_expanded \
  -fields {slew cap input_pin net} \
  -group_path_count 10

report_worst_slack -max
report_worst_slack -min
report_tns
report_clock_skew
report_check_types -max_slew -max_capacitance -max_fanout
```

These commands should be executed within a correctly initialized OpenSTA/OpenROAD timing session. Availability and syntax can vary by tool version and configuration.

For reproducible results, record the corner, timing constraints, database stage, parasitic source, worst setup slack, worst hold slack, TNS, clock skew, and any unconstrained endpoints.

## Multi-Corner Timing Analysis Across 16 PVT Corners

### Purpose of PVT Analysis

PVT analysis evaluates timing under different combinations of:

- **Process:** Manufacturing variation affecting transistor and cell behavior.
- **Voltage:** Operating supply voltage.
- **Temperature:** Operating temperature.

Different conditions can change cell delays and timing margins. A design that meets setup requirements under one corner may fail under another.

A complete multi-corner analysis should use the corresponding characterized libraries, valid constraints, and appropriate interconnect models for every configured condition.

### Recommended Evaluation Workflow

1. Define the intended set of 16 PVT corners.
2. Verify the required standard-cell and macro timing libraries.
3. Check the clock definitions and I/O constraints.
4. Load the appropriate design database and parasitic information.
5. Run maximum-delay analysis for setup checks.
6. Run minimum-delay analysis for hold checks.
7. Save the reports using identifiable corner names.
8. Extract the actual timing metrics into a summary table.
9. Plot the setup and hold slack values for comparison.
10. Repeat the analysis after routing using the appropriate routed parasitics.

Each corner must be analyzed with its corresponding models and constraints; a report from one corner should not simply be relabeled as another.

## TNS Timing Results

The supplied **tns vs PVT Corners** plot summarizes minimum-delay timing behavior across the 16 analyzed corners.

| Metric | Reported result |
|---|---:|
| Number of corners | 16 |
| Minimum hold slack | 0.19 ns |
| Corner with minimum hold slack | `ff_n40C_1v95` |
| Maximum hold slack | 1.68 ns |
| Corner with maximum hold slack | `ss_n40C_1v28` |

<img width="1600" height="900" alt="tns" src="https://github.com/user-attachments/assets/927035ff-c4ca-425a-b14c-6b41e20fd0aa" />

### Interpretation

The supplied graph reports positive hold slack across all 16 corners, ranging from 0.19 ns to 1.68 ns.

The lowest reported margin occurs at `ff_n40C_1v95`. This corner has the smallest hold margin in the provided comparison and is worth examining when reviewing timing robustness.

These values support the reported hold results for the analyzed paths. Complete hold sign-off requires confirming that all relevant paths and timing constraints are covered.

## Setup Timing Results

The **Worst Slack (max) vs PVT Corners** plot compares the reported maximum-delay timing results.

| Metric | Reported result |
|---|---:|
| Number of corners | 16 |
| Worst setup slack | -29.05 ns |
| Corner with worst setup slack | `ss_n40C_1v28` |
| Highest setup slack | 9.03 ns |
| Corner with highest setup slack | `ff_100C_1v95` |

<img width="1600" height="900" alt="wns" src="https://github.com/user-attachments/assets/45b269c3-27dc-4a2d-8a7b-12c7e115cdf8" />

### Interpretation

The most negative reported setup slack is **-29.05 ns** at `ss_n40C_1v28`. This indicates that the analyzed setup requirement is not satisfied at that corner.

The highest reported setup slack is **9.03 ns** at `ff_100C_1v95`.

The variation between these results highlights the importance of checking multiple operating conditions. The negative setup results require investigation of the relevant critical paths, clock and I/O constraints, and interconnect-delay assumptions before choosing an optimization strategy.

## Physical Design Evidence

The following screenshots document the workspace, implementation stages, GUI inspection, and timing-analysis results. Their descriptions distinguish visible evidence from conclusions that require additional reports.

### Figures 1–3: Workspace and Design Sources

**Figure 1 — OpenROAD Flow Workspace**

Shows the Linux workspace containing the OpenROAD flow environment and project files.

<img width="1917" height="1198" alt="OpenROAD workspace" src="https://github.com/user-attachments/assets/a8400e53-d0a2-4681-9913-ced9df563698" />

**Figure 2 — VSDBabySoC RTL Directory**

Shows the design source directory containing the Verilog/SystemVerilog files used by the implementation flow.

<img width="1901" height="1131" alt="VSDBabySoC source directory" src="https://github.com/user-attachments/assets/ffe6488b-c6b6-4c7f-9d8e-8a51d87fa0f5" />

**Figure 3 — Design Workspace in VS Code**

Shows the project directory and terminal environment used to run implementation commands.

<img width="1917" height="1136" alt="VSDBabySoC workspace in VS Code" src="https://github.com/user-attachments/assets/7f1f6a11-a3f1-412e-a533-93b958aa35d6" />

### Figures 4–6: Timing and Connectivity Inspection

**Figure 4 — OpenROAD GUI Timing Inspection**

Shows the loaded physical-design database and timing-related inspection in the GUI.

<img width="720" height="425" alt="OpenROAD GUI timing inspection" src="https://github.com/user-attachments/assets/6913a39c-3479-4651-ae41-d43621f5bd71" />

**Figure 5 — Timing Path Slack**

Shows a timing report containing a slack value of approximately -0.156. Its units, path type, constraints, and implementation stage should be confirmed from the associated report before comparing it with other timing values.

<img width="717" height="427" alt="Timing path slack report" src="https://github.com/user-attachments/assets/93eb94c4-f66a-4b0a-9d87-a0f539de5cf7" />

**Figure 6 — VSS Ground-Net Inspection**

Shows the VSS ground net identified as routed in the loaded database. This demonstrates visible routing information but does not independently establish complete power connectivity or power-integrity sign-off.

<img width="719" height="429" alt="VSS ground-net inspection" src="https://github.com/user-attachments/assets/a41904c6-ead6-4980-b5fb-9914295aba74" />

### Figures 7–10: Synthesis and Floorplan Preparation

**Figure 7 — Synthesis Terminal Output**

Shows synthesis-related command execution and technology-library information.

<img width="719" height="428" alt="Synthesis terminal output" src="https://github.com/user-attachments/assets/0f78a409-544f-4152-ac7f-6781de755163" />

**Figure 8 — Synthesis Database and Constraint Outputs**

Shows the design-linking process and generation of database and constraint artifacts for downstream implementation.

<img width="719" height="430" alt="Synthesis output artifacts" src="https://github.com/user-attachments/assets/67af8f93-e9d9-4a45-ac8e-2d3714a215de" />

**Figure 9 — Floorplan Target Correction**

Shows the failed command caused by the misspelled `floorplane` target. The correct target is `floorplan`.

<img width="719" height="428" alt="Floorplan target correction" src="https://github.com/user-attachments/assets/5c7d4776-44f8-4014-99d7-a4df7a9206d5" />

**Figure 10 — Tapcell and PDN Preparation**

Shows physical implementation output associated with floorplan preparation, tapcell insertion, and power distribution network generation.

<img width="719" height="429" alt="Tapcell and PDN preparation" src="https://github.com/user-attachments/assets/9476807d-a6d0-4be8-aa8b-44a10afcfbf0" />

### Figures 11–12: Macro Placement and Power Geometry

**Figure 11 — DAC Hard-Macro Placement**

Shows the DAC macro within the floorplan. Macro placement can influence routing access, local congestion, and the arrangement of surrounding cells.

<img width="719" height="427" alt="DAC macro placement" src="https://github.com/user-attachments/assets/73c92165-1ea5-451e-a466-a4f7aaa512b3" />

**Figure 12 — Power and Ground Metal Geometry**

Shows the visible power/ground structures and metal layers in the layout.

<img width="719" height="428" alt="Power and ground metal geometry" src="https://github.com/user-attachments/assets/cde18cf1-ebbf-4378-ad06-a71e20df193f" />

### Figures 13–15: Standard-Cell Placement

**Figure 13 — Global Placement Output**

Shows placement statistics alongside an initial Make error. The affected run must be treated as unsuccessful until the underlying error is resolved.

<img width="714" height="382" alt="Global placement output" src="https://github.com/user-attachments/assets/79d910b4-d2d8-4db7-b896-7fc59412b3de" />

**Figure 14 — Detailed Placement Legalization**

Shows the reported placement checks, with zero issues in the displayed wrong-region, row-alignment, site-alignment, and overlap categories.

<img width="718" height="389" alt="Detailed placement legalization" src="https://github.com/user-attachments/assets/b18ad9f5-4993-49e2-ac0a-c414870e0bf0" />

**Figure 15 — Placed Standard Cells and DAC Macro**

Shows the physical arrangement of the standard cells and DAC macro in the OpenROAD GUI.

<img width="719" height="388" alt="Placed standard cells and DAC macro" src="https://github.com/user-attachments/assets/24207b77-b2b4-4869-b0c8-2f28b8668573" />

### Figures 16–19: Clock-Tree Synthesis and Layout Inspection

**Figure 16 — Clock-Tree Synthesis Initiation**

Shows the `CLK` net and clock sinks involved in the CTS stage.

<img width="719" height="388" alt="Clock-tree synthesis initiation" src="https://github.com/user-attachments/assets/19d48282-9dc5-4822-a719-ea57a57da9d7" />

**Figure 17 — CTS Final Timing Checks**

Shows the CTS-stage output reporting no setup or hold violations in that particular run.

<img width="719" height="387" alt="CTS final timing checks" src="https://github.com/user-attachments/assets/9669843e-5fab-4c71-b4f6-a4efa1cfb145" />

**Figure 18 — Post-CTS Physical Layout**

Shows the design layout after clock-tree synthesis, with physical geometry visible in the GUI.

<img width="720" height="386" alt="Post-CTS physical layout" src="https://github.com/user-attachments/assets/03a0a423-aa1d-4ec8-a755-bf8c7d02071a" />

**Figure 19 — Layout Heat Map**

Shows a heat-map view of the layout near the DAC macro. The metric represented by the heat map should be identified before drawing conclusions about congestion or physical-design quality.

<img width="719" height="382" alt="Physical layout heat map" src="https://github.com/user-attachments/assets/6fb72709-d362-4164-aa8a-d561a5c8cf5a" />

### Figures 20–22: Blockages, Sequential Cells, and Technology Layers

**Figure 20 — Macro and Blockage Inspection**

Shows the DAC macro and visible placement or routing blockages.

<img width="719" height="426" alt="Macro and blockage inspection" src="https://github.com/user-attachments/assets/aa2498ad-8fe5-43ca-b901-4b330e335560" />

**Figure 21 — Sequential Standard Cell**

Shows a sequential cell associated with the processor logic. Sequential elements are central to clocked timing analysis.

<img width="719" height="429" alt="Sequential standard-cell inspection" src="https://github.com/user-attachments/assets/5abd8ffd-4374-4f2c-8598-33e336630c4f" />

**Figure 22 — Technology-Layer Inspection**

Shows technology-layer information in the physical-design GUI, including a displayed via layer and its properties.

<img width="719" height="424" alt="Technology-layer inspection" src="https://github.com/user-attachments/assets/08a10202-e6cf-4b50-99ec-29e59d4f582b" />

### Figure 23 — Routing-Stage GUI and Routed-Net Inspection

The screenshot shows the VSDBabySoC design in the OpenROAD GUI at the `5_route` stage. Metal layers, vias, and interconnect geometry are visible, and the Inspector marks `net258` as `ROUTED`.

The console includes commands for route inspection, global-routing parasitic estimation, and timing updates.

<img width="859" height="466" alt="Routing-stage GUI and routed-net inspection" src="https://github.com/user-attachments/assets/8629ddae-0a1b-403b-bd07-3b57d8d89149" />

This is evidence of routing-related geometry in the loaded database. It does not independently establish complete design routing or DRC/LVS sign-off.

### Multi-Corner Timing Evidence

**Figure 24 — Multi-Corner STA Terminal Output**

Shows terminal evidence associated with the 16-corner timing analysis.

<img width="862" height="468" alt="Multi-corner STA terminal output" src="https://github.com/user-attachments/assets/7808b322-80fe-45b2-b641-f2ce9b595167" />

**Figure 25 — Hold Slack Across PVT Corners**

Shows positive reported hold slack across the 16 corners, with values ranging from 0.19 ns to 1.68 ns.

<img width="853" height="429" alt="Hold slack across PVT corners" src="https://github.com/user-attachments/assets/c936dff7-e101-499d-802d-3517e1025e9f" />

**Figure 26 — Worst Setup Slack Across PVT Corners**

Shows the reported setup-slack comparison, including the worst value of -29.05 ns at `ss_n40C_1v28`.

<img width="851" height="419" alt="Worst setup slack across PVT corners" src="https://github.com/user-attachments/assets/dc1847cf-17f1-4b9f-b9e2-6d27077bc1d5" />

## Debugging and Verification Checklist

For a reliable implementation, verify the following items before treating the design as complete.

### Physical Implementation

- Confirm that synthesis completed successfully.
- Check the floorplan configuration and macro locations.
- Review tapcell and PDN generation logs.
- Investigate PLL power-pin connectivity warnings.
- Confirm detailed-placement legalization results.
- Identify the cause of any failed placement or routing stage.
- Confirm routing completion using stage logs and reports rather than a single GUI view.

### Timing Analysis

- Verify that each PVT corner uses the appropriate Liberty models.
- Validate the SDC clock and I/O constraints.
- Confirm the database and parasitic source used for every timing run.
- Generate separate setup and hold reports for all required corners.
- Check WNS, TNS, clock skew, and unconstrained endpoints.
- Re-run timing with appropriate routed parasitics when routing is complete.
- Compare summary plots with the underlying timing reports.

### Sign-Off Evidence

A layout screenshot alone does not establish final sign-off. Retain the relevant reports for timing, routing completion, DRC, LVS, and power integrity wherever those checks are required.

## Results Summary

| Stage or analysis | Reported outcome |
|---|---|
| Synthesis | Netlist/database and constraint generation are shown |
| Floorplanning | Floorplan database and constraint setup are shown |
| Tapcell and PDN | Stage outputs are shown; PLL supply warnings require review |
| Global placement | An initial attempt failed; later implementation progressed |
| Detailed placement | Displayed legalization checks report zero issues in the listed categories |
| Clock-tree synthesis | 11,444 clock sinks; no setup or hold violations reported at the displayed CTS final stage |
| Routing inspection | Routing-related geometry and a routed-net status are shown |
| Hold timing | Positive slack across 16 corners, from 0.19 ns to 1.68 ns |
| Setup timing | Reported slack ranges from -29.05 ns to 9.03 ns |
| Timing optimization | Further investigation is required at corners with negative setup slack |
| DRC/LVS sign-off | Not established by the supplied screenshots and logs |

## Conclusion

This project examines the physical implementation of VSDBabySoC using the SKY130HD technology platform and OpenROAD-flow-scripts. The work covers synthesis, floorplan preparation, tapcell insertion, power distribution network generation, placement, clock-tree synthesis, routing-related inspection, and multi-corner timing evaluation.

The supplied timing results show positive hold slack across all 16 reported PVT corners, ranging from **0.19 ns to 1.68 ns**. Setup timing varies significantly across operating conditions, with the worst reported slack of **-29.05 ns at `ss_n40C_1v28`** and the highest reported slack of **9.03 ns at `ff_100C_1v95`**.

These findings illustrate the importance of evaluating timing across multiple operating corners rather than relying on a single nominal condition. Further work should focus on investigating setup-critical paths, validating constraints and parasitic assumptions, confirming complete routing, and collecting the necessary physical-verification reports.

**Overall outcome:** The reported implementation has progressed through several physical-design stages, and the multi-corner timing results identify setup timing as the main area requiring further investigation before sign-off.

## Tools and Technologies

- OpenROAD-flow-scripts
- OpenROAD GUI
- OpenSTA
- SkyWater SKY130HD PDK
- Verilog/SystemVerilog
- Liberty timing libraries
- LEF physical abstracts
- SDC timing constraints
- ODB physical design databases
- Parasitic-aware timing analysis
- Multi-corner PVT evaluation
- WNS and TNS reporting

---

<p align="center">
  <b>“From synthesized logic to physical layout, every stage shapes the performance of an ASIC.”</b>
  <br>
  <i>VSDBabySoC | ASIC Physical Design | OpenROAD | SKY130HD</i>
</p>

