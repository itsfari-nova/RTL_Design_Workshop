# PHYSICAL DESIGN MODULE-4
## Pre-Layout Timing Analysis & Clock Tree Synthesis

> **VLSI Design & Implementation Workshop**
> **Technology:** SkyWater SKY130
> **Focus:** Timing Analysis, Delay Modeling, Static Timing Analysis, Clock Tree Synthesis & Signal Integrity
> **Tools:** OpenSTA • TritonCTS • OpenROAD • Magic • SKY130 PDK

---

## Overview

This module covers pre-layout timing analysis, timing characterization, Static Timing Analysis (STA), clock uncertainty, timing optimization, Clock Tree Synthesis (CTS), and signal integrity.

The module begins with understanding and developing timing information for standard cells and then progresses toward detailed timing analysis using both ideal and practical clock signals.

The main objective of this module is to understand the importance of a properly designed clock tree and how it helps ensure reliable timing and stable operation in synchronous digital systems.

```

## Objectives

The main objectives of this module are:

* Understand the relationship between physical layout and timing information.
* Convert layout grid information into routing-track information.
* Generate a standard-cell LEF from a Magic layout.
* Understand standard-cell timing libraries.
* Understand delay tables used for timing modeling.
* Include a custom inverter cell during synthesis.
* Perform pre-layout timing analysis.
* Understand setup time and setup violations.
* Understand clock jitter and clock uncertainty.
* Configure OpenSTA for post-synthesis timing analysis.
* Optimize synthesis to reduce timing violations.
* Perform basic timing ECO.
* Understand Clock Tree Synthesis (CTS).
* Study H-Tree based clock distribution.
* Understand clock buffering and routing.
* Understand crosstalk and clock shielding.
* Run CTS using TritonCTS.
* Verify CTS results.
* Perform setup and hold analysis using real clocks.
* Analyze the impact of CTS buffer sizing on timing.

---

# Tools & Technologies

| Tool / Technology         | Purpose                                              |
| ------------------------- | ---------------------------------------------------- |
| **SKY130 PDK**            | Technology library and physical design rules         |
| **OpenSTA**               | Static Timing Analysis                               |
| **TritonCTS**             | Clock Tree Synthesis                                 |
| **OpenROAD**              | Physical design and CTS flow                         |
| **Magic VLSI**            | Layout and physical design                           |
| **LEF**                   | Standard-cell physical abstract                      |
| **Liberty (.lib)**        | Cell timing and power information                    |
| **Synthesis tools**       | RTL-to-gate-level conversion and timing optimization |
| **SPICE / Timing Models** | Electrical and delay modeling                        |

---

# Module Structure

This module is divided into four major sections:

1. **Timing Modeling Using Delay Tables**
2. **Timing Analysis with Ideal Clocks**
3. **Clock Tree Synthesis and Signal Integrity**
4. **Timing Analysis with Real Clocks**

---

# 1️ SKY130_D4_SK1 — Timing Modelling Using Delay Tables

This section introduces the relationship between **standard-cell physical information and timing information**.

A standard cell needs more than its logical function. Physical design tools also require information about:

* Cell dimensions
* Pin locations
* Routing tracks
* Input capacitance
* Output load
* Cell delay
* Transition time
* Timing arcs

These characteristics are represented through **LEF and Liberty timing libraries**.

---

## Lab 1 — Convert Grid Information to Track Information

### `svgSKY_L1`

This lab explains how physical layout grid information is converted into **routing-track information**.

### Concepts Covered

* Layout grid
* Manufacturing grid
* Routing tracks
* Track pitch
* Metal routing
* Track alignment
* Standard-cell placement compatibility

### Why It Matters

Physical design tools use routing tracks to determine where wires can be placed.

Correct track information ensures that:

* Cells align correctly.
* Pins can connect to routing tracks.
* Standard cells can be placed consistently.
* Routing tools can access cell pins.

---

## Lab 2 — Convert Magic Layout to Standard Cell LEF

### `SKY_L2`

This lab demonstrates how a physical standard-cell layout created in Magic is converted into a **LEF (Library Exchange Format)** representation.

### LEF Contains

* Cell dimensions
* Cell name
* Pin information
* Pin direction
* Pin layer
* Pin geometry
* Power and ground pins
* Obstructions
* Routing information

### Layout → LEF Flow

```text
Magic Layout
     ↓
Physical Geometry
     ↓
Pin / Layer Information
     ↓
LEF Generation
     ↓
Physical Design Tools
```

### Why LEF Is Important

LEF provides an abstract physical representation of a standard cell without exposing all transistor-level layout details.

Placement and routing tools use LEF information to understand how cells can be physically placed and connected.

---

## Lab 3 — Timing Libraries and Including a New Cell in Synthesis

### `SKY_L3`

This lab introduces **timing libraries** and explains how a new standard cell can be incorporated into the synthesis flow.

A Liberty timing library contains information such as:

* Cell function
* Timing arcs
* Input capacitance
* Output transition
* Cell delay
* Setup time
* Hold time
* Power information
* Operating conditions

### Typical Flow

```text
RTL
 ↓
Synthesis
 ↓
Standard Cell Library
 ↓
Gate-Level Netlist
 ↓
Timing Analysis
```

Adding a custom cell allows the synthesis tool to use that cell during technology mapping and optimization.

---

#  Lab 4 — Introduction to Delay Tables

### `SKY_L4`

This lab introduces the concept of **delay tables** used in standard-cell timing characterization.

Cell delay is not a fixed value.

It changes depending on parameters such as:

* Input transition
* Output capacitance
* Load
* Operating conditions

Therefore, timing libraries use lookup tables to represent cell delay and transition behavior.

### Basic Concept

```text
Input Transition
        +
Output Load
        ↓
   Delay Table
        ↓
   Cell Delay
```

---

#  Lab 5 — Delay Table Usage — Part 1

### `SKY_L5`

This lab focuses on the practical use of delay tables.

The timing engine determines cell delay by using the relevant combination of:

* Input slew
* Output capacitance

The corresponding value is obtained from the characterized timing table.

---

#  Lab 6 — Delay Table Usage — Part 2

### `SKY_L6`

This lab continues the analysis of delay-table behavior.

It demonstrates how changing:

* Input transition
* Output load
* Cell characteristics

affects the resulting delay.

This provides the foundation for understanding how STA engines calculate path delay.

---

#  Lab 7 — Configure Synthesis to Fix Slack and Include `vsdinv`

### `SKY_L7`

This lab focuses on configuring synthesis settings to improve timing slack and include the custom **`vsdinv`** inverter cell.

### Key Concepts

* Synthesis constraints
* Timing optimization
* Slack
* Cell selection
* Custom standard-cell integration
* Timing-driven synthesis

### Slack

Slack represents the timing margin available on a path.

```text
Slack = Required Time − Arrival Time
```

Interpretation:

* **Positive slack** → timing requirement is met.
* **Zero slack** → path is exactly at the timing limit.
* **Negative slack** → timing violation exists.

##  Figure 1:
<img width="900" height="582" alt="Screenshot 2026-09-13 153809" src="https://github.com/user-attachments/assets/5121a554-8d1f-43b3-95d5-7f4f3c6bccc9" />

##  Figure 2:
<img width="900" height="582" alt="Screenshot 2026-09-13 155136" src="https://github.com/user-attachments/assets/e3427810-74c1-4dfd-95f4-3a5edd2a1b66" />

##  Figure 3:
<img width="900" height="582" alt="Screenshot 2026-09-13 162325" src="https://github.com/user-attachments/assets/7dbed75d-b67c-48e8-bb49-3ab3101427c4" />

##  Figure 4:
<img width="900" height="582" alt="Screenshot 2026-09-13 162433" src="https://github.com/user-attachments/assets/ff5f190b-fb10-49ff-8091-6b033873ea0a" />

##  Figure 5:
<img width="900" height="582" alt="Screenshot 2026-09-20 122332" src="https://github.com/user-attachments/assets/6077dde2-20b6-4c24-8d21-7087e5562324" />

##  Figure 6:
<img width="900" height="582" alt="Screenshot 2026-09-13 163652" src="https://github.com/user-attachments/assets/e38ee9e7-e9e7-43fd-8d5f-1464a44488d0" />

##  Figure 7:
<img width="900" height="582" alt="Screenshot 2026-09-13 164023" src="https://github.com/user-attachments/assets/957bb53a-5e44-43bb-bf45-80f1aef10c08" />

##  Figure 8:
<img width="900" height="582" alt="Screenshot 2026-09-13 164213" src="https://github.com/user-attachments/assets/abc0a46a-1102-4629-bc69-2569ad6e66f0" />

##  Figure 9:
<img width="900" height="582" alt="Screenshot 2026-09-13 164238" src="https://github.com/user-attachments/assets/04d238b7-92e0-4b6e-a6aa-e6179495085f" />

##  Figure 10:
<img width="900" height="582" alt="Screenshot 2026-09-20 225948" src="https://github.com/user-attachments/assets/98e2c81b-61b4-45c8-ba42-58404cc40221" />

##  Figure 11:
<img width="900" height="582" alt="Screenshot 2026-09-20 120559" src="https://github.com/user-attachments/assets/abb3b967-d7b0-4e83-bcf2-d18415424240" />

##  Figure 12:
<img width="900" height="582" alt="Screenshot 2026-09-20 121744" src="https://github.com/user-attachments/assets/cc453655-a3e6-4eb5-b22b-01f496921472" />

##  Figure 13:
<img width="900" height="582" alt="Screenshot 2026-09-20 231843" src="https://github.com/user-attachments/assets/4cedd3b2-c445-46d9-9a8f-73ef449666d1" />

##  Figure 14:
<img width="900" height="582" alt="Screenshot 2026-09-20 141054" src="https://github.com/user-attachments/assets/1f69f4dd-f5d6-45d1-83dc-1af56f37b24a" />

##  Figure 15:
<img width="900" height="582" alt="Screenshot 2026-09-20 140422" src="https://github.com/user-attachments/assets/56bf78a0-8360-46ec-ad74-af41fadf0b6b" />

---

# 2️  SKY130_D4_SK2 — Timing Analysis with Ideal Clocks Using OpenSTA

This section introduces **Static Timing Analysis using ideal clocks**.

Before building a physical clock tree, clock arrival is initially treated as idealized.

This allows the design's basic timing behavior to be analyzed independently of physical clock-tree effects.

---

#  Lab 1 — Setup Timing Analysis & Flip-Flop Setup Time

### `svgSKY_L1`

This lab introduces **setup timing analysis**.

### Setup Time

Setup time is the minimum amount of time that data must remain stable **before the active clock edge** of a flip-flop.

A simplified timing relationship is:

```text
Data Launch
     ↓
Combinational Logic
     ↓
Data Arrival
     ↓
Setup Requirement
     ↓
Capture Clock Edge
```

If data arrives too late, a **setup violation** occurs.

---

#  Lab 2 — Clock Jitter and Uncertainty

### `SKY_L2`

Real clocks are not perfectly periodic.

Clock behavior can be affected by:

* Jitter
* Variation
* Noise
* Clock uncertainty

### Clock Jitter

Clock jitter represents variation in the timing of clock edges from their ideal positions.

### Clock Uncertainty

Clock uncertainty provides a timing margin to account for clock variations and uncertainty.

Conceptually:

```text
Ideal Clock Edge
       ↓
Clock Variation
       ↓
Timing Uncertainty
       ↓
Reduced Timing Margin
```

---

#  Lab 3 — Configure OpenSTA for Post-Synthesis Timing Analysis

### `SKY_L3`

This lab demonstrates how to configure **OpenSTA** for timing analysis after synthesis.

Typical inputs include:

* Gate-level netlist
* Liberty timing library
* SDC constraints
* Clock definition
* Input/output constraints

### OpenSTA Flow

```text
Gate-Level Netlist
        +
Timing Library
        +
SDC Constraints
        ↓
      OpenSTA
        ↓
Timing Reports
        ↓
Setup / Hold Analysis
```

---

#  Lab 4 — Optimize Synthesis to Reduce Setup Violations

### `SKY_L4`

This lab focuses on improving timing by modifying synthesis settings.

Possible optimization mechanisms include:

* Cell sizing
* Buffer insertion
* Logic optimization
* Cell selection
* Timing constraints
* Fanout optimization

The goal is to reduce negative setup slack and improve the timing margin.

---

#  Lab 5 — Basic Timing ECO

### `SKY_L5`

ECO stands for **Engineering Change Order**.

A timing ECO makes targeted modifications to an existing design instead of completely rebuilding the design.

Typical timing ECO operations can include:

* Cell replacement
* Buffer insertion
* Cell sizing
* Logic modification

The objective is to correct timing problems while minimizing unnecessary changes.

##  Figure 1:
<img width="1917" height="1136" alt="Screenshot 2026-09-20 141647" src="https://github.com/user-attachments/assets/60d413a2-0b54-40f5-87cd-19d27eb3cac8" />

##  Figure 2:
<img width="1917" height="1140" alt="Screenshot 2026-09-20 220101" src="https://github.com/user-attachments/assets/9ed624fa-dfd5-40d6-8b4e-69034391c974" />

##  Figure 3:
<img width="957" height="1135" alt="Screenshot 2026-09-20 215729" src="https://github.com/user-attachments/assets/c583f6fe-790b-4916-a624-7f06ade4f882" />

##  Figure 4:
<img width="1917" height="1136" alt="Screenshot 2026-09-20 222800" src="https://github.com/user-attachments/assets/a21a4ad3-9189-44d3-94ec-36d074301274" />

##  Figure 5:
<img width="1917" height="1142" alt="Screenshot 2026-09-20 222826" src="https://github.com/user-attachments/assets/271ed54f-c02a-4127-9c0c-26e470eddac8" />

##  Figure 6:
<img width="1917" height="1141" alt="Screenshot 2026-09-20 222841" src="https://github.com/user-attachments/assets/5780e850-7b2c-483b-82ba-66b79bb7162d" />

##  Figure 7:
<img width="1910" height="810" alt="Screenshot 2026-09-20 231843" src="https://github.com/user-attachments/assets/07377d8d-f155-4b97-9b0a-9acc64b010da" />

##  Figure 8:
<img width="1917" height="1142" alt="Screenshot 2026-09-20 234221" src="https://github.com/user-attachments/assets/cca8715d-90b0-4c01-a298-669af7981173" />

##  Figure 9:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 001044" src="https://github.com/user-attachments/assets/7b4e48b5-7cb1-4ef3-9fae-adbdc6ca17a8" />

##  Figure 10:
<img width="1917" height="1136" alt="Screenshot 2026-09-21 001109" src="https://github.com/user-attachments/assets/3d715a25-02b7-452f-b47b-4cace4d3846c" />

##  Figure 11:
<img width="1917" height="1143" alt="Screenshot 2026-09-21 002305" src="https://github.com/user-attachments/assets/fe462f48-66e3-488b-bc61-9a04c2b71b3d" />

##  Figure 12:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 002331" src="https://github.com/user-attachments/assets/e75c8f90-6338-4488-bd9a-d19876e2f2a4" />

##  Figure 13:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 002915" src="https://github.com/user-attachments/assets/6af30de3-47ef-418b-bfac-76c28032a9d7" />

##  Figure 14:
<img width="1917" height="1142" alt="Screenshot 2026-09-21 143349" src="https://github.com/user-attachments/assets/f93af6dd-088c-43b9-aa51-a5c9abf1355b" />

---

#  3️ SKY130_D4_SK3 — Clock Tree Synthesis & Signal Integrity

This section introduces **Clock Tree Synthesis (CTS)**.

A clock signal must reach many sequential elements with controlled:

* Delay
* Skew
* Transition
* Fanout
* Signal integrity

A good clock distribution network is therefore essential for reliable synchronous operation.

---

#  Lab 1 — Clock Tree Routing and Buffering Using H-Tree

### `svgSKY_L1`

An **H-Tree** is a balanced clock distribution structure.

The clock is progressively divided into branches so that different endpoints can receive approximately balanced clock paths.

```text
                 CLK
                  │
             ─────┼─────
            │           │
         ───┼───     ───┼───
        │       │   │       │
       FF      FF  FF      FF
```

### Main Objective

The purpose of balanced clock distribution is to reduce differences in clock arrival time, commonly referred to as **clock skew**.

---

#  Lab 2 — Crosstalk and Clock Net Shielding

### `SKY_L2`

Clock signals are particularly sensitive to unwanted coupling from nearby signal wires.

### Crosstalk

Crosstalk occurs when electrical coupling between neighboring wires causes unwanted effects on a signal.

For clock networks, this can affect:

* Clock delay
* Clock transition
* Clock skew
* Timing margins

### Clock Shielding

Shielding places a suitable fixed-potential wire near a sensitive clock wire to reduce coupling from neighboring signal nets.

Conceptually:

```text
Signal | Shield | Clock | Shield | Signal
```

The objective is to protect the clock network from unwanted capacitive coupling.

---

#  Lab 3 — Run CTS Using TritonCTS

### `SKY_L3`

This lab demonstrates the execution of **Clock Tree Synthesis using TritonCTS**.

CTS introduces clock buffers and routing structures to distribute the clock to sequential elements.

### CTS Flow

```text
Clock Source
     ↓
Clock Tree Synthesis
     ↓
Buffer Insertion
     ↓
Clock Distribution
     ↓
Clock Routing
     ↓
Sequential Elements
```

The CTS process considers factors such as:

* Clock fanout
* Buffer selection
* Clock delay
* Clock skew
* Transition
* Physical location of sinks

---

# Lab 4 — Verify CTS Runs

### `SKY_L4`

After CTS, the generated clock tree must be verified.

Important parameters include:

* Clock skew
* Clock latency
* Clock transition
* Buffer count
* Clock fanout
* Timing impact
* Clock routing

The CTS result must be checked before proceeding to further physical-design stages.

##  Figure 1:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 143420" src="https://github.com/user-attachments/assets/eb8a7c70-25aa-4675-94a4-7642d7ab6b18" />

##  Figure 2:
<img width="1917" height="1137" alt="Screenshot 2026-09-21 143436" src="https://github.com/user-attachments/assets/80735e18-1b96-4660-83f2-523415dce5c5" />

##  Figure 3:
<img width="1912" height="1143" alt="Screenshot 2026-09-21 144002" src="https://github.com/user-attachments/assets/c7c5ceb5-0d39-42da-87a2-e8adb24b7ec4" />

##  Figure 4:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 144019" src="https://github.com/user-attachments/assets/6cbf77fd-1cfe-44fd-82f1-17ec92a0e2ef" />

##  Figure 5:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 144038" src="https://github.com/user-attachments/assets/d2dcdeed-4d0d-4248-b01c-739d7c57c8bd" />

##  Figure 6:
<img width="1917" height="1142" alt="Screenshot 2026-09-21 174226" src="https://github.com/user-attachments/assets/9b5c6364-f3ed-4036-8f1d-7ef954272204" />

##  Figure 7:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 174416" src="https://github.com/user-attachments/assets/c2e27513-5cc0-4e75-bbae-425aace09340" />

##  Figure 8:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 175009" src="https://github.com/user-attachments/assets/c08d2a4d-ffc2-4d53-94b2-41c5271a6399" />

##  Figure 9:
<img width="1917" height="1093" alt="Screenshot 2026-09-21 183939" src="https://github.com/user-attachments/assets/9323d245-9f13-42b2-9039-b8a94ba13356" />

##  Figure 10:
<img width="958" height="1137" alt="Screenshot 2026-09-21 182623" src="https://github.com/user-attachments/assets/30be88a8-b49f-427a-b5be-6f11a44c1f3e" />

##  Figure 11:
<img width="958" height="1141" alt="Screenshot 2026-09-21 182638" src="https://github.com/user-attachments/assets/42603242-f933-423a-a3e4-c910ed2fbbd0" />

##  Figure 12:
<img width="953" height="1138" alt="Screenshot 2026-09-21 182702" src="https://github.com/user-attachments/assets/a375df75-159a-40f3-8909-09a830eb6ae9" />

##  Figure 13:
<img width="932" height="1121" alt="Screenshot 2026-09-21 182844" src="https://github.com/user-attachments/assets/423aed43-422d-4be7-9cb0-4a1af7f0075a" />

---

#  4️ SKY130_D4_SK4 — Timing Analysis with Real Clocks Using OpenSTA

This section introduces timing analysis after the clock tree has been physically implemented.

Unlike ideal-clock analysis, real-clock analysis considers the actual clock distribution network.

```text
Ideal Clock
     ↓
CTS
     ↓
Clock Buffers + Routing
     ↓
Real Clock Arrival Times
     ↓
Real-Clock STA
```

---

#  Lab 1 — Setup Timing Analysis Using Real Clocks

### `svgSKY_L1`

This lab analyzes setup timing after CTS.

The analysis now includes actual clock propagation through the clock tree.

Factors include:

* Clock insertion delay
* Clock skew
* Data path delay
* Setup requirement
* Clock uncertainty

Real-clock STA therefore provides a more physical representation of timing than ideal-clock STA.

---

#  Lab 2 — Hold Timing Analysis Using Real Clocks

### `SKY_L2`

This lab introduces hold timing analysis with propagated clocks.

### Hold Time

Hold time is the minimum duration for which data must remain stable **after the active clock edge**.

A hold violation occurs when data reaches the capture flip-flop too early.

Conceptually:

```text
Capture Clock Edge
       ↓
Hold Window
       ↓
Data Must Remain Stable
```

Hold analysis becomes especially important after CTS because clock skew can change the relative arrival time of launch and capture clocks.

---

#  Lab 3 — Analyze Real-Clock Timing Using OpenSTA

### `SKY_L3`

This lab demonstrates how to analyze timing after CTS using OpenSTA.

The analysis includes:

* Propagated clocks
* Clock tree delays
* Setup timing
* Hold timing
* Clock skew
* Timing slack

The resulting reports provide a more realistic representation of post-CTS timing behavior.

---

#  Lab 4 — Execute OpenSTA with Correct Timing Libraries and CTS Assignment

### `SKY_L4`

This lab focuses on correctly configuring OpenSTA after CTS.

Important inputs include:

* Correct Liberty timing libraries
* Gate-level netlist
* SDC constraints
* CTS-generated clock information
* Clock assignments
* Propagated-clock configuration

Using the correct timing library and clock configuration is essential for obtaining meaningful STA results.

---

#  Lab 5 — Impact of Larger CTS Buffers on Setup and Hold Timing

### `SKY_L5`

This lab investigates how increasing the size of CTS buffers affects timing.

Larger buffers can change:

* Clock transition
* Clock delay
* Clock latency
* Clock skew
* Setup slack
* Hold slack
* Power consumption

The important point is that changing clock-buffer size can affect **both setup and hold timing**, sometimes in different directions.

Therefore, CTS optimization requires considering the complete timing picture rather than optimizing only one metric.

## Figure 1:
<img width="1906" height="1198" alt="Screenshot 2026-09-21 184013" src="https://github.com/user-attachments/assets/90632339-312c-468d-a684-f9eb816184bc" />

## Figure 2:
<img width="1917" height="1187" alt="Screenshot 2026-09-21 185434" src="https://github.com/user-attachments/assets/f3a3ec7f-5f5c-4447-a140-b256144327ca" />

## Figure 3:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 190119" src="https://github.com/user-attachments/assets/1f50a463-f8be-4676-8ed5-1f2e26346944" />

## Figure 4:
<img width="1917" height="1077" alt="Screenshot 2026-09-21 190140" src="https://github.com/user-attachments/assets/45b80fd1-c9b4-4831-8d0b-8e0a1128b0fa" />

## Figure 5:
<img width="1913" height="1138" alt="Screenshot 2026-09-21 190202" src="https://github.com/user-attachments/assets/4fcf497b-f4c1-4ad1-8f2a-1a7cfbdc9a81" />

## Figure 5:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 190221" src="https://github.com/user-attachments/assets/b13bca21-6c15-4261-ac41-63f1a7dba3cd" />

## Figure 5:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 192824" src="https://github.com/user-attachments/assets/2f568a86-6b47-4bbd-bc18-b4a5d36bae02" />

## Figure 6:
<img width="1915" height="1141" alt="Screenshot 2026-09-21 192844" src="https://github.com/user-attachments/assets/1a08c54a-c492-40b2-911b-07c4e7f3752d" />

## Figure 7:
<img width="1917" height="1142" alt="Screenshot 2026-09-21 192903" src="https://github.com/user-attachments/assets/d7376139-c30f-4f18-b3ba-dd903d5523fe" />

## Figure 8:
<img width="1917" height="1142" alt="Screenshot 2026-09-21 194534" src="https://github.com/user-attachments/assets/f5362a68-a73c-4413-aa22-ad9cb7d4fd1a" />

## Figure 9:
<img width="1917" height="1138" alt="Screenshot 2026-09-21 194557" src="https://github.com/user-attachments/assets/d9419def-3c42-4e55-857e-56da10ba3fba" />

## Figure 10:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 195842" src="https://github.com/user-attachments/assets/9a43b0aa-cb28-480b-a5e3-5bc82ca5e225" />

## Figure 11:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 195902" src="https://github.com/user-attachments/assets/8196a322-3450-45be-be99-d918f88dd0b0" />

## Figure 12:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 201938" src="https://github.com/user-attachments/assets/4b75fd2f-56be-4771-a7cf-744162016c10" />

## Figure 13:
<img width="1908" height="1135" alt="Screenshot 2026-09-21 212920" src="https://github.com/user-attachments/assets/5b64b242-d069-453f-b998-1c4f67637549" />

## Figure 14:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 212944" src="https://github.com/user-attachments/assets/ea5cd937-5871-4c87-93f3-a56c8d94ad28" />

## Figure 15:
<img width="1913" height="1138" alt="Screenshot 2026-09-21 213824" src="https://github.com/user-attachments/assets/3ab248f9-490d-485f-a30e-0434866f84a6" />

## Figure 16:
<img width="1917" height="1137" alt="Screenshot 2026-09-21 214132" src="https://github.com/user-attachments/assets/33a3d7c7-0798-44e6-ac98-5a30fe1739f2" />

## Figure 17:
<img width="1917" height="1142" alt="Screenshot 2026-09-21 221340" src="https://github.com/user-attachments/assets/32c700cc-4b00-4019-9972-17bc3913b8e7" />

## Figure 18:
<img width="1917" height="1141" alt="Screenshot 2026-09-21 221359" src="https://github.com/user-attachments/assets/cf76de46-74af-4181-9fc8-c31ddb203e68" />

## Figure 19:
<img width="1917" height="1140" alt="Screenshot 2026-09-21 223307" src="https://github.com/user-attachments/assets/69279b1b-ffba-419d-a59f-14599d3a9b60" />

## Figure 20:
<img width="955" height="1142" alt="Screenshot 2026-09-21 232724" src="https://github.com/user-attachments/assets/5f73d185-1269-4d53-bf49-e1808536321a" />

## Figure 21:
<img width="1917" height="1135" alt="Screenshot 2026-09-23 143147" src="https://github.com/user-attachments/assets/50eba49f-694f-492f-b327-a0e1e8daf60e" />

## Figure 22:
<img width="1917" height="1140" alt="Screenshot 2026-09-23 143226" src="https://github.com/user-attachments/assets/e7095fc9-2639-435e-8024-9606d85106ad" />

---

# Ideal Clock vs Real Clock

| Feature         | Ideal Clock                | Real Clock               |
| --------------- | -------------------------- | ------------------------ |
| Clock network   | Abstract / idealized       | Physically implemented   |
| Clock buffers   | Not physically represented | Included                 |
| Clock routing   | Not represented            | Included                 |
| Clock latency   | Idealized                  | Actual/estimated         |
| Clock skew      | Simplified                 | Physical effect included |
| CTS impact      | Not included               | Included                 |
| Timing accuracy | Pre-CTS analysis           | Post-CTS analysis        |

---

#  Key Concepts Learned

## Timing Modeling

* Standard-cell timing libraries
* Liberty files
* Delay tables
* Input slew
* Output capacitance
* Cell delay
* Timing arcs
* Timing characterization

## Static Timing Analysis

* Setup timing
* Hold timing
* Arrival time
* Required time
* Slack
* Timing constraints
* Clock uncertainty
* Clock jitter

## Clock Tree Synthesis

* Clock distribution
* Clock buffering
* Clock latency
* Clock skew
* H-Tree
* Clock fanout
* Clock routing
* CTS verification

## Signal Integrity

* Crosstalk
* Coupling
* Clock shielding
* Clock transition
* Impact of interconnect on timing

## Timing Optimization

* Synthesis optimization
* Cell sizing
* Buffer insertion
* Slack improvement
* Timing ECO
* CTS buffer optimization

---

#  Important Timing Terms

| Term                  | Meaning                                                           |
| --------------------- | ----------------------------------------------------------------- |
| **Arrival Time**      | Time at which a signal reaches a timing endpoint                  |
| **Required Time**     | Latest/earliest permissible arrival depending on the timing check |
| **Slack**             | Difference between required and actual timing                     |
| **Setup Time**        | Time data must be stable before the capture clock edge            |
| **Hold Time**         | Time data must remain stable after the capture clock edge         |
| **Clock Jitter**      | Variation in clock edge timing                                    |
| **Clock Uncertainty** | Timing margin representing clock uncertainty                      |
| **Clock Latency**     | Delay from the clock source to a sequential element               |
| **Clock Skew**        | Difference in clock arrival times between endpoints               |
| **Crosstalk**         | Unwanted electrical coupling between nearby interconnects         |
| **CTS**               | Clock Tree Synthesis                                              |
| **ECO**               | Engineering Change Order                                          |

---

#  Why Clock Tree Quality Matters

In a synchronous digital circuit, many flip-flops depend on the same clock.

If the clock reaches different flip-flops at significantly different times, the available timing margin can change.

Therefore, clock-tree design must consider:

* Balanced clock distribution
* Controlled skew
* Acceptable clock latency
* Good transition
* Controlled fanout
* Signal integrity
* Setup timing
* Hold timing

---
#  Conclusion

SKY130 Module 4 provided hands-on understanding of **pre-layout timing analysis, timing modeling, Static Timing Analysis (STA), Clock Tree Synthesis (CTS), and real-clock timing analysis**.

The module progressed from **standard-cell LEF and timing-library preparation** to **delay-table analysis**, followed by **ideal-clock STA using OpenSTA**. Setup timing, clock jitter, clock uncertainty, synthesis optimization, and timing ECO techniques were studied to understand and improve timing performance.

The module then introduced **Clock Tree Synthesis using TritonCTS**, including clock buffering, H-Tree concepts, clock skew, crosstalk, shielding, and CTS verification. Finally, **real-clock setup and hold analysis** was performed using OpenSTA to understand the impact of the physical clock network on overall timing.

Overall, this module demonstrated how **standard-cell timing, synthesis, clock distribution, and physical implementation are closely connected in achieving timing closure** and provided a strong foundation for advanced physical-design and RTL-to-GDSII workflows.

```

