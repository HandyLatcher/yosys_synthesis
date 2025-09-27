# **RTL Design Week1 Overview**🚀

This week covers the fundamentals of register-transfer level (RTL) design, including Verilog coding, simulation using Icarus Verilog, and basic logic synthesis with Yosys. The sessions guide through the complete RTL workflow, from writing and testing designs to synthesizing gate-level circuits.


<p align="center">
  <img src="https://img.shields.io/badge/Program-VSD-blue" alt="Program"/>
  <img src="https://img.shields.io/badge/Week-1-green" alt="Week"/>
  <img src="https://img.shields.io/badge/RTL_Design-orange" alt="Topic"/>
</p>

---

# Day 5: Optimization in Verilog Synthesis ⚡

**Overview:**  
Day 5 focuses on **optimizing RTL for synthesis**. Key topics include:

- Efficient use of **if-else statements**  
- Proper **for loops** in Verilog  
- Using **generate blocks** for scalable designs  
- Avoiding **inferred latches** caused by incomplete or improper coding  

---

# 1. Conditional Statements in Verilog: If-Else 🔹

**Purpose:**  
Use if-else blocks to **control the flow of execution** based on a condition within procedural constructs like `always`, `initial`, or tasks/functions.

---

### Basic Structure
```verilog
if (condition) begin
    // Executes when the condition evaluates to true
end else begin
    // Executes when the condition evaluates to false
end
````

**Notes:**

* `condition` is any expression returning true (non-zero) or false (zero).
* `begin ... end` groups multiple statements; for a single statement, braces can be omitted.
* Including an `else` block is optional.

---

### Multiple Conditions (Nested If-Else)

```verilog
if (condition1) begin
    // Executes if condition1 is true
end else if (condition2) begin
    // Executes if condition1 is false but condition2 is true
end else begin
    // Executes if neither condition1 nor condition2 is true
end
```

**Tip:**
Nested conditional blocks make it easy to **evaluate several alternatives** in a structured way, avoiding repeated checks.

---

# 2. Inferred Latches in Verilog ⚠️

**Overview:**  
A **latch can be unintentionally inferred** when a combinational block does not assign a value to an output under all conditions. Synthesis tools then assume the signal must **hold its previous value**, creating a latch. This is often **not intended** by the designer.

---

### Problematic Example
```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        if (sel == 1'b1)
            y = a; // 'y' is unassigned when sel == 0
    end
endmodule
````

**Issue:**
When `sel` is `0`, `y` is left undefined. The synthesizer infers a **storage element (latch)** to maintain its previous value.

---

### Corrected Version (Prevent Latch)

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        case(sel)
            1'b1: y = a;
            default: y = 1'b0; // Ensure 'y' is assigned in all paths
        endcase
    end
endmodule
```

**Best Practice:**

* Always **assign outputs in every possible execution path** within combinational blocks.
* Use `else` or `default` statements to avoid **unintended latches**.

---

# 3. Labs: If-Else and Case Statements ⚙️

## Lab 1: Incomplete If Statement

**Objective:**  
Demonstrate how missing assignments in conditional blocks can lead to unintended behavior or inferred latches.

**Verilog Code:**
```verilog
module incomp_if (
    input i0, 
    input i1, 
    input i2, 
    output reg y
);
    always @(*) begin
        if (i0)
            y <= i1; // 'y' is not assigned when i0 = 0
    end
endmodule
````

**Observation:**

* When `i0` is `0`, `y` remains undefined.
* This can cause **unintended latches** during synthesis.

**Lesson:**
* Always ensure that **every output is assigned in all possible conditional paths**.


<p align="center">
  <img width="800" alt="Lab Output" src="https://github.com/user-attachments/assets/88e7f4b6-cf7f-49ec-8e55-e5449da93b50" />
</p>

---

# Lab 2: Synthesis Result of Lab 1 ⚡

**Objective:**  
Synthesize the `incomp_if` module from Lab 1 to observe how incomplete conditional assignments are handled in gate-level synthesis.

**Yosys Synthesis Steps:**
```tcl
# 1. Load standard cell library (optional)
read_liberty -lib <your_lib>.lib

# 2. Read the RTL design
read_verilog incomp_if.v

# 3. Run synthesis for the top module
synth -top incomp_if

# 4. Map to standard cells (optional)
abc -liberty <your_lib>.lib

# 5. Export the synthesized netlist
write_verilog -noattr incomp_if.v
```

<p align="center">
  <img width="800" alt="Lab Output" src="https://github.com/user-attachments/assets/88e7f4b6-cf7f-49ec-8e55-e5449da93b50" />
</p>

---

# Lab 3: Nested If-Else Statement ⚙️

**Objective:**  
Demonstrate how missing assignments in nested if-else structures can lead to **unintended latches** during synthesis.

**Verilog Code:**
```verilog
module incomp_if2 (
    input i0, 
    input i1, 
    input i2, 
    input i3, 
    output reg y
);
    always @(*) begin
        if (i0)
            y <= i1;
        else if (i2)
            y <= i3; // 'y' is not assigned if both i0 and i2 are 0
    end
endmodule
````

**Observation:**

* When `i0` = 0 and `i2` = 0, `y` remains **undefined**, potentially causing **inferred latches**.

**Lesson:**

* Always assign outputs in **all possible paths**, even in nested conditional blocks, to ensure combinational logic behaves as intended.

<p align="center">
  <img width="800" alt="Nested If-Else Output" src="https://github.com/user-attachments/assets/1c850134-4b6e-485b-89f1-c729f007cc80" />
</p>

---

# Lab 4: Synthesis Result of Lab 3 ⚡

<p align="center">
  <img width="800" alt="Synthesis Result of Nested If-Else" src="https://github.com/user-attachments/assets/d8e78f5a-475e-43ac-a0fd-605aeb05e70a" />
</p>

---

# Lab 5: Complete Case Statement ⚙️

**Objective:**  
Demonstrate a **case statement** in Verilog with all possible input conditions covered to avoid inferred latches.

**Verilog Code:**
```verilog
module comp_case (
    input i0, 
    input i1, 
    input i2, 
    input [1:0] sel, 
    output reg y
);
    always @(*) begin
        case(sel)
            2'b00: y = i0;
            2'b01: y = i1;
            default: y = i2; // Covers all remaining cases
        endcase
    end
endmodule
````

**Key Points:**

* The `default` branch ensures **all possible `sel` values are handled**, preventing latches.
* Case statements are a cleaner alternative to nested if-else for multi-way selection.

<p align="center">
  <img width="800" alt="Complete Case Statement Output" src="https://github.com/user-attachments/assets/47d0f039-e466-4514-8f54-f2921693359b" />
</p>

---

**Lab 6: Synthesis Result of Lab 5**

<p align="center">
  <img width="800" alt="Complete Case Statement GLS Output" src="https://github.com/user-attachments/assets/690a0544-87d7-4edd-adc5-6660cb87ca09" />
</p>

---

# Lab 7: Incomplete Case Statement ⚠️

**Objective:**  
Show how **missing branches in a case statement** can lead to **unintended latches** during synthesis.

**Verilog Code:**
```verilog
module incomp_case (
    input i0, 
    input i1, 
    input i2, 
    input [1:0] sel, 
    output reg y
);
    always @(*) begin
        case(sel)
            2'b00: y = i0;
            2'b01: y = i1;
            // Missing cases for 2'b10 and 2'b11 can cause inferred latches
        endcase
    end
endmodule
````

**Key Points:**

* Only two cases are explicitly assigned; other input values leave `y` undefined.
* This triggers **inferred latches** in synthesis tools.
* Best practice: Always include a **`default`** branch to handle all unassigned inputs.


<p align="center">
  <img width="800" alt="Incomplete Case Handling Output" src="https://github.com/user-attachments/assets/02ae4978-082f-4805-aeb5-e4cd8340e621" />
</p>

---

**Lab 8: Synthesis Result of Lab 7**

<p align="center">
  <img width="800" alt="Incomplete Case Statement Output" src="https://github.com/user-attachments/assets/4cb239e

---

# Lab 9: Partial Assignments in Case Statements ⚠️

**Objective:**  
Demonstrate how **incomplete assignments within a case statement** can lead to **unintended latches** on outputs.

**Verilog Code:**
```verilog
module partial_case_assign (
    input i0, 
    input i1, 
    input i2,
    input [1:0] sel,
    output reg y, 
    output reg x
);
    always @(*) begin
        case(sel)
            2'b00: begin
                y = i0;
                x = i2;
            end
            2'b01: y = i1; // 'x' is not assigned here
            default: begin
                x = i1;
                y = i2;
            end
        endcase
    end
endmodule
````

**Key Points:**

* In the `2'b01` case, only `y` is assigned; `x` is left undefined.
* This causes **inferred latches** for `x`.
* Best practice: Ensure **all outputs are assigned in every case branch** or include a `default` branch covering all signals.

**Synthesis Result of module partial_case_assign**

<p align="center">
  <img src="https://github.com/user-attachments/assets/2d3fc036-702b-473a-925f-5a71e423d165" width="800" alt="Simulation Waveform"/>
</p>


---

# Lab 10: Bad Case Handling⚠️

```
module bad_case (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // '?' is a wildcard; be careful with incomplete cases!
    endcase
end
endmodule
```
<p align="center">
  <img src="https://github.com/user-attachments/assets/aef6a9da-2875-4356-8da5-a369b404032d" width="800" />
</p>

---

# 4. Looping Magic in Verilog🔁

In Verilog, `for` loops let you repeat a set of instructions multiple times inside procedural blocks like `always`, `initial`, or functions/tasks. Perfect for iterating over arrays or buses!  

---

## ✨ Syntax Template
```verilog
for (start_value; stop_condition; step) begin
    // Your repeated code here
end
````

**Notes:**

* Must be placed inside procedural blocks (`always`, `initial`, or tasks/functions).
* Only synthesizable when the loop count is known at compile time.

---

## 🛠 Example: 4-to-1 Multiplexer Using a Loop

Here’s a clean way to implement a 4-to-1 MUX with a `for` loop:

```verilog
module mux_4to1_loop (
    input wire [3:0] data,  // 4 data inputs
    input wire [1:0] sel,   // 2-bit selector
    output reg y            // Output
);
    integer idx;

    always @(data, sel) begin
        y = 1'b0; // Default output
        for (idx = 0; idx < 4; idx = idx + 1) begin
            if (idx == sel)
                y = data[idx];
        end
    end
endmodule
```

**How it works:**

* `idx` runs from 0 to 3.
* When `idx` matches the selector `sel`, the corresponding data bit is assigned to `y`.

This approach keeps the code compact and easy to maintain if the number of inputs grows. 🚀

---


#5. 🏗️Build Hardware Dynamically with Generate Blocks

In Verilog, **generate blocks** let you create repetitive hardware structures automatically at compile time. Great for instantiating multiple modules or connecting wires systematically. Often paired with `for` loops and the `genvar` keyword.  

---

## ✨ Syntax Snapshot
```verilog
genvar name;
generate
    for (name = start; name < end; name = name + step) begin : block_label
        // Hardware instantiation or logic
    end
endgenerate
````

**Notes:**

* `genvar` is used as the loop counter and must be an integer.
* The generate block runs **at compile time**, not at simulation runtime.
* Each iteration can create a uniquely labeled block for clarity.

---

## 🔧 Example: Repeating AND Gates

```verilog
module repeated_and_gates (
    input wire [4:0] in,   // Input bus
    output wire [3:0] out  // Output bus
);
    genvar i;

    generate
        for (i = 0; i < 4; i = i + 1) begin : and_block
            and_gate and_inst (
                .a(in[i]),
                .b(in[i+1]),
                .y(out[i])
            );
        end
    endgenerate
endmodule
```

**Explanation:**

* `i` counts from 0 to 3.
* Each iteration generates a separate `and_gate` instance.
* Using `: and_block` gives each generated block a unique hierarchical name for easier debugging. 🔍

This method is perfect for scalable designs—add more gates without rewriting code! 🚀

---

# 6.➕ Ripple Carry Adder (RCA)

A **Ripple Carry Adder** adds binary numbers using a chain of **full adders**. Each full adder handles one bit, and the **carry-out** of one stage feeds the **carry-in** of the next.  

---

## ⚙️ Key Points
- Adds **n-bit numbers** with **n full adders**.  
- Carry “ripples” through the chain, creating a delay proportional to the number of bits.  
- Simple design but slower for wide bit-widths compared to advanced adders like **Carry Lookahead Adders**.  

---

## 🔹 Concept
```
A0 + B0 -> FA0 -> C1 -> FA1 -> ... -> FAn-1 -> Sum, Carry-out
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/725622c1-9870-4bee-9093-d49bc9faaf16" width="800" />
</p>

---

# 🧪 Lab 9: 4-to-1 MUX Using a For Loop

This lab demonstrates how to implement a **4-to-1 multiplexer** using a `for` loop in Verilog.  

```verilog
module mux_generate (
    input wire i0, i1, i2, i3,   // 4 data inputs
    input wire [1:0] sel,        // 2-bit selector
    output reg y                  // Output
);
    // Combine individual inputs into a single 4-bit wire vector
    wire [3:0] inputs;
    assign inputs = {i3, i2, i1, i0};

    integer k; // Loop counter

    // Use a for loop to select the appropriate input based on sel
    always @(*) begin
        y = 1'b0; // Default output
        for (k = 0; k < 4; k = k + 1) begin
            if (k == sel)
                y = inputs[k];
        end
    end
endmodule
````

### ✅ Key Concepts

* The `for` loop iterates over all input lines.
* The selected input is assigned to the output.
* Efficient way to scale multiplexers without writing repetitive code.

<p align="center">
  <img src="https://github.com/user-attachments/assets/da04aba6-a49d-4f69-a958-34b16a27b6fa" width="800" />
</p>

---

# 🧪 Lab 10: 8-to-1 Demultiplexer Using Case Statement

This lab demonstrates an **8-to-1 demultiplexer** using a `case` statement in Verilog.  

```verilog
module demux_case (
    input wire i,             // Input signal
    input wire [2:0] sel,     // 3-bit select
    output wire o0, o1, o2, o3,
    output wire o4, o5, o6, o7
);
    // Internal 8-bit register to hold outputs
    reg [7:0] y_int;

    // Connect internal register to outputs
    assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;

    // Case-based demultiplexing
    always @(*) begin
        y_int = 8'b0; // Default: all outputs low
        case(sel)
            3'b000: y_int[0] = i;
            3'b001: y_int[1] = i;
            3'b010: y_int[2] = i;
            3'b011: y_int[3] = i;
            3'b100: y_int[4] = i;
            3'b101: y_int[5] = i;
            3'b110: y_int[6] = i;
            3'b111: y_int[7] = i;
        endcase
    end
endmodule
````

### ✅ Key Points

* `case` statement efficiently selects one output based on `sel`.
* `y_int` ensures only the selected output is high at a time.
* Easily scalable for larger demultiplexers.

<p align="center">
  <img src="https://github.com/user-attachments/assets/d995b4b3-fd8d-4dbf-9bcd-0b6d1b25ff79" width="800" />
</p>

---


# 🧪 Lab 11: 8-to-1 Demultiplexer Using For Loop

This lab demonstrates an **8-to-1 demultiplexer** using a `for` loop in Verilog.  

```verilog
module demux_generate (
    input wire i,             // Input signal
    input wire [2:0] sel,     // 3-bit selector
    output wire o0, o1, o2, o3,
    output wire o4, o5, o6, o7
);
    // Internal register to hold outputs
    reg [7:0] y_int;

    // Connect internal register to outputs
    assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;

    integer k; // Loop counter

    // For loop-based demultiplexing
    always @(*) begin
        y_int = 8'b0; // Default: all outputs low
        for (k = 0; k < 8; k = k + 1) begin
            if (k == sel)
                y_int[k] = i; // Activate selected output
        end
    end
endmodule
````

### ✅ Key Points

* The `for` loop iterates over all outputs.
* Only the output matching `sel` is set high.
* Makes the design easily scalable for larger demultiplexers.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e44d82a3-de1b-4581-8f45-b7fe654e7c12" width="800" />
</p>

---

  # 🧪 Lab 12: 8-bit Ripple Carry Adder Using Generate Block

This lab demonstrates an **8-bit Ripple Carry Adder (RCA)** implemented with a generate block and full adders in Verilog.  

```verilog
// Full Adder Module
module fa (
    input wire a, b, c,   // Inputs: a, b, carry-in
    output wire co, sum    // Outputs: carry-out, sum
);
    assign {co, sum} = a + b + c; // Simple addition
endmodule

// 8-bit Ripple Carry Adder
module rca (
    input wire [7:0] num1, num2,   // 8-bit inputs
    output wire [8:0] sum           // 9-bit output (includes final carry)
);
    wire [7:0] int_sum;             // Internal sum wires
    wire [7:0] int_co;              // Internal carry wires

    // Instantiate first full adder separately
    fa u_fa_0 (
        .a(num1[0]),
        .b(num2[0]),
        .c(1'b0),
        .co(int_co[0]),
        .sum(int_sum[0])
    );

    // Generate remaining full adders
    genvar i;
    generate
        for (i = 1; i < 8; i = i + 1) begin : fa_gen
            fa u_fa (
                .a(num1[i]),
                .b(num2[i]),
                .c(int_co[i-1]),
                .co(int_co[i]),
                .sum(int_sum[i])
            );
        end
    endgenerate

    // Assign outputs
    assign sum[7:0] = int_sum;
    assign sum[8]   = int_co[7]; // Final carry-out
endmodule
````

### ✅ Key Points

* Uses **generate block** to instantiate multiple full adders efficiently.
* `int_co` wires propagate carry from LSB to MSB.
* Modular design with separate `fa` module improves readability and scalability.

<p align="center">
  <img src="https://github.com/user-attachments/assets/242baea0-87fe-4820-882b-65efdef1510a" width="800" />
</p>

---

# 📝Summary

- Always write **complete `if-else` and `case` statements** to avoid unintended latch behavior.  
- Utilize **for loops** and **generate blocks** to create **scalable and synthesizable Verilog designs**.  
- Ensure **all signals are assigned** in every possible path when designing combinational logic.  
- Use lab exercises to **practice concepts**, verify designs, and analyze **synthesis results**.  

---









