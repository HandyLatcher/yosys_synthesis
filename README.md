## **RTL Design Week1 Overview**🚀

Introduction to RTL design with Verilog, simulation using Icarus Verilog, and basic synthesis using Yosys.


<p align="center">
  <img src="https://img.shields.io/badge/Program-VSD-blue" alt="Program"/>
  <img src="https://img.shields.io/badge/Week-<week_number>-green" alt="Week"/>
  <img src="https://img.shields.io/badge/<Topic_Label>-orange" alt="Topic"/>
</p>


---

# **Day 1: Fundamentals of RTL Design and Synthesis**🖥️

The first day of the RTL design workshop introduces the essential concepts of digital circuit design at the register-transfer level (RTL). This session focuses on writing hardware descriptions using **Verilog**, verifying functionality with the open-source simulator **Icarus Verilog (iverilog)**, and performing basic **logic synthesis** with **Yosys**.

The workshop emphasizes understanding the complete RTL workflow, from coding and simulation to gate-level synthesis, through structured exercises and explanations.

---

## **1. Simulation, Design, and Testbench Concepts**

**Simulator:**
A simulator is a software tool that evaluates the behavior of digital circuits. It applies input patterns to the design and produces output responses, enabling verification before hardware implementation.

**Design:**
The design represents the hardware functionality described in Verilog code. It captures the intended logic behavior of a digital module.

**Testbench:**
A testbench is an environment used to validate the design. It generates input stimuli, monitors outputs, and ensures that the circuit functions correctly under various scenarios.

<p align="center">
  <img src="https://github.com/user-attachments/assets/65df29b4-a2ae-4b34-9247-404fb1a3c3ee" alt="Image" width="800"/>
</p>

---

## **2. Using Icarus Verilog for Simulation**

**Icarus Verilog (iverilog)** is an open-source Verilog compiler and simulator. The typical simulation workflow involves:

1. Compiling the Verilog design along with its testbench.
2. Running the compiled simulation executable.
3. Generating a waveform file (.vcd) to visualize signals in **GTKWave**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/1e8aeb69-1eac-4690-b3e4-fb68858c498f" alt="Image" width="800"/>
</p>

---

## **Week 1 Lab Setup: RTL Workshop (Root Mode)**

### **Step 1: Switch to Root Mode**

Start a root shell to perform administrative tasks:

```bash
sudo -i
```
Note- Root mode is not strictly necessary for cloning or creating folders in your home directory.

> The shell prompt changes to `root@hostname:#`. All commands are now executed with root privileges.

---

### **Step 2: Install Required Tools**

Install **Icarus Verilog** and **GTKWave**:

```bash
apt update
apt install iverilog
apt install gtkwave
```

> These tools are required for Verilog simulation and waveform visualization.

---

### **Step 3: Navigate to a Workspace in Home Directory**

Even in root mode, you can work in your user’s home directory:

```bash
cd /home/<username>
```

> Replace `<username>` with your Ubuntu username (run `whoami` if unsure).

Create a dedicated folder for lab files:

```bash
mkdir VLSI
cd VLSI
```

---

### **Step 4: Clone the Workshop Repository**

Clone the repository into the `VLSI` folder:

```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

---

### **Step 5: Access the Verilog Files**

Navigate into the cloned repository:

```bash
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

List all files to see available lab exercises:

```bash
ls
```

> This directory contains all Verilog source files and corresponding testbenches needed for simulations.

---

## **Using GTKWave for Simulation and Waveform Analysis**

This section demonstrates the process of compiling a Verilog design, running the simulation, and visualizing signal waveforms using **Icarus Verilog** and **GTKWave**.

---

### **Step 1: Navigate to the Verilog Files Directory**

```bash
cd /home/<username>/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

> Replace `<username>` with your Ubuntu username. This directory contains the Verilog source files and testbenches for the lab exercises.

---

### **Step 2: Compile the Design and Testbench**

```bash
iverilog good_mux.v tb_good_mux.v
```

> This command compiles the multiplexer module (`good_mux.v`) along with its testbench (`tb_good_mux.v`).

---

### **Step 3: Verify Compilation Output**

```bash
ls
```

* After compilation, a file named `a.out` will appear in the directory.
* This is the executable produced by Icarus Verilog.

---

### **Step 4: Run the Simulation**

```bash
./a.out
```

> Running this executable performs the simulation and generates a waveform file (`tb_good_mux.vcd`).

---

### **Step 5: Open GTKWave to Visualize Signals**

```bash
gtkwave tb_good_mux.vcd
```

* GTKWave will launch and display the waveform corresponding to the simulation.
* Users can analyze the behavior of the 2-to-1 multiplexer by observing signal transitions for different input combinations.
  
---

<p align="center">
  <img src="https://github.com/user-attachments/assets/be358150-78e6-4d67-9514-6d36b7189501" alt="Image" width="800"/>
</p>

---

## **Code Analysis for 2-to-1 Multiplexer**

This section describes the Verilog implementation of the **2-to-1 multiplexer** and its corresponding **testbench** used for simulation.

---

### **Testbench Code (`tb_good_mux.v`)**

```verilog
`timescale 1ns / 1ps
module tb_good_mux;

  // Inputs
  reg i0, i1, sel;

  // Outputs
  wire y;

  // Instantiate the Unit Under Test (UUT)
  good_mux uut (
    .sel(sel),
    .i0(i0),
    .i1(i1),
    .y(y)
  );

  initial begin
    $dumpfile("tb_good_mux.vcd");   // Create waveform file
    $dumpvars(0, tb_good_mux);      // Dump all variables
    // Initialize inputs
    sel = 0;
    i0 = 0;
    i1 = 0;
    #300 $finish;                   // End simulation after 300 ns
  end

  // Input stimulus
  always #75 sel = ~sel;
  always #10 i0 = ~i0;
  always #55 i1 = ~i1;

endmodule
```

**Explanation:**

* Initializes inputs and toggles them at different intervals to test all possible input combinations.
* Generates a `.vcd` file for waveform visualization in GTKWave.

---

### **Multiplexer Module (`good_mux.v`)**

```verilog
module good_mux (
    input i0, 
    input i1, 
    input sel, 
    output reg y
);

always @(*) begin
    if (sel)
        y <= i1;
    else
        y <= i0;
end

endmodule
```

**Explanation:**

* Implements a **2-to-1 multiplexer**.
* Output `y` follows `i1` when `sel = 1` and `i0` when `sel = 0`.
* Uses a combinational `always @(*)` block to ensure the output updates immediately when inputs change.

---

<p align="center">
  <img src="https://github.com/user-attachments/assets/8294482e-9151-49a9-86ac-e7e9dec04ace" alt="Image" width="800"/>
</p>


---


### **Getting Started with Yosys and Gate Libraries**

#### **What is Yosys?**

Yosys is an open-source digital synthesis tool widely used for turning Verilog designs into gate-level representations. Essentially, it transforms your high-level HDL description into a blueprint of actual logic gates, ready for hardware implementation.

---

#### **Key Capabilities of Yosys**

* **Synthesis:** Converts Verilog code into a network of logic gates.
* **Optimization:** Refines the design for faster operation, smaller area, or lower power.
* **Technology Mapping:** Matches logical gates to real hardware cells from standard libraries.
* **Verification:** Confirms that the synthesized circuit behaves as intended.
* **Extensibility:** Allows custom scripts and flows for specialized design needs.

---

#### **Why Gate Libraries Have Multiple Variants**

Standard cell libraries (often `.lib` files) provide multiple versions of the same logic gate to suit different design goals. For example, there can be several types of an AND gate, each optimized for:

* **Speed:** High-performance gates for timing-critical paths.
* **Power Consumption:** Low-power gates to save energy.
* **Area:** Compact gates to reduce chip size.
* **Drive Strength:** Stronger gates to drive large fan-outs.
* **Signal Quality:** Gates designed for noise-sensitive or high-speed circuits.

During synthesis, Yosys selects the most suitable gate “flavor” based on your design’s priorities, balancing speed, power, area, and reliability.

---


     
## **Synthesis Lab: 2-Input MUX with Yosys**

Learn how to synthesize a simple 2-input multiplexer (**good\_mux**) using Yosys and map it to a standard cell library.

---

### **Step 1: Launch Yosys**

Open a terminal and start Yosys:

```bash
yosys
```

---

### **Step 2: Load the Standard Cell Library**

Yosys needs a `.lib` file to map your logic to actual gates. Use the following command:

```yosys
read_liberty -lib /path/to/your/library_file.lib
```

**Notes:**

* Replace `/path/to/your/library_file.lib` with the location of your standard cell library (e.g., SkyWater 130nm or any other library).
* This file contains all the gates Yosys can use for mapping.

---

### **Step 3: Read Your Verilog Design**

Load your 2-input MUX Verilog code:

```yosys
read_verilog /path/to/your/verilog_files/good_mux.v
```

**Notes:**

* Replace `/path/to/your/verilog_files/` with the folder containing your Verilog module.
* Ensure the filename matches your module definition, e.g., `good_mux.v`.

---

### **Step 4: Synthesize the Design**

Convert the high-level Verilog into a gate-level netlist:

```yosys
synth -top good_mux
```

* The `-top` flag tells Yosys which module is the top-level design.
* Yosys performs **logic optimization** and prepares the design for mapping.

---

### **Step 5: Technology Mapping**

Map the synthesized logic to real library cells using ABC:

```yosys
abc -liberty /path/to/your/library_file.lib
```

* Yosys replaces generic logic with **real gates** from the library.
* Optimization is applied based on timing, area, and power specifications in the library.

---

### **Step 6: Visualize the Gate-Level Netlist**

Inspect the synthesized netlist graphically:

```yosys
show
```

* This generates a **circuit diagram** showing how the inputs, outputs, and internal gates are connected.
* Useful for verifying your FSM or MUX logic visually.

---

<p align="center">
  <img src="https://github.com/user-attachments/assets/8294482e-9151-49a9-86ac-e7e9dec04ace" alt="Image" width="800"/>
</p>

---

### **Step 7: Export the Synthesized Netlist (Optional)**

Write the final gate-level netlist to a Verilog file:

```yosys
write_verilog -noattr -norename good_mux_netlist.v
```

**Options:**

* `-noattr` → Removes synthesis attributes for a clean netlist.
* `-norename` → Preserves your original signal names instead of using internal numbers.

--- 

Lab Summary🔄

Explored the workflow of digital design, including writing Verilog code, creating testbenches, and running simulations.

Performed the first Verilog simulation using Icarus Verilog and visualized circuit behavior with waveform tools.

Analyzed the functionality of a 2-to-1 multiplexer at both behavioral and gate levels.

Gained hands-on experience with Yosys, understanding how designs are synthesized and how standard cell libraries provide multiple gate options for optimizing speed, power, and area.

---
