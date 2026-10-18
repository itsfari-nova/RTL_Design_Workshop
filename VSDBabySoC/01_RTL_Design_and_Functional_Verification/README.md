# RTL Design and Functional Verification

This section showcases my hands-on experience with **RTL design, functional simulation, waveform debugging, logic synthesis, and design hierarchy analysis** using open-source EDA tools, including **Icarus Verilog, GTKWave, and Yosys**.

Through these experiments, I explored how Verilog designs are developed, tested, synthesized, and visualized, gaining practical insight into the digital hardware design and verification workflow.

## Experiments Covered

1. **Good MUX — RTL Simulation and Synthesis**
2. **Multiple Verilog Modules — Synthesis and Hierarchical Visualization**
3. **BabySoC — RTL Exploration and Pre-Synthesis Simulation**
4. **Testbench Execution and Functional Waveform Analysis**

---

# 1. Good MUX — RTL Simulation and Synthesis

## Objective

To understand the implementation and verification of a 2:1 multiplexer, from writing Verilog RTL and developing a testbench to simulating input combinations and examining the synthesized circuit.

### 1.1 Testbench Development

A dedicated testbench was used to instantiate the `good_mux` module and apply different combinations of input and select signals. A Value Change Dump (VCD) file was generated to capture signal transitions throughout the simulation.

```verilog
$dumpfile("tb_good_mux.vcd");
$dumpvars(0, tb_good_mux);
```

**Signals monitored:**
- `i0` — First data input
- `i1` — Second data input
- `sel` — Select signal
- `y` — Multiplexer output

![MUX Testbench](https://github.com/user-attachments/assets/0b43588a-9301-4d53-b7bd-2f684ac3df32)

### 1.2 RTL Simulation

The design and testbench were compiled using Icarus Verilog, and the generated simulation executable was run to evaluate the multiplexer’s behavior.

```bash
iverilog -o tb_good_mux.out good_mux.v tb_good_mux.v
vvp tb_good_mux.out
```

The simulation produces `tb_good_mux.vcd`, which records the signal activity for subsequent waveform inspection.

### 1.3 Waveform Analysis Using GTKWave

The VCD output was opened in GTKWave to inspect the relationship between the data inputs, select signal, and output.

```bash
gtkwave tb_good_mux.vcd
```

The waveform was examined to verify that the output follows the selected input:

- When `sel = 0`, the output follows `i0`.
- When `sel = 1`, the output follows `i1`.

![MUX Simulation Waveform](https://github.com/user-attachments/assets/c3891025-f081-420f-ab91-4bb77497758d)

### 1.4 Logic Synthesis and Netlist Visualization

Yosys was used to process the RTL description and synthesize the multiplexer into a hardware-oriented representation. The synthesized circuit was then visualized to inspect its logic structure and connectivity.

```bash
vi good_mux.v
```

Within the Yosys environment, the design can be displayed using:

```tcl
show
```

![Synthesized MUX Schematic](https://github.com/user-attachments/assets/55fc92ae-4960-4d35-8466-25fbbcadb7a1)

### Key Observation

The RTL simulation demonstrates the expected selection behavior, while the synthesized schematic provides a visual understanding of how the multiplexer is represented after logic synthesis.

---

# 2. Multiple Verilog Modules — Synthesis and Hierarchical Visualization

## Objective

To investigate how separate Verilog modules can be instantiated and interconnected to form a larger digital design, and how synthesis tools represent these relationships.

### 2.1 Synthesis Analysis

Yosys was used to process the design, optimize its logic, and examine the synthesis results. The synthesis reports and schematic views help illustrate how RTL descriptions are transformed into a circuit representation.

![Synthesis Results](https://github.com/user-attachments/assets/3e43f919-d01a-4d49-94e2-035b79b672a3)

### 2.2 Synthesized Circuit Visualization

The following Yosys command generates a schematic representation of the current design:

```tcl
show
```

This view helps identify the connections between signals and logic elements, making the synthesized design easier to inspect.

![Synthesized Circuit View](https://github.com/user-attachments/assets/8bddff3a-6de4-4e15-b8c5-179cf999675c)

### 2.3 Module Hierarchy and Interconnections

The multi-module design was visualized using:

```tcl
show multiple_modules
```

The schematic illustrates the relationship between the two submodules and the signals connecting them.

**Design elements examined:**
- Inputs: `a`, `b`, and `c`
- First submodule: `submodule1`
- Intermediate connection: `net1`
- Second submodule: `submodule2`
- Final output: `y`

![Multiple Module Hierarchy](https://github.com/user-attachments/assets/e202bfd0-5bba-4dc6-bc4c-8905c441dfe5)

### Key Observation

This experiment demonstrates the importance of modular RTL design. Individual modules can be developed independently and connected through well-defined signals to build more complex digital systems.

---

# 3. BabySoC — RTL Exploration and Pre-Synthesis Simulation

## Objective

To explore the RTL architecture of BabySoC, understand its major functional blocks, and perform pre-synthesis simulation to examine the interaction between the processor and peripheral interfaces.

The design integrates multiple components, including:

- **RVMYTH** — RISC-V processor core
- **PLL** — Phase-locked loop interface
- **DAC** — Digital-to-analog converter interface
- **Clock-gating logic**
- Supporting modules and testbench infrastructure

### 3.1 Exploring Design Hierarchy

The design structure was examined using Yosys. Netlist export and hierarchy operations were used to investigate how the modules are represented and interconnected.

```tcl
write_verilog -noattr multiple_modules_netlist.v
flatten
show multiple_modules
```

The `flatten` command brings the instantiated hierarchy into a flattened design representation. The schematic can then be used to study the overall connectivity.

![Design Hierarchy Exploration](https://github.com/user-attachments/assets/34b6b0b0-04dd-4268-a148-37bfbf604f5c)

### 📁 3.2 Exploring BabySoC RTL Source Files

The source directory contains the Verilog files responsible for implementing the system’s major components.

| RTL file | Purpose |
|---|---|
| `avsdac.v` | DAC module |
| `avspll.v` | PLL module |
| `clk_gate.v` | Clock-gating logic |
| `pseudo_rand.v` | Pseudorandom logic |
| `pseudo_rand_gen.v` | Pseudorandom generation |
| `rvmyth.v` | RVMYTH processor core |
| `rvmyth_gen.v` | Processor-related supporting logic |
| `testbench.v` | Simulation testbench |
| `vsdbabysoc.v` | BabySoC top-level design |

![BabySoC RTL Source Files](https://github.com/user-attachments/assets/2e0aca5c-4208-44ce-aec9-b8f0d9fedc6f)

### 3.3 Understanding the RVMYTH Processor

The `rvmyth.v` source file contains the RVMYTH processor implementation. Its internal logic includes clock and reset handling, program-counter operation, instruction fetch and decode functionality, and instruction execution.

Examining the processor RTL provides insight into how a processor core operates within a larger system-on-chip design.

![RVMYTH RTL Exploration](https://github.com/user-attachments/assets/b10b537f-a770-4601-a017-74575d843095)

---

# 4. Testbench Inspection and Verification Setup

Before simulation, the testbench was inspected to identify the top-level module declaration and the included source dependencies.

The following commands were used:

```bash
grep -n "module vsdbabysoc" testbench.v
grep -n "include" testbench.v
```

These checks help locate the relevant module declaration and source include directives before compiling the design.

---

# 5. BabySoC Pre-Synthesis Simulation

## Compilation and Execution

The BabySoC RTL and testbench were compiled with Icarus Verilog in pre-synthesis simulation mode.

```bash
iverilog -o pre_synth_sim.out -DPRE_SYNTH_SIM -I ./include testbench.v
```

**Command breakdown:**

| Option | Purpose |
|---|---|
| `iverilog` | Compiles the Verilog design |
| `-o pre_synth_sim.out` | Specifies the simulation executable |
| `-DPRE_SYNTH_SIM` | Defines the pre-synthesis simulation mode |
| `-I ./include` | Adds the include-file search directory |
| `testbench.v` | Provides the testbench source |

Run the compiled simulation:

```bash
./pre_synth_sim.out
```

The testbench generates the waveform file `pre_synth_sim.vcd`, provided that VCD dumping is enabled in the testbench.

![BabySoC Pre-Synthesis Simulation](https://github.com/user-attachments/assets/3a7f07f8-7bf7-4246-8833-0f3a1cdea093)

---

# 6. Waveform Inspection with GTKWave

The generated VCD file was opened in GTKWave to inspect the activity of the BabySoC signals over simulation time.

```bash
gtkwave pre_synth_sim.vcd
```

**Key signals examined:**

- `CLK` — Clock signal
- `reset` — Reset control
- `REF` — Reference input
- `ENb_CP` — Charge-pump enable control
- `ENb_VCO` — Voltage-controlled oscillator enable control
- `VCO_IN` — VCO-related input
- `VREFH` — Reference voltage signal
- `RV_TO_DAC` — Processor-to-DAC data bus
- `OUT` — Design output

![BabySoC Waveform Overview](https://github.com/user-attachments/assets/f5b189dc-4cd7-4567-bb82-29cf62871092)

### Detailed Bus Analysis

The `RV_TO_DAC` bus was expanded into individual bits to make its transitions easier to inspect:

`RV_TO_DAC[9:0]`

This view helps analyze the processor’s digital output interface and its relationship with the DAC input during simulation.

![RV_TO_DAC Bus Analysis](https://github.com/user-attachments/assets/7041a770-d09d-4d46-b4d6-6d9d7e7cbf05)

### Key Observation

GTKWave provides a time-based view of signal transitions, making it easier to inspect control signals, data buses, and their interactions. These observations help assess whether the RTL behaves as expected under the applied testbench stimulus.

---

# 7. Command Reference

### Good MUX

```bash
vi good_mux.v
iverilog -o tb_good_mux.out good_mux.v tb_good_mux.v
vvp tb_good_mux.out
gtkwave tb_good_mux.vcd
```

Yosys visualization:

```tcl
show
```

### Multiple Modules

```tcl
show
show multiple_modules
```

### BabySoC Hierarchy and Netlist Export

```tcl
write_verilog -noattr multiple_modules_netlist.v
flatten
show multiple_modules
write_verilog -noattr multiple_modules_netlist2.v
```

### BabySoC Testbench and Simulation

```bash
grep -n "module vsdbabysoc" testbench.v
grep -n "include" testbench.v
iverilog -o pre_synth_sim.out -DPRE_SYNTH_SIM -I ./include testbench.v
./pre_synth_sim.out
gtkwave pre_synth_sim.vcd
```

*Note: Run commands from the directory containing the relevant files, and adjust source paths or include directories to match your project structure.*

---

# Overall Learning Outcomes

These experiments provided practical exposure to key stages of the digital hardware design workflow:

- Writing and simulating Verilog RTL
- Developing testbenches to exercise design behavior
- Generating and inspecting VCD waveform data
- Using GTKWave for signal-level debugging
- Applying Yosys to synthesize and visualize logic
- Understanding module instantiation and hierarchical connectivity
- Exploring a processor-based SoC through its RTL files
- Inspecting pre-synthesis behavior using a dedicated testbench

## RTL Design Workflow

**RTL Development → Testbench Creation → Functional Simulation → Waveform Analysis → Logic Synthesis → Netlist Visualization**

Through this work, I strengthened my understanding of RTL design and functional verification while gaining hands-on experience with open-source digital design tools.

