# Riscv_Tapeout_Week0

<details>
<summary><b> Day 1:</b> Design and verify a 2:1 mux </summary>   
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



