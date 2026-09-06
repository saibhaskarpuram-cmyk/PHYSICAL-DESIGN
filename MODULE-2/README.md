# Floorplanning and Placement – PicoRV32A

## 1. Introduction

Floorplanning and placement are important stages in the **physical design flow** of an ASIC. After synthesis, the logical netlist is converted into a physical representation by deciding the chip dimensions, core area, power distribution, I/O locations, and positions of standard cells.

This stage ensures that the design can be implemented efficiently while satisfying **area, timing, power, and routing requirements**.

### Physical Design Flow

```text
Synthesized Netlist
        ↓
   Floorplanning
        ↓
   Power Planning
        ↓
   Pin Placement
        ↓
     Placement
        ↓
Placement Optimization
        ↓
       CTS
        ↓
     Routing
        ↓
Timing Analysis
```

---

## 2. Core and Die

### Core

The **core** is the internal region of the chip where standard cells and other logic components are placed.

### Die

The **die** represents the complete physical silicon area of the chip. It contains the core along with the surrounding regions required for I/O, power distribution, and other physical-design requirements.

```text
+--------------------------------+
|              DIE               |
|                                |
|      +------------------+      |
|      |                  |      |
|      |       CORE       |      |
|      |                  |      |
|      +------------------+      |
|                                |
+--------------------------------+
```

The final physical design is eventually fabricated on a silicon wafer.

---

## 3. Aspect Ratio and Utilization

### Aspect Ratio

Aspect ratio describes the shape of the core or die.

```text
Aspect Ratio = Height / Width
```

For example:

```text
Aspect Ratio = 1
```

indicates a square-shaped core.

An aspect ratio greater than or less than 1 results in a rectangular shape.

### Utilization

Utilization represents the percentage of the core area occupied by placed cells.

```text
Utilization (%) =
(Cell Area / Core Area) × 100
```

Higher utilization allows more cells to fit into a smaller area, but excessive utilization can increase **routing congestion** and make timing closure more difficult.


---

## 4. Floorplanning

**Floorplanning** is one of the first major steps in physical design after synthesis.

It determines the overall physical organization of the chip.

### Major Floorplanning Decisions

* Core dimensions
* Die dimensions
* Aspect ratio
* Core utilization
* I/O locations
* Placement of large blocks
* Power distribution requirements
* Routing resources

A well-designed floorplan can reduce congestion, shorten interconnects, and improve overall timing.


---

## 5. Floorplan Configuration in OpenLane

OpenLane provides several configuration variables that control the floorplanning process.

Important parameters include:

```text
FP_CORE_UTIL
FP_ASPECT_RATIO
FP_SIZING
DIE_AREA
FP_IO_HMETAL
FP_IO_VMETAL
FP_IO_MODE
FP_PDN_VPITCH
FP_PDN_HPITCH
```

### Purpose of Important Variables

| Variable          | Purpose                                    |
| ----------------- | ------------------------------------------ |
| `FP_CORE_UTIL`    | Defines the target core utilization        |
| `FP_ASPECT_RATIO` | Controls the height-to-width ratio         |
| `FP_SIZING`       | Determines the floorplan sizing method     |
| `DIE_AREA`        | Specifies the die dimensions               |
| `FP_IO_HMETAL`    | Defines the horizontal metal layer for I/O |
| `FP_IO_VMETAL`    | Defines the vertical metal layer for I/O   |
| `FP_IO_MODE`      | Controls I/O placement configuration       |
| `FP_PDN_VPITCH`   | Sets vertical power-grid pitch             |
| `FP_PDN_HPITCH`   | Sets horizontal power-grid pitch           |


---

## 6. OpenLane Configuration

The OpenLane configuration file contains the parameters required to run the physical design flow.

Example:

```tcl
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) \
"./designs/picorv32a/src/picorv32a.v"

set ::env(SDC_FILE) \
"./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"

set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)
```

### Configuration Parameters

* `DESIGN_NAME` – specifies the name of the design.
* `VERILOG_FILES` – specifies the RTL source file.
* `SDC_FILE` – specifies the timing constraint file.
* `CLOCK_PERIOD` – defines the clock period.
* `CLOCK_PORT` – identifies the clock input port.
* `CLOCK_NET` – identifies the clock network.


---

## 7. Pre-Placed Cells and Blocks

Certain cells or blocks may need to be assigned fixed physical locations before the automated placement process.

These are commonly referred to as **pre-placed cells or blocks**.

Examples include:

* Memory blocks
* Large macros
* Clock-related cells
* Interface blocks
* Other fixed IP blocks

Pre-placement helps the tool organize the remaining standard cells around these fixed regions.

```text
+---------------------------+
| Standard Cells            |
|                           |
|     +-------------+       |
|     | Fixed Block |       |
|     +-------------+       |
|                           |
| Standard Cells            |
+---------------------------+
```

---

## 8. Power Planning

Power planning establishes the power distribution network required to deliver a stable supply voltage to all cells.

The primary power signals are:

```text
VDD → Power Supply
VSS → Ground
```

The power distribution network generally consists of:

```text
VDD / VSS
    ↓
Power Rings
    ↓
Power Straps
    ↓
Standard Cell Rails
    ↓
Logic Cells
```

A properly designed power network helps reduce:

* IR voltage drop
* Ground bounce
* Supply noise
* Power integrity problems

It also ensures that standard cells receive adequate power during operation.

---

## 9. Decoupling Capacitors

**Decoupling capacitors**, also called **decap cells**, are used to reduce fluctuations in the local power supply.

When a large number of cells switch simultaneously, the instantaneous current demand can cause temporary voltage variations.

A decoupling capacitor stores charge and can supply it locally when required.

```text
       VDD
        |
        +------+
        | Decap|
        | Cell |
        +------+
        |
     Circuit
        |
       VSS
```

### Benefits

* Reduces supply-voltage fluctuations
* Improves power integrity
* Helps reduce local noise
* Provides temporary local charge during switching activity

---

## 10. Pin Placement

**Pin placement** determines the physical locations of input and output pins around the chip.

The placement of pins affects routing length, congestion, and timing.

Pins can generally be positioned along:

* Left side
* Right side
* Top side
* Bottom side

Clock-related pins require special attention because the clock network has a significant effect on timing.

Good pin placement helps:

* Reduce routing distance
* Reduce congestion
* Improve timing
* Simplify routing

---

## 11. Placement Blockages

A **placement blockage** is a region where standard cells are not allowed to be placed.

Blockages may be created to protect:

* Fixed macros
* Power structures
* Special routing regions
* Reserved physical areas

Example:

```text
+---------------------------+
| Standard Cells            |
|                           |
|     +-------------+       |
|     |   BLOCKED   |       |
|     |    AREA     |       |
|     +-------------+       |
|                           |
| Standard Cells            |
+---------------------------+
```

Placement blockages provide better control over cell distribution and help avoid conflicts with important physical structures.

---

## 12. Standard Cell Placement

After floorplanning and power planning, the standard cells are assigned physical locations inside the core.

The placement process attempts to achieve:

* Shorter interconnects
* Lower congestion
* Better timing
* Legal cell positions
* Efficient area utilization
* Better connectivity between related cells

### Placement Flow

```text
Synthesized Netlist
        ↓
Global Placement
        ↓
Legalization
        ↓
Detailed Placement
        ↓
Placement Optimization
```

### Global Placement

Global placement determines approximate locations for cells while optimizing wire length and congestion.

### Legalization

Legalization moves cells into valid positions according to the physical placement rules.

### Detailed Placement

Detailed placement performs local adjustments to improve the quality of the placement.


---

## 13. Placement Optimization

After the initial placement, optimization is performed to improve the physical and timing characteristics of the design.

The tool considers parameters such as:

* Wire length
* Capacitance
* Delay
* Congestion
* Setup timing
* Hold timing
* Cell density

If necessary, the tool may resize cells, move cells, or insert additional buffers.

### Buffer and Repeater Insertion

Long interconnects can introduce significant delay and signal degradation.

Buffers or repeaters can be inserted along long paths:

```text
Source Cell
     |
     | Long Wire
     |
   Buffer
     |
     | Long Wire
     |
Destination Cell
```

These buffers help improve signal integrity and reduce the impact of long interconnects.

---

## 14. Placement Statistics

After placement, OpenLane provides various statistics that help evaluate the quality of the physical implementation.

Example placement results:

```text
Total Instances      : 21699
Fixed Instances      : 6354
Nets                 : 15449
Design Area          : 420473.3 um²
Utilization          : 36%
Utilization Padded   : 55%
Rows                 : 238
```

### Important Placement Metrics

| Parameter          | Description                               |
| ------------------ | ----------------------------------------- |
| Total Instances    | Total number of cell instances            |
| Fixed Instances    | Number of instances with fixed locations  |
| Nets               | Number of electrical connections          |
| Design Area        | Physical area occupied by the design      |
| Utilization        | Percentage of available area occupied     |
| Utilization Padded | Utilization considering placement padding |
| Rows               | Number of standard-cell placement rows    |
| Wire Length        | Estimated interconnect length             |
| Displacement       | Movement of cells during optimization     |


These statistics are useful for identifying potential problems with **area, congestion, placement quality, and timing**.

---

## 15. Floorplanning and Placement Results

### Floorplanning Result

The floorplanning stage establishes the physical boundaries of the design and defines the regions where cells and other physical structures will be placed.


### Placement Result

After placement, standard cells are distributed within the core while considering timing, congestion, and connectivity.


---

## 16. Key Learnings

Through the floorplanning and placement stage of the PicoRV32A design, the following concepts were studied:

```text
Core
Die
Aspect Ratio
Utilization
Floorplanning
Floorplan Configuration
Pre-Placed Cells
Power Planning
Decoupling Capacitors
Pin Placement
Placement Blockages
Standard Cell Placement
Placement Optimization
Placement Statistics
```

The major purpose of this stage is to convert the synthesized logical design into a physically organized layout that is suitable for further implementation.

---

## 17. Physical Design Flow Covered

The overall flow completed so far is:

```text
RTL
 ↓
Synthesis
 ↓
Gate-Level Netlist
 ↓
Floorplanning
 ↓
Power Planning
 ↓
Pin Placement
 ↓
Standard Cell Placement
 ↓
Placement Optimization
```

The next major stages of the physical design flow are:

```text
Clock Tree Synthesis (CTS)
        ↓
Routing
        ↓
Parasitic Extraction
        ↓
Static Timing Analysis
        ↓
Physical Verification
        ↓
Final Layout
```

---

## 18. Conclusion

Floorplanning and placement are essential steps in converting a synthesized netlist into a physically implementable chip layout.

The floorplan determines the physical organization of the design, while placement assigns actual locations to standard cells. Power planning, pin placement, blockages, and placement optimization further improve the quality of the physical implementation.

The PicoRV32A design has therefore progressed from the synthesized netlist toward a physically organized implementation, providing the foundation for the next stages of **Clock Tree Synthesis, routing, and timing analysis**.
