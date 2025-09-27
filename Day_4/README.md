# **RTL Design Week1 Overview**🚀

This week covers the fundamentals of register-transfer level (RTL) design, including Verilog coding, simulation using Icarus Verilog, and basic logic synthesis with Yosys. The sessions guide through the complete RTL workflow, from writing and testing designs to synthesizing gate-level circuits.


<p align="center">
  <img src="https://img.shields.io/badge/Program-VSD-blue" alt="Program"/>
  <img src="https://img.shields.io/badge/Week-1-green" alt="Week"/>
  <img src="https://img.shields.io/badge/RTL_Design-orange" alt="Topic"/>
</p>


---

## **Day 4: Gate-Level Simulation, Verilog Assignments, and Synthesis-Simulation Mismatch** 🛠️⚡

(1) **Gate-Level Simulation (GLS)**
Covers how synthesized gate-level netlists behave and the methods to simulate them accurately.

(2) **Blocking vs. Non-Blocking Assignments in Verilog**
Explains the differences between `=` (blocking) and `<=` (non-blocking) assignments, including timing effects and correct usage in sequential and combinational logic.

(3) **Synthesis-Simulation Mismatch**
Discusses common causes of mismatches between RTL simulation and post-synthesis behavior, along with strategies to reduce or avoid such discrepancies.

The session combines **theoretical explanations** with **hands-on lab exercises** to reinforce the concepts.

---

## 1. Gate-Level Simulation (GLS) ⚡

**Overview:**  
Gate-Level Simulation is a step in the VLSI verification flow where the **netlist produced by synthesis** is simulated. The purpose is to ensure the circuit behaves correctly once RTL is transformed into actual logic gates.  

**Objectives:**  
- Confirm that the circuit **produces correct outputs**.  
- Examine the effects of **signal propagation delays**.  
- Estimate **post-synthesis power consumption**.  
- Verify **test-related features**, such as scan chains for DFT.  

**Importance:**  
- **Verify Synthesis Results:** Checks that tools accurately convert RTL to gate-level representation.  
- **Timing Checks:** Using annotated delays (like SDF), it helps spot setup/hold timing issues.  
- **Test Verification:** Ensures scan chains and other test circuits function after synthesis.  

**When to Run GLS:**  
- After the RTL has been **synthesized into a netlist**.  
- Before **layout and physical design** to catch potential issues early.  

**Simulation Types:**  
- **Logic-Only GLS:** Ignores or uses minimal delays; focuses purely on functional correctness.  
- **Timing-Aware GLS:** Incorporates realistic timing data to verify critical path behavior.  

---

## 2. Post-Synthesis vs. RTL Mismatches ⚠️

**What It Is:**  
Sometimes the **behavior seen in RTL simulation** differs from what occurs in **gate-level simulation or hardware**, leading to mismatches.  

**Typical Causes:**  
- **Unsupported RTL Constructs:** Use of delays, `initial` blocks, or other non-synthesizable elements.  
- **Incomplete Logic Coding:** Missing `else` statements, incomplete sensitivity lists, or ambiguous constructs.  
- **Tool Differences:** RTL may be interpreted slightly differently by simulation and synthesis tools.  

**Best Practices:**  
- Stick to **synthesizable and unambiguous code**.  
- Always define **all logic paths clearly**.  
- Follow **consistent coding conventions** to reduce post-synthesis surprises.

---


## Blocking vs. Non-Blocking Assignments in Verilog ⚡

Verilog provides two ways to assign values within procedural blocks, each with different timing and use cases.

---

## 1. Blocking Assignments (`=`)

- **Syntax:** `=`  
- **Execution Behavior:** Happens **immediately in sequence**; the next statement waits for the current one to finish.  
- **Typical Usage:** Best suited for **combinational logic**, such as in `always @(*)` blocks.  
- **Example:**
```verilog
always @(*) begin
    y = a & b; // Evaluates immediately
end
````

---

## 2. Non-Blocking Assignments (`<=`)

* **Syntax:** `<=`
* **Execution Behavior:** Updates are **scheduled and applied concurrently** at the end of the time step.
* **Typical Usage:** Designed for **sequential logic**, like in `always @(posedge clk)` blocks.
* **Example:**

```verilog
always @(posedge clk) begin
    q <= d; // Scheduled for end-of-step update
end
```

---

**Key Point:**

* Use **blocking** for combinational logic to maintain predictable execution order.
* Use **non-blocking** for flip-flop or register behavior to avoid race conditions and maintain proper sequential timing.

---

## **Labs**

# Lab 1: 2-to-1 Multiplexer Using Ternary Operator 🔹

**Objective:**  
Implement a simple 2:1 multiplexer using a ternary operator in Verilog.

**Verilog Code:**
```verilog
module ternary_operator_mux (
    input i0,
    input i1,
    input sel,
    output y
);
    assign y = sel ? i1 : i0; // Select i1 if sel=1, else i0
endmodule
````

**Functionality:**

* When `sel = 1`, the output `y` takes the value of `i1`.
* When `sel = 0`, the output `y` takes the value of `i0`.
* Acts as a **2:1 multiplexer** with a compact ternary assignment.

<p align="center">
  <img width="800" alt="Lab 2 Output" src="https://github.com/user-attachments/assets/f24fbf43-a5f7-4c79-be2a-5cfceb008811" />
</p>

---

## Lab 2: Synthesizing the 2:1 MUX Using Yosys ⚡

**Objective:**  
Generate a **gate-level netlist** for the 2:1 multiplexer from Lab 1 using the Yosys synthesis flow.

**Yosys Synthesis Steps:**

```tcl
# 1. Load the standard cell library (optional)
read_liberty -lib <your_lib>.lib

# 2. Read the RTL Verilog file
read_verilog ternary_operator_mux.v

# 3. Run synthesis for the top module
synth -top ternary_operator_mux

# 4. Map the logic to the standard cell library (optional)
abc -liberty <your_lib>.lib

# 5. Export the synthesized gate-level netlist
write_verilog -noattr ternary_operator_mux_net.v
````

**Notes:**

* Replace `<your_lib>.lib` with the actual path to your standard cell library.
* `ternary_operator_mux.v` is the RTL design file from Lab 1.
* The generated file `ternary_operator_mux_net.v` contains the **gate-level netlist** of the multiplexer, ready for GLS or further verification.


<p align="center">
  <img width="800" alt="Lab Output" src="https://github.com/user-attachments/assets/dfcd3784-4b88-444a-9908-b369bddb764e" />
</p>

---

## Lab 3: Gate-Level Simulation (GLS) of the MUX ⚙️

**Objective:**  
Run a **Gate-Level Simulation (GLS)** on the synthesized 2:1 multiplexer to verify its functionality after synthesis.

**GLS Command:**

```bash
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v

````

**Notes:**

* `/path/to/primitives.v` → path to the technology library primitives file.
* `/path/to/sky130_fd_sc_hd.v` → correct standard cell library model file.
* `ternary_operator_mux.v` → synthesized gate-level netlist from Lab 2.
* `testbench.v` → provides input stimulus for simulation.

✅ This ensures the **synthesized netlist behaves the same as the RTL design**, confirming synthesis correctness.

<p align="center">
  <img width="800" alt="Lab 3 Output" src="https://github.com/user-attachments/assets/5c467952-dbdb-41fe-9a53-a705cc36e8da" />
</p>

---


## Lab 4: Bad MUX Example ⚠️

**Objective:**  
Highlight common mistakes when coding a multiplexer in Verilog and demonstrate the corrected approach.

---

### 🚫 Faulty Verilog Code:
```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
````

**Problems:**

1. **Incomplete sensitivity list** → only `sel` is included, but `i0` and `i1` are missing.
2. **Incorrect assignment type** → non-blocking (`<=`) is used inside combinational logic; blocking (`=`) should be used instead.

---

### ✅ Corrected Verilog Code:

```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

**Fixes:**

* `@(*)` ensures all inputs (`i0`, `i1`, `sel`) are considered.
* Uses **blocking assignment (`=`)**, which is the right choice for combinational logic.

<p align="center">
  <img width="800" alt="Lab 4 Output" src="https://github.com/user-attachments/assets/d47cedcd-973e-496e-bf5b-f9417d99cbe4" />
</p>

---

## Lab 5: Gate-Level Simulation of bad_mux ⚠️

**Objective:**  
Run **Gate-Level Simulation (GLS)** on the `bad_mux` module.  

**Expected Outcome:**  
- Simulation may produce **mismatches or warnings** due to the issues in the design (incomplete sensitivity list, non-blocking assignment in combinational logic).  
- Compare with RTL simulation to observe differences in behavior.

<p align="center">
  <img width="800" alt="bad_mux GLS Output" src="https://github.com/user-attachments/assets/693084dc-b7de-4218-a674-9c415fcbbe10" />
</p>

---

## Lab 6: Blocking Assignment Caveat ⚠️

**Objective:**  
Demonstrate a common pitfall when using **blocking assignments (`=`)** in combinational logic.

---

## 🚫 Problematic Verilog Code:
```verilog
module blocking_caveat (
    input a,
    input b,
    input c,
    output reg d
);
    reg x;
    always @ (*) begin
        d = x & c;
        x = a | b;
    end
endmodule
````

**Issue:**

* `d` uses the **previous value of `x`** because assignments are executed sequentially in blocking style.
* This leads to incorrect combinational behavior.

---

## ✅ Corrected Verilog Code:

```verilog
always @ (*) begin
    x = a | b;  // Compute intermediate value first
    d = x & c;  // Use updated value
end
```

**Best Practice:**

* When using blocking assignments, **always assign intermediate variables before using them** to avoid race-like issues.

**Wavefrom without GLS**
<p align="center">
  <img width="800" alt="bad_mux GLS Output" src="https://github.com/user-attachments/assets/e93eb20c-33cf-408c-ae3c-4314e59c8824" />
</p>

**Wavefrom with GLS**

<p align="center">
  <img width="800" alt="Lab 6 Output" src="https://github.com/user-attachments/assets/4fc83810-b238-4c5f-836b-fabfb27aba20" />
</p>

---


## Lab 7: Synthesis of the Blocking Caveat Module ⚡

**Objective:**  
Synthesize the **corrected `blocking_caveat` module** to generate a gate-level netlist and observe its behavior.

**Yosys Synthesis Steps:**

```tcl
# 1. (Optional) Load the standard cell library
read_liberty -lib <your_lib>.lib

# 2. Read the corrected RTL
read_verilog blocking_caveat.v

# 3. Run synthesis for the top module
synth -top blocking_caveat

# 4. Map to the standard cell library (optional)
abc -liberty <your_lib>.lib

# 5. Write the gate-level netlist
write_verilog -noattr blocking_caveat.v
````

**Notes:**

* `<your_lib>.lib` → path to your standard cell library.
* `blocking_caveat.v` → corrected RTL file.
* The output netlist represents the **gate-level synthesized design**, ready for GLS or further verification.

<p align="center">
  <img width="800" alt="Lab 7 Output" src="https://github.com/user-attachments/assets/94e41e11-f4b9-4fc9-a526-596a9341809b" />
</p>

---

## Summary of Key Concepts ✅

- **Gate-Level Simulation (GLS):**  
  Validates the functionality, timing behavior, and test structures of the synthesized netlist.

- **Synthesis-Simulation Mismatch:**  
  Prevent mismatches by writing **synthesizable, unambiguous RTL**.

- **Blocking vs. Non-Blocking Assignments:**  
  - Use **blocking (`=`)** for combinational logic.  
  - Use **non-blocking (`<=`)** for sequential logic.

- **Labs:**  
  Reinforce the above concepts and illustrate **common RTL pitfalls**, including sensitivity list issues, incorrect assignment usage, and combinational sequencing errors.

---
