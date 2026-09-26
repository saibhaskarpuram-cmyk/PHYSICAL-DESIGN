# 🚀 Module 5 – RTL to GDSII Physical Design Flow

<p align="center">
  <b>PDN Generation • Routing • DRC • SPEF • STA • Final GDSII</b>
</p>

<p align="center">
  A practical implementation of the final physical-design stages using
  OpenLane, OpenROAD, TritonRoute, Magic, KLayout and OpenSTA with the SKY130A PDK.
</p>

---

## 📚 Table of Contents

1. [🎯 Objective](#1--objective)
2. [🛠️ Tools Used](#2--tools-used)
3. [📁 Design and Run Information](#3--design-and-run-information)
4. [🐳 OpenLane Docker Environment](#4--openlane-docker-environment)
5. [🔍 Checking the Existing Design](#5--checking-the-existing-design)
6. [⚡ Physical Design Flow](#6--physical-design-flow)
7. [🔋 Power Distribution Network](#7--power-distribution-network)
8. [🛣️ Detailed Routing](#8--detailed-routing)
9. [🔎 DRC Verification](#9--drc-verification)
10. [📡 SPEF Generation](#10--spef-generation)
11. [📊 Post-Route Static Timing Analysis](#11--post-route-static-timing-analysis)
12. [📈 WNS and TNS](#12--wns-and-tns)
13. [🎨 Final GDSII](#13--final-gdsii)
14. [🔬 KLayout Verification](#14--klayout-verification)
15. [📂 Final Output Files](#15--final-output-files)
16. [🔄 Complete Physical Design Flow](#16--complete-physical-design-flow)
17. [📊 Verification Summary](#17--verification-summary)
18. [🎯 Key Takeaways](#18--key-takeaways)
19. [🏁 Conclusion](#19--conclusion)

---

# 1. 🎯 Objective

The objective of Module 5 is to complete the final stages of the physical-design flow for the **inverter** design.

The main stages covered are:

* Power Distribution Network generation
* Detailed routing
* Design Rule Check
* Parasitic extraction
* SPEF generation
* Post-route Static Timing Analysis
* Final GDSII generation
* Final layout verification using KLayout

The complete flow is:

```text
RTL
 │
 ▼
Synthesis
 │
 ▼
Floorplan
 │
 ▼
Placement
 │
 ▼
Clock Tree Synthesis
 │
 ▼
PDN Generation
 │
 ▼
Routing
 │
 ▼
DRC
 │
 ▼
Parasitic Extraction
 │
 ▼
SPEF
 │
 ▼
Post-Route STA
 │
 ▼
Final GDSII
 │
 ▼
KLayout Verification
```

---

# 2. 🛠️ Tools Used

| Tool            | Purpose                                |
| :-------------- | :------------------------------------- |
| **OpenLane**    | RTL-to-GDSII physical-design flow      |
| **Docker**      | OpenLane execution environment         |
| **OpenROAD**    | Physical-design implementation         |
| **TritonRoute** | Detailed routing                       |
| **OpenSTA**     | Static Timing Analysis                 |
| **Magic**       | GDSII generation and layout processing |
| **KLayout**     | Layout visualization and verification  |
| **SKY130A PDK** | Technology/process information         |

---

# 3. 📁 Design and Run Information

### 🔹 Design

```text
inverter
```

### 🔹 Technology

```text
SKY130A
```

### 🔹 OpenLane Docker Image

```text
efabless/openlane:v0.21
```

### 🔹 Run Used

```text
26-09_15-08
```

### 🔹 OpenLane Working Directory

```text
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane
```

The design directory is:

```text
designs/inverter
```

The Module 5 run directory is:

```text
designs/inverter/runs/26-09_15-08
```

---

# 4. 🐳 OpenLane Docker Environment

The OpenLane environment was started using Docker.

### 🔹 Start Docker Container

```bash
sudo docker run -it \
-v $HOME/Desktop/work/tools/openlane_working_dir:/home/vsduser/share \
efabless/openlane:v0.21
```

Inside the container:

```bash
cd /home/vsduser/share/openlane
```

Check the working directory:

```bash
pwd
```

Expected:

```text
/home/vsduser/share/openlane
```

---

# 5. 🔍 Checking the Existing Design

The inverter design was checked using:

```bash
ls designs/inverter
```

The existing runs were checked using:

```bash
ls -lt designs/inverter/runs
```

Available runs:

```text
18-09_23-24
26-09_15-08
```

The latest run used for Module 5 was:

```text
26-09_15-08
```

---

# 6. ⚡ Physical Design Flow

The Module 5 physical-design flow can be represented as:

```text
              Existing Placement
                     │
                     ▼
              ┌─────────────┐
              │     CTS     │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │     PDN     │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │   Routing   │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │     DRC     │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │    SPEF     │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │   OpenSTA   │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │  Final GDS  │
              └──────┬──────┘
                     │
                     ▼
                  KLayout
```

The required outputs were already available in the `26-09_15-08` run.

---

# 7. 🔋 Power Distribution Network

The Power Distribution Network provides power and ground connections to the standard cells.

The PDN stage creates:

* Power rings
* Power straps
* Standard-cell power rails
* VDD and VSS connections

The OpenLane command used for PDN generation is:

```tcl
gen_pdn
```

The PDN stage is located between placement/CTS and detailed routing.

```text
Placement / CTS
       │
       ▼
PDN Generation
       │
       ▼
Routing
```

### 💡 Key Idea

> **PDN provides the physical VDD and VSS distribution network required by the placed standard cells.**

---

# 8. 🛣️ Detailed Routing

Detailed routing connects the placed cells according to the logical netlist.

The main routing command in OpenLane is:

```tcl
run_routing
```

The final routed DEF generated for the inverter is:

```text
results/routing/inverter.def
```

The routed layout image generated by OpenLane is:

```text
results/routing/inverter.def.png
```

### 🔹 Routed Design

```text
Standard Cells
      │
      ▼
Global / Detailed Routing
      │
      ▼
Metal Interconnect
      │
      ▼
Routed DEF
```

### 💡 Key Idea

> **Routing creates the physical metal interconnections between the placed standard cells.**

---

# 9. 🔎 DRC Verification

Design Rule Check verifies whether the physical layout follows the manufacturing rules of the selected technology.

The TritonRoute DRC report is:

```text
reports/routing/22-tritonRoute.drc
```

The report was checked using:

```bash
cat reports/routing/22-tritonRoute.drc
```

The command produced no output.

Therefore:

```text
No DRC violations were reported.
```

---

## 🔹 KLayout DRC Report

The KLayout DRC report is:

```text
reports/routing/22-tritonRoute.klayout.xml
```

It was checked using:

```bash
head -50 reports/routing/22-tritonRoute.klayout.xml
```

The report contained:

```xml
<categories/>
<items/>
```

Therefore, no violation items were reported.

### 💡 Key Idea

> **DRC verifies that the physical layout satisfies the design rules required by the technology.**

---

# 10. 📡 SPEF Generation

After routing, parasitic information was extracted from the physical interconnect.

The extracted SPEF file is:

```text
results/routing/inverter.spef
```

SPEF stands for:

**Standard Parasitic Exchange Format**

It contains extracted parasitic information associated with the routed design.

### 🔹 SPEF Flow

```text
Routed DEF
     │
     ▼
Parasitic Extraction
     │
     ▼
   SPEF
     │
     ▼
Post-Route STA
```

### 💡 Key Idea

> **SPEF provides extracted parasitic information for post-route timing analysis.**

---

# 11. 📊 Post-Route Static Timing Analysis

Static Timing Analysis was performed using the generated SPEF information.

The main OpenSTA report is:

```text
reports/synthesis/25-opensta_spef.rpt
```

The report was checked using:

```bash
cat reports/synthesis/25-opensta_spef.rpt
```

The report showed:

```text
No paths found.
```

The WNS report was checked using:

```bash
cat reports/synthesis/25-opensta_spef_wns.rpt
```

Result:

```text
wns 0.00
```

The TNS report was checked using:

```bash
cat reports/synthesis/25-opensta_spef_tns.rpt
```

Result:

```text
tns 0.00
```

### 🔹 STA Result

| Parameter       |            Result |
| :-------------- | ----------------: |
| WNS             |            `0.00` |
| TNS             |            `0.00` |
| Main STA report | `No paths found.` |

The `No paths found` result is documented directly from the generated report.

### 💡 Key Idea

> **Post-route STA uses extracted parasitics to analyze timing after physical routing.**

---

# 12. 📈 WNS and TNS

Two important timing metrics are:

* **WNS – Worst Negative Slack**
* **TNS – Total Negative Slack**

## 🔹 WNS

WNS represents the minimum slack reported among analyzed timing paths.

```text
WNS = Minimum Slack
```

The generated report contains:

```text
wns 0.00
```

---

## 🔹 TNS

TNS represents the total negative slack.

```text
TNS = Sum of Negative Slack
```

The generated report contains:

```text
tns 0.00
```

### 🔹 Generated Results

```text
WNS = 0.00
TNS = 0.00
```

### 💡 Key Idea

> **WNS represents the worst slack, while TNS represents the total negative slack reported by STA.**

---

# 13. 🎨 Final GDSII

The final physical layout was generated in GDSII format.

The final GDS file is:

```text
results/magic/inverter.gds
```

The generated GDS image is:

```text
results/magic/inverter.gds.png
```

The generated image was approximately:

```text
293 KB
```

Check the GDS image:

```bash
ls -lh \
~/Desktop/work/tools/openlane_working_dir/openlane/designs/inverter/runs/26-09_15-08/results/magic/inverter.gds.png
```

### 🔹 Final Layout Flow

```text
Routed Design
      │
      ▼
     Magic
      │
      ▼
 inverter.gds
      │
      ▼
   KLayout
```

### 💡 Key Idea

> **GDSII is the final physical layout database used to represent the implemented chip layout.**

---

# 14. 🔬 KLayout Verification

The final GDSII was opened using KLayout.

First go to the OpenLane directory:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
```

Open the GDS:

```bash
klayout \
designs/inverter/runs/26-09_15-08/results/magic/inverter.gds
```

KLayout was used to inspect the final physical layout of the inverter.

---

# 15. 📂 Final Output Files

The important Module 5 files generated are:

```text
results/
│
├── routing/
│   ├── inverter.def
│   ├── inverter.def.png
│   └── inverter.spef
│
└── magic/
    ├── inverter.gds
    ├── inverter.gds.png
    ├── inverter.lef
    ├── inverter.lef.mag
    ├── inverter.mag
    └── .magicrc
```

Important reports:

```text
reports/
│
├── routing/
│   ├── 22-tritonRoute.drc
│   └── 22-tritonRoute.klayout.xml
│
└── synthesis/
    ├── 25-opensta_spef.rpt
    ├── 25-opensta_spef_tns.rpt
    ├── 25-opensta_spef_wns.rpt
    ├── 25-opensta_spef.min_max.rpt
    ├── 25-opensta_spef.slew.rpt
    └── 25-opensta_spef.timing.rpt
```

---

# 16. 🔄 Complete Physical Design Flow

The complete Module 5 flow can be summarized as:

```text
                 Inverter Design
                       │
                       ▼
                 Existing Run
                       │
                       ▼
                PDN Generation
                       │
                       ▼
                    Routing
                       │
                       ▼
                 TritonRoute
                       │
                       ▼
                    DRC
                       │
                       ▼
               Routed DEF File
                       │
                       ▼
              Parasitic Extraction
                       │
                       ▼
                     SPEF
                       │
                       ▼
                  OpenSTA
                       │
                       ▼
                  WNS / TNS
                       │
                       ▼
                 Final GDSII
                       │
                       ▼
                   KLayout
                       │
                       ▼
              Final Layout
```

---

# 17. 📊 Verification Summary

| Parameter            | Result                        |
| :------------------- | :---------------------------- |
| Design               | **Inverter**                  |
| Technology           | **SKY130A**                   |
| OpenLane             | **v0.21**                     |
| Run                  | **26-09_15-08**               |
| Routing              | ✅ Completed                   |
| Routed DEF           | ✅ Generated                   |
| SPEF                 | ✅ Generated                   |
| TritonRoute DRC      | ✅ No violations reported      |
| KLayout DRC          | ✅ No violation items reported |
| Post-SPEF STA        | ✅ Report generated            |
| WNS                  | **0.00**                      |
| TNS                  | **0.00**                      |
| Final GDSII          | ✅ Generated                   |
| GDS Image            | ✅ Generated                   |
| KLayout Verification | ✅ Completed                   |

---

# 18. 🎯 Key Takeaways

### 🧠 Major Concepts Learned

* PDN provides physical power and ground distribution.
* Routing creates physical metal connections between cells.
* TritonRoute performs detailed routing.
* DRC checks the layout against technology design rules.
* SPEF contains extracted parasitic information.
* Post-route STA uses physical parasitics for timing analysis.
* WNS represents the worst reported slack.
* TNS represents the total negative slack.
* Magic generates the final GDSII layout.
* KLayout is used to inspect the final GDSII.
* The final physical design is represented by the GDSII database.

---

# 19. 🏁 Conclusion

Module 5 completed the final physical-design stages of the inverter using the SKY130A technology and OpenLane flow.

The implementation progressed through:

```text
PDN
 ↓
Routing
 ↓
DRC
 ↓
SPEF
 ↓
Post-Route STA
 ↓
GDSII
 ↓
KLayout
```

The routed DEF, SPEF, DRC reports, OpenSTA reports and final GDSII were generated.

The TritonRoute DRC report contained no reported violations, and the KLayout DRC report contained no violation items.

The final GDSII layout was generated and opened using KLayout for physical-layout inspection.

---

## 📌 Final Module 5 Result

```text
                  MODULE 5
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       Routing                  DRC
          │                     │
          └──────────┬──────────┘
                     ▼
                    SPEF
                     │
                     ▼
                  OpenSTA
                     │
                ┌────┴────┐
                ▼         ▼
              WNS        TNS
             0.00       0.00
                │
                ▼
             Final GDS
                │
                ▼
             KLayout
                │
                ▼
          Final Inverter Layout
```

# 🚀 Module 5 Completed

```
```
