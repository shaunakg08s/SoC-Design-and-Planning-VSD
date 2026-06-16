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
