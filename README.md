Absolutely! Here’s a **fully rewritten, original version of Day 1**, including theory, lab overview, and workflow, in **formal third-person style**. This is ready for your GitHub Week 1 submission:

---

# **Day 1: Fundamentals of RTL Design and Synthesis**

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

---

## **2. Using Icarus Verilog for Simulation**

**Icarus Verilog (iverilog)** is an open-source Verilog compiler and simulator. The typical simulation workflow involves:

1. Compiling the Verilog design along with its testbench.
2. Running the compiled simulation executable.
3. Generating a waveform file (.vcd) to visualize signals in **GTKWave**.

---
Heading
subh
Workspace and Repository Preparation:

## **Week 1 Lab Setup: RTL Workshop (Root Mode)**

### **Step 1: Switch to Root Mode**

Start a root shell to perform administrative tasks:

```bash
sudo -i
```

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
