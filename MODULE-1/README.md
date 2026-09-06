# OpenLane Physical Design Flow – PicoRV32A

## 📌 Project Overview

This project documents a practical **RTL-to-GDSII ASIC implementation flow** using the **PicoRV32A RISC-V processor**, the **OpenLane flow**, and the **Sky130 PDK**.

The work covers the transformation of a digital design from Verilog RTL into a technology-mapped netlist and then into a physical layout. The major implementation stages include synthesis, floorplanning, power planning, placement, clock-tree synthesis, routing, static timing analysis, physical verification, and GDSII generation.

### Complete Flow

```text
RTL Design
    ↓
Logic Synthesis
    ↓
Gate-Level Netlist
    ↓
Floorplanning
    ↓
Power Planning
    ↓
Placement
    ↓
Clock Tree Synthesis
    ↓
Routing
    ↓
Static Timing Analysis
    ↓
Physical Verification
    ↓
Signoff
    ↓
GDSII
```


---

## 1. PicoRV32A Processor

**PicoRV32A** is a compact processor implementation based on the **RISC-V instruction set architecture**. In this project, its Verilog RTL serves as the starting point for the ASIC implementation process.

The processor RTL contains the digital structures required for instruction processing and data manipulation, including:

- Arithmetic and logic circuitry
- Registers and flip-flops
- Multiplexers
- Control circuitry
- Instruction-decoding logic
- Datapath components
- Memory-interface logic

The RTL describes the intended behavior of the processor. During synthesis, this description is converted into technology-specific logic cells that can later be physically placed and connected.

---

## 2. Sky130 PDK

A **Process Design Kit (PDK)** provides the technology information required by EDA tools to implement a circuit for a particular semiconductor process.

This project uses the **SkyWater SKY130** technology.

The PDK supplies information required for both logical and physical implementation, including:

- Standard-cell libraries
- Technology layers
- Cell dimensions
- Timing models
- Physical cell information
- Manufacturing design rules
- Technology-specific layout constraints

Using the PDK, synthesis and physical-design tools can select appropriate standard cells and construct the layout using the available metal and routing layers.

---

## 3. OpenLane

**OpenLane** is an open-source automated flow for taking an RTL design toward a final **GDSII** layout.

It connects several open-source EDA tools so that different ASIC implementation stages can be executed as a coordinated flow.

A simplified OpenLane process is:

```text
RTL
 ↓
Logic Synthesis
 ↓
Floorplanning
 ↓
Placement
 ↓
Clock Tree Synthesis
 ↓
Routing
 ↓
Timing / Signoff Checks
 ↓
GDSII
```

The automated flow reduces the amount of repetitive manual work involved in moving from RTL to physical layout.

---

## 4. RTL Design

**Register Transfer Level (RTL)** is the abstraction used to describe how information is transferred between registers and how the associated combinational and sequential logic behaves.

For the PicoRV32A implementation, Verilog RTL represents the logical design before technology-specific implementation.

```text
Verilog RTL
     ↓
Logic Synthesis
     ↓
Technology-Mapped Netlist
```

At this stage, the design is primarily a logical description rather than a physical representation.

---

## 5. Logic Synthesis

Logic synthesis transforms the RTL description into a **gate-level netlist**.

During this process, the synthesis tool interprets the RTL, performs logic transformations, and maps the resulting logic to cells available in the selected technology library.

```text
RTL Description
      ↓
   Synthesis
      ↓
Technology-Mapped Netlist
```

The generated netlist may contain cells such as:

- AND gates
- OR gates
- NAND gates
- NOR gates
- Inverters
- Buffers
- Multiplexers
- Flip-flops
- Other standard logic cells

### Synthesis Result



---

## 6. Gate-Level Netlist

A **gate-level netlist** describes the circuit after synthesis in terms of library cells and their connections.

It identifies:

- Standard cells used by the implementation
- Connections between cells
- Primary inputs and outputs
- Sequential elements
- Combinational logic

The transformation can be viewed as:

```text
RTL
 ↓
Synthesis
 ↓
Library Cells
 ↓
Cell Interconnections
 ↓
Gate-Level Netlist
```

### Generated Netlist



The synthesized netlist becomes the logical input for the following physical-design stages.

---

## 7. Floorplanning

**Floorplanning** establishes the initial physical organization of the chip.

At this stage, the physical dimensions and placement regions are defined before individual standard cells are positioned.

Important floorplan elements include:

- Die dimensions
- Core dimensions
- Standard-cell placement area
- I/O locations
- Placement boundaries
- Initial physical organization

Floorplan quality affects later stages such as routing congestion, timing, utilization, and total chip area. A poorly planned design can create routing or timing problems that are difficult to resolve later.

---

## 8. Power Distribution Network

The **Power Distribution Network (PDN)** supplies power to the cells across the core.

A typical power network contains structures such as:

- VDD rails
- VSS rails
- Power rings
- Power straps
- Standard-cell power connections

A simplified representation is:

```text
VDD
 │
 ├── Power Ring
 │
 ├── Power Straps
 │
 └── Standard-Cell Rails


VSS
 │
 ├── Power Ring
 │
 ├── Power Straps
 │
 └── Standard-Cell Rails
```

The purpose of the PDN is to establish reliable power and ground connectivity throughout the implemented core.

---

## 9. Placement

**Placement** assigns physical locations to the standard cells inside the floorplanned core.

Placement generally happens in two broad steps.

### Global Placement

Global placement determines approximate cell locations while considering implementation objectives such as:

- Interconnect length
- Timing
- Cell density
- Routing congestion

### Detailed Placement

Detailed placement adjusts the cells so that their final positions satisfy the legal placement requirements of the technology.

A well-optimized placement can help:

- Lower routing congestion
- Shorten interconnects
- Improve timing
- Make routing easier
- Improve area utilization

---

## 10. Clock Tree Synthesis

**Clock Tree Synthesis (CTS)** builds the physical clock distribution network required to deliver the clock signal to sequential elements.

Because a processor may contain many flip-flops at different physical locations, the clock must be distributed in a controlled manner.

A simplified clock tree is:

```text
             Clock
               |
             Buffer
            /      \
        Buffer    Buffer
        /   \      /   \
       FF1  FF2   FF3  FF4
```

CTS primarily aims to:

- Control clock skew
- Manage clock latency
- Provide adequate clock drive
- Distribute the clock reliably

Clock-tree quality directly influences the timing behavior of sequential paths.

---

## 11. Routing

After placement and clock-tree construction, the design must be physically connected using metal layers.

Routing establishes physical paths for:

- Data signals
- Clock signals
- Power-related connections
- Other required nets

Routing can be divided into two major stages.

### Global Routing

Global routing selects approximate paths and evaluates the available routing resources.

### Detailed Routing

Detailed routing creates the actual metal and via connections while observing the rules of the selected technology.

```text
Placed Standard Cells
        ↓
Global Routing
        ↓
Detailed Routing
        ↓
Physically Connected Design
```

### RTL-to-GDSII Illustration



---

## 12. Static Timing Analysis

**Static Timing Analysis (STA)** evaluates whether the implemented design can satisfy its timing requirements.

Unlike functional simulation, STA examines timing paths systematically without requiring every possible input sequence to be simulated.

Important timing quantities include:

- Clock period
- Cell delay
- Net delay
- Setup time
- Hold time
- Clock skew
- Signal slew
- Slack

### Understanding Slack

Slack represents the available timing margin on a path.

```text
Positive Slack
      ↓
Timing Requirement Met


Negative Slack
      ↓
Timing Violation
```

A positive slack value generally indicates that the path meets its timing constraint, while a negative value indicates that additional timing optimization is required.

**OpenSTA** is used for static timing analysis in the open-source implementation flow.

### STA Report


---

## 13. Physical Verification

After routing, the physical implementation must be checked to ensure that it follows the technology requirements and still represents the intended circuit.

Two important checks are **DRC** and **LVS**.

### Design Rule Check (DRC)

**DRC** verifies whether the physical layout follows the manufacturing rules specified by the technology.

Typical checks include:

- Minimum metal width
- Minimum spacing
- Layer restrictions
- Via requirements
- Metal geometry constraints

### Layout Versus Schematic (LVS)

**LVS** compares the extracted physical connectivity against the intended circuit representation.

The purpose is to verify that the layout corresponds to the expected netlist and that important connectivity has not been altered during physical implementation.

---

## 14. Signoff

**Signoff** is the final verification stage before the physical layout is accepted for final output.

The implementation is checked for aspects such as:

- Timing compliance
- Routing correctness
- Design-rule compliance
- Layout consistency
- Power connectivity
- Netlist consistency

After the required checks are successfully completed, the final physical database can be generated in **GDSII** format.

```text
Physical Implementation
        ↓
Verification
        ↓
Signoff
        ↓
GDSII
```

---

## 15. OpenLane Execution

OpenLane can be launched using the flow script supplied with the installation.

For an interactive session, a typical command is:

```bash
./flow.tcl -interactive
```

The design can then be prepared using the appropriate command supported by the installed OpenLane release:

```tcl
prep -design picorv32a
```

> **Note:** OpenLane syntax and configuration options can vary between releases. The commands used in the project should match the version installed in the VSDIAT environment.

The exact sequence of commands should therefore be taken from the OpenLane version being used rather than assuming that commands from another release are interchangeable.

---

## 16. Project Directory Structure

A typical OpenLane project can be organized approximately as follows:

```text
openlane/
│
├── designs/
│   └── picorv32a/
│       ├── config.tcl
│       ├── src/
│       │   └── *.v
│       └── runs/
│
├── flow.tcl
├── scripts/
└── configuration files
```

The main design directory contains the RTL source and configuration information.

The source directory stores the Verilog files, while the configuration file controls important implementation parameters. The `runs` directory contains generated outputs and reports from individual implementation runs.

---

## 17. Design Statistics

The implementation flow produces several useful statistics that help evaluate the synthesized and physically implemented design.

Some important parameters are:

| Parameter | Meaning |
| --- | --- |
| **Total Cells** | Overall number of cells present in the synthesized design |
| **Flip-Flops** | Number of sequential storage elements |
| **Wires** | Number of logical connections |
| **Wire Bits** | Number of individual wire bits represented in the design |
| **Area** | Physical area occupied by the implemented cells |
| **Utilization** | Portion of the available core area occupied by cells |
| **Timing** | Information describing the timing performance of the implementation |

### Design Statistics



---

## 18. Flip-Flop Ratio

The **flip-flop ratio** expresses how many of the total cells are flip-flops.

It can be calculated as:

```text
Flip-Flop Ratio =
(Number of Flip-Flops / Total Number of Cells) × 100
```

For the example values in the project:

```text
Flip-Flops = 1613
Total Cells = 14876
```

Therefore:

```text
Flip-Flop Ratio =
(1613 / 14876) × 100

≈ 10.84%
```

Hence, the calculated flip-flop ratio is approximately:

**10.84%**

This value gives a simple indication of the proportion of sequential storage elements within the synthesized cell population.

---

## 19. Complete Physical Design Flow

The complete ASIC implementation process covered in this project can be summarized as:

```text
                         RTL
                          ↓
                       Synthesis
                          ↓
                  Gate-Level Netlist
                          ↓
                    Floorplanning
                          ↓
                  Power Distribution
                          ↓
                      Placement
                          ↓
                Clock Tree Synthesis
                          ↓
                       Routing
                          ↓
              Static Timing Analysis
                          ↓
                 Physical Verification
                          ↓
                       Signoff
                          ↓
                        GDSII
```

Each stage prepares the design for the next stage, gradually moving from an abstract logical description toward a physical representation.

---

## 20. Tools and Technologies

| Tool / Technology | Main Role |
| --- | --- |
| **PicoRV32A** | RISC-V processor RTL used as the design |
| **OpenLane** | Automated RTL-to-GDSII implementation flow |
| **Yosys** | RTL synthesis |
| **OpenROAD** | Physical-design implementation |
| **OpenSTA** | Static timing analysis |
| **Sky130 PDK** | Technology and standard-cell information |
| **Magic** | Layout-related verification and physical checks |
| **Netgen** | LVS verification |
| **GDSII** | Final physical layout database format |

---

## 21. Key Learning Outcomes

This project provides practical exposure to the major stages involved in digital ASIC physical design.

The main concepts covered include:

- Verilog RTL design
- RISC-V processor implementation
- Process Design Kits
- Logic synthesis
- Technology-mapped netlists
- Standard-cell based implementation
- Floorplanning
- Power distribution
- Standard-cell placement
- Clock Tree Synthesis
- Global routing
- Detailed routing
- Static Timing Analysis
- Setup and hold timing
- Slack analysis
- Design Rule Checking
- Layout Versus Schematic verification
- Signoff
- GDSII generation

The flow also demonstrates how decisions made during earlier stages can affect area, routing, and timing in later stages.

---

## 22. Project Outcome

The PicoRV32A RTL is processed through the major stages of an open-source ASIC implementation flow.

The overall transformation can be represented as:

```text
Verilog RTL
     ↓
Synthesized Netlist
     ↓
Physical Implementation
     ↓
Timing Analysis
     ↓
Physical Verification
     ↓
Final Layout
```

The project demonstrates how a processor described at the RTL level can be transformed into a physical chip layout using the **Sky130 technology** and **OpenLane-based implementation flow**.

---

## 23. Final Project Flow

```text
RTL
 ↓
Logic Synthesis
 ↓
Gate-Level Netlist
 ↓
Floorplan
 ↓
Power Planning
 ↓
Placement
 ↓
Clock Tree Synthesis
 ↓
Routing
 ↓
Static Timing Analysis
 ↓
Physical Verification
 ↓
Signoff
 ↓
GDSII
```

This sequence represents the progression from functional hardware description to a physical layout database.

---

## 24. Conclusion

The project provides hands-on exposure to the **RTL-to-GDSII ASIC physical-design process** using PicoRV32A and open-source EDA tools.

The implementation starts with Verilog RTL and proceeds through synthesis, netlist generation, floorplanning, power planning, placement, clock-tree construction, routing, timing analysis, and physical verification.

The work helps demonstrate the relationship between logical design and physical implementation. It also provides practical familiarity with tools such as **Yosys, OpenROAD, OpenSTA, Magic, Netgen, OpenLane, and the Sky130 PDK**.

### Final Takeaway

```text
RTL
 ↓
Netlist
 ↓
Physical Design
 ↓
Timing & Physical Verification
 ↓
Signoff
 ↓
GDSII
```

**RTL → Netlist → Physical Implementation → Verification → GDSII**

This is the fundamental transformation used to take a digital hardware design from its RTL description toward a manufacturable physical layout.

