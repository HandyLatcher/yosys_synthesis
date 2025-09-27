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
