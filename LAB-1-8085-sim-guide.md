# Jubin Mitra's 8085 Simulator — User Manual

> A practical guide for installation, navigation, and running programs on Windows 11.

---

## Table of Contents

1. [Installation Guide (Windows 11)](#1-installation-guide-windows-11)
2. [Introduction to the UI Interface](#2-introduction-to-the-ui-interface)
3. [How to Open the 8085 Trainer Kit](#3-how-to-open-the-8085-trainer-kit)
4. [How to Execute Code](#4-how-to-execute-code)
5. [Suggestions & Tips](#5-suggestions--tips)

---

## 1. Installation Guide (Windows 11)

### Step 1 — Install Java

The simulator is a Java-based `.jar` application, so Java must be installed before running it.

1. Open your browser and go to: **https://www.java.com/en/download/**
2. Click **Download Java** and run the installer.
3. Follow the on-screen prompts and complete the installation.

**Verify Java is installed:**

Open **Command Prompt** (`Win + R` → type `cmd` → press Enter) and run:

```
java -version
```

You should see output like:
```
java version "21.0.x" ...
```

If you see a version number, Java is ready.

---

### Step 2 — Download the Simulator

1. Go to the official GitHub page:
   **https://github.com/8085simulator/8085simulator.github.io**

2. Download the file named **`8085Compiler.jar`**.

3. Save it to a convenient location, e.g.:
   ```
   C:\Users\YourName\8085Simulator\8085Compiler.jar
   ```

---

### Step 3 — Run the Simulator

**Option A — Double-click (easiest):**
Simply double-click `8085Compiler.jar`. It should launch directly.

> ⚠️ If it opens as a zip file instead of running, Java is either not installed or not set as the default handler for `.jar` files. Re-install Java and try again.

**Option B — Command Prompt:**
Navigate to the folder containing the file and run:

```
java -jar 8085Compiler.jar
```

---

### Step 4 — Windows 11 Security Note

Windows 11 may show a **SmartScreen warning** when running the `.jar` file for the first time.

- Click **More info** → then **Run anyway**.
- This is expected for downloaded `.jar` files and is safe to proceed.

---

## 2. Introduction to the UI Interface

When the simulator launches, you will see a multi-panel interface divided into the following main areas:

---

### Assembler Editor (Left Panel)

The main code writing area. This is where you type your 8085 assembly language programs.

- Supports **syntax highlighting**
- Supports **auto-indent** and **auto-correct**
- Supports **inline comments** using `;`
- Can load programs written in other simulators

---

### Assembler Workspace (Center Panel)

Populated after you assemble your code. Displays:

| Column | Description |
|---|---|
| Address | Memory address of each instruction |
| Label | Any labels defined in your code |
| Mnemonics | The assembly instruction |
| Hex Code | The machine code (opcode) equivalent |
| M-Cycles | Machine cycles consumed |
| T-States | Clock cycles consumed |

---

### Register Bank (Right Panel)

Shows the live state of all 8085 registers during simulation:

- **Accumulator (A)**
- **General Purpose Registers:** B, C, D, E, H, L
- **Program Counter (PC)**
- **Stack Pointer (SP)**
- **Flag Register** (Sign, Zero, Auxiliary Carry, Parity, Carry)
- **PSW (Program Status Word)** (accumulator+flag 16bit)

Updates in real-time with every step during simulation.

---

### Memory Editor Tab

Lets you directly view and edit memory contents.

Three display modes:
- **Show entire memory** — all 64KB of simulated memory
- **Show only loaded locations** — only addresses with data
- **Store to specific address** — directly write a value to any address

---


### Tools Menu

Accessible from the top menu bar. Includes:

- **Number Conversion Tool** — converts between Hex, Decimal, and Binary
- **Delay Subroutine Tool** — generates delay subroutines for a given frequency
- **Interrupt Service Subroutine Tool** — sets memory values at vector interrupt addresses
- **Code Wizard** — helps beginners write 8085 programs with minimal assembly knowledge

---

## 3. How to Open the 8085 Trainer Kit

The **8085 Trainer Kit** simulates a real hardware lab kit — complete with a hex keypad and 7-segment display. It is the recommended mode for manual opcode entry, mimicking what students do on actual lab hardware.

### Method 1 — Keyboard Shortcut (Fastest)

Press **`F9`**

The Trainer Kit window opens immediately.

### Method 2 — Menu

Go to **Simulator** (or **Tools**) in the top menu bar → click **8085 Trainer Kit**.

---

### Using the Trainer Kit

Once open, you will see:

- A **hex keypad** (0–F, and function keys like EXEC, NEXT, PREV, RESET)
- A **7-segment display** showing the current address and data

**To enter opcodes manually:**

1. Press **RESET** to clear the state.
2. Press **SET/MEM** to enter the starting address mode.
3. Enter your **base address** (e.g., `8`, `0`, `0`, `0`).
4. Press the **Enter key** on your keyboard or the **INR** button on the simulator to confirm.
5. Continue entering opcodes byte by byte. Press **Enter** / **INR** after each byte to advance to the next address.

**To execute the entered code:**

Press **RESET** → **GO** → enter the **base address** of your code → press **EXEC**.

---

## 4. How to Execute Code
---

1. **Write your program** in the Assembler Editor tab.

   Example:
   ```
   MVI A, 05H   ; Load 5 into Accumulator
   MVI B, 03H   ; Load 3 into Register B
   ADD B         ; Add B to A
   HLT           ; Halt
   ```

1. Convert your assembly instructions to opcodes manually (or use the Number Conversion Tool).
2. Press **F9** to open the Trainer Kit.
3. Enter opcodes byte-by-byte using the hex keypad (see Section 3).
4. Press **EXEC** to run.
---

*Manual prepared by Rammani Acharya for Jubin Mitra's 8085 Simulator (Java edition). https://github.com/8085simulator/8085simulator.github.io*
