# RTL Design & VLSI Physical Design Workshop

<p align="center">
  <img src="https://img.shields.io/badge/RTL-Verilog-blue?style=for-the-badge" alt="RTL Verilog">
  <img src="https://img.shields.io/badge/Simulation-Icarus%20Verilog%20%7C%20GTKWave-orange?style=for-the-badge" alt="Simulation tools">
  <img src="https://img.shields.io/badge/Synthesis-Yosys-purple?style=for-the-badge" alt="Yosys">
  <img src="https://img.shields.io/badge/Technology-SKY130-green?style=for-the-badge" alt="SKY130">
  <img src="https://img.shields.io/badge/Physical%20Design-OpenLane%20%7C%20OpenROAD-red?style=for-the-badge" alt="Physical design">
  <img src="https://img.shields.io/badge/Timing%20Analysis-OpenSTA-black?style=for-the-badge" alt="OpenSTA">
</p>

<p align="center">
  <b>My hands-on journey from Verilog RTL design and logic synthesis to ASIC physical implementation and static timing analysis.</b>
</p>

<p align="center">
  <a href="#-about-the-repository">About</a> •
  <a href="#-repository-roadmap">Repository Roadmap</a> •
  <a href="#-rtl-design--synthesis">RTL Design</a> •
  <a href="#-asic-physical-design">Physical Design</a> •
  <a href="#-vsdbabysoc-project">VSDBabySoC</a> •
  <a href="#-tools--technologies">Tools</a>
</p>

---

## About the Repository

Welcome to my **RTL Design Workshop repository**!

I created this repository to document my practical learning experience in **VLSI design, Verilog HDL, digital circuit simulation, logic synthesis, standard-cell libraries, ASIC physical design, and static timing analysis**.

Throughout this workshop, I explored how a digital design progresses from RTL code to a synthesized netlist and then through physical implementation and timing evaluation. I documented my experiments, concepts, terminal outputs, circuit visualizations, waveforms, and implementation results in individual module and project folders.

This repository brings together my RTL design exercises, VLSI learning notes, physical design modules, and my VSDBabySoC implementation work in one place.

## What I Explored

- Writing and understanding Verilog RTL.
- Simulating digital circuits and examining waveforms.
- Synthesizing RTL designs using Yosys.
- Exploring hierarchical and flat synthesis.
- Understanding standard-cell libraries and technology mapping.
- Studying combinational and sequential logic optimization.
- Comparing RTL simulation with gate-level simulation.
- Understanding CMOS inverter design and characterization.
- Exploring floorplanning, placement, power planning, and routing.
- Learning static timing analysis and clock tree synthesis.
- Working with the VSDBabySoC design and multi-corner timing analysis.

---

## Repository Roadmap

The repository is organized into RTL learning modules, physical design modules, a VSDBabySoC project, and VSD VLSI learning notes.

| Section | Focus |
|---|---|
| [Module 1](#-rtl-design--synthesis) | RTL simulation, synthesis, and standard-cell libraries |
| [Module 2](#-rtl-design--synthesis) | Multi-module designs, flip-flops, and synthesis optimization |
| [Module 3](#-rtl-design--synthesis) | Combinational and sequential logic optimization |
| [Module 4](#-rtl-design--synthesis) | Gate-level simulation and synthesis–simulation mismatch |
| [Module 5](#-rtl-design--synthesis) | Conditional constructs and loops in Verilog |
| [Physical Design Modules](#-asic-physical-design) | ASIC implementation, CMOS design, timing, and routing |
| [VSDBabySoC](#-vsdbabysoc-project) | RTL, synthesis, physical implementation, and timing analysis |
| [VSD VLSI](#-vsd-vlsi-learning-notes) | VLSI fundamentals and design-flow learning notes |

---

## RTL Design & Synthesis

These modules document my hands-on experiments with Verilog RTL, simulation, synthesis, optimization, and gate-level verification.

### 🔹 Module 1 — Introduction to RTL and Synthesis

I explored the fundamentals of RTL simulation, the role of testbenches, synthesis using Yosys, and the information available inside standard-cell library files.

| Day | Topics | Documentation |
|---|---|---|
| Day 1 | 2:1 MUX, Verilog simulation, Icarus Verilog, GTKWave | [📘 Open Day 1](Module%201/Day1/README.md) |
| Day 2 | RTL-to-netlist synthesis, Yosys, `.lib` files, cell flavors | [📘 Open Day 2](Module%201/Day2/README.md) |
| Day 3 | SKY130 standard-cell library and AND-cell exploration | [📘 Open Day 3](Module%201/Day3/README.md) |

### 🔹 Module 2 — RTL Structure and Sequential Logic

This module covers multi-module RTL designs, hierarchical and flat synthesis, flip-flop coding styles, and synthesis optimizations.

| Day | Topics | Documentation |
|---|---|---|
| Day 1 | Multiple modules and hierarchical vs. flat synthesis | [📘 Open Day 1](Module%202/Day1/README.md) |
| Day 2 | Flip-flops, synchronous reset, asynchronous reset, and set | [📘 Open Day 2](Module%202/Day2/README.md) |
| Day 3 | Arithmetic RTL and synthesis optimizations | [📘 Open Day 3](Module%202/Day3/README.md) |

### 🔹 Module 3 — Logic Optimization

I explored how synthesis tools simplify digital logic and optimize sequential circuits while preserving the required behavior.

| Day | Topics | Documentation |
|---|---|---|
| Day 1 | Combinational logic optimization and Boolean simplification | [📘 Open Day 1](Module%203/Day1/README.md) |
| Day 2 | Sequential logic optimization and constant DFF inputs | [📘 Open Day 2](Module%203/Day2/README.md) |
| Day 3 | Counter optimization and unused sequential logic | [📘 Open Day 3](Module%203/Day3/README.md) |

### 🔹 Module 4 — Gate-Level Simulation

This module focuses on validating synthesized hardware and understanding how Verilog coding styles can cause differences between RTL simulation and synthesized behavior.

| Day | Topics | Documentation |
|---|---|---|
| Day 1 | Gate-level simulation and ternary-operator-based MUX | [📘 Open Day 1](Module%204/Day1/README.md) |
| Day 2 | Blocking and non-blocking assignments, synthesis–simulation mismatch | [📘 Open Day 2](Module%204/Day2/README.md) |

### 🔹 Module 5 — Verilog Constructs

I explored conditional statements and looping constructs used to describe decision-making logic and repeated hardware structures.

| Day | Topics | Documentation |
|---|---|---|
| Day 1 | `if`, `case`, incomplete assignments, and latch inference | [📘 Open Day 1](Module%205%20/Day1/README.md) |
| Day 2 | `for` loops and generate loops | [📘 Open Day 2](Module%205%20/Day2/README.md) |

---

## 🏗️ ASIC Physical Design

The physical design modules document my exploration of the stages involved in transforming a synthesized digital circuit into a physical layout.

### 🔸 Physical Design Module 1 — ASIC Implementation Flow

I explored the ASIC physical design flow using OpenLane and the SKY130 PDK, including floorplanning, power planning, placement, routing, timing analysis, and physical verification.

**[📂 Explore Physical Design Module 1](PD_Module%201/README.md)**

### 🔸 Physical Design Module 2 — Floorplanning and Power Integrity

This module covers core and die dimensions, utilization, aspect ratio, placement, power planning, library characterization, clock tree synthesis, and standard-cell design concepts.

**[📂 Explore Physical Design Module 2](PD_Module%202/README.md)**

### 🔸 Physical Design Module 3 — CMOS Inverter Design

I explored CMOS inverter design, SPICE simulation, transient response, voltage transfer characteristics, transistor sizing, SKY130 standard-cell layout, post-layout simulation, and CMOS fabrication.

**[📂 Explore Physical Design Module 3](PD_Module%203/README.md)**

### 🔸 Physical Design Module 4 — Timing Analysis and CTS

This module focuses on pre-layout timing analysis, standard-cell timing models, setup timing, clock uncertainty, timing optimization, clock tree synthesis, and signal integrity.

**[📂 Explore Physical Design Module 4](PD_Module%204/README.md)**

### 🔸 Physical Design Module 5 — Routing and Verification

I explored routing, Power Distribution Network (PDN) implementation, detailed routing, Design Rule Checking (DRC), parasitic extraction, and post-route static timing analysis.

**[📂 Explore Physical Design Module 5](PD_Module%205/README.md)**

---

## 🔬 VSDBabySoC Project

The `VSDBabySoC` directory brings together my work on the BabySoC design, from RTL exploration and functional verification through synthesis, physical implementation, and timing analysis.

| Stage | What it covers | Documentation |
|---|---|---|
| 1. RTL Design & Functional Verification | RTL exploration, simulation, testbenches, and waveform analysis | [📘 Explore Stage 1](VSDBabySoC/01_RTL_Design_and_Functional_Verification/README.md) |
| 2. Logic Synthesis & Gate-Level Verification | Yosys synthesis, SKY130 technology mapping, and GLS | [📘 Explore Stage 2](VSDBabySoC/02_Logic_Synthesis_and_Gate_Level_Verification/README.md) |
| 3. ASIC Physical Implementation | Physical design implementation and timing evaluation | [📘 Explore Stage 3](VSDBabySoC/03_ASIC_Physical_Implementation/README.md) |
| 4. Multi-Corner Static Timing Analysis | OpenSTA, PVT corners, setup and hold checks, and timing reports | [📘 Explore Stage 4](VSDBabySoC/04_Multi_Corner_Static_Timing_Analysis/README.md) |

**[📂 Open the VSDBabySoC project overview](VSDBabySoC/README.md)**

---

## VSD VLSI Learning Notes

This section contains my VSD VLSI learning notes, covering introductory VLSI concepts, the design flow, simulation, synthesis, and related practical exercises.

- **Week 1:** [📘 Explore Week 1](VSD_VLSI/Week%201/README.md)
- **Week 2:** [📘 Explore Week 2](VSD_VLSI/Week%202/README.md)

---

## Tools & Technologies

The repository documents experiments involving the following tools and technologies.

| Tool / Technology | Purpose |
|---|---|
| Verilog HDL | RTL design and digital hardware description |
| Icarus Verilog | RTL and gate-level simulation |
| GTKWave | Waveform visualization and debugging |
| Yosys | Logic synthesis and netlist generation |
| SKY130 PDK | Open-source semiconductor process design kit |
| Standard-cell Liberty files | Cell functionality and timing characterization |
| OpenLane | Automated ASIC implementation flow |
| OpenROAD | Physical design implementation |
| OpenSTA | Static timing analysis |
| Magic | Layout inspection and physical verification workflows |
| Netgen | Layout-versus-schematic comparison |
| NGSPICE | Circuit-level simulation |
| Linux and Tcl | Command-line workflows and design-flow scripting |

*The exact tool used depends on the experiment and module.*

---

## My Learning Journey

Through these practical exercises, I explored how different stages of digital IC design connect to one another.

**RTL Design → Functional Simulation → Logic Synthesis → Gate-Level Verification → Physical Design → Static Timing Analysis**

I also learned the importance of examining waveforms, understanding synthesized netlists, exploring standard-cell libraries, and interpreting timing reports rather than relying only on RTL code.

Each module represents a step in my continued development of practical VLSI design and implementation skills.

---

## About Me

I am an Electronics and Communication Engineering student interested in **VLSI design, digital electronics, RTL development, and semiconductor implementation**.

This repository serves as a record of my workshop experiments, technical learning, and hands-on exploration of open-source EDA tools.

I continue to build my understanding by documenting each experiment, reviewing results, and connecting theoretical concepts with practical design workflows.

---

<p align="center">
  ⭐ <b>Learn. Design. Simulate. Synthesize. Implement.</b> ⭐
</p>

<p align="center">
  <i>“Every layer of silicon begins with a deeper understanding of the design.”</i>
</p>
