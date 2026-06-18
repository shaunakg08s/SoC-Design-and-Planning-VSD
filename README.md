# SoC-Design-and-Planning-VSD
RTL-to-GDSII physical design flow using OpenLANE &amp; Sky130 PDK | VSD SoC Design and Planning Workshop.

# VSD Course - Day 1: Introduction to Chips, Pads, Core, Die, and IPs

## 📖 Overview
This document summarizes the core concepts from Day 1 of the VSD Course. It covers the fundamental anatomy of a System on a Chip (SoC), key semiconductor terminology, the software-to-hardware bridge (ISA), and the initial stages of the physical design flow (RTL to GDSII).

---

## 🔬 1. Anatomy of an SoC (System on a Chip)
A **System on a Chip (SoC)** is an entire computer system compressed onto a single piece of silicon. 

* **Physical Appearance:** The IC we see in real life is usually just a ceramic protective covering. At its center lies the main silicon chip, which communicates with the outside world via **wire bonding**.
* **Core:** The inner region of the chip where the primary circuit logic lives. 
* **Die:** The region encompassing both the Core and the I/O Pads. (i.e., `Die = Core + Pads`).

---

## 📚 2. Key Industry Terminology
* **Foundry:** The physical manufacturing facility where chips are fabricated (e.g., TSMC, GlobalFoundries).
* **Foundry IPs:** Intellectual Property blocks that require specialized process knowledge and manual design to implement (e.g., PLLs, SRAMs, ADCs).
* **Macros:** Purely digital, reusable logic blocks.

---

## 🌉 3. The Software to Hardware Bridge
The **Instruction Set Architecture (ISA)** acts as the critical bridge between software programs and hardware execution. The flow from high-level code to physical layout looks like this:

1.  **High-Level Code:** Code is written in languages like C or C++.
2.  **Compilation:** A compiler converts the code into an executable (`.exe`) file.
3.  **Assembly:** The assembler converts the executable into **Binary Machine Code** (0s and 1s).
4.  **RTL Implementation:** The binary code requires a Register Transfer Level (RTL) implementation of the ISA to be understood by the hardware.
5.  **Synthesis:** The RTL is synthesized to produce a **Netlist** (a list of logic gates and their connections).
6.  **Physical Layout:** The netlist is implemented into a physical, geometric layout ready for fabrication.

---

## 🛠️ 4. Digital ASIC Design & PDKs
**ASIC (Application-Specific Integrated Circuit) Design** relies on three primary components:
> `ASIC = RTL IPs + EDA Tools + PDK Data`

### Process Design Kits (PDKs)
A **PDK** is a collection of files used to model a specific fabrication process for Electronic Design Automation (EDA) tools. It acts as the primary interface between the Fabs (Foundries) and the Designers.
* **Process Design Rules:** Essential checks included in the PDK to ensure manufacturability.
    * **DRC:** Design Rule Check
    * **LVS:** Layout Versus Schematic
    * **PEX:** Parasitic Extraction

---

## ⚙️ 5. Simplified RTL to GDSII Flow
The RTL to GDSII flow represents the physical design steps required to turn a synthesized netlist into a manufacturable layout.

### A. Floor and Power Planning
* **Chip Floor Planning:** Partitioning the chip die between different system building blocks and placing the I/O Pads.
* **Macro Floor Planning:** Defining dimensions, pin locations, and routing tracks for macros.
* **Power Planning:** Constructing the power delivery network. The chip is powered by multiple VDD and GND pins. Parallel structures (like power grids/meshes) are used to significantly reduce resistance and prevent voltage drop.

### B. Placement
Placement determines the exact physical locations of all standard cells on the core. It is done in two distinct steps:
1.  **Global Placement:** Places cells roughly to optimize wire length and timing, without worrying about overlaps.
2.  **Detailed Placement:** Refines the global placement, legalizing cell positions to ensure there are no overlapping cells and that they snap to the site rows.

# VSD Course Documentation

## 📖 Overview
This repository contains notes and lab documentation from the VSD Course, covering the end-to-end process of digital ASIC design—from fundamental silicon anatomy to open-source physical implementation.

---

## 🔬 1. Anatomy of an SoC (System on a Chip)
A **System on a Chip (SoC)** is an entire computer system compressed onto a single piece of silicon. 

* **Physical Appearance:** The IC we see in real life is usually just a ceramic protective covering. At its center lies the main silicon chip, which communicates with the outside world via **wire bonding**.
* **Core:** The inner region of the chip where the primary circuit logic lives. 
* **Die:** The region encompassing both the Core and the I/O Pads (i.e., `Die = Core + Pads`).

---

## 📚 2. Key Industry Terminology
* **Foundry:** The physical manufacturing facility where chips are fabricated (e.g., TSMC, GlobalFoundries).
* **Foundry IPs:** Intellectual Property blocks that require specialized process knowledge (e.g., PLLs, SRAMs).
* **Macros:** Purely digital, reusable logic blocks.

---

## 🌉 3. The Software to Hardware Bridge (ISA)
The **Instruction Set Architecture (ISA)** acts as the critical bridge between software programs and hardware.

1. **High-Level Code:** Written in C/C++.
2. **Compilation:** Converted to an executable file.
3. **Assembly:** Converted into **Binary Machine Code** (0s and 1s).
4. **RTL Implementation:** The RTL represents the ISA in hardware logic.
5. **Synthesis:** RTL is synthesized into a **Netlist** (logic gates and connections).
6. **Physical Layout:** The netlist is implemented into a physical geometric layout.

---

## 🛠️ 4. Digital ASIC Design & PDKs
> `ASIC = RTL IPs + EDA Tools + PDK Data`

* **Process Design Kits (PDKs):** The interface between Foundries and Designers. It includes **DRC** (Design Rule Check), **LVS** (Layout Versus Schematic), and **PEX** (Parasitic Extraction) rules.

---

## ⚙️ 5. Simplified RTL to GDSII Flow


### A. Floor and Power Planning
* **Chip Floor Planning:** Partitioning the die and placing I/O Pads.
* **Macro Floor Planning:** Defining dimensions, pin locations, and routing tracks.
* **Power Planning:** Constructing a power network with multiple VDD and GND pins to reduce resistance.

### B. Placement
1. **Global Placement:** Rough placement to optimize wire length and timing.
2. **Detailed Placement:** Legalizing cell positions to ensure no overlaps.

---

## 🌐 6. Open Source ASIC Flow: OpenLANE
**OpenLANE** is an automated RTL-to-GDSII flow designed to produce a clean GDSII without human intervention. It is optimized for the **SkyWater 130nm Open PDK**.

| Stage | Tool(s) Used |
| :--- | :--- |
| **Synthesis** | Yosys, ABC |
| **Floorplan & PDN** | OpenROAD |
| **Placement** | OpenROAD |
| **CTS** | TritonCTS |
| **Routing** | FastRoute, TritonRoute |
| **SPEF Extraction** | OpenRCX |
| **GDS Streaming** | Magic, KLayout |
| **Timing Analysis** | OpenSTA |
| **DRC & LVS** | Magic, Netgen |


 ![image](./images/OPENLANE_runnning_inetractively.png)
 
---
## 💻 7. Lab: Running OpenLANE for `picorv32a`
To execute the flow in interactive mode:
1. `docker` → `./flow.tcl -interactive`
2. `prep -design picorv32a`
3. `run_synthesis`

---
## Initial OpenLANE setup and preparation stage for the PicoRV32 design before starting synthesis.

 ![image](./images/prep_design_picorv32a.png)

---
## Successful RTL synthesis of the PicoRV32 core with timing and area reports generated.
 ![image](./images/Synthesis_completion.png)
 
 ---
 ## Generated floorplan showing standard cell rows and initial chip layout after floorplanning.
 ![image](./images/floorplan.png)
 
 ---
 


## 📐 8. Core and Die Dimensions
#### 1. Utilization Factor
> $$\text{Utilization Factor} = \frac{\text{Area occupied by NETLIST}}{\text{Total Area of the Core}}$$

* **Definition:** This metric determines the efficiency of space usage within the core, representing the ratio of the area occupied by standard cells to the total available core area.
* **100% (1.0) Utilization:** Indicates a fully saturated core with no room for routing, which usually leads to severe congestion and design rule violations.
* **Design Best Practice:** Designers typically aim for a specific utilization percentage (e.g., 50%–70%) to provide enough "white space" to accommodate interconnect routing and timing buffers.

#### 2. Aspect Ratio
> $$\text{Aspect Ratio} = \frac{\text{Height}}{\text{Width}}$$

* **Definition:** This metric defines the physical proportions of the chip core.
* **Square Core:** A value of 1.0 indicates a square geometry.
* **Rectangular Core:** A value not equal to 1.0 indicates a rectangular geometry, which is often chosen based on the placement of I/O pads or specific floorplanning constraints.tion.

### Pre-Placed Cells
IPs/Macros with user-defined, fixed locations placed before automated routing.

---

## 🔬 9. Standard Cell Design & Characterization


* **Design Flow:** PDKs/Rules ➔ Circuit Design ➔ Layout Design ➔ Characterization ➔ Outputs (CDL).
* **Characterization Steps:** Read models ➔ Read SPICE Netlist ➔ Recognize behavior ➔ Apply power/stimulus ➔ Measure output capacitance.

---

## ⏱️ 10. Timing Definitions
* **Propagation Delay:** Time between 50% threshold of input and output.
* **Transition Time (Slew):** Time taken to transition between thresholds (typically 20% to 80% of Vdd).

---
## ⚡ 11. Static Behavior Evaluation: CMOS Inverter Robustness
The robustness of a CMOS inverter is analyzed by observing its **Voltage Transfer Characteristic (VTC)**.

* **VTC Analysis:** We examine how the $V_{out}$ vs. $V_{in}$ graph shifts in response to variations in the Width-to-Length ($W/L$) ratio of the PMOS and NMOS transistors.
* **Switching Threshold ($V_M$):** Found at the intersection where $V_{in} = V_{out}$. At this equilibrium point, both the PMOS and NMOS transistors are operating in the saturation region, meaning both are partially "on."

---

## 🏭 12. 16-Mask CMOS Fabrication Process
The fabrication process involves a sequence of 16 photolithography masks to precisely define the layers required for CMOS integration.

### Core Fabrication Sequence
1. **N-Well Formation (Mask 1):** Establishing the region for PMOS devices in the p-substrate.
2. **Active Area (Mask 2):** Defining transistor formation regions.
3. **Isolation (Mask 3):** Growing field oxide for device insulation.
4. **Threshold Adjustment (Masks 4-5):** Ion implantation to tune $V_t$ for NMOS and PMOS.
5. **Gate Stack:** Gate oxide growth followed by Polysilicon patterning (Mask 6).
6. **Doping (Masks 7-10):** LDD and Source/Drain implantation.
7. **Interconnects (Masks 11-14):** Contact formation, Metal-1, Vias, and Metal-2 layers.
8. **Finalization (Masks 15-16):** Passivation layer deposition and Bond Pad openings for packaging.

---

## 🧪 13. Lab: Characterizing a Custom Inverter Cell
To analyze a standard cell, we perform layout extraction and SPICE simulation using open-source tools.

---

### Layout Extraction
Clone the repository and open the layout in **Magic**:
```bash
git clone [https://github.com/nickson-jose/vsdstdcelldesign.git](https://github.com/nickson-jose/vsdstdcelldesign.git)

magic -T sky130A.tech sky130_inv.mag &
```

CMOS Inverter Layout View on Magic
![image](./images/cmos_inverter.png)

----
#### Extracting the SPICE Netlist from Magic
To convert the physical layout into a format that the circuit simulator can understand, use the `tkcon` console in Magic and execute the following commands:

```tcl
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

![image](./images/spice_netlist_creation.png)

---
Screenshot of created spice file: 
![image](./images/inverter_spice_file.png)

---
Running ngspice Simulation
![image](./images/transient_sol_inv.png)

---

Plotting using the command 
```
plot y vs time a
![image](./images/intersection_point.png)  
```

---
Generated PLot :
![image](./images/inv_plot_yvstime.png) 

---

#### Propagation Delay
Propagation delay measures the time elapsed between the input signal reaching its $50\%$ threshold and the output signal reaching its corresponding $50\%$ threshold.

* **Threshold Point ($50\% V_{dd}$):** $1.65\text{V}$
##

![image](./images/propagation_delay.png)


## Screenshot of poly rules
![image](./images/periphery_rules.png)

![image](./images/drc_test_folder.png)
![image](./images/poly_magic_file.png)
![image](./images/pol3_mag.png)
![image](./images/mask_layer_info.png)
![image](./images/nwell_mag.png)
![image](./images/nwell6.png)
![image](./images/nwell_newdrc.png)
###
---
###
![image](./images/define_port.png)

## The data inside the sky130A tech file :
![image](./images/vim_sky130.png)

![image](./images/cifmaxwidth.png)

![image](./images/variant.png)

## Generating the LEF File of the CMOS Inverter 

![image](./images/generate_lef.png)

![image](./images/inv_lef.png)






