# RISC-V-Reference_SoC-VSD-IITGN-Week_2
This repository documents the labs of week 2 of the program - RISC-V Reference SoC - Design to Silicon Tapeout conducted by VSD and IIT Gandhi Nagar (sky130RTLDesignAndSynthesisWorkshop). It covers simulation and synthesis flow from RTL to Netlist generation, **Icarus Verilog**, **gtkwave** and **Yosys**, tailored for the Sky130 process node.

Focus on week 2 is on understanding the VSD BabySoC Fundamental and Functional Modelling
## RISC-V Reference SoC Tapeout Program VSD IIT Gandhi Nagar

****************************************************************************************************
### VSDBabySoC Design and Modeling
****************************************************************************************************

### 📚 Contents

- [Introduction to the VSDBabySoC](#introduction-to-the-vsdbabysoc)  
- [What is VSDBabySoC?](#what-is-vsdbabysoc)  
- [Problem Statement](#problem-statement)  
- [What is SoC](#what-is-soc)  
- [What is RVMYTH](#what-is-rvmyth)  
- [What is PLL](#what-is-pll)  
- [What is DAC](#what-is-dac)  
- [Project Structure](#project-structure)  
- [Setup and Prepare Project Directory](#setup-and-prepare-project-directory)  
- [🛠️ Cloning the Project](#️-cloning-the-project)  
- [TLV to Verilog Conversion for RVMYTH](#tlv-to-verilog-conversion-for-rvmyth)  
- [Simulation Steps](#simulation-steps)  
    - [Pre-Synthesis Simulation](#pre-synthesis-simulation)  
    - [Viewing Waveform in GTKWave](#viewing-waveform-in-gtkwave)
    - [Viewing DAC output in analog mode](#Viewing-DAC-output-in-analog-mode)
- [Troubleshooting Tips](#Trouble-shooting-tips)
- [Why Pre-Synthesis and Post-Synthesis?](#why-pre-synthesis-and-post-synthesis)

### Introduction to the VSDBabySoC
VSDBabySoC is a small yet powerful RISCV-based SoC. 

The main purpose of designing such a small SoC is to test three open-source IP cores together for the first time and calibrate the analog part of it. 

VSDBabySoC contains one RVMYTH microprocessor, an 8x-PLL to generate a stable clock, and a 10-bit DAC to communicate with other analog devices.

### What is VSDBabySoC?
  VSDBabySoC is a compact light weight, educational SoC that brings together three critical IPs in a unified design.
  - **RVMYTH**: A simple RISC-V-based CPU core.
  - **PLL**: An 8x Phase-Locked Loop for stable clock generation.
  - **DAC**: A 10-bit Digital-to-Analog Converter for interfacing with analog devices.

  Its primary purpose is to integrate and test these IPs collaboratively and calibrate the analog part of the SoC.

  It provides a platform for observing digital-to-analog behavior in a controlled environment

 ![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Picture1.png)
 

### Problem Statement
This project delves into designing a compact, open-source System on Chip (SoC) based on RVMYTH, a RISC-V-based processor core. The SoC integrates a Phase-Locked Loop (PLL) for precise clock generation and control, alongside a 10-bit Digital-to-Analog Converter (DAC) for interfacing with external analog systems. By converting digital signals into analog, this DAC allows BabySoC to communicate with devices that accept analog inputs, such as televisions and mobile phones, enabling output in the form of audio or video. Ultimately, this Sky130-technology-based SoC aims to provide a highly documented, educational platform for learning and experimentation in digital-analog interfacing.

### What is SoC
An SoC is a complete system as a single-die chip that has several integrated IP cores on it. These IPs could vary from microprocessors (CPU) (completely digital), memory blocks, peripherals and communication interfaces to 5G broadband modems (completely analog).

### What is RVMYTH
The RVMYTH core is a simple RISC V-based CPU designed for educational purposes and small-scale applications. It provides a practical example of a RISC-V processor implementation.
It illustrates fundamental CPU architecture concepts and is small enough to be implemented easily using open-source flows. It handles instruction execution, memory operations, and basic control logic.

### What is PLL
Phase-Locked Loop (PLL): A phase-locked loop or PLL is a control system that generates an output signal whose phase is related to the phase of an input signal. PLLs are widely used for synchronization purposes, including clock generation and distribution.

A Phase-Locked Loop (PLL) is a feedback-driven circuit that locks the output frequency in sync with a reference clock. In this SoC, it is used to multiply the input clock frequency to generate a high-speed internal clock, essential for running the digital logic efficiently and with low jitter.

### What is DAC
Digital-to-Analog Converter (DAC): A DAC is a system that converts a digital signal into an analog signal. DACs are widely used in modern communication systems, enabling the generation of digitally-defined transmission signals.
A Digital-to-Analog Converter (DAC) transforms digital values into analog voltages or currents. The 10-bit DAC in this SoC enables the system to interact with real-world analog devices, converting CPU-driven digital outputs into continuous signals for use in multimedia, sensing, or other analog domains.

### Project Structure
- src/include/ - Contains header files (*.vh) with necessary macros or parameter definitions. This directory contains header files (with .vh extension) that define key macros and parameters for the SoC design. These may include configuration settings like clock frequencies, reset behaviors, or other design-specific constants.
- src/module/ - Contains Verilog files for each module in the SoC design. This directory contains the Verilog files that define the logic and behavior of the individual modules in the SoC. These include the Verilog code for the RISC-V processor, PLL, DAC, and possibly other supporting modules.
- output/ - The output directory is where all the generated files from the compilation and simulation processes will be stored. This includes compiled files, waveform files, and any other results generated from running simulations.
-

### Setup and Prepare Project Directory
Clone or set up the directory structure as follows:
```txt
VSDBabySoC/
├── src/
│   ├── include/
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   ├── module/
│   │   ├── vsdbabysoc.v      # Top-level module integrating all components
│   │   ├── rvmyth.v          # RISC-V core module
│   │   ├── avsdpll.v         # PLL module
│   │   ├── avsddac.v         # DAC module
│   │   └── testbench.v       # Testbench for simulation
└── output/
└── compiled_tlv/         # Holds compiled intermediate files if needed
```
## Simulations
The project involves two key stages of simulation:

Pre-synthesis simulation: This is done before any synthesis of the design (mapping it to actual hardware) and focuses on simulating the RTL (Register Transfer Level) design to ensure functional correctness.

Post-synthesis simulation: This is done after synthesis has been performed and takes into account how the design will behave once it is mapped to the actual hardware (e.g., gates, flip-flops, etc.). It is used to verify that the design will work as intended on actual hardware.

## Requirements
To work with this project, you will need the following software tools installed:

Icarus Verilog: This is an open-source Verilog compiler and simulator that will be used to compile the Verilog code in the project. It converts the Verilog code into simulation-ready files.

GTKWave: This is a waveform viewer used to visualize the results of the simulations. It allows you to view the signals and check if your SoC is behaving as expected during the simulations.

The project is intended to be run on a Unix-like environment, meaning it is designed to work on operating systems such as Linux or macOS. If you are using Windows, you may need to use a compatibility layer such as WSL (Windows Subsystem for Linux) to set up a similar environment.

### 🛠️ Cloning the Project **VSDBabySoC** (Top-Level SoC Module)

To begin, clone the VSDBabySoC repository using the following command:

```bash
cd ~/VSD_RISCV_Kasturi
$ git clone https://github.com/manili/VSDBabySoC.git
$ ls
VSDBabySoC

cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
$ ls
images  LICENSE  Makefile  README.md  src

cd ~/VSD_RISCV_Kasturi/VSDBabySoC/src/module/
$ ls
avsddac.v avsdpll.v clk_gate.v pseudo_rand_gen.sv pseudo_rand.sv rvmyth.tlv testbench.rvmyth.post-routing.v testbench.v  vsdbabysoc.v
```

![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_1.png)

---

### TLV to Verilog Conversion for RVMYTH

Initially, only the `rvmyth.tlv` file inside `src/module/` is seen, 
IT IS SO....since the RVMYTH core is written in TL-Verilog.

![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_5.png)

To convert it into a `.v` file for simulation, follow the steps below:

<strong>🔧 TLV to Verilog Conversion Steps</strong>


#### Step 1: Install python3-venv (if not already installed) (these 2 commands to be run at /home/pkasturi)
```bash
$ sudo apt update
$ sudo apt install python3-venv python3-pip
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_3.png)
#### Step 2: Create and activate a virtual environment
```
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
$ python3 -m venv sp_env
$ source sp_env/bin/activate
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_6.png)

#### Step 3: Install SandPiper-SaaS inside the virtual environment (this was done at VSD_RISCV_Kasturi/VSDBabySoC/sp_env/)
```
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/sp_env
$ pip install pyyaml click sandpiper-saas
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_7.png)

#### Step 4: Convert rvmyth.tlv to Verilog  (this is to done at VSD_RISCV_Kasturi/VSDBabySoC/)
```
~/VSD_RISCV_Kasturi/VSDBabySoC/
$ sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_9.png)


✅ After running the above command, rvmyth.v will be generated in the src/module/ directory.

Confirm this by listing the files:

```bash
pkasturi@pkasturi-VirtualBox:~$ cd VSD_RISCV_Kasturi/VSDBabySoC/
pkasturi@pkasturi-VirtualBox:~/VSD_RISCV_Kasturi/VSDBabySoC/$ ls src/module/
avsddac.v  avsdpll.v  clk_gate.v  pseudo_rand_gen.sv  pseudo_rand.sv  rvmyth_gen.v  rvmyth.tlv  rvmyth.v  testbench.rvmyth.post-routing.v  testbench.v  vsdbabysoc.v
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_10.png)


#### Note 
To use this environment in future sessions, always activate it first:
```bash
pkasturi@pkasturi-VirtualBox:~$ source sp_env/bin/activate
```
To exit:
```bash
pkasturi@pkasturi-VirtualBox:~$ deactivate
```
---

##
### Simulation Steps

#### <ins>Pre-Synthesis Simulation</ins>

Run the following command to perform a pre-synthesis simulation:

```bash
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
$ mkdir -p output/pre_synth_sim
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_11.png)

```
$ iverilog -o ~/VSD_RISCV_Kasturi/VSDBabySoC/output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM -I ~/VSD_RISCV_Kasturi/VSDBabySoC/src/include -I ~/VSD_RISCV_Kasturi/VSDBabySoC/src/module ~/VSD_RISCV_Kasturi/VSDBabySoC/src/module/testbench.v
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_12.png)

Then run:
```bash
cd output/pre_synth_sim
$ ./pre_synth_sim.out
```

Explanation:

- DPRE_SYNTH_SIM: Defines the PRE_SYNTH_SIM macro for conditional compilation in the testbench.
- The resulting **pre_synth_sim.vcd** file can be viewed in GTKWave.

#### Viewing Waveform in GTKWave

After running the simulation, open the VCD file in GTKWave: 

```bash
$ gtkwave pre_synth_sim.vcd
```
![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_14.png)

Drag and drop the CLK, reset, OUT (DAC), and RV TO DAC [9:0] signals to their respective locations in the simulation tool

![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_15.png)

In this picture we can see the following signals:

**CLK**: This is the input `CLK signal` of the `RVMYTH` core. This signal comes from the PLL, originally.

**reset**: This is the `input reset` signal of the `RVMYTH` core. This signal comes from an external source, originally.

**OUT**: This is the `output OUT` signal of the `VSDBabySoC` module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital signal which is incorrect), originally.

**RV_TO_DAC[9:0]**: This is the `10-bit output [9:0] OUT port` of the `RVMYTH` core. This port comes from the RVMYTH register #17, originally.

**OUT**: This is a `real datatype wire` which can simulate analog values. It is the `output wire real OUT` signal of the `DAC` module. This signal comes from the DAC, originally. 

This can be viewed by changing the Data Format of the signal to Analog → Step

#### Viewing DAC output in analog mode

Drag and drop the CLK, reset, OUT (DAC) (as analog step), and RV TO DAC [9:0] signals to their respective locations in the simulation tool 

![](https://github.com/poonamkasturi/RISC-V-Reference_SoC-VSD-IITGN-Week_2/blob/main/Image_16.png)


### Trouble shooting tips

   - Module Redefinition: If you encounter redefinition errors, ensure modules are included only once, either in the testbench or in the command line.
   - Path Issues: Verify paths specified with -I are correct. Use full paths if relative paths cause errors.

### Why Pre-Synthesis and Post-Synthesis?

1. **Pre-Synthesis Simulation**: 
   - Focuses only on verifying functionality based on the RTL code.
   - Zero-delay environment, with events occurring on the active clock edge.

2. **Post-Synthesis Simulation (GLS)**:
   - Uses the synthesized netlist (gate-level) to simulate both functionality and timing.
   - Identifies timing violations and potential mismatches (e.g., unintended latches).
   - Helps verify dynamic circuit behavior that static methods may miss.
  
****************************************************************************************************
If need be then rvmyth, avsdpll, avsddac also needto be cloned along with VSDBabySoC
REST all the steps remain same 
only while clonning step 2,3,4 become additional that too if need be 
****************************************************************************************************
#### 1. Setup and Prepare Project Directory
Clone or set up the directory structure as follows

### Simulation Steps
##### Pre-synthesis Simulation
##### Step 1: Clone **VSDBabySoC** (Top-Level SoC Module)
```bash
git clone https://github.com/manili/VSDBabySoC.git
```

##### Step 2: Clone **rvmyth** (RISC-V Core)
```bash
git clone https://github.com/kunalg123/rvmyth.git
```

##### Step 3: Clone **avsdpll** (PLL Module)
```bash
git clone https://github.com/lakshmi-sathi/avsdpll_1v8.git
```

##### Step 4: Clone **avsddac** (DAC Module)
```bash
git clone https://github.com/vsdip/rvmyth_avsddac_interface.git
```

#### DEBUGGING STEPS
##### Debugging Guide: Single Clock Cycle Output Issue in Simulation

When working with digital simulations, encountering an issue where output is only visible for a single clock cycle is common. This issue can have multiple causes, and this guide provides a beginner-friendly explanation of possible sources and debugging steps.
