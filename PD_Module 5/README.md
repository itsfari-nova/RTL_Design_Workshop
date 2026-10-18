# PHYSICAL DESIGN Module 5
## Advanced Physical Design: Routing, PDN, DRC & Parasitic Extraction
<p align="left">
  <img src="https://img.shields.io/badge/Flow-OpenLane%20%7C%20OpenROAD-green" alt="OpenLane / OpenROAD">
  <img src="https://img.shields.io/badge/Routing-TritonRoute-blueviolet" alt="TritonRoute">
  <img src="https://img.shields.io/badge/Timing-OpenSTA-orange" alt="OpenSTA">
  <img src="https://img.shields.io/badge/Technology-SKY130-blue" alt="SKY130">
  <img src="https://img.shields.io/badge/Verification-DRC-yellow" alt="Design Rule Checking">
  <img src="https://img.shields.io/badge/Formats-LEF%20%7C%20DEF%20%7C%20SPEF-red" alt="LEF / DEF / SPEF">
  <img src="https://img.shields.io/badge/Layout-Magic-lightgrey" alt="Magic">
</p>

> **VLSI Physical Design Training | SKY130 PDK | Module 5**

## Overview

This module explores the routing and verification stages of the VLSI physical-design flow using the SKY130 process-design kit. It covers routing fundamentals, maze-based pathfinding, Power Distribution Network (PDN) implementation, Design Rule Checking (DRC), TritonRoute detailed routing, interconnect parasitic extraction, and post-route Static Timing Analysis (STA).

The module connects the concepts of physical implementation with the tools and intermediate design formats used to transform a placed circuit into a physically routed layout.

| Category | Details |
|---|---|
| **EDA Tools** | OpenLane, OpenROAD, TritonRoute, OpenSTA, Yosys, Magic |
| **Technology** | SKY130 PDK |
| **Design Formats** | LEF, DEF, SPEF |
| **Scripting & Environment** | Tcl, Docker |
| **Core Topics** | Routing, PDN, DRC, parasitic extraction, timing analysis |
| **Prerequisites** | Configured OpenLane/OpenROAD environment, SKY130 PDK, and a design prepared for physical implementation |

## Table of Contents

1. [Physical Routing Fundamentals](#1️⃣-physical-routing-fundamentals)
2. [Maze Routing Using Lee's Algorithm](#2️⃣-maze-routing-using-lees-algorithm)
3. [Power Distribution Network](#3️⃣-power-distribution-network-pdn)
4. [Design Rule Checking](#4️⃣-design-rule-checking-drc)
5. [TritonRoute Detailed Routing](#5️⃣-tritonroute-detailed-routing)
6. [Routing Connectivity and Topology](#6️⃣-routing-connectivity-and-topology)
7. [Parasitic Extraction and SPEF](#7️⃣-parasitic-extraction-and-spef-generation)
8. [OpenLane Physical Implementation](#8️⃣-openlane-physical-implementation)
9. [Post-Route Timing Analysis](#9️⃣-post-route-timing-analysis)
10. [End-to-End RTL-to-GDSII Flow](#🔟-end-to-end-rtl-to-gdsii-flow)
11. [Tools and Technologies](#1️⃣1️⃣-tools-and-technologies)
12. [Key Learnings](#-key-learnings)
13. [Author](#-author)

---

## 1️⃣ Physical Routing Fundamentals

### 1.1 Purpose of Routing

Physical routing establishes electrical connections between standard cells, macros, and input/output terminals through the available metal layers. A successful routing solution must provide complete connectivity while respecting technology-specific design rules and making efficient use of routing resources.

The process typically includes global routing, route-guide generation, detailed routing, and physical verification.

<img width="1666" height="790" alt="Physical routing overview" src="https://github.com/user-attachments/assets/630ae945-356e-44a6-9bda-23ee3480dc1c" />

### 1.2 Global Routing and Detailed Routing

Physical routing is divided into two major stages, each addressing a different level of implementation detail.

**Global routing** estimates the paths that nets should follow across the routing region. It divides the region into a resource grid, estimates available capacity, identifies congestion, and generates routing guides for subsequent implementation.

**Detailed routing** converts these guides into physical wire segments and vias. It considers exact geometry, legal metal-layer usage, pin access, obstacles, and detailed design-rule constraints.

| Attribute | Global Routing | Detailed Routing |
|---|---|---|
| Primary objective | Determine approximate net paths | Establish actual electrical connections |
| Routing representation | Resource grid and route guides | Physical wires and vias |
| Main output | Routing guides | Detailed routing geometry |
| Key concern | Congestion and resource allocation | Connectivity and design-rule compliance |
| Implementation detail | Coarse-grained | Fine-grained |

### 1.3 Fast Routing and Guide Generation

Fast-routing techniques help establish an initial routing solution and produce guides that direct the detailed router. These guides define preferred routing regions and help the implementation tools coordinate resource allocation between routing stages.

| Stage | Function |
|---|---|
| **Fast / Global Routing** | Estimates net paths and creates routing guides |
| **Detailed Routing** | Implements physical wires and vias using the guides while satisfying detailed routing constraints |

<img width="1548" height="828" alt="Routing guide generation" src="https://github.com/user-attachments/assets/3c6cf241-2146-4ce6-933b-1b1607a1f793" />

---

## 2️⃣ Maze Routing Using Lee's Algorithm

Lee's Algorithm is a grid-based pathfinding method used to explain how a valid route can be found between two points while avoiding obstacles. It explores the available grid through wavefront expansion, records the distance from the source, and reconstructs a route by tracing back from the destination.

### 2.1 Algorithm Procedure

1. Initialize the source grid location.
2. Expand the search into adjacent, accessible grid cells.
3. Assign distance labels to the explored cells.
4. Exclude blocked cells and routing obstacles.
5. Continue the expansion until the destination is reached or no valid path remains.
6. Backtrack through the distance labels to reconstruct the shortest path.
7. Record the resulting route.

<img width="1653" height="822" alt="Lee's maze routing algorithm" src="https://github.com/user-attachments/assets/5bcb9ea8-8663-4d5c-8293-59b965e2ceb2" />

### 2.2 Strengths and Limitations

**Advantages**

- Systematically explores the routing grid.
- Finds a shortest valid path in an unweighted grid when a path exists.
- Avoids blocked locations during exploration.
- Provides a useful foundation for understanding maze-based routing.

**Limitations**

- May consume substantial memory on large grids.
- Can explore many cells that are not part of the final route.
- Does not inherently optimize congestion, electrical delay, or metal-layer preferences.
- Requires additional cost functions and optimization strategies for practical VLSI routing.

---

## 3️⃣ Power Distribution Network (PDN)

### 3.1 PDN Architecture and Objectives

The Power Distribution Network delivers supply voltage and ground connections to the standard cells and macros throughout the chip. Its structure must support current demand while limiting voltage drop and maintaining reliable electrical connectivity.

The primary objectives are to:

- Distribute power and ground throughout the core.
- Connect standard-cell rails and macro power pins to the supply network.
- Reduce voltage drop along power-delivery paths.
- Provide adequate current-carrying capacity.
- Establish continuous connections between the power source and circuit components.

Typical PDN structures include power and ground pins, rings, straps, standard-cell rails, macro power connections, and vias between metal layers.

### 3.2 PDN Generation Workflow

A simplified PDN construction sequence is shown below:

```text
Define Power and Ground Nets
             |
             v
Configure PDN Layers and Geometry
             |
             v
Generate Power Rings (if configured)
             |
             v
Create Power Straps
             |
             v
Connect Standard-Cell Rails
             |
             v
Connect Macro Power Pins
             |
             v
Verify Power-Network Connectivity
```

In OpenROAD-based flows, power-network generation is controlled by Tcl configuration and technology-specific settings. The `pdngen` command executes the configured PDN-generation process.

The actual rings, straps, layers, widths, and connections depend on the design configuration and the capabilities of the selected technology.

<img width="1262" height="847" alt="PDN generation and power planning" src="https://github.com/user-attachments/assets/4ea6ea50-6fcc-41bb-bca7-e125f4270eee" />

### 3.3 Power Straps and Cell-Level Connections

**Power straps** are wider metal conductors that distribute supply and ground across the core. They connect to the broader PDN through designated metal layers and vias.

**Standard cells** receive supply connections through their power pins and the rails associated with their placement rows. These rails must be connected to the higher-level PDN.

**Macros**, such as memory blocks or other hard IP, may contain dedicated power pins that require appropriate connections to the chip-level supply network.

Important PDN design considerations include metal-layer selection, strap dimensions, spacing, via placement, current demand, voltage drop, macro locations, and supply-pin connectivity.

---

## 4️⃣ Design Rule Checking (DRC)

Design Rule Checking verifies whether physical layout geometry satisfies the manufacturing constraints defined by the target process technology. These rules help ensure that the layout can be fabricated according to the required geometric specifications.

### 4.1 Common Physical Design Rules

Typical checks include:

- Minimum metal width.
- Minimum spacing between wires and vias.
- Via enclosure and metal-area requirements.
- Layer-specific geometric restrictions.
- Overlap and spacing constraints.
- Connectivity-related checks supported by the verification tool.

A violation occurs when layout geometry fails one or more applicable rules. The identified violations must be investigated and corrected according to the requirements of the design and verification flow.

### 4.2 Minimum Wire-Width Verification

Each metal layer has technology-defined width requirements. A conductor narrower than the permitted minimum may violate fabrication rules and affect interconnect reliability.

<img width="865" height="406" alt="DRC wire-width example" src="https://github.com/user-attachments/assets/221df795-50eb-4b06-aa01-7efde6b247eb" />

### 4.3 Via-Spacing Verification

Vias connect conductors across different metal layers. Their placement must satisfy the specified spacing and enclosure rules to avoid manufacturing violations and unintended electrical shorts.

<img width="847" height="422" alt="DRC via-spacing example" src="https://github.com/user-attachments/assets/768aeb48-1c2b-4366-a976-0e66f08f7e76" />

### 4.4 Interpreting a DRC-Clean Result

A DRC-clean result indicates that the layout satisfies the set of design rules checked under the selected verification conditions.

However, DRC cleanliness alone does not establish that the design also meets timing requirements, has complete electrical connectivity, passes antenna checks, or satisfies all other physical sign-off criteria. These require their respective verification steps.

<img width="1243" height="587" alt="DRC layout verification" src="https://github.com/user-attachments/assets/3b3f5884-415b-4b44-8c65-abd10da8ad32" />

---

## 5️⃣ TritonRoute Detailed Routing

### 5.1 Detailed Routing Engine

TritonRoute is the detailed-routing engine used in OpenROAD-based physical implementation flows. It converts routing guides into physical interconnect geometry while considering pin accessibility, obstacles, metal-layer restrictions, and detailed design rules.

Its responsibilities include:

- Processing global-routing guides.
- Identifying accessible pin locations.
- Creating wire segments and vias.
- Navigating routing obstacles.
- Resolving routing conflicts.
- Improving routing quality while maintaining connectivity and design-rule compliance.

A simplified representation of the process is:

```text
Global Routing
      |
      v
Route Guide Generation
      |
      v
TritonRoute Detailed Routing
      |
      v
Wire and Via Construction
      |
      v
Routing Verification and Optimization
      |
      v
Post-Route Implementation
```

An illustrative OpenROAD command for this stage is `detailed_route`. The actual command options and execution sequence depend on the installed version and the flow configuration.

<img width="1471" height="543" alt="TritonRoute detailed routing" src="https://github.com/user-attachments/assets/88e06beb-0b6b-4b02-92e2-bab8309a78b1" />

<img width="1600" height="953" alt="Detailed routing tool output" src="https://github.com/user-attachments/assets/a3eaaa84-156d-4c64-bec6-7c5f3ce6116c" />

### 5.2 Route-Guide Preprocessing

Route guides identify preferred regions in which individual nets should be routed. They are produced during global routing and guide the detailed router toward paths selected during earlier optimization.

Effective route guides help:

- Direct the router toward allocated routing resources.
- Coordinate global and detailed routing decisions.
- Reduce unnecessary path exploration.
- Respect routing-layer direction and geometric constraints.
- Improve the efficiency of detailed routing.

Guide dimensions and routing directions are interpreted according to the relevant router and technology conventions; exact constraints depend on the flow implementation.

<img width="1296" height="837" alt="Route-guide preprocessing" src="https://github.com/user-attachments/assets/5c6913f1-a94e-43e4-9787-e8bd15956e91" />

<img width="1600" height="948" alt="Routing guide visualization" src="https://github.com/user-attachments/assets/dc1e8be6-618b-466f-b258-f9fbcf776703" />

<img width="1600" height="951" alt="Routing guide details" src="https://github.com/user-attachments/assets/5d1ade17-624e-4332-8f14-6bd8731ac023" />

### 5.3 Intra-Layer and Inter-Layer Connections

Routing can use the same metal layer for a connection or move between different layers through vias.

| Routing Type | Description |
|---|---|
| **Intra-layer routing** | Establishes wire connections within a single metal layer. |
| **Inter-layer routing** | Connects conductors on different metal layers using vias. |

The router selects suitable layers and connection points according to the technology rules, routing guides, available resources, and physical constraints.

<img width="1342" height="711" alt="Multi-layer routing" src="https://github.com/user-attachments/assets/d3a2fc47-b364-40ec-9596-4332b3eb87d0" />

Example of a multi-layer interconnect:

```text
Source Pin
    |
    v
Metal Layer 1
    |
    v
   Via
    |
    v
Metal Layer 2
    |
    v
   Via
    |
    v
Destination Pin
```

---

## 6️⃣ Routing Connectivity and Topology

### 6.1 Access Points and Access Point Clusters

**Access Point (AP):** a legal candidate location through which a pin or routing segment can connect to a route on an available metal layer.

**Access Point Cluster (APC):** a group of related access points associated with a routing object, such as a pin or a guide-related segment. These groupings help the router evaluate possible connection locations.

Reliable access-point selection is essential for connecting pins without violating geometric constraints.

<img width="1466" height="770" alt="Access points and routing connectivity" src="https://github.com/user-attachments/assets/b6ca48bf-67f9-4a84-9bf3-2c5e6a0888cc" />

### 6.2 Routing Obstacles and Optimization

Routing obstacles restrict the legal locations available for wires and vias. They may include macro blockages, existing interconnects, restricted regions, pin-access limitations, and areas affected by spacing rules.

The router must navigate these restrictions while ensuring that every required terminal of a net is connected. An incomplete connection represents a routing-connectivity problem that must be identified and resolved.

Routing optimization evaluates factors such as:

- Total wirelength.
- Routing congestion.
- Number of vias.
- Design-rule compliance.
- Signal delay and interconnect parasitics.
- Availability of routing resources.
- Connectivity across all required terminals.

<img width="1600" height="951" alt="Routing obstacles and connectivity" src="https://github.com/user-attachments/assets/56df0f41-ff0e-4e71-b900-99b97423cff1" />

<img width="1600" height="950" alt="Routing optimization example" src="https://github.com/user-attachments/assets/35ca128c-19d5-4f28-bffd-0e34ad27be24" />

### 6.3 Routing Topology

Routing topology defines how the terminals of a multi-terminal net are interconnected. A suitable topology provides complete connectivity while managing routing cost, wirelength, congestion, and physical constraints.

After the topology is selected, detailed routing implements the connections using actual wires and vias. The selected topology can influence both routing-resource usage and the parasitic characteristics of the resulting interconnect.

<img width="836" height="415" alt="Routing topology illustration" src="https://github.com/user-attachments/assets/28a669c5-7aaa-431c-85b3-a31d0020e67a" />

---

## 7️⃣ Parasitic Extraction and SPEF Generation

### 7.1 Interconnect Parasitics

Once physical wires and vias have been established, their electrical effects must be considered during timing analysis. Metal interconnect introduces resistance and capacitance, while coupling between neighboring conductors can affect signal behavior.

Parasitic extraction estimates these electrical properties from the physical layout and routing geometry.

<img width="1790" height="806" alt="Parasitic extraction overview" src="https://github.com/user-attachments/assets/9f4e1314-915a-4091-9054-4a13111b6d9c" />

### 7.2 Standard Parasitic Exchange Format (SPEF)

**SPEF** stands for Standard Parasitic Exchange Format. It represents extracted interconnect parasitics, including resistance and capacitance, in a form that timing-analysis tools can consume.

A simplified extraction sequence is:

```text
Routed Physical Design
          |
          v
Interconnect Geometry
          |
          v
Parasitic Extraction
          |
          v
Resistance and Capacitance Models
          |
          v
SPEF Generation
          |
          v
Parasitic-Aware Timing Analysis
```

The extraction process uses the physical design data and technology information required by the chosen extractor. Depending on the flow, inputs may include routed DEF data, technology files, and extraction rules.

<img width="951" height="595" alt="SPEF generation example" src="https://github.com/user-attachments/assets/55e26362-e2cc-45a8-82f3-882e027d695f" />

<img width="1600" height="952" alt="Extracted parasitic data" src="https://github.com/user-attachments/assets/5c0d0e40-24c0-4c7f-a990-be4a7c9bae14" />

---

## 8️⃣ OpenLane Physical Implementation

### 8.1 Floorplanning and Power Planning

Floorplanning establishes the physical foundation of the design. It defines the die and core regions, placement rows, macro locations, and other geometric constraints needed by subsequent implementation stages.

Power planning establishes the supply network within this physical framework, including configured power rings, straps, rails, and connections to cell and macro power pins.

<img width="1247" height="720" alt="Floorplanning and power planning" src="https://github.com/user-attachments/assets/421a8ef1-0340-460c-b2b3-47a15ca4600d" />

### 8.2 OpenLane Configuration

OpenLane configuration settings determine how different implementation stages operate. Depending on the selected flow and version, these settings may control:

- Floorplan dimensions and utilization.
- Placement density and optimization.
- Clock-tree synthesis.
- Routing-layer restrictions.
- Routing and timing optimization.
- Physical verification.
- Layout generation and output handling.

Correct configuration is important because the selected parameters affect physical quality, resource usage, and implementation results.

<img width="1900" height="840" alt="OpenLane configuration parameters" src="https://github.com/user-attachments/assets/73e6d019-9a93-4fca-b285-e9462edb16e9" />

### 8.3 Run Directories and Implementation Outputs

Physical-design flows commonly organize generated artifacts by run and implementation stage. The exact directory structure depends on the tool version and configuration.

A conceptual organization is shown below:

```text
results/
├── synthesis/
├── floorplan/
├── placement/
├── cts/
├── routing/
└── signoff/
```

Typical intermediate and final artifacts may include synthesized netlists, placement data, routed DEF files, timing reports, parasitic files, verification reports, and layout databases. Not every flow generates every artifact in this exact structure.

<img width="1600" height="953" alt="Physical design run directories" src="https://github.com/user-attachments/assets/2472fec6-b465-4fbe-b881-0b2e4fa5e62a" />

---

## 9️ Post-Route Timing Analysis

### 9.1 Static Timing Analysis with OpenSTA

Static Timing Analysis evaluates whether signals can propagate through the design within the required timing limits. OpenSTA can analyze the timing of a design using the appropriate netlist, timing constraints, cell timing libraries, clock definitions, and parasitic information.

For post-route analysis, extracted interconnect parasitics help account for the effects of physical wires and vias on signal delay.

The general analysis sequence is:

```text
Routed Netlist
      |
      v
Timing Constraints (SDC)
      |
      v
Cell Timing Libraries
      |
      v
Extracted Parasitics (SPEF)
      |
      v
OpenSTA Analysis
      |
      v
Arrival Time and Required Time
      |
      v
Setup and Hold Slack
      |
      v
Timing Evaluation
```

<img width="1600" height="956" alt="Post-route timing analysis" src="https://github.com/user-attachments/assets/c746c647-edfd-475e-9354-6f06d62653c4" />

### 9.2 Physical Implementation Verification Checklist

Use this checklist to track the completion and verification of the relevant implementation stages.

- [ ] Power Distribution Network generated and inspected.
- [ ] Global routing completed.
- [ ] Detailed routing completed.
- [ ] Routing connectivity verified.
- [ ] Applicable DRC checks executed and reviewed.
- [ ] Parasitic extraction completed and SPEF generated.
- [ ] Post-route setup and hold timing analyzed.
- [ ] Remaining timing violations investigated.
- [ ] Final physical verification completed.
- [ ] Required final layout files generated.

**Note:** These are sign-off goals, not automatic claims of completion. Mark each item only after confirming the corresponding output and verification results for the actual design run.

---

## End-to-End RTL-to-GDSII Flow

The complete physical-design process connects logical implementation with physical layout generation.

```text
RTL Design
    |
    v
RTL Synthesis
    |
    v
Floorplanning
    |
    v
Power Distribution Network Generation
    |
    v
Standard-Cell Placement
    |
    v
Clock Tree Synthesis
    |
    v
Global Routing
    |
    v
Route-Guide Generation and Preparation
    |
    v
Detailed Routing — TritonRoute
    |
    v
Design Rule Checking
    |
    v
Parasitic Extraction — SPEF
    |
    v
Post-Route Timing Analysis — OpenSTA
    |
    v
Physical Verification and Sign-Off
    |
    v
GDSII Layout Generation
```

Each stage depends on the quality and correctness of the preceding stages. Routing establishes physical connectivity, parasitic extraction characterizes the resulting interconnect, and timing analysis evaluates the design under the specified constraints.

The generation of a final GDSII file is a distinct implementation milestone; complete physical sign-off also depends on the required verification results.

---

## Tools and Technologies

| Tool / Format | Role in Physical Design |
|---|---|
| **OpenLane** | Automates the configured RTL-to-GDSII implementation flow. |
| **OpenROAD** | Performs physical implementation and optimization. |
| **TritonRoute** | Generates detailed routing geometry. |
| **OpenSTA** | Performs static timing analysis. |
| **Yosys** | Synthesizes RTL into a gate-level netlist. |
| **SKY130 PDK** | Provides process technology information for the 130 nm platform. |
| **Magic** | Supports layout inspection and physical verification workflows. |
| **LEF** | Describes technology and physical cell information. |
| **DEF** | Represents physical design information, including placement and routing. |
| **SPEF** | Stores extracted interconnect parasitic information. |
| **Tcl** | Configures and automates implementation commands. |
| **Docker** | Provides a containerized execution environment. |

---

## Key Learnings

Through this module, I explored the major concepts involved in routing and post-route physical verification:

- **Routing fundamentals:** I studied the differences between global routing and detailed routing, including resource allocation and physical wire generation.
- **Maze routing:** I examined Lee's Algorithm, its wavefront-expansion method, obstacle handling, and computational limitations.
- **Power distribution:** I learned how PDN structures use rings, straps, rails, and vias to distribute power and ground.
- **Design rule checking:** I explored wire-width, via-spacing, and other geometric constraints required for manufacturable layouts.
- **TritonRoute:** I studied the role of route guides, detailed wire/via generation, and routing optimization.
- **Connectivity and topology:** I explored access points, routing obstacles, multi-terminal connectivity, and routing-cost considerations.
- **Parasitic extraction:** I learned how interconnect resistance and capacitance are represented in SPEF for timing analysis.
- **Post-route STA:** I studied how OpenSTA uses timing constraints, cell libraries, and parasitic information to evaluate setup and hold timing.
- **Complete implementation flow:** I connected routing, physical verification, parasitic extraction, timing analysis, and final layout generation into an end-to-end physical-design workflow.

## 👤 Author

**Shaik Farida**  
B.Tech — Electronics and Communication Engineering  
Anurag University, Hyderabad

---

<p align="center">
  <i>“Every routed connection brings logical design one step closer to physical silicon.”</i>
</p>
