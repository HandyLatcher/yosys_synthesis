# **RTL Design Week1 Overview**🚀

This week covers the fundamentals of register-transfer level (RTL) design, including Verilog coding, simulation using Icarus Verilog, and basic logic synthesis with Yosys. The sessions guide through the complete RTL workflow, from writing and testing designs to synthesizing gate-level circuits.


<p align="center">
  <img src="https://img.shields.io/badge/Program-VSD-blue" alt="Program"/>
  <img src="https://img.shields.io/badge/Week-1-green" alt="Week"/>
  <img src="https://img.shields.io/badge/RTL_Design-orange" alt="Topic"/>
</p>


---

# Day 2: Timing, Synthesis & Flip-Flop Optimization ⏱️

Day 2 of the RTL Workshop covers three key topics:

- **Timing Libraries**  
  Overview of `.lib` files in open-source PDKs (e.g., `sky130_fd_sc_hd__tt_025C_1v80.lib`), including delays, setup/hold times, and drive strengths for accurate timing analysis.

- **Synthesis Approaches**  
  Comparison of **hierarchical** vs **flat** synthesis, highlighting modular management versus global optimization trade-offs.

- **Efficient Flip-Flop Coding**  
  Best practices for RTL flip-flop design to optimize area, power, and timing while avoiding hazards.

The session provides a clear understanding of timing library usage, synthesis methodology selection, and optimized flip-flop coding.

---

# Timing Libraries: SKY130 PDK Overview ⚙️

**SKY130 PDK**  
The SKY130 Process Design Kit is an open-source PDK based on SkyWater Technology's 130nm CMOS process. It provides essential models and libraries for IC design, including timing, power, and process variation data.  

**Decoding `tt_025C_1v80`**  
- `tt`: Typical process corner  
- `025C`: Temperature of 25°C  
- `1v80`: Core voltage of 1.8V  

This naming convention specifies the process, voltage, and temperature conditions represented in the timing library.

---

# Opening and Exploring the `.lib` File 📄

To open the `sky130_fd_sc_hd__tt_025C_1v80.lib` file:

1. **Install a text editor** (if not already installed):
```
sudo apt install gedit
```
Open the file:
```
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```
<p align="center"> <img src="https://github.com/user-attachments/assets/33d3ebbc-e3c5-42d1-bd65-0da68eac22dc" width="800" /> </p> 

---

# Hierarchical vs. Flattened Synthesis ⚙️

## Hierarchical Synthesis

**Definition:**  
Retains the module hierarchy as defined in RTL, synthesizing modules separately.

**How it Works:**  
Tools like Yosys process each module independently, using commands such as `hierarchy` to analyze and set up the design structure.

**Advantages:**  
- Faster synthesis time for large designs.  
- Improved debugging and analysis due to maintained module boundaries.  
- Modular approach, aiding integration with other tools.  

**Disadvantages:**  
- Cross-module optimizations are limited.  
- Reporting may require additional configuration.  

**An example of Hierarchical Synthesis:**
Code:
<p align="center">
  <img src="https://github.com/user-attachments/assets/eef57193-3a60-4f6a-ba30-a770c3b616d9" width="800" />
</p>

**Netlist:**
<p align="center">
  <img src="https://github.com/user-attachments/assets/2fbdbac1-93da-4fa3-8fb5-e19dd7f48df4" width="800" />
</p>

---

## Flattened Synthesis ⚡

**Definition:**  
Merges all modules into a single flat netlist, eliminating hierarchy.

**How it Works:**  
The `flatten` command in Yosys collapses the hierarchy, allowing whole-design optimizations.

**Advantages:**  
- Enables aggressive, cross-module optimizations.  
- Results in a unified netlist, sometimes simplifying downstream processes.  

**Disadvantages:**  
- Longer runtime for large designs.  
- Loss of hierarchy complicates debugging and reporting.  
- Can increase memory usage and netlist complexity.

**An example of Flattened Synthesis:**

**Code:**
<p align="center">
  <img src="https://github.com/user-attachments/assets/6db70630-1259-4193-8105-b987a1694772" width="800" />
</p>

**Netlist:**
<p align="center">
  <img src="https://github.com/user-attachments/assets/7000614f-efae-485b-a370-1a0512af3c7c" width="800" />
</p>

---

**Sub Module📂**

(1)It is used when we have multiple instances of same module

(2)It is also used for Divide and Conquer technique

<p align="center">
  <img src="https://github.com/user-attachments/assets/44f913e8-d223-474d-a20f-dd9a10f45775" width="800" />
</p>

---

# Flip-Flop Coding Styles ⚡

Flip-flops are fundamental sequential elements in digital design, used to store binary data. Below are efficient coding styles for different reset/set behaviors.

---

## Asynchronous Reset D Flip-Flop 🛑

```verilog
module dff_asyncres (
    input clk,
    input async_reset,
    input d,
    output reg q
);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
````

* **Asynchronous reset:** Overrides clock, setting `q` to 0 immediately.
* **Edge-triggered:** Captures `d` on rising clock edge if reset is low.

---

## Asynchronous Set D Flip-Flop ⚡

```verilog
module dff_async_set (
    input clk,
    input async_set,
    input d,
    output reg q
);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```

* **Asynchronous set:** Overrides clock, setting `q` to 1 immediately.

---

## Synchronous Reset D Flip-Flop ⏱️

```verilog
module dff_syncres (
    input clk,
    input async_reset,
    input sync_reset,
    input d,
    output reg q
);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```

**Synchronous reset:** Takes effect only on the clock edge.

---

# Simulation and Synthesis Workflow ⚙️

## Icarus Verilog Simulation 🖥️

**For asynchronous reset**:

1. **Compile your design and testbench**  
```bash
iverilog dff_asyncres.v tb_dff_asyncres.v
````

2. **Execute the compiled program**

```bash
./a.out
```

3. **Inspect the waveform output**

```bash
gtkwave tb_dff_asyncres.vcd
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/155c7fcb-5728-4e61-a248-cdf81af0f6b0" width="800" />
</p>


# Synthesis with Yosys ⚙️

## Step-by-Step Workflow 🛠️

1. **Start Yosys**

```tcl
yosys
```

2. **Read the Liberty library**

```tcl
read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

3. **Read your Verilog design**

```tcl
read_verilog /path/to/dff_asyncres.v
```

4. **Synthesize the top module**

```tcl
synth -top dff_asyncres
```

5. **Map flip-flops to library cells**

```tcl
dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

6. **Perform technology mapping**

```tcl
abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
```

7. **Visualize the gate-level netlist**

```tcl
show
```
<p align="center">
  <img src="https://github.com/user-attachments/assets/1bf4dd4f-63ff-43d0-a496-871b2beec62f" width="800" />
</p>

---

## Icarus Verilog Simulation for synchronous reset:

<p align="center">
  <img src="https://github.com/user-attachments/assets/7d62de27-1679-4800-94be-e5292c37451e" width="800" />
</p>

---

## A special opimization:💾
Multiplier without any hardware and with just wires.


<p align="center">
  <img src="https://github.com/user-attachments/assets/13b1cf24-c060-4df9-bca7-6c3965301bab" width="800" />
</p>

---

# Summary 📚
This guide covers essential aspects of Verilog design, including module organization, flip-flop coding styles, simulation with Icarus Verilog, and synthesis with Yosys. Practice these workflows to strengthen RTL design and digital synthesis skills.






