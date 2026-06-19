# SoC-Design-and-Planning-VSD
RTL-to-GDSII physical design flow using OpenLANE &amp; Sky130 PDK | VSD SoC Design and Planning Workshop.

# Introduction to Chips, Pads, Core, Die, and IPs

## 📖 Overview
This document summarizes the core concepts of the VSD Course - SoC Design and Planning Workshop. It covers the fundamental anatomy of a System on a Chip (SoC), key semiconductor terminology, the software-to-hardware bridge (ISA), and the initial stages of the physical design flow (RTL to GDSII).

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

## 14. Propagation Delay
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

![image](./images/custom_lef.png)

---

## ⏱️ 15. Setup Time Analysis (Ideal vs. Real Clocks)

Static Timing Analysis (STA) verifies that data stabilizes before the clock edge captures it, avoiding metastability issues.

### A. Timing under an Ideal Clock
In an ideal scenario with zero clock network delay, the data path's total combinational delay ($\theta$) must simply be less than the clock period ($T$):

$$\theta < T$$

* **Fan-out and Slew:** A higher fan-out (driving more load gates) increases output capacitance, which directly increases the **slew** (transition time) and degrades signal speed.
* **Timing Margins:** In practice, an ideal model must still account for the capture flip-flop's **Setup Time** (minimum time data must be stable before the clock edge), **Jitter** (temporary clock period variations), and **Uncertainty** (random noise).

### B. Timing under a Real Clock
Real clocks pass through a network of distribution buffers, introducing a **Clock Network Delay** (insertion delay). 
* Let $\Delta_1$ be the buffer delay to the launch flip-flop.
* Let $\Delta_2$ be the buffer delay to the capture flip-flop.

$$\theta + \Delta_1 < T + \Delta_2$$

> **Clock Skew:** The spatial difference in clock arrival times between the launch and capture flops:
> $$\text{Skew} = \Delta_2 - \Delta_1$$

### C. The Complete Setup Equation
When incorporating all real-world constraints, the complete setup analysis expression is defined as:

$$\theta + \Delta_1 < (T + \Delta_2) - \text{Setup Time} - \text{Jitter}$$

* **Data Arrival Time (LHS):** The time it takes for data to launch and propagate through the combinational logic ($\theta + \Delta_1$).
* **Data Required Time (RHS):** The latest allowable time for data to arrive at the destination pin without violating setup constraints ($(T + \Delta_2) - \text{Setup Time} - \text{Jitter}$).

#### Setup Slack and Violations
Slack measures the safety margin of a design. For setup analysis, it is calculated as:
$$\text{Setup Slack} = \text{Data Required Time} - \text{Data Arrival Time}$$

* **Positive Slack ($\ge 0$):** The timing requirements are met successfully.
* **Negative Slack ($< 0$):** A **Setup Violation** has occurred, meaning the data arrived too late to be successfully captured.

---

![image](./images/base_sdc.png)

![image](./images/pre_sta_config.png)

![image](./images/sta_pre_sta.png)

---
## 🔏 16. Clock Tree Synthesis (CTS) & Shielding

Clock Tree Synthesis balances the arrival of the clock signal to every sequential element across the chip.

* **Symmetrical Repeaters:** The buffers used during CTS are specially designed to have perfectly equal rise and fall times to maintain a stable clock duty cycle.
* **Clock Net Shielding:** Because clock lines switch constantly, they generate severe electromagnetic interference. Clock nets are shielded by running parallel VDD/GND lines next to them. This protects the clock from the outside world and avoids **Crosstalk**, **Glitches**, and **Delta Delay** caused by switching neighboring signal wires.

---
![image](./images/cts_tcl.png) 

## 🔒 17. Hold Timing Analysis

Hold analysis ensures that the data launched by the current clock edge does not overwrite the data captured by the previous clock edge at the destination flip-flop.

### A. Core Concept
The combinational delay must be long enough to survive the hold time ($H$) requirements of the capture register:

$$\theta > H$$

### B. Real Clock Constraints
With insertion delays ($\Delta_1, \Delta_2$) and uncertainty taken into account, the real hold timing expression becomes:

$$\theta + \Delta_1 > H + \Delta_2 + \text{Uncertainty}$$

* **Data Arrival Time (LHS):** $\theta + \Delta_1$
* **Data Required Time (RHS):** $H + \Delta_2 + \text{Uncertainty}$

#### Hold Slack Calculation
Unlike setup, hold slack checks that data arrives *after* the required window, changing the equation order:
$$\text{Hold Slack} = \text{Data Arrival Time} - \text{Data Required Time}$$

* Hold violations are critical because they **cannot** be fixed by lowering the chip's external operating clock frequency after fabrication.

---

## 💻 18. Lab: Post-CTS Timing Analysis inside OpenROAD

After completing Clock Tree Synthesis (CTS), we invoke the **OpenROAD** tool internally to perform timing analysis using the actual propagated clock network rather than an ideal clock.

### OpenROAD Initialization and Database Setup

Once inside the OpenROAD environment, run the following sequence of commands to load the design data, link the timing libraries, and configure the clock network:

```tcl
# 1. Read the merged Library Exchange Format (LEF) file containing cell macro definitions
read_lef /OpenLane/designs/picorv32a/runs/24-03_10-03/tmp/merged.nom.lef

# 2. Read the Design Exchange Format (DEF) file generated after the CTS stage
read_def /OpenLane/designs/picorv32a/runs/24-03_10-03/results/cts/picorv32a.def

# 3. Create and save an OpenROAD database (.db) to preserve the current design state
write_db pico_cts.db

# 4. Reload the newly created database into OpenROAD
read_db pico_cts.db

# 5. Import the post-CTS structural Verilog netlist
read_verilog /OpenLane/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.v

# 6. Load the complete timing library (.lib) specified by the environment variables
read_liberty $::env(LIB_SYNTH_COMPLETE)

# 7. Bind the Verilog netlist logic structures to the physical timing library
link_design picorv32a

# 8. Load the custom Synopsys Design Constraints (SDC) file defining timing goals
read_sdc /OpenLane/designs/picorv32a/src/my_base.sdc

# 9. Direct the timing engine to calculate real latency delays for all clock networks
set_propagated_clock [all_clocks]
```
---

![image](./images/reading_openlef_openroad.png)

![image](./images/pico_cts_db_file_creation.png)

![image](./images/deffile_reading.png)

---


## Final RTL to GDSII using TritonRoute & OpenSTA

Generating Power Distribution Network
## Power Distribution Network : 
```tcl
gen_pdn
```

Screenshots of PDN def
![image](./images/pdn_gen.png)

## 🗺️ 19. Routing and the Design Rule Check (DRC) Stage

Once cells are placed and the clock tree is built, routing connects the physical signal nets together.

### A. Maze Routing & Lee's Algorithm
The router uses algorithms to establish the most efficient connection path between two points (Source and Target) while generating minimal zigzagging lines.



* **How it works:** The algorithm maps a grid over the layout background. Starting at the source point, it labels adjacent open grid squares sequentially ($1, 2, 3 \dots$) until it hits the target point. 
* **Path Selection:** The router traces backward along the path containing the least total number of bends to minimize wire length and resistance.


![image](./images/var_routing.png)

### B. Design Rule Checks (DRC)
Foundries impose geometric constraints on layouts to account for physical limitations in **optical lithography** (the light wavelength limits used to print features on silicon). Common routing rules include:

* **Wire Width:** The minimum width of a metal trace to prevent fractures or electromigration.
* **Wire Spacing:** The minimum empty gap between two adjacent parallel wires.
* **Wire Pitch:** The center-to-center distance between neighboring wires (`Pitch = Width + Spacing`).
* **Via Width & Spacing:** Size and clearance constraints for the vertical interlayer contacts (vias).

#### Signal Shorts and Layering
If two signal wires cross or get too close, a **Signal Short** occurs, resulting in permanent functionality failure. Routers resolve this routing congestion by moving intersecting lines to different metal layers (e.g., using Metal-1 for horizontal routing and Metal-2 for vertical routing).


## TritonRoute Algorithm 
![image](./images/tritonroute_algo.png)

---

## Routing Complete 
To run routing :
```tcl
   run_routing
```

![image](./images/routing_completed.png)


## 🎓 Key Learnings & Takeaways

As an Electronics and Communication Engineering (ECE) student and VLSI enthusiast, this course effectively bridged the gap between academic textbook theory and industry-standard ASIC physical design workflows.

1. **Physical SoC Architecture**
   * Learned that a commercial IC package is simply a protective shell housing the internal silicon **die**.
   * Discovered how the die is structurally split into an inner **core** (where the circuit logic lives) and outer **I/O pads** connected to the external world via wire bonding.

2. **Core Geometry Optimization**
   * Mastered how chip density and physical shape are balanced during floorplanning using the **Utilization Factor** (the ratio of netlist area to total core area).
   * Learned to manipulate the **Aspect Ratio** (the core's height-to-width proportion) to meet design and routing constraints.

3. **Automated Open-Source ASIC Flows**
   * Gained hands-on experience with **OpenLANE**, an automated, open-source RTL-to-GDSII compiler flow.
   * Explored how this framework orchestrates the entire physical design pipeline targeting the **SkyWater 130nm Open PDK** with zero human intervention.

4. **Toolchain Orchestration**
   * Discovered how discrete open-source EDA tools seamlessly interact within the automated environment.
   * Utilized **Yosys/ABC** for logical synthesis, **OpenROAD** for floorplanning and routing, **Magic** for layout visualization, and **OpenSTA** for timing verification.

5. **Silicon Fabrication Mechanics**
   * Mapped abstract CMOS schematics directly to the physical **16-Mask CMOS Process**.
   * Understood how a precise sequence of photolithography masks layer-by-layer constructs N-wells, active areas, polysilicon gates, and metal routing planes.

6. **Lithography-Driven Design Rules**
   * Learned that Design Rule Checks (DRC)—such as minimum wire width, wire spacing, and wire pitch—are not arbitrary guidelines.
   * Understood them as strict physical constraints imposed directly by the resolution limits of optical lithography tools.

7. **Real-World Static Timing Analysis (STA)**
   * Transitioned from ideal clock assumptions to analyzing real clock distribution networks.
   * Mastered how clock network buffer delays create spatial variance (**Clock Skew**), which directly alters the setup and hold slack equations of sequential networks.

8. **Standard Cell Characterization**
   * Executed a full layout-to-simulation loop by extracting SPICE netlists from a custom inverter layout inside Magic (`ext2spice`).
   * Utilized **Ngspice** waveforms to calculate critical cell parameters like propagation delay ($50\% V_{dd}$) and transition slew ($20\%\text{--}80\% V_{dd}$).


-----

## Tools & Environment

| Tool | Purpose |
|---|---|
| **OpenLANE** | RTL-to-GDSII automation flow |
| **Yosys** | RTL synthesis |
| **OpenROAD** | Floorplan, Placement, CTS, Routing |
| **Magic** | Layout editor, DRC, LVS |
| **OpenSTA** | Static Timing Analysis |
| **ngspice** | SPICE simulation |
| **TritonRoute** | Detailed routing |
| **Netgen** | LVS (Layout vs Schematic) |
| **Sky130 PDK** | SkyWater 130nm open-source PDK |

---

## 🙏 Acknowledgements

A huge thank you to **Kunal Ghosh** (Co-founder, VSD Corp. Pvt. Ltd.) and **Nickson P Jose** (Physical Design Engineer) for creating such a wonderful, empowering platform for students and VLSI enthusiasts. This well-structured and genuinely practical workshop breaks down complex industry concepts into digestible steps. Running a real CPU (`picorv32a`) from RTL to GDSII using entirely open-source tools is an incredible milestone—and this course made it entirely accessible.

* **Kunal Ghosh** — Co-founder, VSD (VLSI System Design), for pioneering accessible, high-quality cloud-based VLSI education.
* **Nickson P Jose** — for providing the foundational `vsdstdcelldesign` repository used to bridge layout design with SPICE simulations in the labs.


