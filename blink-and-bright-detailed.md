# Instrumentation II
## LAB 01 — From Pins to Sensors: Building a Complete Sensing and Control System -Detailed version

---

| | |
|---|---|
| **Subject** | Instrumentation II |
| **Lab** | LAB 01 |
| **Title** | From Pins to Sensors: Building a Complete Sensing and Control System |
| **Level** | Bachelor of Engineering |
| **Prerequisites** | Basic electronics (Ohm's Law, voltage divider), Basic C/C++ programming concepts |
| **Hardware** | Arduino UNO, Potentiometer, LED (with 220 Ω resistor), LDR, Push Button, LM35, 10 kΩ resistors (×2), Breadboard, Jumper Wires |
| **Software** | Arduino IDE 1.8.x or 2.x |
| **Author** | Er. Rammani Acharya|

---

## Section 0 — Table of Contents

---

- [Section 1 — Safety and Precautions](#section-1--safety-and-precautions)
- [Section 2 — Objectives](#section-2--objectives)
- [Section 3 — Prerequisites](#section-3--prerequisites)
- [Section 4 — Component Identification Guide](#section-4--component-identification-guide)
- [Section 5 — Arduino IDE Setup](#section-5--arduino-ide-setup)

---

- [Section 6 — Step 1: Meeting the Arduino UNO](#section-6--step-1-meeting-the-arduino-uno) — *Board architecture, pin categories, serial communication, and your first sketch*
  - [Background — What Is a Microcontroller?](#background--what-is-a-microcontroller)
  - [Background — The Pins: A Complete Map](#background--the-pins--a-complete-map)
  - [Background — Serial Communication](#background--serial-communication--talking-to-your-pc)
  - [Task 1.1 — Observe a Floating Analog Pin](#task-11--observe-a-floating-analog-pin)
  - [Task 1.2 — Control the Built-in LED](#task-12--control-the-built-in-led)

---

- [Section 7 — Step 2: Reading the Physical World — The Potentiometer and the ADC](#section-7--step-2-reading-the-physical-world--the-potentiometer-and-the-adc) — *How the ADC converts voltage to numbers, the potentiometer as a manual analog source, and the map() function*
  - [Background — Analog-to-Digital Conversion](#background--analog-to-digital-conversion)
  - [Background — The Potentiometer](#background--the-potentiometer)
  - [Task 2.1 — Read Raw Potentiometer ADC Value](#task-21--read-raw-potentiometer-adc-value)
    - [Observation Table 2 — Potentiometer Position vs ADC vs Voltage](#task-21--observation-table)
  - [Task 2.2 — Map ADC Values to a Meaningful Range](#task-22--map-adc-values-to-a-meaningful-range)
    - [Observation Table 3 — ADC vs Mapped Values](#task-22--observation-table)

---

- [Section 8 — Step 3: Controlling LED Brightness — PWM and analogWrite()](#section-8--step-3-controlling-led-brightness--pwm-and-analogwrite) — *LED electrical requirements, PWM duty cycle, and mapping potentiometer to brightness*
  - [Background — The LED](#background--the-led)
  - [Background — PWM (Pulse Width Modulation)](#background--pwm-pulse-width-modulation)
  - [Task 3.1 — Confirm LED Wiring with Basic ON/OFF](#task-31--confirm-led-wiring-with-basic-onoff)
  - [Task 3.2 — Control LED Brightness with the Potentiometer](#task-32--control-led-brightness-with-the-potentiometer)
    - [Observation Table 4 — LED Brightness vs Potentiometer vs PWM](#task-32--observation-table)
  - [Task 3.3 — Display Brightness with a Visual Progress Bar](#task-33--display-brightness-with-a-visual-progress-bar)

---

- [Section 9 — Step 4: Making It Automatic — The LDR and the Voltage Divider](#section-9--step-4-making-it-automatic--the-ldr-and-the-voltage-divider) — *Photoconductive effect, voltage divider derivation, sensor calibration, and automatic LED control*
  - [Background — The LDR](#background--the-ldr)
  - [Background — The Voltage Divider Applied to the LDR](#background--the-voltage-divider-applied-to-the-ldr)
  - [Task 4.1 — Read and Display Raw LDR Values](#task-41--read-and-display-raw-ldr-values)
    - [Observation Table 5 — LDR Light Condition vs ADC vs Voltage](#task-41--observation-table)
  - [Task 4.2 — Calibrate: Find Your Threshold Value](#task-42--calibrate-find-your-threshold-value)
    - [Observation Table 6 — LDR Calibration Values](#task-42--observation-table)
  - [Task 4.3 — Automatic LED: ON in Dark, OFF in Bright](#task-43--automatic-led-on-in-dark-off-in-bright)
    - [Observation Table 7 — LDR Auto LED Verification](#task-43--observation-table)

---

- [Section 10 — Step 5: Human Input — Pull-up, Pull-down, and the Push Button](#section-10--step-5-human-input--pull-up-pull-down-and-the-push-button) — *Floating pin problem, pull-down and pull-up resistors, INPUT_PULLUP, contact bounce, and millis()-based debouncing*
  - [Background — The Floating Pin Problem](#background--the-floating-pin-problem)
  - [Background — Pull-down Resistors](#background--pull-down-resistors)
  - [Background — Pull-up Resistors](#background--pull-up-resistors)
  - [Background — Arduino's Internal Pull-up](#background--arduinos-internal-pull-up)
  - [Background — Contact Bounce](#background--contact-bounce)
  - [Background — Software Debouncing with millis()](#background--software-debouncing-with-millis)
  - [Task 5.1 — Observe a Floating Input Pin](#task-51--observe-a-floating-input-pin)
    - [Observation Table 8 — Floating Pin Readings](#task-51--observation-table)
  - [Task 5.2 — Read Button with External Pull-down](#task-52--read-button-with-external-pull-down)
    - [Observation Table 9 — Button with Pull-down](#task-52--observation-table)
  - [Task 5.3 — Read Button Using INPUT_PULLUP](#task-53--read-button-using-input_pullup)
    - [Observation Table 10 — Button with INPUT_PULLUP](#task-53--observation-table)
  - [Task 5.4 — Toggle LED with Debounced Button](#task-54--toggle-led-with-debounced-button)

---

- [Section 11 — Step 6: Measuring the Environment — The LM35 Temperature Sensor](#section-11--step-6-measuring-the-environment--the-lm35-temperature-sensor) — *LM35 pin identification, 10 mV/°C output characteristic, full ADC-to-temperature derivation, and threshold alerts*
  - [Background — The LM35 Temperature Sensor](#background--the-lm35-temperature-sensor)
  - [Background — Converting ADC to Temperature: Full Derivation](#background--converting-adc-to-temperature-full-derivation)
  - [Task 6.1 — Read and Display Temperature](#task-61--read-and-display-temperature)
  - [Task 6.2 — Formatted Multi-Sensor Display](#task-62--formatted-multi-sensor-display)
    - [Observation Table 11 — All Sensors Combined](#task-62--observation-table)
  - [Task 6.3 — Temperature Threshold Alert](#task-63--temperature-threshold-alert)

---

- [Section 12 — Consolidated Observation Tables](#section-12--consolidated-observation-tables)
  - [Table 1 — Floating Pin Readings](#table-1--floating-pin-readings-task-11)
  - [Table 2 — Potentiometer: Position vs ADC vs Voltage](#table-2--potentiometer-position-vs-adc-vs-voltage-task-21)
  - [Table 3 — Potentiometer: ADC vs Mapped Values](#table-3--potentiometer-adc-vs-mapped-values-task-22)
  - [Table 4 — LED Brightness: Potentiometer vs PWM vs Observation](#table-4--led-brightness-potentiometer-vs-pwm-vs-observation-task-32)
  - [Table 5 — LDR: Light Condition vs ADC vs Voltage](#table-5--ldr-light-condition-vs-adc-vs-voltage-task-41)
  - [Table 6 — LDR Calibration Values](#table-6--ldr-calibration-values-task-42)
  - [Table 7 — LDR Auto LED Verification](#table-7--ldr-auto-led-verification-task-43)
  - [Table 8 — Button: Floating Pin Readings](#table-8--button-floating-pin-readings-task-51)
  - [Table 9 — Button with Pull-down](#table-9--button-with-pull-down-task-52)
  - [Table 10 — Button with INPUT_PULLUP](#table-10--button-with-input_pullup-task-53)
  - [Table 11 — LM35 Temperature: Condition vs Readings](#table-11--lm35-temperature-condition-vs-readings-task-62)

- [Section 13 — Troubleshooting Guide](#section-13--troubleshooting-guide)

- [Section 14 — Post-Lab Questions](#section-14--post-lab-questions)
  - [ADC and Pins (Q1–Q8)](#adc-and-pins-q1q8)
  - [Potentiometer and PWM (Q9–Q16)](#potentiometer-and-pwm-q9q16)
  - [LDR and Voltage Divider (Q17–Q24)](#ldr-and-voltage-divider-q17q24)
  - [Pull-up, Pull-down and Floating Pins (Q25–Q32)](#pull-up-pull-down-and-floating-pins-q25q32)
  - [Debouncing (Q33–Q38)](#debouncing-q33q38)
  - [LM35 and Temperature (Q39–Q44)](#lm35-and-temperature-q39q44)
  - [Cross-topic and Design Questions (Q45–Q47)](#cross-topic-and-design-questions-q45q47)

- [Section 15 — Quick Reference](#section-15--quick-reference)
  - [Arduino Functions Used in This Lab](#arduino-functions-used-in-this-lab)
  - [Formula Sheet](#formula-sheet)
  - [Complete Circuit Pin Assignment](#complete-circuit-pin-assignment-end-of-lab)
  - [Component Summary](#component-summary)

---

## Section 1 — Safety and Precautions

Read this section completely before touching any component or connecting any wire. Every precaution here exists because the described failure has happened in real labs, often more than once. None of these are formalities.

---

**Never wire or rewire a circuit while the Arduino is connected to power.** The Arduino is powered the moment its USB cable is plugged into a PC. Any wire you place, move, or accidentally touch while the board is live can bridge two pins together in ways your circuit was not designed for. The most common consequence is a direct short between the 5V rail and GND — this forces the Arduino's onboard voltage regulator to absorb the full short-circuit current, generating heat rapidly. In mild cases the regulator shuts down temporarily. In worse cases the regulator fails permanently and the board stops powering anything. The habit to build right now is simple: before touching any wire, pull the USB cable out. Plug it back in only when you are done and have visually verified the connections.

---

**Every LED in this lab must have a current-limiting resistor in series with it.** An LED is not a simple resistor — it is a diode with a forward voltage drop of approximately 2.0V. When connected to 5V, the remaining 3V drives current through the LED limited only by whatever resistance is in the path. Without a resistor, that current can be 50 mA or more — far above the 20 mA maximum the LED is rated for and far above the 40 mA maximum a single Arduino pin can safely supply. The LED may appear to work briefly before its internal junction burns out, or the Arduino's output driver on that pin degrades permanently without any visible sign. A 220 Ω resistor in series limits the current to approximately 14 mA — enough for clear visibility, well within safe limits for both the LED and the pin.

---

**The LM35 temperature sensor will be destroyed in seconds if its power pins are reversed.** The LM35 comes in a small three-pin TO-92 package that looks nearly identical from both sides. If VCC and GND are swapped, current flows in the reverse direction through the sensor's internal semiconductor structure. The device heats up within 3 to 5 seconds — you will feel warmth emanating from the sensor body, not from the component absorbing heat from the air. Once this happens, the internal junction is damaged and the sensor produces incorrect readings permanently. There is no recovery. Before every power-on, hold the LM35 with its flat face toward you and confirm from left to right: left pin to 5V, middle pin to your analog input, right pin to GND. Do this check twice before plugging in the USB cable.

---

**The 5V and GND rails on the breadboard must never be shorted together, even briefly.** On a breadboard, the long horizontal rails running along each side carry 5V and GND throughout the entire board. A single jumper wire accidentally bridging these two rails creates a dead short across the USB power supply. The Arduino's voltage regulator attempts to maintain 5V against a near-zero load resistance, which means it tries to push very large current — current that flows through the regulator as heat. Depending on how long the short persists, the outcome ranges from the Arduino resetting itself to the voltage regulator failing permanently. Before plugging in USB, run your finger along the 5V rail and the GND rail and confirm that no wire connects them directly.

---

**Analog pins A0 through A5 and digital pins D0 through D13 accept input voltages strictly between 0V and 5V.** The ATmega328P's internal ADC is designed for this range. A voltage even slightly above 5V — from a sensor powered by a different supply, or from two voltage sources accidentally interacting in the circuit — can damage the ADC permanently. The damage may not manifest immediately as a dead pin; instead, the affected pin may give systematically incorrect readings that are difficult to trace. In this lab, all sensors are powered from the Arduino's own 5V pin, so this risk is low — but it is worth understanding before you work with sensors that have their own power supplies in future projects.

---

**Pins D0 and D1 are reserved for serial communication and must not be used in this lab.** These pins are labeled TX and RX on the board. They are hardwired to the USB interface chip that handles communication between the Arduino and your PC. When you open the Serial Monitor or upload a sketch, data flows through these pins. If any external component is connected to D0 or D1, it interferes with this data, causing corrupted Serial Monitor output, failed uploads, or both. Simply leave these two pins unconnected for the entire duration of this lab.

---

**If anything smells like burning or a component becomes unexpectedly hot, disconnect immediately.** A burning smell during a lab session almost always means current is flowing somewhere it should not — through a reversed component, through a short circuit, or through a component operating far outside its ratings. The correct response is to remove the USB cable immediately, wait for everything to cool, then diagnose the circuit with the power off. Do not attempt to diagnose a live circuit that smells wrong. Report any damaged components to the lab instructor.

---

**If you want to know about the usage of specific functions, functions of specific pins or detailed error messages, you can refer to Arduino's official documentation page.** Refering to the documentation provides direct knowledge from the creators in great detatil. Here is the link to the documentation page of Arduino uno R3, the version we are using for this lab. [Arduinodocs](https://docs.arduino.cc/hardware/uno-rev3/)

---

## Section 2 — Objectives

This lab is a single continuous build. You start with nothing but a bare board and a USB cable, and by the last task you will have assembled a working system that reads ambient light, measures temperature, responds to a button press, and controls an LED's brightness — all simultaneously. That is not a collection of separate experiments. It is one system built piece by piece, where each new component you add depends on understanding what came before it.

The understanding you develop along the way covers the full chain from physical world to digital value and back. You will learn how a microcontroller converts a continuously varying voltage into a number your code can reason about, how that number relates to something physically real like brightness or heat, and how to express it in a form that is meaningful and readable. You will also encounter two problems that appear in virtually every real embedded system — an input pin with no defined state, and a mechanical switch that lies about how many times it was pressed — and you will solve both of them properly. By the time the final sensor is connected, you will be able to look at any part of the circuit or any line of the code and explain exactly what it does, why it is there, and what would break if it were removed.

---

## Section 3 — Prerequisites

You should be comfortable with the following before this lab. Nothing here requires prior Arduino experience — that is introduced from scratch. But the electronics and programming foundations below are assumed.

**From electronics:** Ohm's Law in the form V = IR and its rearrangements. What a resistor does in a circuit. What a voltage divider is — two resistors in series between a supply and ground, with an output voltage measured at their junction. How to calculate that output voltage using the formula V_out = V_in × (R2 / (R1 + R2)).

**From programming:** What a variable is and what a data type means. The difference between an integer and a floating-point number and why that matters in division. What a function is and what it means to call one. What `if` / `else` does. What a loop is and what it means for code to repeat indefinitely.

---

## Section 4 — Component Identification Guide

Before any wiring begins, you need to be able to look at a component and know exactly what it is and which way it goes. Get this right once here and you will not need to second-guess it during any task.

**Resistors — Color Code:**

Resistors encode their value in colored bands. You need two values in this lab.

*220 Ω — for the LED current limiter:*
The band sequence is Red – Red – Brown – Gold. Red = 2, Red = 2, Brown = ×10 multiplier. So: 22 × 10 = 220 Ω. Gold means ±5% tolerance.

*10 kΩ — for the LDR voltage divider and button pull-down:*
The band sequence is Brown – Black – Orange – Gold. Brown = 1, Black = 0, Orange = ×1000 multiplier. So: 10 × 1000 = 10,000 Ω = 10 kΩ.

**LED — Anode and Cathode:**

The longer leg is the anode — connect this side toward the Arduino pin, through the 220 Ω resistor. The shorter leg is the cathode — connect this to GND. If both legs have been trimmed to equal length, look at the plastic body of the LED: the flat edge on the rim marks the cathode side.

**Potentiometer — Three Pins:**

Hold the potentiometer with its rotating shaft pointing upward and its three pins facing you. The leftmost pin goes to GND, the rightmost pin goes to 5V, and the middle pin is the wiper — this is the signal output you connect to an analog pin. If your readings decrease as you turn clockwise instead of increasing, simply swap the GND and 5V connections on the two outer pins.

**LM35 — Pin Identification:**

Hold the sensor with its flat face (the printed side) facing toward you and its three legs pointing downward. From left to right: the left pin is VCC (connect to 5V), the middle pin is VOUT (connect to your analog input pin), and the right pin is GND. Memorize this orientation. Confirm it before every power-on.

**Push Button — 4-Pin Layout:**

A standard tactile button has four pins in a 2×2 arrangement. The two pins on the same side of the button body are always connected to each other internally, regardless of whether the button is pressed. The two pins on opposite sides are connected only when the button is pressed. Place the button so it straddles the center gap of the breadboard, with two pins on each side of the gap. This ensures that the two sides of the button are electrically independent until pressed.

---

## Section 5 — Arduino IDE Setup

Complete all steps in this section before moving to any lab task. All of this needs to happen once at the start of the session.

**Installing the IDE:** Download the Arduino IDE from `https://www.arduino.cc/en/software` and install it with default settings. The installer includes all necessary USB drivers for genuine Arduino boards.

**Connecting the board:** Use the USB Type-A to Type-B cable to connect the Arduino UNO to your PC. The moment it connects, the green LED labeled ON on the Arduino board lights up. This confirms the board is receiving power from the USB port.

**Selecting the board:** In the IDE, navigate to `Tools → Board → Arduino AVR Boards → Arduino UNO`. If you skip this step, the IDE may compile code for a different microcontroller and the upload will fail.

**Selecting the port:** Navigate to `Tools → Port`. A port labeled "Arduino UNO" should appear. On Windows this is typically COM3 or COM4. On Linux or macOS it appears as `/dev/ttyUSB0` or `/dev/cu.usbmodem...`. If nothing appears, the USB driver may need to be installed separately — on Windows, open Device Manager, look for an unknown device under Ports, and install the CH340 or ATmega16U2 driver depending on your board variant.

**The sketch structure:** Every Arduino program is called a sketch. Every sketch must contain exactly two functions, and you cannot change their names or remove either of them:

```cpp
void setup() {
  // Runs exactly once — when the board powers on or after a reset.
  // Everything that needs to happen before the main loop starts goes here:
  // pin mode configuration, Serial initialization, initial states.
}

void loop() {
  // Runs forever in a continuous loop after setup() completes.
  // The Arduino keeps executing this function repeatedly until power is removed.
  // Your main program logic lives here.
}
```

**Verifying and uploading:** The Verify button (✓) compiles your sketch and checks for errors. Error messages appear in the orange panel at the bottom — they always include a line number and a description. Read them. The Upload button (→) compiles and then transfers the compiled program to the Arduino's flash memory. The TX and RX LEDs on the board blink during transfer. "Done uploading." in the status bar means it worked.

**Opening the Serial Monitor:** Go to `Tools → Serial Monitor` or press `Ctrl + Shift + M`. In the bottom-right corner of the Serial Monitor window, set the baud rate dropdown to 9600. This number is the communication speed in bits per second and must always match the value you pass to `Serial.begin()` in your code. A mismatch produces garbled characters instead of readable text.

| Error Message | Meaning | Fix |
|---|---|---|
| `expected ';' before ...` | Missing semicolon at end of statement | Add `;` at the line number shown |
| `was not declared in this scope` | Variable used before being declared | Declare the variable before first use |
| `'pinMode' was not declared` | Sketch structure is broken | Ensure code is inside `setup()` or `loop()` |
| `Board at COMX not found` | Wrong port or board not connected | Check `Tools → Port` and USB connection |
| `stray '\' in program` | Invalid character in code | Delete and retype the affected line |

---

## Section 6 — Step 1: Meeting the Arduino UNO

You have a board in front of you and a USB cable connecting it to your PC. Nothing else is connected yet. Before you touch a single component, you need to understand this board — what it is, what each region does, and what each type of pin is capable of. Everything you build in this lab connects to this board, and every mistake you might make will trace back to a misunderstanding of one of these pins. Spend time here.

### Background — What Is a Microcontroller?

The Arduino UNO is a development board built around the **ATmega328P** microcontroller chip manufactured by Microchip Technology. The board itself is just a convenient package — it adds a USB connector, a power regulator, some indicator LEDs, and pin headers so you can connect wires easily. The intelligence is entirely in the ATmega328P chip at the center of the board.

A microcontroller is not a general-purpose computer. Your laptop has a processor that is designed to run an operating system, manage files, render graphics, and handle many programs simultaneously. A microcontroller is designed to do one job — read inputs, make decisions, and control outputs — and do it reliably, repeatedly, in real time, with very little power. The ATmega328P runs at 16 MHz, meaning it executes up to 16 million instructions per second. It has 32 KB of flash memory where your uploaded sketch is stored permanently even when power is removed, 2 KB of SRAM where variables live while the program runs, and 1 KB of EEPROM for data that needs to survive a reset but can be changed by your code.

The Arduino UNO wraps this chip with supporting circuitry: a 5V voltage regulator that cleans up the USB supply, a second chip (ATmega16U2) that handles USB-to-serial conversion, a 16 MHz crystal oscillator that drives the clock, a reset button, and status LEDs. When you press Upload in the IDE, your code travels through USB, through the ATmega16U2, into the ATmega328P's flash memory, and starts running immediately.

### The Pins — A Complete Map

The pins are the board's interface with the physical world. There are three distinct categories and understanding the difference between them is the single most important thing in this step.

**Power Pins:**

These pins do not carry signals. They supply voltage and ground to your components.

| Pin Label | Function |
|---|---|
| 5V | Regulated 5V output. Powers sensors and components. Total current budget across all 5V usage: approximately 400 mA. |
| 3.3V | 3.3V output from the onboard regulator. Not used in this lab. |
| GND | Ground — 0V reference. Three GND pins exist on the board, all connected internally. Use whichever is nearest to your component. |
| VIN | Raw input voltage from the barrel jack. Not used in this lab. |
| RESET | Momentarily connecting this to GND resets the board and restarts the sketch from the beginning. |

**Digital Pins (D0 – D13):**

These 14 pins operate in binary. They understand exactly two states: HIGH, which means 5V, and LOW, which means 0V. Each pin can be configured as an input (listening for a signal from outside the board) or as an output (sending a signal to a component). You set this in `setup()` using `pinMode()`.

```cpp
// Configuring pin modes — always done in setup(), before loop() runs.

pinMode(9, OUTPUT);   // Pin 9 will SEND signals — used for the LED later.
                      // As output, you control its voltage with digitalWrite().

pinMode(4, INPUT);    // Pin 4 will RECEIVE signals — used for the button later.
                      // As input, you read its voltage with digitalRead().
```

Once configured as OUTPUT, you drive the pin with `digitalWrite()`:

```cpp
digitalWrite(9, HIGH);  // Set pin 9 to 5V — turns an LED on.
digitalWrite(9, LOW);   // Set pin 9 to 0V — turns an LED off.
```

Once configured as INPUT, you read the pin with `digitalRead()`:

```cpp
int state = digitalRead(4);  // Returns HIGH (1) if pin sees ~5V,
                              // returns LOW (0) if pin sees ~0V.
                              // Nothing in between — strictly binary.
```

> ⚠️ **Warning:** D0 (TX) and D1 (RX) are hardwired to the USB communication chip. Connecting anything to these pins during this lab will interfere with uploading and Serial Monitor output. Leave them unconnected.

**PWM-Capable Digital Pins (~3, ~5, ~6, ~9, ~10, ~11):**

Six of the digital pins are marked with a tilde (~) symbol on the board's silkscreen. These pins can do something the other digital pins cannot: simulate a smoothly varying voltage by switching between HIGH and LOW very rapidly. This technique is called **PWM (Pulse Width Modulation)** and you will use it in Step 3 to control LED brightness. For now, simply note which pins carry the ~ symbol — you will need pin ~9 in that step.

**Analog Input Pins (A0 – A5):**

These six pins are fundamentally different from digital pins. While a digital pin only recognizes two states, an analog pin measures any voltage between 0V and 5V continuously and converts it to a number. This is what makes them useful for sensors — light intensity, temperature, and position all vary smoothly rather than switching between two extremes.

The conversion from voltage to number is performed by the ATmega328P's built-in ADC (Analog-to-Digital Converter). You will learn exactly how this works in the next step, at the moment you first use it. For now, just know that `analogRead()` is the function that reads an analog pin:

```cpp
int value = analogRead(A0);  // Measures voltage at A0.
                              // Returns an integer from 0 (= 0V) to 1023 (= 5V).
                              // Everything in between is proportional.
```

### Serial Communication — Talking to Your PC

Once a sketch is uploaded, the Arduino runs independently. It does not need the PC to execute your code. But during development, you need a way to see what is happening inside the program — what values sensors are returning, whether a condition is triggering correctly, whether your math is producing the right result. This is done through serial communication.

The Arduino sends text back to the PC through the same USB cable used for uploading. On the PC side, the Serial Monitor in the IDE receives and displays this text. The protocol used is called UART (Universal Asynchronous Receiver Transmitter). You do not need to understand UART in detail — just know that both sides must agree on a communication speed called the **baud rate**, measured in bits per second. This is why `Serial.begin(9600)` in your code and the 9600 setting in the Serial Monitor must always match. A mismatch produces garbled characters.

```cpp
// Serial communication functions — used in every task in this lab.

Serial.begin(9600);       // Initialize serial at 9600 baud. Always in setup().
                          // Without this line, no Serial.print() calls will work.

Serial.print("Value: ");  // Print text WITHOUT a newline at the end.
                          // The cursor stays on the same line.
                          // Use this when more values follow on the same line.

Serial.println(value);    // Print a value WITH a newline at the end.
                          // The cursor moves to the next line.
                          // Use this as the last print call on each output line.
```

**Formatting numbers for display:**

```cpp
int raw = 742;
float voltage = 3.628;

Serial.println(raw);          // Prints: 742
Serial.println(voltage);      // Prints: 3.63   (2 decimal places by default)
Serial.println(voltage, 4);   // Prints: 3.6280 (4 decimal places — more precise)
Serial.println(voltage, 0);   // Prints: 4      (no decimals — rounded)
```

**Building a structured output line:**

```cpp
// To produce: "ADC: 742  |  Voltage: 3.628 V"
// Use a sequence of print() calls ending with println().

Serial.print("ADC: ");
Serial.print(raw);
Serial.print("  |  Voltage: ");
Serial.print(voltage, 3);
Serial.println(" V");   // println() here ends the line and starts a new one.
```

---

### Task 1.1 — Observe a Floating Analog Pin

**Objective:** Read an analog pin that has nothing connected to it. This is not a useful measurement — it is a demonstration of a problem. Understanding what a floating pin does and why it does it will explain every pull-down and pull-up resistor you use later in this lab.

**Circuit:** Nothing. Just the Arduino connected to your PC via USB. Do not connect anything to pin A0.

```cpp
// Task 1.1 — Reading a floating (unconnected) analog pin.
// A0 has nothing connected to it — no component, no resistor, nothing.
// We are observing what the ADC reads when the input voltage is undefined.
// This matters because buttons, and some sensor configurations, can leave
// a pin in this undefined state if the circuit is not designed carefully.

void setup() {
  Serial.begin(9600);                         // Start serial so we can see the output.
  Serial.println("Task 1.1 — Floating Pin");
  Serial.println("Pin A0 is unconnected. Watch the readings.");
  Serial.println("-------------------------------------------");
}

void loop() {
  int reading = analogRead(A0);               // Read pin A0 — nothing is connected.
                                              // The ADC will sample whatever voltage
                                              // the pin happens to see at this moment.

  Serial.print("A0: ");
  Serial.println(reading);                    // Print the raw ADC value.

  delay(300);                                 // 300ms between readings.
}
```

**Expected Serial Monitor Output:**

```
Task 1.1 — Floating Pin
Pin A0 is unconnected. Watch the readings.
-------------------------------------------
A0: 347
A0: 892
A0: 104
A0: 671
A0: 823
A0: 56
A0: 445
```

The numbers change randomly on every reading. This is the expected behavior of a floating pin. The pin is not connected to anything that defines its voltage, so it picks up electromagnetic interference from the environment — signals from nearby power lines, Wi-Fi, fluorescent lights, and even the motion of your hand near the board. The ADC converts whatever noise voltage it happens to see into a number. The key lesson: an analog pin must always have a component defining its voltage. A sensor connected to it, or a resistor pulling it to a known rail — something. Without that, the reading is meaningless. This is what voltage dividers and pull resistors solve, and you will understand why they are non-negotiable by the end of this lab.

---

### Task 1.2 — Control the Built-in LED

**Objective:** Confirm the board works and introduce `pinMode()`, `digitalWrite()`, and `delay()` in their simplest form. No external components are needed — the Arduino UNO has an LED built onto the board, connected to digital pin D13 through a resistor. Note that it is a good programming practice to declare pin numbers as const variable at top level rather than hardcoding them inside pinMode. 

```cpp
// Task 1.2 — Blinking the built-in LED on pin D13.
// D13 has a LED and resistor built onto the Arduino board itself.
// This is the simplest possible sketch and confirms the board is functional.

void setup() {
  pinMode(13, OUTPUT);   // Configure D13 as an output. Without this, the pin
                         // is in INPUT mode by default and digitalWrite() has
                         // no visible effect on the LED.
  Serial.begin(9600);
  Serial.println("Task 1.2 — Built-in LED Blink");
}

void loop() {
  digitalWrite(13, HIGH);        // Drive D13 to 5V — current flows through the
                                 // onboard resistor and LED, lighting it up.
  Serial.println("LED: ON");
  delay(1000);                   // Pause for 1000 ms = 1 second.
                                 // During this pause, the sketch does nothing else.

  digitalWrite(13, LOW);         // Drive D13 to 0V — no current flows, LED is off.
  Serial.println("LED: OFF");
  delay(1000);
}
```

**Expected Serial Monitor Output:**

```
Task 1.2 — Built-in LED Blink
LED: ON
LED: OFF
LED: ON
LED: OFF
```

The small LED labeled **L** on the Arduino board should blink in sync with the Serial Monitor output. If it does, the board is working correctly and you are ready to connect external components.

**Exercise 1.2:**
- Change both `delay(1000)` values to `delay(100)`. How does the LED behavior change? What does this tell you about how `delay()` controls timing?
- Change the first delay to `delay(100)` and the second to `delay(900)`. The LED is ON for 100 ms out of every 1000 ms total — that is 10% of the time. This ratio is called a **duty cycle of 10%**. Hold this concept in your mind. It becomes the foundation of LED brightness control in Step 3.
- What happens if you remove `pinMode(13, OUTPUT)` from `setup()` and upload again? Does the LED still blink? Why or why not?

---

## Section 7 — Step 2: Reading the Physical World — The Potentiometer and the ADC

You saw in Task 1.1 that a digital pin only knows HIGH or LOW. But most things you want to measure — how bright a room is, how warm it is, how far an object is — do not exist in two states. They vary continuously. To read this kind of information, you need the analog input pins and the ADC. The potentiometer is your first analog sensor — not because it is the most useful, but because it is the most transparent. It gives you direct, manual control over a voltage, so you can turn a knob and immediately see the effect in the numbers. Once you understand how those numbers relate to voltage and to physical position, every other analog sensor in this lab becomes a variation on the same idea.

### Background — Analog-to-Digital Conversion

The ATmega328P has a built-in 10-bit ADC shared across the six analog input pins A0–A5. Here is exactly what happens every time you call `analogRead()`:

The selected pin is connected internally to the ADC circuit. The ADC takes a snapshot — a sample — of the voltage at that pin at that instant. It then compares this sampled voltage against a reference voltage, which by default is the same as the supply voltage: 5V. The comparison produces a 10-bit binary number. Ten bits can represent 2¹⁰ = 1024 distinct values, numbered 0 through 1023. The result is a whole number (an integer) in this range, returned to your sketch.

The mapping is perfectly linear:

```
0V   at the pin  →  ADC returns    0
5V   at the pin  →  ADC returns 1023
2.5V at the pin  →  ADC returns  511  (approximately)
```

**Converting ADC value to voltage:**

```
Voltage (V) = ADC_value × (5.0 / 1023.0)
```

**Converting voltage to expected ADC value:**

```
ADC_value = Voltage (V) × (1023.0 / 5.0)
```

**Voltage resolution — the smallest voltage change the ADC can detect:**

```
Resolution = 5.0V / 1023 ≈ 0.004887 V ≈ 4.89 mV per ADC step
```

Two voltages that differ by less than 4.89 mV will produce the same ADC reading. The ADC cannot distinguish them.

> 💡 **Why `5.0` and not `5`?** In C++, dividing two integers performs integer division — the result is truncated to a whole number. `5 / 1023` evaluates to `0`, not `0.004887`. Writing `5.0` forces the compiler to treat the division as floating-point, giving the correct decimal result. This is one of the most common bugs in Arduino sketches. Always use `5.0 / 1023.0` when you need a decimal result.

**Three worked examples:**

*Example 1:* The voltage at A0 is 3.30V. What does `analogRead(A0)` return?
```
ADC = 3.30 × (1023.0 / 5.0) = 3.30 × 204.6 = 675.2  →  returns 675
```

*Example 2:* `analogRead(A1)` returns 204. What is the voltage at A1?
```
Voltage = 204 × (5.0 / 1023.0) = 204 × 0.004887 = 0.9970 V  ≈  1.00 V
```

*Example 3:* The LM35 temperature sensor (which you will use in Step 6) outputs 10 mV per degree Celsius. At 27°C it produces 270 mV = 0.270 V. What ADC value does this generate?
```
ADC = 0.270 × (1023.0 / 5.0) = 0.270 × 204.6 = 55.2  →  returns 55
```
Keep this example in mind — it previews the exact calculation you will perform in Step 6.

### Background — The Potentiometer

A potentiometer is a manually adjustable voltage divider built into a single package. Inside its housing is a circular resistive track with a total resistance of 10 kΩ. Both ends of this track connect to two of the three external pins. A third pin, the **wiper**, connects to a sliding contact that can be positioned anywhere along the track by rotating the shaft.

As you rotate the shaft, the wiper divides the 10 kΩ track into two portions. At one extreme all 10 kΩ is on one side and zero is on the other. At the other extreme the proportions reverse. At the center, each side is exactly 5 kΩ.

Connect the two outer pins to 5V and GND. The wiper then sits at a voltage that varies linearly with shaft position:

```
V_wiper = 5V × (shaft_position / full_rotation)

Shaft at GND end:   V_wiper = 0V    →  ADC = 0
Shaft at midpoint:  V_wiper = 2.5V  →  ADC ≈ 511
Shaft at 5V end:    V_wiper = 5V    →  ADC = 1023
```

This makes the potentiometer ideal for learning `analogRead()` — it gives you a clean, stable, manually controllable voltage anywhere between 0V and 5V, with no physics to worry about and no conversion formula needed yet. Real-world uses include volume knobs in audio equipment, brightness dials on monitors, position feedback in servo motor control systems, joystick axes, and calibration controls on test equipment.

### Wiring — Potentiometer to Arduino

Disconnect USB before wiring. Connect the potentiometer as follows:

| Potentiometer Pin | Connect To | Wire Color (Suggested) |
|---|---|---|
| Left outer pin | Arduino GND | Black |
| Right outer pin | Arduino 5V | Red |
| Middle pin (wiper) | Arduino A0 | Yellow |

> 💡 If readings decrease as you turn the knob clockwise instead of increasing, swap the GND and 5V connections on the outer pins. The wiper direction reverses.

---

### Task 2.1 — Read Raw Potentiometer ADC Value

**Objective:** See the ADC in action. Read the wiper voltage as a raw number and convert it to voltage simultaneously, so you can verify the conversion formula against real measurements.

```cpp
// Task 2.1 — Reading the potentiometer's wiper voltage.
// The wiper is connected to A0. As you turn the knob, the wiper
// voltage changes from 0V to 5V. analogRead() converts this to 0–1023.
// We also convert back to voltage using the ADC formula to verify it.

const int POT_PIN = A0;   // Name the pin — if you need to change it later,
                          // you only change it here, not everywhere in the code.

void setup() {
  Serial.begin(9600);
  Serial.println("Task 2.1 — Potentiometer Reading");
  Serial.println("------------------------------------");
  Serial.println("  ADC Value  |  Voltage (V)");
  Serial.println("------------------------------------");
}

void loop() {
  int rawValue = analogRead(POT_PIN);               // Read wiper voltage: 0 to 1023.

  float voltage = rawValue * (5.0 / 1023.0);        // Convert ADC to voltage.
                                                    // 5.0 and 1023.0 are floats to
                                                    // force decimal division.
                                                    // Writing 5/1023 would give 0.

  Serial.print("     ");
  Serial.print(rawValue);
  Serial.print("       |     ");
  Serial.print(voltage, 3);                         // 3 decimal places for voltage.
  Serial.println(" V");

  delay(300);                                       // 300ms — fast enough to feel
                                                    // responsive while turning the knob.
}
```

**Expected Serial Monitor Output:**

```
Task 2.1 — Potentiometer Reading
------------------------------------
  ADC Value  |  Voltage (V)
------------------------------------
     0        |     0.000 V     ← knob fully counterclockwise
     127      |     0.621 V
     341      |     1.667 V
     511      |     2.497 V     ← knob at center
     683      |     3.338 V
     921      |     4.500 V
     1023     |     5.000 V     ← knob fully clockwise
```

**Task 2.1 — Observation Table:**

Turn the potentiometer to each position listed and record both the ADC value from the Serial Monitor and the voltage it displays. Then calculate the expected voltage using the formula and check whether they match.

| Knob Position | ADC (measured) | Voltage (displayed) | Calculated Voltage (ADC × 5.0/1023) | Match? |
|---|---|---|---|---|
| Fully counterclockwise | | | | |
| ~25% rotation | | | | |
| ~50% (center) | | | | |
| ~75% rotation | | | | |
| Fully clockwise | | | | |

---

### Task 2.2 — Map ADC Values to a Meaningful Range

The raw ADC value (0 to 1023) is not always the most natural number to work with. If you want to display a percentage, represent an angle, or feed a value to another function that expects a different range, you need to rescale it. Arduino provides a built-in function for this:

```cpp
int result = map(value, fromLow, fromHigh, toLow, toHigh);
```

`map()` performs a linear transformation. It takes `value`, which lies somewhere in the range `fromLow` to `fromHigh`, and returns the proportionally equivalent position within `toLow` to `toHigh`. The math it performs:

```
result = toLow + (value − fromLow) × (toHigh − toLow) / (fromHigh − fromLow)
```

Example: `map(512, 0, 1023, 0, 100)` returns `50`. The value 512 is at the 50% position of 0–1023, so the output is 50% of 0–100 = 50.

```cpp
// Task 2.2 — Mapping ADC range to percentage and to degrees.
// map() rescales a number from one range to another linearly.
// This is essential whenever the ADC range (0–1023) does not match
// what another part of the system expects (e.g. analogWrite needs 0–255,
// percentage displays need 0–100).

const int POT_PIN = A0;

void setup() {
  Serial.begin(9600);
  Serial.println("Task 2.2 — Mapped Values");
  Serial.println("  ADC   |  Percent  |  Angle (0–270°)");
  Serial.println("------------------------------------------");
}

void loop() {
  int rawValue = analogRead(POT_PIN);

  // Map 0–1023 to 0–100 to express position as a percentage.
  int percent = map(rawValue, 0, 1023, 0, 100);

  // Map 0–1023 to 0–270 because most potentiometers rotate 270 degrees.
  int angle   = map(rawValue, 0, 1023, 0, 270);

  Serial.print("  ");
  Serial.print(rawValue);
  Serial.print("   |    ");
  Serial.print(percent);
  Serial.print("%     |    ");
  Serial.print(angle);
  Serial.println("°");

  delay(300);
}
```

**Expected Serial Monitor Output:**

```
Task 2.2 — Mapped Values
  ADC   |  Percent  |  Angle (0–270°)
------------------------------------------
  0     |    0%     |    0°
  256   |    25%    |    67°
  511   |    49%    |    134°
  768   |    75%    |    202°
  1023  |    100%   |    270°
```

> 💡 **Important limitation of `map()`:** It uses integer arithmetic internally and truncates — it does not round. `map(1, 0, 1023, 0, 100)` returns `0`, not `1`. For most sensor applications this truncation error is insignificant. But if you need precise fractional output, perform the calculation manually using `float` variables.

**Exercise 2.2:**
- Using the formula for `map()` written above, manually calculate what `map(300, 0, 1023, 0, 100)` should return. Then verify by running the sketch with the potentiometer at that ADC value.
- Modify Task 2.2 to also display the voltage (from Task 2.1) on the same output line. The output line should show: ADC, voltage, percent, and angle — all four values.
- What does `map(rawValue, 0, 1023, 255, 0)` do differently from `map(rawValue, 0, 1023, 0, 255)`? When would the reversed version be useful?
---

## Section 8 — Step 3: Controlling LED Brightness — PWM and analogWrite()

You can read a value. Now do something physical with it. The most immediate, visible demonstration of analog control is LED brightness — watching a light dim and brighten smoothly as you turn a knob. This step introduces two things: the LED as a controlled output with its electrical requirements, and PWM as the mechanism that allows a digital pin to simulate variable voltage. Keep the potentiometer connected from Step 2. You are adding to the circuit, not replacing it.

### Background — The LED

An **LED** (Light Emitting Diode) emits light when current flows through it in one direction — from anode to cathode. The word diode means it is a one-way device. Current flowing the wrong way produces no light and no damage. Current flowing the right way at the right level produces light proportional to that current.

Two electrical characteristics define how an LED must be used:

**Forward voltage (Vf):** When conducting, an LED drops a fixed voltage across itself regardless of the current. For a standard red LED this is approximately 2.0V. Green and blue LEDs are typically 2.0–3.5V. This voltage is consumed by the LED and cannot be recovered.

**Maximum forward current (If_max):** A standard 5mm LED is rated for 20 mA maximum. Exceed this even briefly and the internal junction degrades or fails outright.

**Calculating the current-limiting resistor:**

The Arduino pin provides 5V. The LED consumes ~2V. The remaining voltage must be dropped across the series resistor to limit current to a safe value. Using Ohm's Law:

```
R = (V_supply − V_forward) / I_desired
  = (5.0V − 2.0V) / 0.010A       ← targeting 10 mA — safe and clearly visible
  = 3.0V / 0.010A
  = 300 Ω
```

220 Ω is the nearest standard value that is below 300 Ω. At 220 Ω the current is:

```
I = (5.0 − 2.0) / 220 = 3.0 / 220 ≈ 13.6 mA
```

13.6 mA is safe for the LED and comfortable for the Arduino pin. This is why 220 Ω is used throughout this lab.

> ⚠️ **Warning:** Never connect an LED from an Arduino pin to GND without a current-limiting resistor. The pin's output driver is rated for 40 mA absolute maximum, and an unprotected LED may draw significantly more. Even if the LED survives, the pin driver degrades. Always include the 220 Ω resistor.

### Background — PWM (Pulse Width Modulation)

Here is the constraint: a digital output pin can only be fully ON (5V) or fully OFF (0V). It cannot output 2.5V or 1.0V directly — there is no mechanism for that. So how do you dim an LED to half brightness?

The answer is PWM. Instead of a steady intermediate voltage, the pin switches between HIGH and LOW very rapidly — at hundreds of times per second. The LED's light output is proportional to the average power delivered, which depends on how much of each cycle the pin spends at HIGH versus LOW. This ratio is the **duty cycle**.

```
Duty Cycle = (Time at HIGH / Total Period) × 100%

0% duty cycle   →  always LOW  → LED completely off
25% duty cycle  →  HIGH 25% of the time → LED at ~25% brightness
50% duty cycle  →  HIGH 50% of the time → LED at ~50% brightness
100% duty cycle →  always HIGH → LED at full brightness
```

The switching frequency on Arduino's PWM pins is approximately 490 Hz (pins ~5 and ~6 run at 980 Hz). At 490 Hz, each cycle lasts about 2 ms — far too fast for your eyes to detect as flicker. What you perceive is a steady brightness equal to the average.

`analogWrite()` generates this PWM signal automatically on the PWM-capable pins (~3, ~5, ~6, ~9, ~10, ~11). It accepts a value from 0 to 255, where 0 means 0% duty cycle and 255 means 100%:

```cpp
analogWrite(9, 0);     // 0% duty cycle — LED fully off.
analogWrite(9, 64);    // 25% duty cycle — quarter brightness.
analogWrite(9, 128);   // 50% duty cycle — half brightness.
analogWrite(9, 255);   // 100% duty cycle — LED fully on.
```

**The range mismatch problem — and how `map()` solves it:**

`analogRead()` returns 0 to 1023. `analogWrite()` expects 0 to 255. These ranges do not match. If you feed an ADC reading directly into `analogWrite()`, any value above 255 gets clamped to 255 and the upper three-quarters of the potentiometer rotation all produce full brightness. The fix is `map()`:

```cpp
int potValue   = analogRead(A0);                   // 0 to 1023
int brightness = map(potValue, 0, 1023, 0, 255);   // rescaled to 0 to 255
analogWrite(9, brightness);                        // correct range for PWM
```

### Wiring — Potentiometer + LED

Keep the potentiometer on A0 from Step 2. Add the LED circuit on pin 9.

| Connection | Details |
|---|---|
| LED anode (long leg) | One end of the 220 Ω resistor |
| Other end of 220 Ω resistor | Arduino Pin 9 |
| LED cathode (short leg) | Arduino GND |

> ⚠️ Confirm the anode (long leg) is on the pin-9 side and the cathode (short leg) is on GND. A reversed LED simply will not light up — reconnect correctly if this happens.

**Full wiring summary for this step:**

| Arduino Pin | Connected To |
|---|---|
| A0 | Potentiometer wiper (middle pin) |
| 5V | Potentiometer right outer pin |
| GND | Potentiometer left outer pin, LED cathode |
| Pin 9 (~PWM) | 220 Ω resistor → LED anode |

---

### Task 3.1 — Confirm LED Wiring with Basic ON/OFF

**Objective:** Verify the LED is correctly wired before applying PWM. A simple blink test confirms polarity and the resistor connection before any analog control is attempted.

```cpp
// Task 3.1 — Basic LED ON/OFF to confirm wiring.
// analogWrite() works on PWM pins, but so does digitalWrite().
// Before adding complexity, verify the LED works with the simplest control.

const int LED_PIN = 9;   // Pin 9 supports PWM (~) — we need this for Task 3.2.

void setup() {
  pinMode(LED_PIN, OUTPUT);   // Must declare output mode before controlling the pin.
  Serial.begin(9600);
  Serial.println("Task 3.1 — LED ON/OFF Confirmation");
}

void loop() {
  digitalWrite(LED_PIN, HIGH);        // Drive pin to 5V — LED fully on.
  Serial.println("LED: ON");
  delay(1000);

  digitalWrite(LED_PIN, LOW);         // Drive pin to 0V — LED off.
  Serial.println("LED: OFF");
  delay(1000);
}
```

If the LED blinks, the wiring is correct. If it does not, recheck the anode/cathode orientation and confirm the 220 Ω resistor is in series, not bypassed.

---

### Task 3.2 — Control LED Brightness with the Potentiometer

**Objective:** Map the potentiometer's ADC range (0–1023) to the PWM range (0–255) and drive the LED accordingly. Turn the knob and watch the LED dim and brighten continuously.

```cpp
// Task 3.2 — Smooth LED brightness control via potentiometer.
// The potentiometer wiper connects to A0. analogRead() gives 0–1023.
// analogWrite() needs 0–255. map() bridges the gap.
// The LED brightness follows the knob position continuously.

const int POT_PIN = A0;
const int LED_PIN = 9;       // Must be a PWM pin. Pin 9 has the ~ symbol on the board.

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  Serial.println("Task 3.2 — LED Brightness Control");
  Serial.println("  POT (0–1023)  |  PWM (0–255)  |  Brightness");
  Serial.println("--------------------------------------------------");
}

void loop() {
  int potValue   = analogRead(POT_PIN);              // Read potentiometer: 0 to 1023.

  int pwmValue   = map(potValue, 0, 1023, 0, 255);   // Rescale to PWM range.
                                                     // Without this, values above 255
                                                     // would all appear as full brightness.

  int percent    = map(potValue, 0, 1023, 0, 100);   // For human-readable display.

  analogWrite(LED_PIN, pwmValue);                    // Set LED brightness via PWM.
                                                     // The pin generates the duty cycle
                                                     // automatically in hardware.

  Serial.print("       ");
  Serial.print(potValue);
  Serial.print("        |      ");
  Serial.print(pwmValue);
  Serial.print("       |    ");
  Serial.print(percent);
  Serial.println("%");

  delay(100);   // 100ms — short enough that the LED responds quickly to knob movement.
}
```

**Expected Serial Monitor Output:**

```
Task 3.2 — LED Brightness Control
  POT (0–1023)  |  PWM (0–255)  |  Brightness
--------------------------------------------------
       0         |       0       |    0%         ← knob at minimum, LED off
       256        |      64       |    25%
       512        |      128      |    50%
       768        |      192      |    75%
       1023       |      255      |    100%       ← knob at maximum, LED fully on
```

**Task 3.2 — Observation Table:**

| Potentiometer Position | POT ADC Value | PWM Value | Observed Brightness |
|---|---|---|---|
| Fully counterclockwise | | | |
| ~25% rotation | | | |
| ~50% (center) | | | |
| ~75% rotation | | | |
| Fully clockwise | | | |

---

### Task 3.3 — Display Brightness with a Visual Progress Bar

**Objective:** Add a text-based visual indicator to the Serial Monitor output so that brightness level is immediately obvious at a glance, without reading a number.

```cpp
// Task 3.3 — Brightness display with a text progress bar.
// A loop prints '#' characters proportional to brightness level.
// This is a common technique for Serial Monitor debugging — turning
// a number into a visual shape makes it easier to spot trends.

const int POT_PIN = A0;
const int LED_PIN = 9;

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  Serial.println("Task 3.3 — Brightness Bar Display");
}

void loop() {
  int potValue = analogRead(POT_PIN);
  int pwmValue = map(potValue, 0, 1023, 0, 255);
  int percent  = map(potValue, 0, 1023, 0, 100);

  analogWrite(LED_PIN, pwmValue);

  Serial.print("Brightness [");

  int bars = percent / 10;           // Each '#' represents 10% brightness.
                                     // Dividing percent by 10 gives 0–10 bars.

  for (int i = 0; i < 10; i++) {
    if (i < bars) {
      Serial.print("#");             // Filled bar for each 10% increment reached.
    } else {
      Serial.print("-");             // Empty dash for remaining segments.
    }
  }

  Serial.print("] ");
  Serial.print(percent);
  Serial.println("%");

  delay(150);
}
```

**Expected Serial Monitor Output:**

```
Brightness [----------] 0%
Brightness [##--------] 20%
Brightness [#####-----] 50%
Brightness [########--] 80%
Brightness [##########] 100%
```

**Exercise 3.3:**
- Move the LED wire from pin 9 to pin 8. Pin 8 does not have the ~ symbol — it is not PWM-capable. Upload Task 3.2. What does the LED do when you turn the knob? Why? What does this tell you about `analogWrite()` on a non-PWM pin?
- Modify Task 3.2 so that when the potentiometer is at 0%, the Serial Monitor prints "LED OFF" instead of "0%", and when at 100% it prints "LED FULL". All intermediate values print normally.
- What would happen if you wrote `analogWrite(LED_PIN, potValue)` directly — without using `map()` first? For what range of potentiometer positions would the LED be at full brightness? Calculate this.

---

## Section 9 — Step 4: Making It Automatic — The LDR and the Voltage Divider

You have been controlling the LED manually with a knob. Now remove the human from the equation entirely. The LED should respond to the environment on its own — turning on when the room is dark, turning off when there is enough light. This is the operating principle of every automatic street light, every laptop keyboard that brightens in a dark room, and every camera that adjusts its exposure. The component that senses light is the LDR. But the LDR alone cannot connect to an Arduino pin — it needs a voltage divider circuit to convert its resistance change into a voltage change. Keep the LED and potentiometer connected. You are adding to the circuit.

### Background — The LDR

An **LDR** (Light Dependent Resistor), also called a photoresistor, is a two-terminal passive component whose resistance decreases as the intensity of light hitting its surface increases. It contains a semiconductor material — typically cadmium sulfide (CdS) — that exhibits the **photoconductive effect**: photons striking the surface transfer energy to bound electrons, freeing them to carry current. More photons mean more free electrons, which means lower resistance.

| Light Condition | Approximate LDR Resistance |
|---|---|
| Bright sunlight or direct flashlight | 100 Ω – 1 kΩ |
| Normal indoor lighting | 5 kΩ – 15 kΩ |
| Dim light / evening | 30 kΩ – 100 kΩ |
| Complete darkness | 500 kΩ – 1 MΩ or more |

Real-world applications of LDRs include automatic street lights that activate at dusk, solar garden lights, camera exposure meters that adjust aperture or shutter speed based on ambient brightness, laptop screen brightness controllers, and alarm systems that detect when a light beam is interrupted.

**The core problem:** The LDR gives you a resistance. `analogRead()` reads a voltage. A resistor connected between a pin and nothing defines no voltage — as you saw in Task 1.1, the pin just floats. You need a circuit that converts the LDR's resistance change into a voltage change. That circuit is the voltage divider.

### Background — The Voltage Divider Applied to the LDR

Place the LDR and a fixed 10 kΩ resistor in series between 5V and GND. Measure the voltage at their junction with `analogRead()`.

```
        5V
         |
       [LDR]          ← resistance changes with light (upper arm)
         |
         +──────────→ A1  (read voltage here)
         |
       [10 kΩ]        ← fixed resistor (lower arm)
         |
        GND
```

The voltage at the junction is given by the voltage divider formula:

```
V_out = V_in × (R_lower / (R_upper + R_lower))
      = 5V   × (10,000  / (R_LDR   + 10,000))
```

**In bright light** (R_LDR ≈ 500 Ω):
```
V_out = 5 × (10,000 / (500 + 10,000))
      = 5 × (10,000 / 10,500)
      = 5 × 0.952
      ≈ 4.76 V   →  ADC ≈ 976   (high number = bright)
```

**In normal room light** (R_LDR ≈ 10 kΩ):
```
V_out = 5 × (10,000 / (10,000 + 10,000))
      = 5 × 0.500
      = 2.50 V   →  ADC ≈ 511
```

**In darkness** (R_LDR ≈ 500 kΩ):
```
V_out = 5 × (10,000 / (500,000 + 10,000))
      = 5 × 0.0196
      ≈ 0.098 V  →  ADC ≈ 20    (low number = dark)
```

The conclusion is clear: **a high ADC reading means bright light; a low ADC reading means darkness.** The LED control logic follows directly — if the reading is low, turn the LED on; if it is high, turn it off.

**Why 10 kΩ for the fixed resistor?** The fixed resistor should fall within the LDR's operating resistance range to produce the most useful voltage swing. The LDR ranges from roughly 500 Ω (bright) to 500 kΩ+ (dark). A fixed resistor of 10 kΩ sits near the middle of this logarithmic range. Choosing a very small fixed resistor (100 Ω) would compress all the dark readings near 0V, making it hard to distinguish different shades of darkness. Choosing a very large fixed resistor (1 MΩ) would compress the bright readings. 10 kΩ is the conventional balanced choice for a general-purpose light sensor.

> ⚠️ **Note:** The LDR has no polarity — either leg can connect to 5V or to the junction. Orientation only determines which direction the voltage increases with light.

### Wiring — LDR Added to Existing Circuit

Keep the potentiometer on A0 and the LED on Pin 9. Add the LDR circuit on A1.

| Connection | Arduino Pin |
|---|---|
| LDR — one leg | 5V |
| LDR — other leg + 10 kΩ resistor top leg | A1 |
| 10 kΩ resistor bottom leg | GND |
| Potentiometer wiper | A0 (unchanged) |
| LED anode (via 220 Ω) | Pin 9 (unchanged) |
| LED cathode | GND (unchanged) |

---

### Task 4.1 — Read and Display Raw LDR Values

**Objective:** Explore what values your specific LDR produces in your specific lighting environment before writing any control logic. You cannot choose a meaningful threshold without first knowing the sensor's range.

```cpp
// Task 4.1 — Observing LDR ADC values across light conditions.
// No control logic yet. Just read, display, and build intuition.
// Cover the LDR with your hand, shine a light on it, and watch
// how the numbers change. Record these before moving to Task 4.2.

const int LDR_PIN = A1;   // LDR voltage divider output on A1.
                          // A0 is kept for the potentiometer.

void setup() {
  Serial.begin(9600);
  Serial.println("Task 4.1 — LDR Raw Reading");
  Serial.println("  ADC  |  Voltage");
  Serial.println("---------------------");
}

void loop() {
  int ldrRaw    = analogRead(LDR_PIN);
  float voltage = ldrRaw * (5.0 / 1023.0);   // Convert to voltage for reference.

  Serial.print("  ");
  Serial.print(ldrRaw);
  Serial.print("  |  ");
  Serial.print(voltage, 3);
  Serial.println(" V");

  delay(400);
}
```

**Task 4.1 — Observation Table:**

| Light Condition | LDR ADC (0–1023) | Voltage (V) |
|---|---|---|
| Normal room lighting | | |
| LDR covered completely with hand | | |
| Phone flashlight directly on LDR | | |
| Near a window (daytime) | | |
| Overhead light directly above LDR | | |

---

### Task 4.2 — Calibrate: Find Your Threshold Value

There is no universally correct threshold value. It depends on your LDR's characteristics, the 10 kΩ resistor's exact value, and the lighting in your lab. You must measure it for your setup.

```cpp
// Task 4.2 — Calibration sketch: automatically tracks min and max seen values
// and suggests a threshold midway between them.
// Run this while covering and uncovering the LDR for 30 seconds.
// The suggested threshold will stabilize and become your value for Task 4.3.

const int LDR_PIN = A1;

int minVal = 1023;   // Will be replaced by the lowest reading seen.
int maxVal = 0;      // Will be replaced by the highest reading seen.

void setup() {
  Serial.begin(9600);
  Serial.println("Task 4.2 — LDR Calibration");
  Serial.println("Cover and expose the LDR. Watch the suggested threshold.");
  Serial.println("----------------------------------------------------------");
}

void loop() {
  int ldrRaw = analogRead(LDR_PIN);

  if (ldrRaw < minVal) minVal = ldrRaw;   // Update minimum if a new low is found.
  if (ldrRaw > maxVal) maxVal = ldrRaw;   // Update maximum if a new high is found.

  int threshold = (minVal + maxVal) / 2;  // Midpoint between darkest and brightest.

  Serial.print("Now: ");
  Serial.print(ldrRaw);
  Serial.print("  Min: ");
  Serial.print(minVal);
  Serial.print("  Max: ");
  Serial.print(maxVal);
  Serial.print("  → Suggested threshold: ");
  Serial.println(threshold);

  delay(300);
}
```

**Task 4.2 — Calibration Table:**

| Measurement | ADC Value |
|---|---|
| Minimum reading (darkest condition) | |
| Maximum reading (brightest condition) | |
| Chosen threshold (midpoint) | |

**Write your threshold here:** ____________ — You will use this number in Task 4.3.

---

### Task 4.3 — Automatic LED: ON in Dark, OFF in Bright

**Objective:** Implement the automatic light-sensing LED controller using the calibrated threshold value from Task 4.2.

```cpp
// Task 4.3 — Automatic LED control based on LDR reading.
// If the ADC reads below the threshold → room is dark → LED on.
// If the ADC reads above the threshold → room is bright → LED off.
// Replace THRESHOLD with the value you found in Task 4.2.

const int LDR_PIN   = A1;
const int LED_PIN   = 9;
const int THRESHOLD = 400;   // ← Replace with YOUR calibrated value from Task 4.2.
                             //   This is not a magic number. It is specific to
                             //   your sensor, your resistor, and your room.

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  Serial.println("Task 4.3 — Automatic Light Control");
  Serial.print("Threshold: ");
  Serial.println(THRESHOLD);
  Serial.println("  LDR ADC  |  LED State  |  Condition");
  Serial.println("------------------------------------------");
}

void loop() {
  int ldrRaw = analogRead(LDR_PIN);

  if (ldrRaw < THRESHOLD) {
    // Low ADC reading means low V_out, which means high R_LDR, which means darkness.
    // Darkness → turn LED on.
    digitalWrite(LED_PIN, HIGH);
    Serial.print("   ");
    Serial.print(ldrRaw);
    Serial.println("     |   ON        |  Dark");
  } else {
    // High ADC reading means high V_out, which means low R_LDR, which means bright.
    // Bright → turn LED off.
    digitalWrite(LED_PIN, LOW);
    Serial.print("   ");
    Serial.print(ldrRaw);
    Serial.println("     |   OFF       |  Bright");
  }

  delay(200);
}
```

**Expected Serial Monitor Output:**

```
Task 4.3 — Automatic Light Control
Threshold: 400
  LDR ADC  |  LED State  |  Condition
------------------------------------------
   820      |   OFF       |  Bright
   815      |   OFF       |  Bright
   312      |   ON        |  Dark       ← hand placed over LDR
   295      |   ON        |  Dark
   819      |   OFF       |  Bright     ← hand removed
   821      |   OFF       |  Bright
```

**Task 4.3 — Verification Table:**

| Condition Created | Expected LED State | Actual LED State | ADC Reading | Correct? |
|---|---|---|---|---|
| Normal room lighting | OFF | | | |
| LDR fully covered | ON | | | |
| Phone flashlight on LDR | OFF | | | |
| Dimmed lights (if available) | ON | | | |

**Exercise 4.3:**
- Your `if` condition is `ldrRaw < THRESHOLD`. What single character change reverses the behavior — LED on when bright, off when dark?
- What happens if you set `THRESHOLD` to `0`? To `1023`? What does this tell you about the importance of calibration?
- When the LDR reading sits right at the threshold value, the LED may flicker rapidly as small fluctuations push it above and below. Search for the term **hysteresis** in the context of comparators and control systems. Describe in your own words how you would modify the `if/else` logic to eliminate this flickering using a high threshold and a low threshold instead of a single value.
- The potentiometer is still connected. Modify Task 4.3 so that the LED does not simply turn ON or OFF in darkness, but instead dims proportionally to how dark it is — the darker the room, the brighter the LED. You will need `analogWrite()` and `map()`.
---

## Section 10 — Step 5: Human Input — Pull-up, Pull-down, and the Push Button

Every circuit so far has responded to the environment automatically. Now add a human to the system. A push button is the most direct form of human input — press it, something happens. It sounds trivial. In practice, reading a button correctly requires solving two distinct problems that trip up nearly every person building their first embedded system. The first problem appears before the button is even pressed. The second appears the moment it is. Understanding both here will save you significant debugging time in every future project.

### Background — The Floating Pin Problem

Configure a digital pin as `INPUT` and connect nothing to it. The pin is now electrically isolated — not connected to 5V, not connected to GND, not connected to anything. In this state the pin's voltage is undefined. It picks up stray electromagnetic signals from the environment: nearby power cables, wireless devices, fluorescent lighting, the electrical field of your own hand moving near the board. `digitalRead()` on such a pin returns HIGH and LOW in an unpredictable, seemingly random sequence even when you are not pressing anything.

This is directly relevant to a button. A button in the open (unpressed) position connects to nothing. The pin has no path to a defined voltage. It floats. You cannot tell whether a random HIGH reading is the button being pressed or just environmental noise. You need a way to define what the pin reads in the absence of a button press. That is exactly what pull-down and pull-up resistors do.

### Background — Pull-down Resistors

A pull-down resistor connects the input pin to GND through a resistance of 10 kΩ. When the button is not pressed, this resistor provides the pin's only electrical connection — to GND. The pin is held firmly at 0V regardless of environmental noise. When the button is pressed, it connects the pin directly to 5V. The small current that flows through the 10 kΩ resistor to GND is negligible and does not prevent the pin from reading HIGH.

```
    5V ──[Button]──┬──→ Digital Pin (INPUT)
                   │
                [10 kΩ]     ← pull-down: holds pin at GND when button open
                   │
                  GND
```

| Button State | Pin Voltage | `digitalRead()` returns |
|---|---|---|
| Not pressed | 0V (held by 10 kΩ to GND) | LOW (0) |
| Pressed | 5V (connected through button) | HIGH (1) |

The logic is intuitive: not pressed = LOW, pressed = HIGH.

### Background — Pull-up Resistors

A pull-up resistor connects the input pin to 5V through 10 kΩ. The resting state of the pin is HIGH. When the button is pressed, it connects the pin to GND, overriding the pull-up and pulling the pin LOW.

```
    5V ──[10 kΩ]──┬──→ Digital Pin (INPUT)
                  │
               [Button]     ← button pulls pin to GND when pressed
                  │
                 GND
```

| Button State | Pin Voltage | `digitalRead()` returns |
|---|---|---|
| Not pressed | 5V (held by 10 kΩ to 5V) | HIGH (1) |
| Pressed | 0V (connected through button to GND) | LOW (0) |

The logic is inverted compared to pull-down: not pressed = HIGH, pressed = LOW. This surprises many people. Keep it in mind — it determines how your `if` condition must be written.

### Background — Arduino's Internal Pull-up

The ATmega328P has built-in pull-up resistors on every digital pin. You activate them in software:

```cpp
pinMode(4, INPUT_PULLUP);   // Activates the internal pull-up on pin 4.
                            // No external resistor needed.
                            // Pin now rests at HIGH when nothing drives it.
                            // Button must connect pin to GND to be detected.
```

The internal pull-up resistance is approximately 20 kΩ to 50 kΩ. The behavior is identical to an external pull-up: resting HIGH, goes LOW when a button connects it to GND. The logic is inverted: **pressed = LOW, released = HIGH.** `INPUT_PULLUP` is the most convenient option for simple buttons — it eliminates the external resistor entirely.

**Full comparison:**

| Configuration | External Parts | Resting State | Pressed State | Logic |
|---|---|---|---|---|
| No resistor (floating) | None | Random | Random | Unreliable — do not use |
| External pull-down (10 kΩ to GND) | 10 kΩ resistor | LOW | HIGH | Normal (pressed = HIGH) |
| External pull-up (10 kΩ to 5V) | 10 kΩ resistor | HIGH | LOW | Inverted (pressed = LOW) |
| `INPUT_PULLUP` (internal) | None | HIGH | LOW | Inverted (pressed = LOW) |

### Background — Contact Bounce

When a mechanical switch closes, the metal contacts inside do not make clean, instantaneous contact. They physically bounce — colliding, separating, colliding again — many times in rapid succession before settling into stable contact. The entire bounce sequence lasts between 1 ms and 20 ms depending on the button's construction.

To a human, a button press feels instantaneous. To the Arduino running at 16 MHz, 10 milliseconds contains 160,000 clock cycles — enough time to register dozens of false transitions. One physical press can appear in your code as 5, 10, or 30 separate press-and-release events.

```
One physical press — what the pin actually sees:

Pressed:  _____||_|__|||||||||||||||||||||_______
               ↑ bounce zone (1–20 ms)    ↑ stable

Intended: _____||||||||||||||||||||||||||||_______
```

Contact bounce causes visible problems whenever your code takes an action on each detected press — toggling an LED, incrementing a counter, sending a command. Without debouncing, a single press might toggle the LED multiple times.

### Background — Software Debouncing with `millis()`

The simplest debounce approach is `delay(50)` after detecting a press — wait for 50 ms and ignore readings during that time. This works, but `delay()` blocks the entire sketch. While it is waiting, nothing else in `loop()` runs. In any application where multiple things need to happen simultaneously — which describes almost every real embedded system — a blocking delay breaks the rest of the program.

The correct approach uses `millis()`. This function returns the number of milliseconds elapsed since the Arduino powered on. It never blocks, never pauses, never stops anything — it simply reports a timestamp. You use it as a non-blocking stopwatch: note the time when the pin state changes, then only accept that change as real once 50 ms of stable readings have followed it.

```cpp
// Debounce logic — conceptual template:

unsigned long lastChangeTime = 0;    // Timestamp of the last pin state change.
const int DEBOUNCE_MS = 50;          // Minimum stable time before accepting a change.
int lastRawReading = HIGH;           // Previous raw pin reading.
int confirmedState = HIGH;           // The last accepted, debounced state.

// In loop():
int currentReading = digitalRead(BUTTON_PIN);

if (currentReading != lastRawReading) {
  lastChangeTime = millis();         // Pin changed — start timing. Don't accept yet.
}

if ((millis() - lastChangeTime) > DEBOUNCE_MS) {
  // 50ms have passed with no further change — this is a stable, real state.
  confirmedState = currentReading;
}

lastRawReading = currentReading;     // Store for comparison on next loop iteration.
```

The key principle: a new button state is only accepted after the pin has been completely stable for 50 consecutive milliseconds. Any bounce within that window resets the timer, preventing the bounced state from being accepted until things fully settle.

### Wiring — Push Button

For Tasks 5.1 through 5.3, the button and its connections change. Read the wiring instructions at the start of each task.

For Task 5.4 (using `INPUT_PULLUP`): one button leg to Pin 4, other button leg to GND. No external resistor needed.

Keep the LED on Pin 9, potentiometer on A0, and LDR on A1 from previous steps.

---

### Task 5.1 — Observe a Floating Input Pin

**Objective:** Demonstrate the floating pin problem directly. No resistor. Watch what happens.

**Wiring for this task:** One button leg to Pin 4. Other button leg to 5V. No resistor of any kind.

```cpp
// Task 5.1 — Floating pin demonstration.
// Pin 4 is in INPUT mode with nothing defining its voltage
// when the button is open. Watch the Serial Monitor without
// pressing the button at all. The readings should be random.

const int BUTTON_PIN = 4;

void setup() {
  pinMode(BUTTON_PIN, INPUT);    // INPUT only — no pull-up, no pull-down.
                                 // When the button is open, pin is floating.
  Serial.begin(9600);
  Serial.println("Task 5.1 — Floating Pin (do NOT press the button yet)");
  Serial.println("--------------------------------------------------------");
}

void loop() {
  int reading = digitalRead(BUTTON_PIN);

  Serial.print("Pin 4: ");
  Serial.println(reading == HIGH ? "HIGH" : "LOW");

  delay(200);
}
```

**Expected Serial Monitor Output (button not pressed):**

```
Task 5.1 — Floating Pin (do NOT press the button yet)
--------------------------------------------------------
Pin 4: HIGH
Pin 4: HIGH
Pin 4: LOW
Pin 4: HIGH
Pin 4: LOW
Pin 4: LOW
Pin 4: HIGH
```

Your specific output will differ — the pattern is random and varies with the electromagnetic environment of your bench. This is the floating pin problem. Now disconnect pin 4 from the button and proceed to Task 5.2.

---

### Task 5.2 — Read Button with External Pull-down

**Objective:** Add the 10 kΩ pull-down resistor and confirm that the readings become stable and meaningful.

**Wiring for this task:** Button leg 1 to Pin 4. Button leg 2 to 5V. 10 kΩ resistor between Pin 4 and GND.

```cpp
// Task 5.2 — Button with external pull-down resistor.
// The 10kΩ resistor holds Pin 4 firmly at GND (LOW) when the button is open.
// Pressing the button connects Pin 4 to 5V, overriding the pull-down.
// Readings are now stable and predictable.

const int BUTTON_PIN = 4;

void setup() {
  pinMode(BUTTON_PIN, INPUT);    // External pull-down is in the circuit — not INPUT_PULLUP.
  Serial.begin(9600);
  Serial.println("Task 5.2 — Button with Pull-down Resistor");
  Serial.println("  Reading  |  Meaning");
  Serial.println("-----------------------------");
}

void loop() {
  int reading = digitalRead(BUTTON_PIN);

  Serial.print("    ");
  if (reading == HIGH) {
    Serial.println("HIGH    |  Button PRESSED");
  } else {
    Serial.println("LOW     |  Button not pressed");
  }

  delay(150);
}
```

**Task 5.2 — Observation Table:**

| Button State | `digitalRead()` value | Meaning |
|---|---|---|
| Released | | |
| Pressed | | |

---

### Task 5.3 — Read Button Using `INPUT_PULLUP`

**Objective:** Use Arduino's internal pull-up to eliminate the external resistor. Observe the inverted logic.

**Wiring for this task:** Remove the external 10 kΩ resistor. Button leg 1 to Pin 4. Button leg 2 to **GND** (not 5V — pull-up logic requires button to pull pin toward GND).

```cpp
// Task 5.3 — Button using Arduino's internal pull-up resistor.
// INPUT_PULLUP activates a ~20–50kΩ resistor inside the chip,
// connecting Pin 4 to 5V internally. No external resistor needed.
// Pressing the button connects Pin 4 to GND → pin reads LOW.
// IMPORTANT: Logic is INVERTED. Pressed = LOW. Not pressed = HIGH.

const int BUTTON_PIN = 4;

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);  // Internal pull-up enabled.
                                     // Pin rests at HIGH automatically.
                                     // No external resistor needed.
  Serial.begin(9600);
  Serial.println("Task 5.3 — INPUT_PULLUP Mode (logic is inverted)");
  Serial.println("  Reading  |  Meaning");
  Serial.println("------------------------------------");
}

void loop() {
  int reading = digitalRead(BUTTON_PIN);

  Serial.print("    ");
  if (reading == LOW) {
    Serial.println("LOW     |  Button PRESSED");     // LOW = pressed with INPUT_PULLUP.
  } else {
    Serial.println("HIGH    |  Button not pressed"); // HIGH = released.
  }

  delay(150);
}
```

**Task 5.3 — Observation Table:**

| Button State | `digitalRead()` value | Meaning |
|---|---|---|
| Released | | |
| Pressed | | |

---

### Task 5.4 — Toggle LED with Debounced Button

**Objective:** Each press of the button toggles the LED exactly once. No double-toggles from bounce. No missed presses. This uses `INPUT_PULLUP` (no external resistor) and `millis()`-based debouncing.

**Wiring:** Button leg 1 to Pin 4, button leg 2 to GND. LED remains on Pin 9.

```cpp
// Task 5.4 — Debounced button toggle.
// Every confirmed press flips the LED state exactly once.
// Without debouncing, one press could toggle the LED 3–15 times.
// millis() is used instead of delay() so that nothing else is blocked
// while waiting for the bounce to settle.

const int BUTTON_PIN = 4;
const int LED_PIN    = 9;

int  lastRawReading       = HIGH;     // Most recent raw pin reading.
int  confirmedButtonState = HIGH;     // Last accepted debounced state.
unsigned long lastChangeTime = 0;     // Timestamp of last raw pin change.
const int DEBOUNCE_MS = 50;           // Pin must be stable for 50ms to be accepted.

bool ledOn = false;                   // Tracks current LED state. false = off.

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);  // Internal pull-up. Resting HIGH.
  pinMode(LED_PIN, OUTPUT);
  digitalWrite(LED_PIN, LOW);         // Start with LED off.
  Serial.begin(9600);
  Serial.println("Task 5.4 — Debounced Button Toggle");
  Serial.println("Each press toggles the LED once.");
  Serial.println("------------------------------------");
}

void loop() {
  int currentReading = digitalRead(BUTTON_PIN);  // Read the raw pin state.

  // Step 1: If the raw reading has changed since last loop, start the debounce timer.
  // We don't trust this change yet — it might be bounce.
  if (currentReading != lastRawReading) {
    lastChangeTime = millis();    // Record when the change happened.
  }

  // Step 2: Check if the pin has been stable for DEBOUNCE_MS.
  // millis() - lastChangeTime gives the time elapsed since the last change.
  if ((millis() - lastChangeTime) > DEBOUNCE_MS) {

    // Step 3: If the stable reading differs from what we last acted on,
    // this is a genuinely new state — process it.
    if (currentReading != confirmedButtonState) {
      confirmedButtonState = currentReading;    // Accept this as the new real state.

      // Step 4: With INPUT_PULLUP, LOW means pressed. Act only on press, not release.
      // Acting on both press and release would toggle twice per button cycle.
      if (confirmedButtonState == LOW) {
        ledOn = !ledOn;                         // Flip the LED state.
        digitalWrite(LED_PIN, ledOn ? HIGH : LOW);

        Serial.print("Button pressed → LED: ");
        Serial.println(ledOn ? "ON" : "OFF");
      }
    }
  }

  lastRawReading = currentReading;   // Save for comparison on next iteration.
                                     // This must be at the end of loop(), not inside
                                     // the if blocks, so it always updates.
}
```

**Expected Serial Monitor Output:**

```
Task 5.4 — Debounced Button Toggle
Each press toggles the LED once.
------------------------------------
Button pressed → LED: ON
Button pressed → LED: OFF
Button pressed → LED: ON
Button pressed → LED: OFF
```

Press the button slowly, then rapidly. Each physical press should produce exactly one line and one LED state change.

**Exercise 5.4:**
- Change `DEBOUNCE_MS` to `5`. Upload and press the button rapidly 10 times. Count the toggles. Does one press still reliably produce exactly one toggle? What does this tell you about choosing the debounce delay?
- In Task 5.4, why does the toggle code check `if (confirmedButtonState == LOW)` rather than `if (confirmedButtonState == HIGH)`? Write out in one sentence what LOW means in this pin configuration.
- If you replaced the `millis()` debounce logic with `delay(50)` immediately after detecting a press, what would happen to the LDR automatic LED control running simultaneously? (Hint: `delay()` blocks everything in `loop()`.)
- Modify Task 5.4 so that instead of toggling, the LED turns ON while the button is held and turns OFF when it is released. How does the code structure change?
- What would happen if you removed the line `lastRawReading = currentReading;` at the end of `loop()`? Trace through the logic carefully and describe the result.
---

## Section 11 — Step 6: Measuring the Environment — The LM35 Temperature Sensor

The last thing to add to the circuit is temperature measurement. Every other sensor in this lab measures something you can directly see or feel — light, position, a button press. Temperature is different: you cannot see it change in real time, yet it affects almost every physical process. Industrial machines overheat. Medical equipment must maintain safe temperatures. Electronics fail at high temperatures. Knowing how to read temperature reliably is a fundamental skill. The LM35 makes this straightforward because it converts temperature directly into a voltage with a clean, linear relationship — and you now have all the tools to go from that voltage to a meaningful number on the Serial Monitor.

### Background — The LM35 Temperature Sensor

The **LM35** is a precision integrated-circuit temperature sensor manufactured by Texas Instruments. It outputs a voltage that is directly and linearly proportional to temperature measured in Celsius. No calibration is needed, no complex conversion table, no lookup — the relationship is a single, fixed constant.

**Output characteristic:** 10 millivolts per degree Celsius.

```
V_out (mV) = Temperature (°C) × 10
```

| Temperature | Output Voltage |
|---|---|
| 0°C | 0 mV (0.000 V) |
| 10°C | 100 mV (0.100 V) |
| 25°C | 250 mV (0.250 V) |
| 50°C | 500 mV (0.500 V) |
| 100°C | 1000 mV (1.000 V) |

**Key specifications:**

| Parameter | Value |
|---|---|
| Supply voltage | 4V to 30V |
| Output | 10 mV/°C, linear |
| Range (basic wiring) | 2°C to 150°C |
| Accuracy | ±0.5°C at 25°C |
| Package | TO-92 (three-pin plastic) |
| Output impedance | Low — can drive an ADC pin directly |

**Where the LM35 is used:** HVAC systems that monitor room temperature, weather stations, industrial process monitoring, computer thermal management, medical equipment, food storage alarms, and any embedded system that needs to know the ambient or surface temperature of an object.

**Pin identification — critical:**

Hold the LM35 with its flat face (the printed side with the part number) facing directly toward you, pins pointing downward.

```
        ┌─────────┐
        │  LM35   │  ← flat face toward you
        └────┬────┘
         |   |   |
         |   |   |
        VCC VOUT GND
        (1)  (2)  (3)
         5V   A2  GND
```

Left pin = VCC → connect to Arduino 5V.
Middle pin = VOUT → connect to Arduino A2.
Right pin = GND → connect to Arduino GND.

> ⚠️ **Warning:** Reversing VCC and GND on the LM35 causes immediate, irreversible damage. The sensor heats rapidly within 3–5 seconds. If you feel warmth from the sensor body within seconds of powering the circuit, disconnect USB immediately. Confirm pin orientation before every power-on. Replace a damaged sensor — its readings cannot be trusted after reverse-voltage exposure.

### Background — Converting ADC to Temperature: Full Derivation

The LM35's VOUT pin is connected to Arduino analog pin A2. `analogRead(A2)` returns a raw integer from 0 to 1023. Here is the complete step-by-step conversion to temperature:

**Step 1 — Convert ADC value to voltage:**

```
V_out (V) = ADC_value × (5.0 / 1023.0)
```

**Step 2 — Convert voltage to temperature:**

The LM35 outputs 10 mV per °C, which means 0.010 V per °C. Rearranging:

```
Temperature (°C) = V_out (V) / 0.010
                 = V_out (V) × 100
```

**Combined into one formula:**

```
Temperature (°C) = ADC_value × (5.0 / 1023.0) × 100
                 = ADC_value × (500.0 / 1023.0)
                 = ADC_value × 0.4888
```

**Voltage resolution revisited for temperature:**

Each ADC step represents 4.89 mV. At 10 mV/°C:

```
Temperature resolution = 4.89 mV / 10 mV per °C = 0.489 °C per ADC step
```

The Arduino can detect temperature changes as small as approximately 0.49°C with the LM35. Finer resolution would require a higher-resolution ADC or a sensor with a higher output sensitivity.

**Three worked examples:**

*Example 1:* `analogRead(A2)` returns 51. What is the temperature?
```
V_out       = 51 × (5.0 / 1023.0) = 51 × 0.004887 = 0.2492 V = 249.2 mV
Temperature = 0.2492 × 100 = 24.92 °C
```

*Example 2:* `analogRead(A2)` returns 62. What is the temperature?
```
V_out       = 62 × 0.004887 = 0.3030 V
Temperature = 0.3030 × 100 = 30.30 °C
```

*Example 3:* The room is at 28°C. What ADC value do you expect?
```
V_out     = 28 × 0.010 = 0.280 V
ADC value = 0.280 × (1023.0 / 5.0) = 0.280 × 204.6 = 57.3 → expect ADC ≈ 57
```

### Wiring — LM35 Added to Existing Circuit

Keep everything from previous steps. Add the LM35 on A2.

| LM35 Pin | Connect To |
|---|---|
| Pin 1 — VCC (left, flat face toward you) | Arduino 5V |
| Pin 2 — VOUT (middle) | Arduino A2 |
| Pin 3 — GND (right) | Arduino GND |

**Full circuit wiring at this point (all steps combined):**

| Arduino Pin | Connected To |
|---|---|
| 5V | Potentiometer right outer pin, LDR leg 1, LM35 VCC |
| GND | Potentiometer left outer pin, LDR 10 kΩ resistor bottom, LED cathode, Button (Task 5.4), LM35 GND |
| A0 | Potentiometer wiper |
| A1 | LDR—Resistor junction |
| A2 | LM35 VOUT |
| Pin 4 | Button leg 1 |
| Pin 9 | 220 Ω resistor → LED anode |

---

### Task 6.1 — Read and Display Temperature

**Objective:** Read the LM35, perform the ADC-to-temperature conversion, and display all intermediate values on the Serial Monitor so you can verify each step of the calculation.

```cpp
// Task 6.1 — Reading temperature from the LM35.
// Three values are displayed for each reading:
// 1. The raw ADC value — for verifying the sensor is responding.
// 2. The voltage — for verifying the ADC conversion.
// 3. The temperature — the final useful value.
// Showing all three lets you catch errors at any step in the chain.

const int TEMP_PIN = A2;   // LM35 VOUT connected to A2.
                           // A0 = potentiometer, A1 = LDR — kept from previous steps.

void setup() {
  Serial.begin(9600);
  Serial.println("Task 6.1 — LM35 Temperature Reading");
  Serial.println("  ADC  |  Voltage (V)  |  Temperature (°C)");
  Serial.println("----------------------------------------------");
}

void loop() {
  int rawADC = analogRead(TEMP_PIN);                    // Raw ADC: 0 to 1023.

  float voltage = rawADC * (5.0 / 1023.0);              // Step 1: ADC to voltage.
                                                        // 5.0 forces float division.

  float temperatureC = voltage * 100.0;                 // Step 2: voltage to °C.
                                                        // LM35 outputs 10mV/°C = 0.010V/°C.
                                                        // Dividing by 0.010 = multiplying by 100.

  Serial.print("  ");
  Serial.print(rawADC);
  Serial.print("  |    ");
  Serial.print(voltage, 4);                             // 4 decimal places to show precision.
  Serial.print(" V    |    ");
  Serial.print(temperatureC, 2);                        // 2 decimal places for temperature.
  Serial.println(" °C");

  delay(1000);    // Read once per second — temperature changes slowly,
                  // so faster reads just duplicate the same value.
}
```

**Expected Serial Monitor Output:**

```
Task 6.1 — LM35 Temperature Reading
  ADC  |  Voltage (V)  |  Temperature (°C)
----------------------------------------------
  51   |    0.2493 V   |    24.93 °C
  51   |    0.2493 V   |    24.93 °C
  52   |    0.2542 V   |    25.42 °C      ← slight fluctuation
  51   |    0.2493 V   |    24.93 °C
  54   |    0.2640 V   |    26.40 °C      ← finger held on sensor
  58   |    0.2836 V   |    28.36 °C      ← finger held longer
  62   |    0.3031 V   |    30.31 °C
```

---

### Task 6.2 — Formatted Multi-Sensor Display

**Objective:** Read all three sensors simultaneously — potentiometer, LDR, and LM35 — and display them in a single formatted output line. This is what a real data acquisition system does.

```cpp
// Task 6.2 — All sensors reading simultaneously on one output line.
// This demonstrates that analogRead() can be called on different pins
// in sequence within the same loop iteration — they are independent.
// The readings happen one after another (the ADC multiplexes between pins),
// but fast enough that all values appear effectively simultaneous.

const int POT_PIN  = A0;
const int LDR_PIN  = A1;
const int TEMP_PIN = A2;
const int LED_PIN  = 9;

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  Serial.println("Task 6.2 — All Sensors Combined");
  Serial.println("  POT   |  LDR   |  Temp (°C)  |  LED State");
  Serial.println("-----------------------------------------------");
}

void loop() {
  // Read all sensors.
  int potRaw  = analogRead(POT_PIN);
  int ldrRaw  = analogRead(LDR_PIN);
  int tempRaw = analogRead(TEMP_PIN);

  // Convert temperature.
  float tempC = tempRaw * (5.0 / 1023.0) * 100.0;   // Combined formula in one line.

  // Control LED brightness from potentiometer (Task 3.2 behavior).
  int pwmValue = map(potRaw, 0, 1023, 0, 255);
  analogWrite(LED_PIN, pwmValue);

  // Map pot to percent for display.
  int potPercent = map(potRaw, 0, 1023, 0, 100);

  Serial.print("  ");
  Serial.print(potPercent);
  Serial.print("%   |  ");
  Serial.print(ldrRaw);
  Serial.print("   |    ");
  Serial.print(tempC, 1);
  Serial.print(" °C   |    ");
  Serial.print(pwmValue);
  Serial.println(" PWM");

  delay(500);
}
```

**Expected Serial Monitor Output:**

```
Task 6.2 — All Sensors Combined
  POT   |  LDR   |  Temp (°C)  |  LED State
-----------------------------------------------
  50%   |  742   |    25.1 °C  |    128 PWM
  50%   |  738   |    25.1 °C  |    128 PWM
  75%   |  740   |    25.4 °C  |    192 PWM    ← pot turned up
  75%   |  312   |    25.4 °C  |    192 PWM    ← LDR covered
  75%   |  820   |    28.8 °C  |    192 PWM    ← finger on LM35
```

**Task 6.2 — Observation Table:**

| Condition | POT ADC | LDR ADC | Temp ADC | Temperature (°C) | LED PWM |
|---|---|---|---|---|---|
| All at rest (no interaction) | | | | | |
| Pot at minimum | | | | | |
| Pot at maximum | | | | | |
| LDR covered | | | | | |
| Finger held on LM35 for 30 sec | | | | | |

---

### Task 6.3 — Temperature Threshold Alert

**Objective:** Add a temperature warning that prints an alert when temperature exceeds a user-defined limit. This is the same logic used in industrial thermal alarms and computer thermal throttling systems.

```cpp
// Task 6.3 — Temperature threshold alert system.
// Prints a warning whenever temperature exceeds TEMP_HIGH_THRESHOLD.
// Named constants make the threshold easy to find and change
// without hunting through the code for a magic number.

const int TEMP_PIN          = A2;
const int LED_PIN           = 9;
const float TEMP_HIGH       = 35.0;   // Alert fires above this temperature.
                                      // Change this to test: set it to 26.0
                                      // and hold your finger on the sensor.
const float TEMP_LOW        = 15.0;   // Alert fires below this temperature.

void setup() {
  pinMode(LED_PIN, OUTPUT);
  Serial.begin(9600);
  Serial.println("Task 6.3 — Temperature Alert System");
  Serial.print("High threshold: ");
  Serial.print(TEMP_HIGH);
  Serial.print(" °C  |  Low threshold: ");
  Serial.print(TEMP_LOW);
  Serial.println(" °C");
  Serial.println("-----------------------------------------");
}

void loop() {
  int rawADC     = analogRead(TEMP_PIN);
  float tempC    = rawADC * (5.0 / 1023.0) * 100.0;

  Serial.print("Temp: ");
  Serial.print(tempC, 1);
  Serial.print(" °C  ");

  if (tempC > TEMP_HIGH) {
    // Above high threshold — flash LED rapidly as a visual alarm.
    // The LED is being used as an indicator here, overriding its other uses.
    digitalWrite(LED_PIN, HIGH);
    Serial.print("  [!] HIGH TEMP WARNING");
  } else if (tempC < TEMP_LOW) {
    digitalWrite(LED_PIN, LOW);
    Serial.print("  [!] LOW TEMP WARNING");
  } else {
    // Temperature is within normal range.
    // LED remains in whatever state the potentiometer has set it.
    Serial.print("  [OK] Normal");
  }

  Serial.println();   // End the current line and move cursor to next line.
  delay(1000);
}
```

**Expected Serial Monitor Output:**

```
Task 6.3 — Temperature Alert System
High threshold: 35.0 °C  |  Low threshold: 15.0 °C
-----------------------------------------
Temp: 25.1 °C    [OK] Normal
Temp: 25.4 °C    [OK] Normal
Temp: 27.9 °C    [OK] Normal           ← finger placed on sensor
Temp: 31.2 °C    [OK] Normal
Temp: 35.8 °C    [!] HIGH TEMP WARNING ← threshold crossed
Temp: 36.4 °C    [!] HIGH TEMP WARNING
Temp: 25.6 °C    [OK] Normal           ← finger removed, cooling down
```

**Exercise 6.3:**
- Set `TEMP_HIGH` to 1.0°C less than your current room temperature. Upload and confirm the warning appears immediately. Then restore it to a realistic value.
- Modify Task 6.3 to also use the LED as an indicator: slow blink when temperature is in the normal range, fast blink when either threshold is exceeded. You will need to use `millis()` for the blink timing instead of `delay()` — why?
- The minimum detectable temperature change with this setup is approximately 0.49°C. If you wanted to detect changes as small as 0.1°C, what would need to change in the hardware? (Hint: the ADC resolution is fixed at 10 bits for this chip.)
---

## Section 12 — Consolidated Observation Tables

Use these tables during the lab session to record your measurements. Reference the formula provided beneath each table to complete the calculated columns.

---

**Table 1 — Floating Pin Readings (Task 1.1)**

| Reading # | A0 Value (no connection) | Pattern Observed |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |

---

**Table 2 — Potentiometer: Position vs ADC vs Voltage (Task 2.1)**

| Knob Position | ADC Value | Displayed Voltage (V) | Calculated Voltage (V) | Match? |
|---|---|---|---|---|
| Fully counterclockwise | | | | |
| ~25% rotation | | | | |
| ~50% (center) | | | | |
| ~75% rotation | | | | |
| Fully clockwise | | | | |

Formula: `Voltage (V) = ADC × (5.0 / 1023.0)`

---

**Table 3 — Potentiometer: ADC vs Mapped Values (Task 2.2)**

| ADC Value | Mapped Percent (%) | Mapped Angle (°) | Manually calculated percent |
|---|---|---|---|
| 0 | | | |
| ~256 | | | |
| ~511 | | | |
| ~768 | | | |
| 1023 | | | |

Formula: `Percent = (ADC / 1023) × 100`

---

**Table 4 — LED Brightness: Potentiometer vs PWM vs Observation (Task 3.2)**

| Knob Position | POT ADC | PWM Value | Perceived Brightness |
|---|---|---|---|
| Fully counterclockwise | | | |
| ~25% | | | |
| ~50% | | | |
| ~75% | | | |
| Fully clockwise | | | |

Formula: `PWM = map(ADC, 0, 1023, 0, 255)`

---

**Table 5 — LDR: Light Condition vs ADC vs Voltage (Task 4.1)**

| Light Condition | LDR ADC | Voltage (V) | Estimated R_LDR (kΩ) |
|---|---|---|---|
| Normal room lighting | | | |
| LDR fully covered | | | |
| Flashlight on LDR | | | |
| Near window (daytime) | | | |
| Overhead tube light | | | |

Formula for R_LDR: `R_LDR = 10,000 × ((5.0 / V_out) − 1)` then divide by 1000 for kΩ.

---

**Table 6 — LDR Calibration Values (Task 4.2)**

| Measurement | ADC Value |
|---|---|
| Minimum (darkest) | |
| Maximum (brightest) | |
| Chosen threshold | |

---

**Table 7 — LDR Auto LED Verification (Task 4.3)**

| Condition | Expected LED State | Actual LED State | ADC Reading | Correct? |
|---|---|---|---|---|
| Normal room lighting | OFF | | | |
| LDR fully covered | ON | | | |
| Flashlight on LDR | OFF | | | |
| Dimmed overhead light | ON | | | |

---

**Table 8 — Button: Floating Pin Readings (Task 5.1)**

| Reading # | `digitalRead(4)` value | Button state during reading |
|---|---|---|
| 1 | | Not pressed |
| 2 | | Not pressed |
| 3 | | Not pressed |
| 4 | | Not pressed |
| 5 | | Not pressed |

---

**Table 9 — Button with Pull-down (Task 5.2)**

| Button State | `digitalRead(4)` value | Consistent? |
|---|---|---|
| Released (5 readings) | | |
| Pressed (5 readings) | | |

---

**Table 10 — Button with INPUT_PULLUP (Task 5.3)**

| Button State | `digitalRead(4)` value | Consistent? | Compare to Table 9 |
|---|---|---|---|
| Released (5 readings) | | | |
| Pressed (5 readings) | | | |

---

**Table 11 — LM35 Temperature: Condition vs Readings (Task 6.2)**

| Condition | ADC Value | Voltage (V) | Temperature (°C) |
|---|---|---|---|
| Ambient (undisturbed) | | | |
| Finger held 20 seconds | | | |
| Finger held 40 seconds | | | |
| Finger held 60 seconds | | | |
| 30 seconds after releasing finger | | | |

Formula: `Temperature (°C) = ADC × (500.0 / 1023.0)`

---

## Section 13 — Troubleshooting Guide

| Problem Observed | Likely Cause | How to Fix |
|---|---|---|
| Serial Monitor shows garbled characters | Baud rate mismatch between code and Serial Monitor | Set Serial Monitor dropdown to 9600. Confirm `Serial.begin(9600)` in code. |
| Serial Monitor shows nothing at all | `Serial.begin()` missing from `setup()`, or wrong COM port | Add `Serial.begin(9600)` to `setup()`. Check `Tools → Port`. |
| Upload fails: "Board at COMX not found" | Wrong port selected, or Arduino not connected | Check `Tools → Port`. Unplug and replug USB. Check Device Manager for driver issues. |
| Built-in LED (L) does not blink | `pinMode(13, OUTPUT)` missing, or sketch not uploaded | Add `pinMode(13, OUTPUT)` to `setup()`. Confirm "Done uploading." message appeared. |
| External LED does not light at all | Reversed polarity, missing resistor, or wrong pin | Confirm long leg (anode) toward the Arduino pin. Confirm 220 Ω in series. Confirm pin number matches code. |
| LED lights but does not dim with potentiometer | `analogWrite()` used on a non-PWM pin | Move LED wire to a pin with ~ symbol: 3, 5, 6, 9, 10, or 11. |
| Potentiometer gives 0 at both extremes | Wiper pin connected to wrong pin, or outer pins both on same rail | Confirm: left outer → GND, right outer → 5V, middle → A0. Outer pins must be on different rails. |
| LDR ADC value does not change with light | A1 is connected to the wrong point in the divider | A1 must connect to the junction between LDR and the 10 kΩ resistor — not to either end. |
| LDR always reads near 0 | 10 kΩ resistor missing — pin is floating toward GND | Confirm resistor is in circuit between junction point and GND. |
| LDR always reads near 1023 | LDR missing or not connected — pin floats toward 5V | Confirm LDR connects from 5V to the junction. |
| Auto LED does not respond to light changes (Task 4.3) | Threshold value wrong for your environment | Re-run Task 4.2 calibration. Replace THRESHOLD constant with your measured midpoint. |
| Button reads random values even with resistor (Task 5.2) | 10 kΩ resistor not actually connected to Pin 4 | Confirm one resistor leg is in the same breadboard row as button leg 1 and the Arduino Pin 4 wire. |
| Button with `INPUT_PULLUP` always reads LOW | Button wired to 5V instead of GND | With INPUT_PULLUP, button must connect pin to GND. Rewire button's second leg to GND. |
| LED toggles multiple times per button press (Task 5.4) | DEBOUNCE_MS too small, or debounce logic has a bug | Increase `DEBOUNCE_MS` to 50 or higher. Confirm `lastRawReading = currentReading` is the last line of `loop()`. |
| LM35 reads 0°C constantly | VOUT (middle pin) not connected to A2 | Check LM35 middle pin → A2 connection. Confirm pin orientation (flat face toward you). |
| LM35 reads very high temperature (>80°C) immediately | VCC and GND are reversed | Disconnect USB immediately. Check that left pin (flat face toward you) goes to 5V, right pin goes to GND. Replace sensor if it felt hot. |
| LM35 readings fluctuate by ±1–2°C with no temperature change | Normal ADC noise at low voltages | Average multiple readings: take 10 readings in a loop, sum them, divide by 10 before converting. |
| All sensor readings are zero | 5V power rail not connected on breadboard | Confirm a wire runs from Arduino 5V to the breadboard's positive rail. |

---

## Section 14 — Post-Lab Questions

### ADC and Pins (Q1–Q8)

**Q1.** A digital pin configured as `INPUT` reads a voltage of 2.8V. What does `digitalRead()` return — HIGH or LOW? Now consider: if the pin is connected to a sensor that outputs exactly 2.5V, what does `digitalRead()` return? What does this tell you about the suitability of digital pins for analog sensors?

**Q2.** The ATmega328P has a 10-bit ADC. How many discrete levels can it represent? If the reference voltage were increased to 10V (using an external reference), what would the new voltage resolution per step be? Would the ADC read a wider range, or a finer range, or both?

**Q3.** `analogRead(A0)` returns 0 in two different situations: when the pin voltage is exactly 0V, and when the pin is floating near GND. How would you determine which of the two situations you are in?

**Q4.** A sketch contains the line `int x = 5 / 1023;`. What value does `x` hold? Why? Rewrite the line so it correctly calculates the voltage resolution of the ADC.

**Q5.** The Arduino's ADC can only read voltages between 0V and 5V. A sensor you want to use outputs voltages between 0V and 3.3V. Will the ADC work with this sensor? What is the maximum ADC value you will ever read from it? Does this represent a problem, and if so, how would you address it?

**Q6.** You call `analogRead(A0)` five times in rapid succession in the same `loop()` iteration. The pin voltage has not changed. Will all five readings be identical? Explain why they might not be, and what causes small variations between readings of the same stable voltage.

**Q7.** Explain what happens to serial output if `Serial.begin()` is placed inside `loop()` instead of `setup()`. Would the Serial Monitor show anything? Would the readings be correct?

**Q8.** A sketch uses `Serial.print(value)` repeatedly without any `Serial.println()`. What does the Serial Monitor display? Rewrite the output section so each reading appears on its own line.

---

### Potentiometer and PWM (Q9–Q16)

**Q9.** A potentiometer is wired with both outer pins connected to the same voltage (5V on both ends). You rotate the shaft. What does `analogRead()` return across the full rotation? Explain using the voltage divider formula.

**Q10.** `map(value, 0, 1023, 0, 255)` is used to convert a potentiometer reading to a PWM value. If `value` is 600, what does `map()` return? Show the calculation using the map formula.

**Q11.** `analogWrite(pin, 180)` is called. What is the duty cycle of the PWM signal this produces? What is the effective average voltage on the pin? At what PWM value does the average voltage equal exactly 2.5V?

**Q12.** Pin 6 is a PWM-capable pin. You connect an LED (with 220 Ω resistor) to pin 6 and call `analogWrite(6, 128)`. The LED is visibly on at roughly half brightness. Now you move the LED to pin 7, which is not PWM-capable, and call `analogWrite(7, 128)`. Predict what happens and explain why. What would `digitalWrite(7, HIGH)` produce on that pin instead?

**Q13.** A potentiometer is used as a volume control in an audio application. The output of the wiper goes directly to an audio amplifier input. The amplifier's input impedance is 10 kΩ. Explain why this loading effect changes the effective voltage divider ratio and therefore the volume control behavior. (This is a reasoning question — you do not need exact numbers.)

**Q14.** `map()` uses integer arithmetic. Calculate `map(1, 0, 1023, 0, 100)` using the map formula. What does the result tell you about using `map()` when you need fractional precision?

**Q15.** You want the LED to be OFF when the potentiometer is at minimum and FULL when at maximum — but you want the brightness to increase in steps of exactly 10% (0%, 10%, 20%, ... 100%) rather than smoothly. Describe in words how you would modify the code to achieve this. No code required — just the logic.

**Q16.** The PWM frequency on most Arduino pins is approximately 490 Hz. A particular application requires PWM at 50 Hz (for servo motor control). The standard `analogWrite()` cannot be easily changed to 50 Hz without modifying timer registers. Why does frequency matter for a servo motor but not for an LED?

---

### LDR and Voltage Divider (Q17–Q24)

**Q17.** In the LDR voltage divider, the LDR is the upper arm (connected to 5V) and the 10 kΩ resistor is the lower arm (connected to GND). The output is read at the junction. If you swap them — 10 kΩ as upper arm and LDR as lower arm — what changes in the behavior of the ADC readings? Does a high reading still mean bright, or does it now mean dark?

**Q18.** The LDR voltage divider gives V_out = 5 × (10,000 / (R_LDR + 10,000)). At what LDR resistance does V_out equal exactly 2.5V? Show your working. What does this resistance value tell you about the optimal choice of fixed resistor?

**Q19.** You replace the 10 kΩ fixed resistor with a 1 kΩ resistor. Calculate V_out in bright light (R_LDR ≈ 500 Ω) and in darkness (R_LDR ≈ 500 kΩ). Compare these with the values calculated in the Background section using 10 kΩ. Which fixed resistor gives a more useful range across both conditions? Explain.

**Q20.** The automatic LED (Task 4.3) uses a single threshold value. You observe that when the light level is exactly at the threshold, the LED rapidly flickers on and off as small fluctuations push the reading above and below. Define the term **hysteresis** and describe, using two threshold values (a high threshold and a low threshold), how you would modify the `if/else` logic to eliminate this flickering.

**Q21.** You want to calculate the actual resistance of the LDR from the ADC reading. Using the voltage divider formula, derive an expression for R_LDR in terms of ADC_value, V_ref (5V), and R_fixed (10 kΩ). Show all algebraic steps.

**Q22.** An LDR circuit is used outdoors where sunlight can make R_LDR drop to 50 Ω. Calculate V_out with R_fixed = 10 kΩ and R_LDR = 50 Ω. Is this a problem for the Arduino's analog pin? Why?

**Q23.** The LDR's resistance changes with temperature as well as light. In a cold environment, resistance tends to be slightly higher even at the same light level. How might this affect the threshold you calibrated in Task 4.2 if the sensor is moved from an air-conditioned lab to an outdoor setting?

**Q24.** A classmate suggests using a digital pin with `digitalRead()` instead of `analogRead()` to detect day and night with the LDR circuit. Describe specifically what information would be lost compared to using `analogRead()`, and describe a scenario where the analog reading is essential.

---

### Pull-up, Pull-down and Floating Pins (Q25–Q32)

**Q25.** Why is a 10 kΩ resistor used for pull-down rather than, say, 100 Ω? Calculate the current that flows through the pull-down resistor when the button is pressed (5V across 10 kΩ). Now calculate the current if the resistor were 100 Ω. Why is the higher current problematic?

**Q26.** In Task 5.1, the floating pin read random values even without pressing the button. Explain the physical mechanism by which a floating conductor picks up stray voltages from its environment. Why does the Arduino's CMOS input circuit make this effect particularly noticeable?

**Q27.** In Task 5.2 (pull-down), pressing the button reads HIGH. In Task 5.3 (INPUT_PULLUP), pressing the button reads LOW. A teammate writes a single sketch that must work with both wiring configurations just by changing a constant. What constant would they define, and how would they use it to make the rest of the code independent of which configuration is used?

**Q28.** The internal pull-up resistance with `INPUT_PULLUP` is approximately 20 kΩ to 50 kΩ — significantly higher than the 10 kΩ external pull-down used in Task 5.2. What are the practical consequences of a weaker (higher resistance) pull-up in a noisy electrical environment compared to a stronger (lower resistance) one?

**Q29.** You connect a button between Pin 7 and GND and use `INPUT_PULLUP`. You also connect a 1 kΩ resistor between Pin 7 and 5V (accidentally creating a pull-up conflict). What voltage does Pin 7 see when the button is pressed? When it is released? Calculate both using the voltage divider formed by the 1 kΩ external resistor and the ~30 kΩ internal pull-up.

**Q30.** Three buttons are connected to pins 2, 3, and 4 using INPUT_PULLUP. Write the `setup()` and the `loop()` logic (in words, not code) to detect which of the three buttons is pressed and print its number. Only one button is pressed at a time.

**Q31.** `INPUT_PULLUP` keeps the pin HIGH when idle. If an analog sensor that outputs 0–3.3V is accidentally connected to a pin configured as `INPUT_PULLUP`, what happens? Is the pin reading still meaningful?

**Q32.** In a real product (not a lab), would you prefer to use `INPUT_PULLUP` or an external pull-down for a critical control button (such as an emergency stop)? Consider what happens if the wire between the button and the Arduino is accidentally disconnected. Which configuration fails safe?

---

### Debouncing (Q33–Q38)

**Q33.** Contact bounce in a typical tactile button lasts 1–20 ms. A sketch runs `loop()` approximately 1000 times per second. In a 10 ms bounce period, how many times might `digitalRead()` be called? How many false button press detections could this cause?

**Q34.** The debounce code in Task 5.4 uses `millis()` rather than `delay()`. List two specific behaviors in the final circuit (with LDR, temperature sensor, and LED all active) that would break or degrade if `delay(50)` were used instead of the `millis()` approach.

**Q35.** A classmate writes this debounce code: after detecting a press, call `delay(50)` and then read the button again to confirm it is still pressed. What is the problem with this approach compared to the millis()-based method in Task 5.4? Consider both correctness and real-time responsiveness.

**Q36.** `millis()` returns an `unsigned long` — an unsigned 32-bit integer. It overflows back to zero after approximately 49.7 days of continuous operation. In the expression `(millis() - lastChangeTime) > DEBOUNCE_MS`, will the overflow cause a bug? Explain why or why not, considering how unsigned integer subtraction behaves in C++.

**Q37.** The debounce delay in Task 5.4 is set to 50 ms. A different button in your lab has a very clean contact mechanism — bounce settles in under 2 ms. Could you reduce DEBOUNCE_MS to 5 ms and still get reliable operation? What is the risk if you reduce it too aggressively?

**Q38.** Hardware debouncing uses a capacitor and resistor at the input pin to smooth out the bounce electrically, rather than handling it in software. What are the advantages of software debouncing (as done in Task 5.4) compared to hardware debouncing for a university lab prototype?

---

### LM35 and Temperature (Q39–Q44)

**Q39.** The LM35 outputs 10 mV/°C. At 0°C the output is 0V. The Arduino's ADC cannot distinguish between 0V from a 0°C reading and 0V from a disconnected pin. How would you determine, in code, whether the temperature is genuinely near 0°C or whether the sensor is simply disconnected?

**Q40.** The minimum detectable temperature change with the LM35 on an Arduino UNO is approximately 0.49°C. A medical application requires resolution of 0.01°C. Identify two hardware-level changes (not software averaging) that could achieve this.

**Q41.** A sketch reads the LM35 every 100 ms (`delay(100)` in loop). Temperature changes slowly — room temperature might shift 1°C over several minutes. Is reading every 100 ms useful, wasteful, or harmful? At what interval would you sample temperature in a real application, and why?

**Q42.** The LM35 is rated accurate to ±0.5°C. The Arduino's ADC resolution is approximately 0.49°C per step. Which of these two limits temperature measurement accuracy more — the sensor or the ADC? What does this tell you about pairing sensor accuracy with converter resolution in a real system?

**Q43.** You want to log temperature every second for 24 hours and store the data for later analysis. The Arduino UNO has 2 KB of SRAM. If each temperature reading is stored as a `float` (4 bytes), how many readings fit in SRAM? How long does this represent at one reading per second? What hardware would you add to the Arduino to store 24 hours of data?

**Q44.** The LM35's output at 25°C is 250 mV. The ADC reads this as approximately ADC = 51. If you replaced the Arduino's 5V reference with a 1.1V internal reference (using `analogReference(INTERNAL)`), what ADC value would the same 250 mV produce? What would the new temperature resolution per ADC step be?

---

### Cross-topic and Design Questions (Q45–Q47)

**Q45.** Design question: You want to build a simple thermostat. It should turn a heater ON when temperature drops below 20°C and turn it OFF when temperature rises above 25°C. Write out the complete logic in pseudocode (not Arduino code), including the hysteresis band, a button to enable/disable the system, and a status LED that blinks when the heater is active. Identify which concepts from each step of this lab appear in your design.

**Q46.** The full circuit assembled at the end of this lab includes: a potentiometer on A0, an LDR on A1, an LM35 on A2, a button on Pin 4, and an LED on Pin 9. If a new requirement is added — a second LED that turns on when temperature exceeds 30°C — which pin would you use? Are there any constraints you must consider (PWM capability, digital vs analog, reserved pins)? Justify your choice.

**Q47.** Reflection question: In Step 1, Task 1.1, you observed random readings on an unconnected pin. This same phenomenon — an undefined, floating signal — can appear in many forms throughout a circuit. Identify two situations from later in this lab where an incorrectly wired or configured pin would also produce floating or undefined behavior, and explain what observable symptom would appear in each case.

---

## Section 15 — Quick Reference

### Arduino Functions Used in This Lab

| Function | Syntax | Returns | Description |
|---|---|---|---|
| `pinMode()` | `pinMode(pin, mode)` | Nothing | Sets a pin as INPUT, OUTPUT, or INPUT_PULLUP |
| `digitalWrite()` | `digitalWrite(pin, value)` | Nothing | Sets a digital output pin to HIGH (5V) or LOW (0V) |
| `digitalRead()` | `digitalRead(pin)` | HIGH or LOW (int) | Reads the state of a digital input pin |
| `analogRead()` | `analogRead(pin)` | int (0–1023) | Reads voltage at an analog pin and returns ADC value |
| `analogWrite()` | `analogWrite(pin, value)` | Nothing | Sets PWM duty cycle on a PWM-capable pin (value: 0–255) |
| `map()` | `map(val, fromL, fromH, toL, toH)` | long | Linearly maps value from one range to another |
| `delay()` | `delay(ms)` | Nothing | Pauses sketch execution for specified milliseconds |
| `millis()` | `millis()` | unsigned long | Returns milliseconds elapsed since board powered on |
| `Serial.begin()` | `Serial.begin(baud)` | Nothing | Initializes serial communication. Always in setup(). |
| `Serial.print()` | `Serial.print(value)` | Nothing | Prints value to Serial Monitor without newline |
| `Serial.println()` | `Serial.println(value)` | Nothing | Prints value to Serial Monitor with newline |

### Formula Sheet

| Formula | Expression |
|---|---|
| ADC value to voltage | `V = ADC × (5.0 / 1023.0)` |
| Voltage to ADC value | `ADC = V × (1023.0 / 5.0)` |
| ADC voltage resolution | `Resolution = 5.0 / 1023 ≈ 4.89 mV per step` |
| Voltage divider output | `V_out = V_in × (R_lower / (R_upper + R_lower))` |
| LDR resistance from ADC | `R_LDR = 10000 × ((5.0 / V_out) − 1)` |
| LED current limiting resistor | `R = (V_supply − V_forward) / I_desired` |
| PWM duty cycle | `Duty (%) = (PWM_value / 255) × 100` |
| LM35 temperature (from ADC) | `T (°C) = ADC × (500.0 / 1023.0)` |
| LM35 temperature (from voltage) | `T (°C) = V_out (V) × 100` |
| Temperature resolution | `≈ 0.49 °C per ADC step` |
| map() internal formula | `result = toLow + (val − fromLow) × (toHigh − toLow) / (fromHigh − fromLow)` |

### Complete Circuit Pin Assignment (End of Lab)

| Arduino Pin | Connected To | Step Introduced |
|---|---|---|
| 5V | Potentiometer (right outer), LDR (one leg), LM35 VCC | Step 2, 4, 6 |
| GND | Potentiometer (left outer), LDR 10 kΩ bottom, LED cathode, Button (to GND), LM35 GND | All steps |
| A0 | Potentiometer wiper (middle pin) | Step 2 |
| A1 | LDR — 10 kΩ junction | Step 4 |
| A2 | LM35 VOUT (middle pin) | Step 6 |
| Pin 4 | Push button leg 1 (button leg 2 to GND) | Step 5 |
| Pin 9 (~PWM) | 220 Ω resistor → LED anode | Step 3 |

### Component Summary

| Component | Value/Type | Purpose in This Lab |
|---|---|---|
| Arduino UNO | ATmega328P, 5V | Main controller |
| Potentiometer | 10 kΩ | Manual analog input — controls LED brightness |
| LED | Standard 5mm | Visual output — brightness and on/off controlled |
| Resistor | 220 Ω | Current limiter for LED |
| LDR | GL5528 or equivalent | Light sensor — automatic LED control |
| Resistor | 10 kΩ | Lower arm of LDR voltage divider |
| Push Button | Tactile, 4-pin | Human input — debounced LED toggle |
| LM35 | TO-92, 10 mV/°C | Temperature sensor |

---
