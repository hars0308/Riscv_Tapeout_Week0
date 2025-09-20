# Riscv_Tapeout_Week0

<details>
<summary><b>Digital VLSI SOC Design and Planning:</b> Summary </summary>   
<br>

## **1. Chip Modelling Stage (Specification) – O1**

* **Objective**: Define what the chip should do before any hardware coding.
* **Activity**:

  * Use a **C model (specifications in C/C++/SystemC)** to describe functionality.
  * This is a *golden reference model* of the system.
  * Testbench is written in C language for validating expected outputs.
* **Output O1**:

  * Verified software model (C model + testbench) that acts as reference for all future stages.

---

## **2. RTL Design (Soft copy of hardware) – O2**

* **Objective**: Convert specifications into actual digital hardware description.
* **Activity**:

  * Write **RTL (Register Transfer Level) code** in **Verilog/VHDL/SystemVerilog**.
  * Describe the architecture: processor core, memory, peripherals, interfaces.
  * Simulate the RTL against the C model testbench for functional correctness.
* **Blocks involved**:

  * Processor (soft IP or custom design)
  * Peripherals / IPs (UART, SPI, I2C, GPIO, etc.)
  * Analog IPs modeled as functional RTL (for mixed-signal chips)
* **Output O2**:

  * A working RTL description validated against the C model.

---

## **3. Synthesis & Netlist Generation – O3**

* **Objective**: Convert RTL into a gate-level representation that can be manufactured.
* **Activity**:

  * Perform **logic synthesis** to map RTL onto standard cell libraries.
  * Generate **Gate Level Netlist (GLN)** (Synthesized netlist).
  * Include **pre-designed macros** (e.g., SRAM, PLLs) and analog IPs.
* **Blocks**:

  * Synthesized processor netlist
  * Synthesized peripheral IPs
  * Hard macros (SRAM, ROM, etc.)
  * Analog IPs
* **SoC Integration**:

  * Combine all netlists and macros into one top-level SoC design.
  * Add IO pads (GPIOs).
* **Output O3**:

  * Gate-level netlist ready for physical design.

---

## **4. Physical Design (RTL2GDS flow)**

* **Objective**: Convert the synthesized netlist into layout (silicon implementation).
* **Steps**:

  1. **Floorplanning** – Place major blocks (processor, memory, IOs, etc.) inside the chip boundary.
  2. **Placement** – Place standard cells within the floorplan.
  3. **Clock Tree Synthesis (CTS)** – Build clock distribution network.
  4. **Routing** – Connect wires between placed cells.
  5. **DRC/LVS Checks** – Ensure physical design meets fabrication rules (Design Rule Check) and logical consistency (Layout vs Schematic).
* **Output**:

  * Final **GDSII file**, which is the format sent to foundry for chip fabrication.

---

## **5. Post-Silicon Validation – O4**

* **Objective**: Test fabricated chip on real hardware boards.
* **Activity**:

  * Run functional tests using **C testbenches** again on silicon.
  * Verify performance (100–130 MHz in example).
* **Applications**:

  * The final chip can be embedded into devices like smartwatches, Arduino boards, TVs, AC applications, etc.
* **Output O4**:

  * Fully validated SoC ready for mass production.

---

# SoC Design Flow (O1 → O4)

```text
+----------------------+     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     +----------------------+
|                      |     :   O1  Chip Model     : |                      |     :   O2  RTL Design     : |                      |     :   O3  Synthesis     : |                      |
|   (O1) SPEC / C      |:::::::::::::::::::::::::::::|  (O2) RTL (Verilog)   |:::::::::::::::::::::::::::::| (O3) Gate-level Netlist |:::::::::::::::::::::::::::::| (O4) Physical & Post-  |
|   Model (SystemC /   |     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     |   silicon validation   |
|   C reference + TB)  |     : Testbench in C       : |  Functional verify    |     : Test against C model: |  Integrate macros (SRAM)|     : Floorplan → Layout : |   (boards, bring-up)   |
|                      |     : (golden reference)   : |  & simulate (RTL)     |     : & refine RTL         : |  + analog IPs + IO     |     : CTS → Routing →   : |   apps deployment      |
+----------------------+     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     +----------------------+     : : : : : : : : : :     +----------------------+
````
## **Important Notes**

* **O1 = O2 = O3 = O4** means:

  * The same functionality defined in C model (O1) must be preserved through RTL (O2), synthesis (O3), and silicon (O4).
  * Verification is done at each step to ensure no functional mismatch.
* **Testbench**:

  * Written once in C.
  * Reused across all stages for consistency in validation.
* **Soft vs Hard IPs**:

  * **Soft IPs**: RTL code (synthesizable).
  * **Hard IPs (HM)**: Pre-designed blocks like SRAM, PLL, ADC, etc.

---
</details>

<details>
<summary><b>Tools Instructions and installation.:</b> Tool Installation </summary>   
<br>


##  System Requirements

- **OS**: Ubuntu 20.04 or later  
- **RAM**: Minimum 6 GB  
- **Storage**: Minimum 50 GB  
- **CPU**: 4 vCPUs  
- **Virtualization**: If using Windows/Mac, install [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)  

----

##  Tools and Installation

### 1. Yosys – Logic Synthesis Tool
Yosys is an **open-source synthesis tool** that converts Verilog RTL designs into **gate-level netlists**.  
It optimizes circuits and prepares them for physical design.  

**Installation**
```bash
sudo apt-get update
git clone https://github.com/YosysHQ/yosys.git
cd yosys
sudo apt install make build-essential clang bison flex \
libreadline-dev gawk tcl-dev libffi-dev git \
graphviz xdot pkg-config python3 libboost-system-dev \
libboost-python-dev libboost-filesystem-dev zlib1g-dev
make config-gcc
make
sudo make install
````

---

### 2. Icarus Verilog (iverilog) – Simulation Tool

Icarus Verilog is used for **simulating Verilog code** to verify functionality before synthesis and layout.

**Installation**

```bash
sudo apt-get update
sudo apt-get install iverilog
```

---

### 3. GTKWave – Waveform Viewer

GTKWave is a **waveform viewer** that allows users to visualize simulation results (`.vcd` files) and debug RTL behavior.

**Installation**

```bash
sudo apt-get update
sudo apt install gtkwave
```

---

### 4. OpenSTA – Static Timing Analysis

OpenSTA performs **timing analysis** of gate-level netlists.
It checks whether a design meets timing constraints without requiring simulation vectors.

**Installation**

```bash
git clone https://github.com/The-OpenROAD-Project/OpenSTA.git
cd OpenSTA
mkdir build
cd build
cmake ..
make
sudo make install
```

---

### 5. Ngspice – Circuit Simulation

Ngspice is a **SPICE-based simulator** used for analog and mixed-signal circuit simulation at the transistor level.

**Installation**

```bash
wget https://downloads.sourceforge.net/project/ngspice/ng-spice-rework/37/ngspice-37.tar.gz
tar -zxvf ngspice-37.tar.gz
cd ngspice-37
mkdir release
cd release
../configure --with-x --with-readline=yes --disable-debug
make
sudo make install
```

---

### 6. Magic – Layout Editor

Magic is a **VLSI layout tool** that supports layout editing, design rule checking (DRC), and netlist extraction.

**Installation**

```bash
sudo apt-get install m4 tcsh csh libx11-dev tcl-dev tk-dev \
libcairo2-dev mesa-common-dev libglu1-mesa-dev libncurses-dev
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure
make
sudo make install
```

---

### 7. OpenLane – RTL to GDSII Flow

OpenLane is a **complete open-source flow** for chip design.
It integrates synthesis, floorplanning, placement, routing, and signoff checks to generate final **GDSII** layouts.

**Installation**

```bash
# Update system
sudo apt-get update
sudo apt-get upgrade

# Install dependencies
sudo apt install -y build-essential python3 python3-venv python3-pip make git
sudo apt install apt-transport-https ca-certificates curl software-properties-common

# Install Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# Verify Docker
sudo docker run hello-world

# Add user to Docker group
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot
```

After reboot:

```bash
docker run hello-world
```

Now install **OpenLane**:

```bash
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane
make
make test
```

---

## Tools Summary

| Tool               | Description                | Use in VLSI Flow                 |
| ------------------ | -------------------------- | -------------------------------- |
| **Yosys**          | Logic synthesis tool       | RTL → Gate-level netlist         |
| **Icarus Verilog** | Verilog simulator          | Functional verification          |
| **GTKWave**        | Waveform viewer            | Debugging simulation outputs     |
| **OpenSTA**        | Timing analysis tool       | Ensures timing closure           |
| **Ngspice**        | SPICE circuit simulator    | Analog & mixed-signal simulation |
| **Magic**          | Layout editor              | IC layout, DRC, LVS              |
| **OpenLane**       | Complete RTL-to-GDSII flow | Full chip design automation      |

---

Screentshots of the tools installed in Virtual Machine:

1.Yosys
--
<img width="831" height="160" alt="Screenshot From 2025-09-20 15-31-51" src="https://github.com/user-attachments/assets/06d0a471-cb21-460d-a00a-19f1b2a1d96b" />

2.Icarus Verilog
--
<img width="832" height="498" alt="Screenshot From 2025-09-20 15-41-35" src="https://github.com/user-attachments/assets/7b5eab01-dfbf-4f93-bb63-db81de91168a" />

3.GTKWave
--
<img width="832" height="498" alt="Screenshot From 2025-09-20 15-43-03" src="https://github.com/user-attachments/assets/de1f19dd-3dd6-4485-9a5d-8f5f185465cc" />

4.Ngspice
-
<img width="866" height="629" alt="Screenshot From 2025-09-20 16-36-08" src="https://github.com/user-attachments/assets/13e3eafe-8a2a-4612-8150-2c32bb2b5655" />

5.Magic
--
<img width="866" height="629" alt="Screenshot From 2025-09-20 16-46-29" src="https://github.com/user-attachments/assets/3117226d-f494-4c7a-955a-12c0e299ea44" />

6.OpenLane
--
<img width="866" height="629" alt="Screenshot From 2025-09-20 19-55-33" src="https://github.com/user-attachments/assets/bd1e1b7b-b857-4500-8411-003d6a86f128" />

 
</details>

