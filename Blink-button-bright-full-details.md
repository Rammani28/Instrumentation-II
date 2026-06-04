**Subject:** Instrumentation II
**Lab:** LAB 01
**Title:** Blink, Button, Bright: Learning Digital and Analog Control with Arduino UNO
**Level:** Bachelor of Engineering
**Prerequisites:** Basic electronics (Ohm's Law, voltage divider), Basic C/C++
**Hardware:** Arduino UNO, 3× LEDs (red, yellow, green), 3× 220Ω resistors, 1× potentiometer (10kΩ), 1× push button, 2× 10kΩ resistors, Breadboard, Jumper Wires, USB cable
**Software:** Arduino IDE 1.8.x or 2.x
---

---

## Precautions

Never connect an LED directly between an Arduino output pin and GND without a current-limiting resistor. An Arduino digital output pin sources a maximum of 40 mA, and the forward voltage drop across a typical LED is only 1.8 V to 2.2 V. With a 5 V supply and no resistor, Ohm's Law tells you that the current through the LED would be catastrophically high — far beyond both the LED's and the pin's rated limits. The LED will burn out instantly, and repeated violations of the 40 mA per-pin limit (or the 200 mA total I/O current limit) will permanently damage the ATmega328P microcontroller on your UNO. Before powering on any circuit, verify that every LED has a series resistor of at least 150Ω, and ideally 220Ω for safe, reliable operation.

Never connect 5 V directly to an analog input pin (A0–A5) or to a digital pin configured as INPUT without understanding the signal source. The ATmega328P's I/O pins are rated for a maximum of 5 V. Exceeding this — even briefly, from an inductive spike or a wiring mistake that connects a pin to a voltage rail higher than 5 V — can latch the pin high, corrupt nearby logic, or destroy the pin permanently. When using the potentiometer, always connect its outer terminals to 5 V and GND as specified. Reversing them will not damage the board, but will invert your readings. Connecting either outer terminal to a voltage above 5 V will damage the ADC input.

Always connect the Arduino to your PC through a USB cable rated for data transfer, not a charge-only cable. Charge-only cables carry power but omit the data lines (D+ and D−). The Arduino IDE communicates with the UNO over USB serial, and with a charge-only cable the board will receive power and appear to run, but the IDE will be unable to upload sketches or open the Serial Monitor. If your IDE reports "Port not found" or the board does not appear in the Tools → Port menu even after installing drivers, suspect the cable first.

Handle the breadboard connections carefully and double-check every wire before powering on. A wire inserted into the wrong row or column is the most common source of hours of wasted debugging. On a standard full-size breadboard, the two horizontal rails at the top and bottom are power buses (connected along their entire length), while the vertical columns in the main area are connected in groups of five. A wire that misses its target hole by one row is electrically invisible to you but will make your circuit behave in ways that seem impossible. Get into the habit of verifying each connection in your wiring table before pressing the upload button.

Keep the total current draw from the Arduino's 5 V pin within safe limits. The 5 V pin on the UNO is powered directly from the USB host's 5 V rail through a polyfuse. The USB specification allows up to 500 mA, but the polyfuse and the onboard regulator limit what the UNO can safely supply to external components. In this lab you have three LEDs (each drawing roughly 15–20 mA through a 220Ω resistor) and a potentiometer (drawing negligible current). This is well within limits. Be aware of this constraint for future labs where more power-hungry components are added.

---

## Objectives

By the end of this lab, you will have built a working multi-output control circuit entirely from scratch — starting with nothing more than a bare Arduino UNO and a handful of components — and you will understand exactly why every wire and every line of code is where it is. Along the way you will master the two fundamental modes of Arduino I/O: digital, where pins are either fully on or fully off, and analog, where you read a continuously varying voltage and write a simulated one using pulse-width modulation.

More specifically, you will understand how a microcontroller differs from a general-purpose processor, how to read both digital and analog inputs reliably (including why unconnected input pins behave erratically and how to fix that), how to sequence multiple outputs over time without freezing the processor, how mechanical switches produce false triggers and how to eliminate them in software, and how to translate a physical knob position into LED brightness or LED selection using the Arduino's built-in ADC and PWM hardware. When you power off at the end, you will have a circuit doing all of these things simultaneously.

---

## Section 1 — Foundations: The Arduino UNO and How It Works

Before you touch a single wire, you need a clear picture of what you are working with. This section gives you that picture. Everything you do in the steps that follow will make more sense once you understand what is actually happening inside the board.

### 1.1 What Is a Microcontroller?

A **microcontroller** (MCU) is a complete computer system integrated onto a single chip. The word "complete" is doing real work in that sentence — an MCU contains not just a processor core, but also program memory (Flash), working memory (SRAM), permanent storage for saved data (EEPROM), and a collection of peripheral hardware blocks (timers, ADC, UART, SPI, I2C, PWM generators) all on the same piece of silicon. You write a program, burn it into the Flash, and the chip runs it forever without needing an operating system, a file system, or any external chips to help it.

This is fundamentally different from a **microprocessor** (MPU), which is what sits inside a laptop or a phone. An MPU is only the processor core — it has no memory and no peripherals of its own. To be useful, an MPU needs external RAM chips, a storage device, a power management IC, and many other supporting components. That is why a laptop motherboard is large and complex. An MPU trades simplicity for raw computing power: it can run a full operating system and process enormous amounts of data at high speed. An MCU trades raw power for simplicity, low cost, low power consumption, and the ability to directly interface with the physical world through its built-in peripherals.

The Arduino UNO uses an **ATmega328P**, an 8-bit AVR MCU made by Microchip (formerly Atmel). It runs at 16 MHz, has 32 KB of Flash for your program, 2 KB of SRAM for variables at runtime, and 1 KB of EEPROM for data that must survive a power cycle. These numbers sound small compared to a modern computer, but they are more than sufficient for sensing, controlling, and communicating with the physical world — which is exactly what embedded systems do.

### 1.2 The Arduino UNO Board

The UNO is not just an ATmega328P chip. It is a development board designed to make that chip easy to use. The board adds a USB-to-serial bridge chip (the ATmega16U2), a 5 V voltage regulator, a 3.3 V regulator, a 16 MHz crystal oscillator, a reset button, and a set of pin headers that expose the MCU's I/O pins in a convenient, breadboard-friendly layout.

The pin headers are divided into groups:

**Power pins** sit along one edge of the board. The important ones for this lab are: `5V` (regulated 5 V output, sourced from USB), `3.3V` (regulated 3.3 V output), `GND` (ground — there are multiple GND pins, all connected together internally), and `VIN` (unregulated input voltage when powering from a barrel jack rather than USB).

**Digital I/O pins** are numbered 0 through 13. Each can be configured as either an input or an output under software control using `pinMode()`. When configured as an output and driven HIGH, they source 5 V. When driven LOW, they sink to 0 V. Pins 0 (RX) and 1 (TX) are also used for serial communication with the PC — avoid using them for other purposes while the Serial Monitor is open.

**PWM-capable pins** are a subset of the digital pins. On the UNO, pins 3, 5, 6, 9, 10, and 11 are connected to the ATmega328P's hardware timer/compare units, which means they can generate a PWM signal using `analogWrite()`. The other digital pins cannot do this. The PWM symbol (~) is printed next to these pin numbers on the board silkscreen.

**Analog input pins** are labelled A0 through A5. Each is connected to one channel of the ATmega328P's 10-bit successive-approximation ADC. They read a voltage between 0 V and 5 V and convert it to a number between 0 and 1023. These pins can also be used as regular digital I/O if needed, though you will not do that in this lab.

### 1.3 The Arduino IDE and Sketch Structure

The **Arduino IDE** is the software on your PC that lets you write, compile, and upload programs (called sketches) to the board. When you press the Upload button, the IDE compiles your sketch into machine code, then uses the USB-serial bridge to transfer it into the ATmega328P's Flash memory via a bootloader that is pre-installed on the chip. The bootloader listens on the serial port for a fraction of a second after reset — that is why the IDE resets the board before uploading.

Every Arduino sketch must contain exactly two functions:

```cpp
void setup() {
    // Runs once when the board powers on or is reset.
    // Use this to configure pins, initialise Serial, set initial states.
}

void loop() {
    // Runs repeatedly, forever, after setup() completes.
    // Your main logic lives here.
}
```

This is not optional. The Arduino framework calls `setup()` once at startup, then enters an infinite loop calling `loop()` over and over. If you omit either function, your sketch will not compile. There is no `main()` in an Arduino sketch — or rather, there is a hidden `main()` inside the Arduino core library that calls `setup()` and `loop()` for you.

### 1.4 The Serial Monitor

The **Serial Monitor** is a terminal window built into the Arduino IDE. The UNO sends text over the USB-serial connection using `Serial.begin()` and `Serial.print()`, and the Serial Monitor displays it. This is your primary debugging tool throughout this lab — when you cannot see what a sensor is reading, printing the value to the Serial Monitor makes the invisible visible.

To open it: Tools → Serial Monitor (or Ctrl+Shift+M). The baud rate in the Serial Monitor dropdown must match the number you pass to `Serial.begin()` in your sketch. A mismatch produces garbled output — garbage characters instead of readable text. Always use 9600 baud in this lab unless instructed otherwise.

💡 The Serial Monitor shares the same USB-serial connection that the IDE uses for uploading. While uploading a sketch, the Serial Monitor is automatically closed and reopened. This is normal.

DOCEOF


## Step 1 — Internal LED Blink: Verifying Your Board

This is your starting point. Before you connect a single external component, you are going to make the Arduino do something visible using nothing but the board itself and a USB cable. The goal is not to build something impressive — it is to confirm that your board, your cable, your IDE installation, and your driver are all working correctly. If anything is broken, you want to find out now, not after you have spent time wiring a circuit.

### Background for Step 1

#### digitalWrite() and pinMode()

Every digital I/O pin on the Arduino must be explicitly configured before use. The function `pinMode(pin, mode)` sets a pin's direction: `OUTPUT` means the pin will drive a voltage onto the circuit (source current when HIGH, sink current when LOW), `INPUT` means the pin will read a voltage from the circuit, and `INPUT_PULLUP` means the pin reads a voltage but also enables an internal pull-up resistor (covered in Step 7).

`digitalWrite(pin, value)` sets an OUTPUT pin to either `HIGH` (5 V) or `LOW` (0 V). Writing HIGH to a pin configured as INPUT does not drive the pin — instead it enables the internal pull-up resistor, which is a different operation. Always call `pinMode()` before `digitalWrite()`.

#### The Onboard LED

Pin 13 on the Arduino UNO has an LED soldered directly onto the board, connected through a series resistor to the pin. Writing HIGH to pin 13 turns it on; writing LOW turns it off. This LED exists for exactly this purpose: testing the board without any external components. It is labeled "L" on the board silkscreen.

#### delay()

`delay(ms)` pauses execution for the given number of milliseconds. During a `delay()` call, the processor is doing nothing — it is burning clock cycles in a wait loop. It cannot read pins, update variables, or respond to button presses while it is waiting. For simple blinking this is acceptable. For more complex programs with multiple simultaneous tasks, you will need `millis()` instead, which you will use starting in Step 5.

### Wiring for Step 1

No external wiring required. The onboard LED on pin 13 is all you need.

### Task 1.1 — Blink the Onboard LED

**Objective:** Upload your first sketch and confirm the board is functioning by blinking the onboard LED at a 1-second interval.

```cpp
// Task 1.1 — Blink the onboard LED
// This sketch verifies the board, IDE, and USB connection are all working.

const int ONBOARD_LED = 13;  // Pin 13 has a built-in LED on every UNO board.
                              // Using a named constant avoids magic numbers in code.

void setup() {
    // Configure pin 13 as an output so we can drive it HIGH or LOW.
    // Without this, digitalWrite() has no effect — the pin defaults to INPUT.
    pinMode(ONBOARD_LED, OUTPUT);

    // Open the serial connection at 9600 baud.
    // This must match the baud rate selected in the Serial Monitor dropdown.
    Serial.begin(9600);

    Serial.println("Board is running. Blink sketch started.");
}

void loop() {
    digitalWrite(ONBOARD_LED, HIGH);   // Drive pin 13 to 5V — LED turns on.
    Serial.println("LED ON");
    delay(1000);                       // Hold for 1000 ms. During this time
                                       // the processor does nothing else.

    digitalWrite(ONBOARD_LED, LOW);    // Drive pin 13 to 0V — LED turns off.
    Serial.println("LED OFF");
    delay(1000);                       // Hold for another 1000 ms.
}
```

**Expected Serial Monitor output:**

```
Board is running. Blink sketch started.
LED ON
LED OFF
LED ON
LED OFF
LED ON
...
```

Each "LED ON" / "LED OFF" pair appears every 2 seconds. The onboard LED changes state in sync with each line.

### Task 1.2 — Variable Blink Rate

**Objective:** Modify the blink timing using variables so the on-time and off-time are independent and easy to change without hunting through the code.

```cpp
// Task 1.2 — Variable blink rate
// Using named variables for timing makes the code self-documenting and easy to adjust.

const int ONBOARD_LED = 13;

// These are declared at the top level so they are easy to find and change.
// Changing ON_TIME here automatically changes every place it is used in the code.
int onTime  = 200;   // LED on duration in milliseconds
int offTime = 800;   // LED off duration in milliseconds

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("Variable blink started.");
    Serial.print("ON time: ");  Serial.print(onTime);  Serial.println(" ms");
    Serial.print("OFF time: "); Serial.print(offTime); Serial.println(" ms");
}

void loop() {
    digitalWrite(ONBOARD_LED, HIGH);
    delay(onTime);

    digitalWrite(ONBOARD_LED, LOW);
    delay(offTime);
}
```

**Expected Serial Monitor output:**

```
Variable blink started.
ON time: 200 ms
OFF time: 800 ms
```

The LED flashes briefly on (200 ms) then stays off longer (800 ms), creating a heartbeat-like pulse. Change `onTime` and `offTime` values, re-upload, and observe the difference.

**Observation Table 1.1**

| `onTime` (ms) | `offTime` (ms) | Visual character of the blink |
|---|---|---|
| 1000 | 1000 | |
| 200 | 800 | |
| 50 | 950 | |
| 900 | 100 | |

**Exercise Questions — Step 1**

1. What happens if you call `digitalWrite(13, HIGH)` without first calling `pinMode(13, OUTPUT)`?
2. Why does the onboard LED have its own series resistor on the board, even though you are not adding one externally?
3. Explain why `delay(1000)` causes problems when you eventually want to also read a button press at the same time.
4. If `onTime = 50` and `offTime = 50`, how many complete blink cycles occur per second?


## Step 2 — External LED Blink: Your First Real Circuit

The onboard LED proved the board works. Now you are going to move onto the breadboard and blink an LED that you wire yourself. This is the transition from "running someone else's hardware" to "building your own circuit." It is also where Ohm's Law stops being a formula you recall for exams and becomes something you actually calculate before touching a wire.

### Background for Step 2

#### Why a Current-Limiting Resistor Is Not Optional

An LED (Light Emitting Diode) is a diode that emits light when current flows through it in the forward direction. Like all diodes, its voltage-current relationship is exponential, not linear. Once the voltage across it exceeds its forward voltage (V_f), typically 1.8 V to 2.2 V for red/yellow/green LEDs, the current rises very steeply. A small increase in voltage produces a dramatic increase in current.

An Arduino output pin sources 5 V when HIGH. If you connect an LED directly from pin to GND, the LED clamps the voltage at V_f and the remaining voltage (5 V − V_f) appears across... nothing. There is no resistance to limit the current, so it rises until either the pin or the LED fails. The ATmega328P datasheet limits each digital pin to 40 mA absolute maximum. A typical LED is rated for 20 mA continuous. Exceeding these limits even briefly can burn the LED out or damage the pin permanently.

The resistor sits in series with the LED and limits current to a safe value by obeying Ohm's Law: V = I × R.

**Resistor value calculation:**

The voltage across the resistor is the supply voltage minus the LED's forward voltage:

```
V_R = V_supply - V_f
V_R = 5V - 2V = 3V   (using V_f = 2V for a typical red/green LED)
```

Choose a target current. A safe, visible brightness is achieved at around 15 mA:

```
R = V_R / I
R = 3V / 0.015A
R = 200Ω
```

A 220Ω resistor (the nearest standard value above 200Ω) limits current to:

```
I = 3V / 220Ω = 13.6 mA
```

This is safe for both the LED and the pin, and provides adequate brightness. This is why every LED in this lab uses a 220Ω resistor.

⚠️ **Warning:** Never omit the series resistor. Never substitute a lower value "just to see if it works." The LED will appear to work briefly before it fails, or the pin will be silently damaged. See the Precautions section for full detail.

#### Breadboard Wiring Basics

A standard breadboard has two regions. The horizontal power rails (usually marked + and −) at the top and bottom run the entire length of the board. The main grid is divided into columns of five holes each — all five holes in a column are connected internally, and the column is broken at the centre gap so the two halves are independent. When you push a component's lead into a column, it is electrically connected to every other lead in the same column.

### Wiring for Step 2

Connect one external LED (any colour) with a 220Ω series resistor to digital pin 8.

| From | To | Notes |
|---|---|---|
| Arduino pin 8 | Breadboard column A | This is the anode side (longer lead of LED goes here via resistor) |
| 220Ω resistor leg 1 | Same column as pin 8 wire | One leg of resistor in same row as pin wire |
| 220Ω resistor leg 2 | LED anode (longer leg) | Resistor in series before LED |
| LED cathode (shorter leg) | Breadboard GND rail | Shorter leg of LED to ground |
| Arduino GND | Breadboard GND rail | Complete the circuit back to Arduino |

💡 The longer lead of an LED is the anode (+). Current flows from anode to cathode. If your LED does not light up and your code is correct, try reversing it — LEDs are polarity-sensitive.

### Task 2.1 — Blink the External LED

**Objective:** Reproduce the blink behaviour from Task 1.1 using an external LED wired on the breadboard.

```cpp
// Task 2.1 — External LED blink
// Everything from Task 1.1 applies here. The only change is the pin number.

const int EXT_LED = 8;   // External LED connected to pin 8 through a 220Ω resistor.
                          // Pin 8 is a plain digital output — no PWM on this pin.

void setup() {
    pinMode(EXT_LED, OUTPUT);  // Must declare as OUTPUT before driving it.
    Serial.begin(9600);
    Serial.println("External LED blink started.");
}

void loop() {
    digitalWrite(EXT_LED, HIGH);   // Source 5V on pin 8 — current flows through
                                   // the 220Ω resistor, through the LED, to GND.
    Serial.println("External LED: ON");
    delay(500);

    digitalWrite(EXT_LED, LOW);    // Pin goes to 0V — no potential difference
                                   // across the circuit — LED turns off.
    Serial.println("External LED: OFF");
    delay(500);
}
```

**Expected Serial Monitor output:**

```
External LED blink started.
External LED: ON
External LED: OFF
External LED: ON
External LED: OFF
...
```

### Task 2.2 — Blink Both LEDs (Alternating)

**Objective:** Blink the onboard LED and the external LED in alternating fashion — when one is on, the other is off — to practice controlling two outputs independently.

```cpp
// Task 2.2 — Alternating blink between onboard and external LED
// This demonstrates that multiple output pins are fully independent.

const int ONBOARD_LED = 13;
const int EXT_LED     = 8;

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);
    pinMode(EXT_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("Alternating blink started.");
}

void loop() {
    // Turn onboard ON and external OFF simultaneously.
    // Both digitalWrite calls execute so fast (~1 µs each) they appear simultaneous.
    digitalWrite(ONBOARD_LED, HIGH);
    digitalWrite(EXT_LED, LOW);
    Serial.println("Onboard: ON  |  External: OFF");
    delay(400);

    // Reverse: onboard OFF, external ON.
    digitalWrite(ONBOARD_LED, LOW);
    digitalWrite(EXT_LED, HIGH);
    Serial.println("Onboard: OFF |  External: ON");
    delay(400);
}
```

**Expected Serial Monitor output:**

```
Alternating blink started.
Onboard: ON  |  External: OFF
Onboard: OFF |  External: ON
Onboard: ON  |  External: OFF
...
```

**Exercise Questions — Step 2**

1. Using V_f = 2.1 V and a target current of 10 mA, calculate the required resistor value for a 5 V supply. What is the nearest standard resistor value?
2. You accidentally insert the LED backwards (cathode toward the pin). What happens? Does anything get damaged?
3. If you use a 100Ω resistor instead of 220Ω, what current flows through the LED? Is this safe?
4. Why does pin 8 work for blinking but cannot be used for LED brightness control with `analogWrite()`? Which pins on the UNO support `analogWrite()`?

DOCEOF


## Step 3 — Traffic Light: Sequencing Multiple Outputs with millis()

You have one external LED working. Now you will wire two more — red, yellow, and green — and sequence them like a traffic light. This step introduces the most important timing concept in Arduino programming: the difference between `delay()` and `millis()`. Understanding this difference is not optional. Every serious Arduino project depends on it.

### Background for Step 3

#### Why delay() Is the Wrong Tool for Multi-Event Timing

`delay()` works by making the processor sit in a tight loop, doing nothing, until the specified time has elapsed. While it is waiting, your program cannot do anything else — read a sensor, respond to a button, update a display, nothing. The processor is frozen.

For a simple single-LED blink this is fine. For a traffic light that you also want to respond to a button press (which you will add later), it is a disaster. Imagine your traffic light is in the middle of a 5-second red phase and a pedestrian presses the button. With `delay(5000)` in your code, the button press is completely ignored — the processor is frozen inside `delay()` and cannot even read the pin. The button press disappears as if it never happened.

`millis()` solves this by returning the number of milliseconds that have elapsed since the board was powered on or reset. It is a counter that runs in the background, incremented by a hardware timer interrupt, regardless of what your code is doing. Instead of freezing and waiting, you use `millis()` to record when something started, and then check whether enough time has passed on every pass through `loop()`.

The fundamental pattern looks like this:

```cpp
unsigned long previousMillis = 0;  // When did the current phase start?
unsigned long interval = 1000;     // How long should this phase last?

void loop() {
    unsigned long currentMillis = millis();  // What time is it right now?

    if (currentMillis - previousMillis >= interval) {
        // Enough time has passed — do the next thing.
        previousMillis = currentMillis;  // Reset the timer to now.
        // ... change state ...
    }
    // If not enough time has passed, loop() returns immediately
    // and runs again. This entire check takes microseconds.
}
```

The key insight is that `loop()` runs thousands of times per second. Most of the time the `if` condition is false and loop() does nothing. When the condition is finally true, you act and reset the timer. The processor is never frozen — it is always free to check other conditions, read other pins, or respond to events.

💡 **Why `unsigned long`?** `millis()` returns an `unsigned long`, which holds values from 0 to 4,294,967,295. The board's millisecond counter rolls over to zero after about 49 days. Using `unsigned long` for both `previousMillis` and `currentMillis` ensures that the subtraction `currentMillis - previousMillis` handles rollover correctly due to unsigned arithmetic properties. If you use `int` instead, the subtraction will produce a negative result after rollover, and your timing will break.

#### Traffic Light State Machine

A traffic light cycles through states: RED → GREEN → YELLOW → RED → ... Each state has a duration. The cleanest way to implement this in code is with a state variable and a `switch` statement. When the timer expires, you advance to the next state and reset the timer.

### Wiring for Step 3

Add a red LED and a yellow LED to the breadboard. You already have one LED from Step 2 — use your green LED for that one, or rewire it now. Each LED has its own 220Ω resistor.

| From | To | Notes |
|---|---|---|
| Arduino pin 2 | 220Ω resistor → Red LED anode | Red LED |
| Red LED cathode | GND rail | |
| Arduino pin 3 | 220Ω resistor → Yellow LED anode | Yellow LED (pin 3 is PWM-capable but used digitally here) |
| Yellow LED cathode | GND rail | |
| Arduino pin 4 | 220Ω resistor → Green LED anode | Green LED |
| Green LED cathode | GND rail | |
| Arduino GND | Breadboard GND rail | Common ground for all LEDs |

### Task 3.1 — Simple Traffic Light with delay() (For Comparison Only)

**Objective:** Implement a basic traffic light using `delay()` so you can clearly see and feel its limitations before switching to `millis()`.

```cpp
// Task 3.1 — Traffic light using delay() — DO NOT use this approach in real projects.
// This version is shown deliberately so you can feel the processor freeze.
// The processor cannot do anything during the delay calls — not even read a button.

const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;

// Phase durations in milliseconds
const int RED_TIME    = 5000;   // Red: 5 seconds
const int GREEN_TIME  = 4000;   // Green: 4 seconds
const int YELLOW_TIME = 1500;   // Yellow: 1.5 seconds (warning before red)

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Traffic light (delay version) started.");
    Serial.println("Notice: during each delay, the board cannot respond to anything.");
}

void loop() {
    // RED phase
    digitalWrite(RED_PIN,    HIGH);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: RED");
    delay(RED_TIME);   // Processor frozen here for 5 full seconds.

    // GREEN phase
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  HIGH);
    digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: GREEN");
    delay(GREEN_TIME);  // Processor frozen for 4 seconds.

    // YELLOW phase — warn before switching back to red
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, HIGH);
    Serial.println("PHASE: YELLOW");
    delay(YELLOW_TIME);  // Processor frozen for 1.5 seconds.
}
```

**Expected Serial Monitor output:**

```
Traffic light (delay version) started.
Notice: during each delay, the board cannot respond to anything.
PHASE: RED
PHASE: GREEN
PHASE: YELLOW
PHASE: RED
...
```

### Task 3.2 — Traffic Light with millis() (The Correct Approach)

**Objective:** Rewrite the traffic light using `millis()` so the processor remains free between phase transitions. This is the version you keep and build on.

```cpp
// Task 3.2 — Traffic light using millis() — non-blocking, correct approach.
// The processor is free on every pass through loop() to check other conditions.
// This is the foundation that makes the button integration in later steps possible.

const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;

// Phase durations in milliseconds
const unsigned long RED_TIME    = 5000;
const unsigned long GREEN_TIME  = 4000;
const unsigned long YELLOW_TIME = 1500;

// State machine: which phase are we currently in?
// Using an enum makes the code self-documenting — STATE_RED is clearer than 0.
enum TrafficState { STATE_RED, STATE_GREEN, STATE_YELLOW };
TrafficState currentState = STATE_RED;   // Start on red (safe default)

unsigned long phaseStartTime = 0;  // Stores the millis() value when current phase began.
                                   // unsigned long required to match millis() return type.

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);

    // Enter the initial state immediately at startup.
    enterState(STATE_RED);
}

// This function turns on the correct LED and records when the phase started.
// Centralising this logic avoids repeating the same digitalWrite calls everywhere.
void enterState(TrafficState newState) {
    currentState   = newState;
    phaseStartTime = millis();  // Record the exact moment this phase began.

    // Turn all LEDs off first, then turn on the correct one.
    // This ensures only one LED is ever on at a time, regardless of previous state.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);

    switch (currentState) {
        case STATE_RED:
            digitalWrite(RED_PIN, HIGH);
            Serial.println("PHASE: RED   (5.0 s)");
            break;
        case STATE_GREEN:
            digitalWrite(GREEN_PIN, HIGH);
            Serial.println("PHASE: GREEN (4.0 s)");
            break;
        case STATE_YELLOW:
            digitalWrite(YELLOW_PIN, HIGH);
            Serial.println("PHASE: YELLOW (1.5 s)");
            break;
    }
}

void loop() {
    unsigned long currentTime = millis();  // Read the current timestamp once per loop.
                                           // Using a local variable avoids calling
                                           // millis() multiple times with slightly
                                           // different values in the same pass.

    // Check whether the current phase has lasted long enough to transition.
    switch (currentState) {
        case STATE_RED:
            if (currentTime - phaseStartTime >= RED_TIME) {
                enterState(STATE_GREEN);  // Time's up — advance to green.
            }
            break;
        case STATE_GREEN:
            if (currentTime - phaseStartTime >= GREEN_TIME) {
                enterState(STATE_YELLOW); // Time's up — advance to yellow.
            }
            break;
        case STATE_YELLOW:
            if (currentTime - phaseStartTime >= YELLOW_TIME) {
                enterState(STATE_RED);   // Time's up — back to red.
            }
            break;
    }

    // At this point, loop() returns. The whole function took microseconds.
    // The processor is free. This is where button reads and sensor checks will go
    // in later steps.
}
```

**Expected Serial Monitor output:**

```
PHASE: RED   (5.0 s)
PHASE: GREEN (4.0 s)
PHASE: YELLOW (1.5 s)
PHASE: RED   (5.0 s)
PHASE: GREEN (4.0 s)
...
```

**Observation Table 3.1**

| Phase | Configured duration | Observed duration (count seconds) | Match? |
|---|---|---|---|
| RED | 5000 ms | | |
| GREEN | 4000 ms | | |
| YELLOW | 1500 ms | | |

---

> ### 🚦 Challenge Question
>
> You now have a working automatic traffic light. Think through the following scenario — you do not need to implement it yet, but you need to reason about the approach.
>
> A pedestrian push button is added to the circuit. When the button is pressed, the traffic light should interrupt whatever phase it is currently in, switch immediately to RED, hold RED for a fixed pedestrian crossing duration (say, 7 seconds), then resume the normal automatic cycle from GREEN.
>
> Consider these questions:
> 1. Why is this challenge impossible to implement cleanly if your traffic light uses `delay()` instead of `millis()`?
> 2. What additional state(s) would you need to add to the `TrafficState` enum?
> 3. Where in `loop()` would you add the button read — before or after the phase transition check, and why does the order matter?
> 4. What happens if the user holds the button down continuously?
>
> Keep your answers in mind. The button is coming in Step 4.

---

**Exercise Questions — Step 3**

1. In the `millis()` version, `phaseStartTime` is declared as `unsigned long`. What happens to the timing logic if you declare it as `int` instead and the board has been running for 33 seconds?
2. Why does `enterState()` turn all three LEDs off before turning one on, rather than just turning the correct one on?
3. Modify the phase durations to match a real Kathmandu intersection: RED 60 s, GREEN 45 s, YELLOW 3 s. What values would you use for the three constants?
4. In the `delay()` version, if you added `Serial.println("Checking...")` inside `loop()` after the three phase blocks, how often would it print? In the `millis()` version, how often would it print?
5. The `enum` keyword is used for `TrafficState`. Explain what an enum is and why it makes the code more readable than using integers 0, 1, 2 for the states.

DOCEOF


---

## Step 4 — Push Button Input: Reading a Digital Input and the Floating Pin Problem

Your traffic light runs automatically. Now you are going to add human input: a push button. This step introduces `digitalRead()`, the concept of a floating input pin, and the pull-down resistor — a small but critical piece of hardware that makes digital inputs behave reliably.

### Background for Step 4

#### digitalRead() and Input Pins

`digitalRead(pin)` reads the voltage currently present on a pin configured as `INPUT` and returns either `HIGH` (the voltage is close to 5 V) or `LOW` (the voltage is close to 0 V). The ATmega328P internally compares the pin voltage to a threshold of approximately 2.5 V — above it is HIGH, below it is LOW.

Calling `digitalRead()` on a pin configured as `OUTPUT` reads back whatever value you last wrote to it, which is rarely useful. Always configure input pins with `pinMode(pin, INPUT)` or `pinMode(pin, INPUT_PULLUP)` before reading them.

#### The Floating Pin Problem

Here is the problem. You connect one terminal of a push button to a digital input pin and the other terminal to 5 V. When the button is pressed, 5 V appears on the pin and `digitalRead()` returns `HIGH`. So far so good. But what does the pin read when the button is NOT pressed?

Nothing. The pin is connected to nothing. It floats.

A floating pin is not reliably HIGH or LOW. It picks up interference from nearby signals through capacitive and inductive coupling — the electric fields from other conductors, from mains frequency interference, from radio waves, from your hand near the board. The pin's reading bounces randomly between HIGH and LOW with no predictable pattern. Your code will think the button is being pressed and released thousands of times per second when nobody is touching it.

This is not a theoretical problem. Try it without a pull-down resistor and watch the chaos in the Serial Monitor.

#### The External Pull-Down Resistor

The fix is a pull-down resistor: a resistor connected between the input pin and GND. Its job is to give the pin a definite reference voltage (0 V) when the button is open.

When the button is open: the pin is connected to GND through the 10kΩ resistor. The resistor pulls the pin down to 0 V. `digitalRead()` returns LOW. No current flows (the pin draws negligible current in input mode).

When the button is pressed: 5 V is connected directly to the pin. The resistor is now connected between 5 V and GND — it carries 5V/10kΩ = 0.5 mA, which is harmless. The pin sees 5 V and `digitalRead()` returns HIGH.

The resistor value of 10kΩ is a good balance: low enough to firmly pull the pin to GND in the presence of interference, high enough that when the button is pressed, most of the 5 V appears at the pin rather than being divided across a small resistor.

### Wiring for Step 4

The three LEDs from Step 3 remain connected. Add the push button and its pull-down resistor.

| From | To | Notes |
|---|---|---|
| Arduino 5V | Button terminal 1 | One side of button goes to 5V |
| Button terminal 2 | Arduino pin 7 | Other side goes to input pin |
| Button terminal 2 | 10kΩ resistor leg 1 | Same node as pin — junction of button and resistor |
| 10kΩ resistor leg 2 | GND rail | Resistor pulls pin down to 0V when button open |

💡 A standard through-hole tactile push button has 4 pins, but they are connected in pairs. Pins on the same side of the button are always connected together. Current flows only when you press the button and bridge the two sides. Orient the button so it bridges across the centre gap of the breadboard.

### Task 4.1 — Read the Button and Print Its State

**Objective:** Read the button state on every loop iteration and print it to the Serial Monitor. Observe the difference in behaviour with and without the pull-down resistor.

```cpp
// Task 4.1 — Read button state and print to Serial Monitor.
// This task is diagnostic — it makes the raw button signal visible.

const int BUTTON_PIN = 7;   // Digital input connected to one side of the button.
                             // The other side of the button connects to 5V.
                             // A 10kΩ pull-down resistor connects pin 7 to GND.

void setup() {
    // Configure pin 7 as a plain input — no internal pull-up yet.
    // The external 10kΩ pull-down resistor handles the idle state.
    pinMode(BUTTON_PIN, INPUT);
    Serial.begin(9600);
    Serial.println("Button read started. Press and release the button.");
}

void loop() {
    // Read the current button state.
    // HIGH means 5V is present on the pin (button pressed).
    // LOW means the pull-down is holding the pin at 0V (button released).
    int buttonState = digitalRead(BUTTON_PIN);

    if (buttonState == HIGH) {
        Serial.println("Button: PRESSED");
    } else {
        Serial.println("Button: RELEASED");
    }

    // Small delay here only to prevent the Serial Monitor from being
    // flooded with thousands of lines per second. This is a monitoring
    // sketch — in real code you would not delay here.
    delay(100);
}
```

**Expected Serial Monitor output (with pull-down resistor properly connected):**

```
Button read started. Press and release the button.
Button: RELEASED
Button: RELEASED
Button: RELEASED
Button: PRESSED    ← button held down
Button: PRESSED
Button: PRESSED
Button: RELEASED   ← button released
Button: RELEASED
...
```

⚠️ If you remove the pull-down resistor and re-upload, you will see the output flip unpredictably between PRESSED and RELEASED even when nobody is touching the button. This is the floating pin problem. Reconnect the resistor before continuing.

### Task 4.2 — Button Controls LED Toggle

**Objective:** Press the button to toggle the red LED on or off. The LED should change state once per button press — not flicker while the button is held.

```cpp
// Task 4.2 — Button toggles the red LED.
// The LED changes state on each PRESS (LOW-to-HIGH transition), not continuously.

const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;   // Current LED state — starts off.
int lastButtonState = LOW;   // What the button read last time through loop().
                              // Used to detect a LOW→HIGH transition (press event).

void setup() {
    pinMode(BUTTON_PIN, INPUT);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("Button toggle started. Press button to toggle red LED.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);

    // Detect a rising edge: button was LOW last time, now it is HIGH.
    // This fires only at the moment of press — once per press, not continuously.
    if (currentButtonState == HIGH && lastButtonState == LOW) {
        // Toggle the LED: if it was LOW, make it HIGH, and vice versa.
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);

        Serial.print("Button pressed → Red LED: ");
        Serial.println(ledState == HIGH ? "ON" : "OFF");
    }

    // Save the current state for comparison on the next loop iteration.
    // Without this, we lose the ability to detect transitions.
    lastButtonState = currentButtonState;

    delay(20);  // Small delay reduces the speed of edge detection,
                // which partially reduces bouncing effects — but not reliably.
                // Step 5 addresses bouncing properly with millis().
}
```

**Expected Serial Monitor output:**

```
Button toggle started. Press button to toggle red LED.
Button pressed → Red LED: ON
Button pressed → Red LED: OFF
Button pressed → Red LED: ON
...
```

💡 You may notice that a single physical press sometimes toggles the LED twice or more. This is contact bounce — the button's metal contacts physically vibrate when they make contact, producing multiple rapid transitions. Step 5 addresses this properly.

**Observation Table 4.1**

| Button state | Pull-down connected? | Expected digitalRead() | Actual digitalRead() |
|---|---|---|---|
| Released | Yes | LOW | |
| Pressed | Yes | HIGH | |
| Released | No | Undefined | |
| Pressed | No | HIGH (mostly) | |

**Exercise Questions — Step 4**

1. Why is the pull-down resistor 10kΩ and not 1kΩ or 100kΩ? What trade-offs does the resistor value involve?
2. In Task 4.2, `lastButtonState` is compared to `currentButtonState` to detect a rising edge. What would happen if you removed `lastButtonState` and just checked `if (currentButtonState == HIGH)`?
3. Describe what you would observe in the Serial Monitor if the 10kΩ pull-down resistor were accidentally connected to 5V instead of GND.
4. A colleague suggests connecting the button between pin 7 and GND, then using a pull-up resistor to 5V. How would the logic in `loop()` need to change? What would HIGH and LOW now mean physically?





---

## Step 5 — Pull-Up Resistors and INPUT_PULLUP

You have used an external pull-down resistor to stabilise a floating input pin. Now you will see the alternative: the pull-up resistor. And then you will discover that you do not need any external resistor at all — because the ATmega328P has pull-up resistors built into the chip itself, switchable in software.

### Background for Step 5

#### External Pull-Up Resistor

A pull-up resistor connects between the input pin and 5V (the supply). The button connects between the input pin and GND. When the button is open, the pull-up holds the pin at 5V — `digitalRead()` returns HIGH. When the button is pressed, GND is connected to the pin, pulling it down to 0V — `digitalRead()` returns LOW.

Notice the logic inversion compared to pull-down: with a pull-down, pressing the button gives you HIGH. With a pull-up, pressing the button gives you LOW. This catches many people off guard. If you switch from pull-down to pull-up and forget to invert your comparison, your program will treat "released" as "pressed" and vice versa.

#### Arduino's Internal INPUT_PULLUP

The ATmega328P has resistors built into the chip for every I/O pin. Each internal pull-up is approximately 20kΩ to 50kΩ and connects between the pin and the 5V rail. You enable it simply by calling `pinMode(pin, INPUT_PULLUP)` instead of `pinMode(pin, INPUT)`. No external resistor needed.

The internal pull-up works identically to an external pull-up resistor: pin is HIGH when button is open, pin is LOW when button is pressed (button connects pin to GND). The same logic inversion applies.

The internal pull-up is convenient for prototyping and simple circuits. The only reason to use an external pull-down or pull-up instead is if: the specific circuit requires active-low vs active-high logic, you need a precise known resistance value, or you are wiring for noise immunity in a long cable run where 20–50kΩ is too weak.

#### Comparison Table: All Three Approaches

| Approach | External components | Button connects to | Idle state | Pressed state | Logic |
|---|---|---|---|---|---|
| External pull-down | 10kΩ resistor to GND | 5V | LOW | HIGH | Active HIGH |
| External pull-up | 10kΩ resistor to 5V | GND | HIGH | LOW | Active LOW |
| Internal INPUT_PULLUP | None | GND | HIGH | LOW | Active LOW |

### Wiring for Step 5

For the INPUT_PULLUP configuration, you need to rewire the button slightly. Remove the 10kΩ pull-down resistor (the one between pin 7 and GND) and rewire the button so that it connects the pin to GND when pressed, instead of to 5V.

| From | To | Notes |
|---|---|---|
| Arduino pin 7 | Button terminal 1 | Input pin connects to button |
| Button terminal 2 | GND rail | Other side of button goes to GND (not 5V!) |
| *(No pull-down resistor)* | | Internal pull-up replaces it |

⚠️ With `INPUT_PULLUP`, the button must connect the pin to GND — the opposite of the pull-down configuration. If you keep the button connected to 5V and enable `INPUT_PULLUP`, pressing the button connects 5V to a pin that already has the internal pull-up at ~5V. Nothing happens — the button appears to do nothing.

### Task 5.1 — Button with Internal INPUT_PULLUP

**Objective:** Use `INPUT_PULLUP` to eliminate the external resistor and observe the inverted logic.

```cpp
// Task 5.1 — Button using Arduino's internal pull-up resistor.
// No external pull-down or pull-up resistor needed.
// IMPORTANT: logic is inverted — LOW means pressed, HIGH means released.

const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;
int lastButtonState = HIGH;  // With INPUT_PULLUP, the idle (unpressed) state is HIGH.
                              // Initialise to HIGH to avoid a false trigger at startup.

void setup() {
    // INPUT_PULLUP enables the internal ~20–50kΩ pull-up resistor.
    // No external components needed on pin 7 (other than the button itself).
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("INPUT_PULLUP demo. Button now connects pin to GND when pressed.");
    Serial.println("Note: LOW = pressed, HIGH = released.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);

    // With INPUT_PULLUP, a press is a HIGH→LOW transition (falling edge).
    // The logic is inverted compared to pull-down — LOW means the button is pressed.
    if (currentButtonState == LOW && lastButtonState == HIGH) {
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);

        Serial.print("Button pressed (pin went LOW) → Red LED: ");
        Serial.println(ledState == HIGH ? "ON" : "OFF");
    }

    lastButtonState = currentButtonState;
    delay(20);
}
```

**Expected Serial Monitor output:**

```
INPUT_PULLUP demo. Button now connects pin to GND when pressed.
Note: LOW = pressed, HIGH = released.
Button pressed (pin went LOW) → Red LED: ON
Button pressed (pin went LOW) → Red LED: OFF
Button pressed (pin went LOW) → Red LED: ON
...
```

**Exercise Questions — Step 5**

1. With `INPUT_PULLUP` enabled, what voltage do you measure on pin 7 with a multimeter when the button is not pressed? When it is pressed?
2. You switch from `INPUT_PULLUP` back to external pull-down but forget to change `currentButtonState == LOW` to `currentButtonState == HIGH` in the comparison. Describe the exact broken behaviour you would observe.
3. Why is the internal pull-up resistance specified as a range (20kΩ–50kΩ) rather than a single value? Does this variation matter for this application?
4. In what situation would you prefer an external pull-down over the internal `INPUT_PULLUP`, even though `INPUT_PULLUP` requires no external components?



---

## Step 6 — Contact Bounce and Software Debouncing

You have noticed it already: a single button press sometimes toggles the LED two or three times. This is contact bounce — a real physical phenomenon that every digital system that interacts with mechanical switches must deal with. This step explains what it is and fixes it permanently using a clean, `millis()`-based software debouncing algorithm.

### Background for Step 6

#### What Contact Bounce Is, Physically

A push button is a pair of metal contacts separated by a spring. When you press the button, the moving contact accelerates toward the fixed contact and strikes it. Metal is elastic. Instead of making clean contact and staying there, the contacts bounce apart, make contact again, bounce, make contact again — typically 5 to 50 times in the first 1 to 20 milliseconds, until the mechanical vibration damps out and the contact settles.

During those milliseconds, the pin voltage looks like a burst of pulses — not the clean single LOW-to-HIGH transition your code expects. Your edge-detection code sees each bounce as a separate press event, so a single physical press produces multiple toggle actions.

You can actually observe this with the Serial Monitor. Set the delay in your loop to 1 ms (or remove it) and press the button once. You will see multiple "Button pressed" lines appear for a single physical press.

#### Why delay() Is a Poor Debounce Method

A common but incorrect approach is to add a fixed `delay(50)` after detecting a press and before checking the button again. This "waits out" the bounce. It works sometimes, but it has two serious problems.

First, `delay()` freezes the processor, reintroducing the blocking problem you eliminated with `millis()` in Step 3. If the button is pressed while the processor is inside a delay, it cannot see it.

Second, the bounce duration varies from button to button, press to press, and with temperature and age. A fixed 50 ms delay may be too short for some buttons (still bouncing) or unnecessarily long for others (wasting responsiveness).

#### millis()-Based Debouncing

The correct approach is to require that the pin remain stable for a minimum duration before treating a transition as valid. This is called a debounce window, typically 50 ms.

The algorithm works as follows:

1. On every pass through `loop()`, read the button's current state.
2. If the current state differs from the last stable state, note the time (`lastDebounceTime = millis()`).
3. If the current state has been different from the last stable state for longer than the debounce window, accept it as a genuine transition and update the stable state.
4. If the pin bounces during the window, the timer resets and we wait again.

This approach adds zero blocking. The processor checks the condition on every loop pass and moves on immediately.

### Wiring for Step 6

No wiring changes. The button is already connected to pin 7 with `INPUT_PULLUP` from Step 5. The three traffic light LEDs remain connected. The red LED on pin 2 will be used for the toggle demonstration.

### Task 6.1 — Observe Contact Bounce Without Debouncing

**Objective:** Remove all debouncing and set the loop to run as fast as possible. Press the button once and count how many events are detected. This makes the problem concrete before fixing it.

```cpp
// Task 6.1 — No debouncing: observe raw bounce events.
// Remove the delay(20) and watch the Serial Monitor while pressing the button once.
// Count the number of "Button pressed" lines per physical press.

const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;
int lastButtonState = HIGH;

void setup() {
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("No debounce — press the button ONCE and count events.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);

    if (currentButtonState == LOW && lastButtonState == HIGH) {
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);
        Serial.println("Button event detected!");  // Count these per press.
    }

    lastButtonState = currentButtonState;
    // No delay — loop runs as fast as possible to catch all bounce events.
}
```

**Expected Serial Monitor output (single physical button press):**

```
No debounce — press the button ONCE and count events.
Button event detected!
Button event detected!
Button event detected!
Button event detected!
```

The number of events per press varies. You might see 1 (lucky clean contact) or up to 10 or more.

**Observation Table 6.1**

| Press number | Number of "Button event detected!" lines observed |
|---|---|
| Press 1 | |
| Press 2 | |
| Press 3 | |
| Press 4 | |
| Press 5 | |

### Task 6.2 — Proper Debouncing with millis()

**Objective:** Implement a robust, non-blocking debounce algorithm. After debouncing, each physical press produces exactly one event, reliably.

```cpp
// Task 6.2 — Proper millis()-based software debouncing.
// This is the standard debounce algorithm. Memorise its structure.

const int BUTTON_PIN            = 7;
const int RED_PIN               = 2;
const unsigned long DEBOUNCE_MS = 50;  // Minimum stable time to accept a transition.
                                        // 50 ms is longer than most contact bounce.
                                        // Decrease this if the button feels sluggish;
                                        // increase it if bounce still occurs.

int ledState           = LOW;
int lastRawState       = HIGH;   // The raw digitalRead() from last loop pass.
int stableButtonState  = HIGH;   // The last confirmed-stable state.
                                  // Only this variable drives program logic.

unsigned long lastDebounceTime = 0;  // When the raw state last changed.
                                      // Reset every time the pin state changes.

void setup() {
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("Debounced button ready. Each press produces exactly one event.");
}

void loop() {
    int rawState = digitalRead(BUTTON_PIN);  // Read the raw, potentially bouncing signal.

    if (rawState != lastRawState) {
        // The pin state has changed — could be a real press or a bounce.
        // Start (or restart) the debounce timer.
        lastDebounceTime = millis();
    }

    // Only accept the new state if it has been stable for longer than DEBOUNCE_MS.
    // If the pin bounces within the window, lastDebounceTime is reset above and
    // this condition does not fire until the signal has been stable again.
    if ((millis() - lastDebounceTime) >= DEBOUNCE_MS) {
        if (rawState != stableButtonState) {
            // The pin has been stable in a NEW state for 50 ms — this is real.
            stableButtonState = rawState;

            // Only act on a press (HIGH→LOW transition with INPUT_PULLUP).
            if (stableButtonState == LOW) {
                ledState = (ledState == LOW) ? HIGH : LOW;
                digitalWrite(RED_PIN, ledState);
                Serial.print("Debounced press → Red LED: ");
                Serial.println(ledState == HIGH ? "ON" : "OFF");
            }
        }
    }

    lastRawState = rawState;  // Save raw state for next loop to detect changes.
}
```

**Expected Serial Monitor output:**

```
Debounced button ready. Each press produces exactly one event.
Debounced press → Red LED: ON
Debounced press → Red LED: OFF
Debounced press → Red LED: ON
```

Each physical press, no matter how hard or fast, produces exactly one line. Bounce events are invisible to the logic because they last less than 50 ms.

**Observation Table 6.2 — Verify Debouncing Effectiveness**

| Press number | Events before debounce (from Table 6.1) | Events after debounce |
|---|---|---|
| Press 1 | | |
| Press 2 | | |
| Press 3 | | |
| Press 4 | | |
| Press 5 | | |

**Exercise Questions — Step 6**

1. Why does the debounce algorithm reset `lastDebounceTime` every time `rawState != lastRawState`, rather than only on a LOW transition?
2. What is the minimum responsiveness penalty introduced by a 50 ms debounce window? In other words, what is the maximum press rate at which the debouncer can reliably detect separate presses?
3. Identify the bug in the following debounce code and explain what incorrect behaviour results:
   ```cpp
   if (currentState == LOW) {
       delay(50);
       if (digitalRead(BUTTON_PIN) == LOW) {
           // treat as press
       }
   }
   ```
4. If your button has particularly severe bounce (up to 80 ms of contact chatter), what single value would you change in Task 6.2's code, and to what?



---

## Step 7 — Potentiometer and Analog Input: Reading a Continuously Varying Signal

Everything so far has been digital: signals that are either fully on or fully off, HIGH or LOW. Now you will work with an analog signal — a voltage that varies smoothly across a continuous range. The component is a potentiometer, and the hardware that reads it is the ATmega328P's Analog-to-Digital Converter.

### Background for Step 7

#### What a Potentiometer Is

A **potentiometer** is a resistor with a sliding contact (called a wiper) that moves along its resistive element. It has three terminals: two fixed ends and the wiper in the middle. When you connect the two fixed ends to 5V and GND, the wiper divides that voltage proportionally to its position. Rotate the knob to one extreme and the wiper delivers 0V; rotate to the other extreme and it delivers 5V. At the midpoint, it delivers 2.5V. Any position in between produces a corresponding voltage between 0V and 5V.

This is a voltage divider, implemented mechanically. In the real world, potentiometers are used as volume controls, position sensors, and calibration knobs. In this lab they serve as a convenient, manual way to produce a variable voltage for you to read with the ADC.

#### The Analog-to-Digital Converter (ADC)

The ATmega328P's ADC measures the voltage on an analog input pin and converts it to a digital number. The UNO's ADC has 10-bit resolution, which means it maps the input voltage range (0V to 5V) to an integer range of 0 to 1023 (2^10 = 1024 possible values).

The conversion formula is:

```
ADC_value = (V_in / V_ref) × 1023
```

Where V_ref is the reference voltage (5V by default on the UNO). Rearranging to find the actual voltage from an ADC reading:

```
V_in = (ADC_value / 1023) × V_ref
V_in = (ADC_value / 1023) × 5.0
```

**Voltage resolution:** The smallest voltage change the ADC can detect is one step:

```
Resolution = V_ref / 1023 = 5.0V / 1023 ≈ 4.89 mV per step
```

This means two voltages that differ by less than ~5 mV will produce the same ADC reading.

**Worked examples:**

If the potentiometer wiper is at the midpoint: V_in = 2.5V
```
ADC_value = (2.5 / 5.0) × 1023 = 511.5 → 511 (integer truncation)
```

If `analogRead()` returns 768:
```
V_in = (768 / 1023) × 5.0 = 3.753V
```

#### The map() Function

`analogRead()` returns a value from 0 to 1023. Often you need to scale this to a different range — for example, 0 to 255 for `analogWrite()`, or 0 to 180 for a servo angle, or any custom range.

`map(value, fromLow, fromHigh, toLow, toHigh)` performs this linear scaling:

```
mapped = toLow + (value - fromLow) × (toHigh - toLow) / (fromHigh - fromLow)
```

For example, mapping ADC value 512 from [0,1023] to [0,255]:
```
mapped = 0 + (512 - 0) × (255 - 0) / (1023 - 0) = 127.5 → 127
```

💡 `map()` uses integer arithmetic internally — it truncates, not rounds. For most applications this is fine. If you need floating-point precision, calculate the mapping manually using `float` variables.

### Wiring for Step 7

All previous components remain connected. Add the potentiometer.

| From | To | Notes |
|---|---|---|
| Potentiometer left pin | Arduino 5V | Fixed end — supplies full voltage |
| Potentiometer right pin | Arduino GND | Fixed end — ground reference |
| Potentiometer centre pin (wiper) | Arduino A0 | Output voltage: varies 0V–5V with knob |

💡 Which side is left and right? It does not matter for the function — swapping 5V and GND only reverses the direction of knob rotation. If turning clockwise increases the ADC reading when you expect it to decrease, simply swap the two fixed-end connections.

### Task 7.1 — Read Raw ADC Value and Voltage

**Objective:** Read the potentiometer position as a raw ADC value and as a calculated voltage. Display both on the Serial Monitor.

```cpp
// Task 7.1 — Read potentiometer: raw ADC value and calculated voltage.

const int POT_PIN = A0;  // Analog input pin A0. No pinMode() needed for analog reads —
                          // analog pins are inputs by default.

void setup() {
    Serial.begin(9600);
    Serial.println("Potentiometer read started.");
    Serial.println("Turn the knob and observe the values change.");
    Serial.println("ADC_Value | Voltage (V)");
    Serial.println("----------|------------");
}

void loop() {
    // Read A0 — returns an integer from 0 (0V) to 1023 (5V).
    // The conversion takes approximately 100 microseconds.
    int adcValue = analogRead(POT_PIN);

    // Convert ADC value to voltage using the formula: V = (ADC / 1023) × 5.0
    // The division must use float to avoid integer truncation (1023/1023 as int = 1, not 1.0).
    float voltage = (adcValue / 1023.0) * 5.0;

    Serial.print(adcValue);
    Serial.print("       | ");
    Serial.println(voltage, 3);  // Print voltage with 3 decimal places.

    delay(200);  // 200 ms between readings — enough to see values change as you turn the knob.
}
```

**Expected Serial Monitor output (knob turned from minimum to maximum):**

```
Potentiometer read started.
Turn the knob and observe the values change.
ADC_Value | Voltage (V)
----------|------------
0         | 0.000     ← knob at minimum
128       | 0.625
255       | 1.246
511       | 2.496
768       | 3.753
895       | 4.375
1023      | 5.000     ← knob at maximum
```

**Observation Table 7.1**

| Knob position (estimate) | ADC value (0–1023) | Calculated voltage (V) | Expected voltage (V) |
|---|---|---|---|
| Fully counter-clockwise | | | 0.000 |
| 25% turned | | | ~1.250 |
| 50% turned (midpoint) | | | ~2.500 |
| 75% turned | | | ~3.750 |
| Fully clockwise | | | 5.000 |

*Formula used: V = (ADC_value / 1023) × 5.0*

### Task 7.2 — Map ADC Value to a Custom Range

**Objective:** Use the `map()` function to convert the ADC range (0–1023) to a percentage (0–100) and to the PWM range (0–255). Display all three values simultaneously.

```cpp
// Task 7.2 — Map ADC value to percentage and to PWM range.
// This demonstrates the map() function before using it for LED brightness control.

const int POT_PIN = A0;

void setup() {
    Serial.begin(9600);
    Serial.println("ADC | Percent | PWM_Range");
    Serial.println("----|---------|----------");
}

void loop() {
    int adcValue = analogRead(POT_PIN);

    // Map from [0,1023] to [0,100] for a human-readable percentage.
    int percent  = map(adcValue, 0, 1023, 0, 100);

    // Map from [0,1023] to [0,255] for use with analogWrite() in the next step.
    // analogWrite() accepts values 0–255 only. Passing values outside this range
    // causes undefined behaviour, so the mapping is essential.
    int pwmValue = map(adcValue, 0, 1023, 0, 255);

    Serial.print(adcValue);
    Serial.print(" | ");
    Serial.print(percent);
    Serial.print("%      | ");
    Serial.println(pwmValue);

    delay(200);
}
```

**Expected Serial Monitor output:**

```
ADC | Percent | PWM_Range
----|---------|----------
0   | 0%      | 0
256 | 25%     | 63
511 | 49%     | 127
768 | 75%     | 191
1023| 100%    | 255
```

**Exercise Questions — Step 7**

1. The ADC returns 682. Calculate the voltage this represents. Show your working.
2. You connect the potentiometer but the ADC always reads 1023 regardless of knob position. What is the most likely wiring mistake?
3. Why does the code use `1023.0` (with the decimal) rather than `1023` in the voltage calculation? What wrong result would you get with integer `1023`?
4. `map()` uses integer arithmetic. If `adcValue` is 512, manually calculate what `map(512, 0, 1023, 0, 255)` returns. Does it round or truncate?
5. Why does `analogRead()` not require a `pinMode()` call for analog pins?



---

## Step 8 — PWM and LED Brightness Control

The potentiometer gives you a number that varies from 0 to 1023. The obvious thing to do with a continuously varying number is to use it to produce a continuously varying output. In this step, you will use `analogWrite()` to control the brightness of an LED by mapping the potentiometer's position directly to PWM duty cycle. Turn the knob, the LED dims or brightens in real time.

### Background for Step 8

#### What PWM Is

An Arduino digital output pin can only be fully ON (5V) or fully OFF (0V). It cannot output 2.5V or 3.3V — it is a digital switch, not a true analog output. But there is a technique called **Pulse-Width Modulation** (PWM) that simulates an analog output by switching rapidly between ON and OFF.

PWM works by producing a square wave at a fixed frequency (approximately 490 Hz on most UNO PWM pins, 980 Hz on pins 5 and 6) and varying the proportion of each cycle that the signal is HIGH. This proportion is called the **duty cycle**.

```
Duty cycle = (time ON / total period) × 100%
```

If duty cycle is 0%: the pin is always LOW. LED is off.
If duty cycle is 50%: the pin is HIGH for half the period, LOW for half. The LED sees an average of 2.5V and glows at half brightness.
If duty cycle is 100%: the pin is always HIGH. LED is at full brightness.

The LED and your eye do not perceive the switching because it happens at hundreds of hertz — far faster than the eye can follow. What you perceive is the average brightness, which is proportional to the duty cycle.

#### analogWrite()

`analogWrite(pin, value)` sets the PWM duty cycle on a PWM-capable pin. The `value` parameter maps to duty cycle as follows:

```
Duty cycle (%) = (value / 255) × 100
```

So `analogWrite(pin, 0)` is 0% (fully off), `analogWrite(pin, 127)` is approximately 50% duty cycle, and `analogWrite(pin, 255)` is 100% (fully on).

Only pins 3, 5, 6, 9, 10, and 11 support `analogWrite()` on the UNO. Calling `analogWrite()` on any other pin has no effect (or undefined behaviour depending on firmware version). This is why the LED for brightness control must be on one of these pins.

⚠️ `analogWrite()` does NOT require `pinMode(pin, OUTPUT)` to be called first (on most Arduino versions), but calling it explicitly is good practice and makes your intent clear. Always call `pinMode()` for every pin you use.

**Worked example:**

`analogWrite(9, 191)` produces a duty cycle of:
```
(191 / 255) × 100 = 74.9%
```
The LED receives 74.9% of its maximum brightness.

### Wiring for Step 8

The potentiometer is already connected to A0. Add a new LED (use any colour — it will serve as the brightness-controlled LED) on pin 9, which is a PWM-capable pin.

| From | To | Notes |
|---|---|---|
| Arduino pin 9 | 220Ω resistor → LED anode | Pin 9 is PWM-capable (marked ~ on board) |
| LED cathode | GND rail | |
| Potentiometer | Already connected to A0 | No change needed |

All three traffic light LEDs and the button remain connected.

### Task 8.1 — Control LED Brightness with the Potentiometer

**Objective:** Map the potentiometer's ADC reading directly to `analogWrite()` duty cycle. Turn the knob to control the LED brightness in real time.

```cpp
// Task 8.1 — Potentiometer controls LED brightness via PWM.
// The potentiometer ADC range (0–1023) is mapped to the PWM range (0–255).

const int POT_PIN    = A0;
const int BRIGHT_LED = 9;   // Must be a PWM-capable pin. Pin 9 uses Timer 1.

void setup() {
    pinMode(BRIGHT_LED, OUTPUT);  // Declare as output even for PWM pins — good practice.
    Serial.begin(9600);
    Serial.println("Turn the potentiometer to control LED brightness.");
    Serial.println("ADC | PWM | Duty%");
}

void loop() {
    int adcValue = analogRead(POT_PIN);   // Read knob position: 0–1023.

    // Map ADC range to PWM range. analogWrite() only accepts 0–255.
    // map() performs linear scaling: 0→0, 1023→255, values in between proportionally.
    int pwmValue = map(adcValue, 0, 1023, 0, 255);

    analogWrite(BRIGHT_LED, pwmValue);   // Set PWM duty cycle on pin 9.
                                          // The LED's perceived brightness changes instantly.

    // Calculate duty cycle for display.
    float dutyCycle = (pwmValue / 255.0) * 100.0;

    Serial.print(adcValue);
    Serial.print(" | ");
    Serial.print(pwmValue);
    Serial.print("  | ");
    Serial.print(dutyCycle, 1);
    Serial.println("%");

    delay(100);  // Update 10 times per second — smooth enough for visual feedback.
}
```

**Expected Serial Monitor output (knob swept from min to max):**

```
Turn the potentiometer to control LED brightness.
ADC | PWM | Duty%
0   | 0   | 0.0%     ← LED off
255 | 63  | 24.7%    ← dim
511 | 127 | 49.8%    ← half brightness
768 | 191 | 74.9%    ← bright
1023| 255 | 100.0%   ← full brightness
```

### Task 8.2 — Reversed Brightness (Inverted Control)

**Objective:** Modify the mapping so that turning the knob clockwise decreases brightness instead of increasing it. This demonstrates that `map()` handles inverted ranges.

```cpp
// Task 8.2 — Inverted brightness: knob clockwise = dimmer.
// map() handles inversion naturally — just swap toLow and toHigh.

const int POT_PIN    = A0;
const int BRIGHT_LED = 9;

void setup() {
    pinMode(BRIGHT_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("Inverted brightness: CW knob = dimmer.");
}

void loop() {
    int adcValue = analogRead(POT_PIN);

    // Swap the output range: map 0→255 and 1023→0.
    // Now maximum knob position produces minimum PWM (LED off).
    int pwmValue = map(adcValue, 0, 1023, 255, 0);

    analogWrite(BRIGHT_LED, pwmValue);

    Serial.print("ADC: "); Serial.print(adcValue);
    Serial.print(" → PWM: "); Serial.println(pwmValue);

    delay(100);
}
```

**Expected Serial Monitor output:**

```
Inverted brightness: CW knob = dimmer.
ADC: 0    → PWM: 255   ← knob at min → LED fully on
ADC: 511  → PWM: 127   ← mid knob → half brightness
ADC: 1023 → PWM: 0     ← knob at max → LED off
```

**Observation Table 8.1**

| Knob position | ADC reading | PWM value (0–255) | Duty cycle (%) | LED appearance |
|---|---|---|---|---|
| Fully CCW | | | | |
| 25% | | | | |
| 50% | | | | |
| 75% | | | | |
| Fully CW | | | | |

*Formula: Duty cycle = (PWM_value / 255) × 100%*

**Exercise Questions — Step 8**

1. `analogWrite(9, 64)` is called. What is the duty cycle? What is the average voltage seen by the LED?
2. You call `analogWrite(8, 200)`. What happens? Why?
3. If the PWM frequency on pin 9 is 490 Hz, what is the period of one PWM cycle in milliseconds? At duty cycle 75%, for how many milliseconds is the pin HIGH in each cycle?
4. Why does PWM work for LED brightness control but would NOT produce accurate audio tones without additional filtering?
5. Explain why `map(adcValue, 0, 1023, 0, 255)` might occasionally return 256 for an ADC value of 1023 due to integer arithmetic, and how you would guard against this.



---

## Step 9 — Multi-LED Analog Control: Potentiometer Selects the Active LED

This is the final step and it brings the entire circuit together. The potentiometer's full 0–1023 range is divided into three zones. Each zone lights a different LED from the traffic light array: the red, yellow, or green. Turn the knob through its range and you scroll through the three LEDs. This combines everything you have learned — `analogRead()`, range division, `digitalWrite()`, and the habit of turning all outputs off before turning one on.

### Background for Step 9

#### Dividing a Range Into Zones

The ADC output spans 0 to 1023. Dividing this into three equal zones gives:

```
Zone 1 (RED):    0   to  340
Zone 2 (YELLOW): 341 to  681
Zone 3 (GREEN):  682 to 1023
```

In code, you check which zone the ADC value falls in using `if/else if` conditions. For clarity and maintainability, define the zone boundaries as named constants rather than magic numbers in the comparisons.

The key discipline here is always turning all LEDs off before turning one on. Without this, a transition from RED to GREEN might briefly leave both on (for the microseconds between two consecutive `digitalWrite()` calls — invisible to humans, but correct practice).

### Wiring for Step 9

The complete circuit is now fully assembled. No new components. All components from Steps 1–8 remain:

| Pin | Component | Direction |
|---|---|---|
| 2 | Red LED (via 220Ω) | OUTPUT |
| 3 | Yellow LED (via 220Ω) | OUTPUT |
| 4 | Green LED (via 220Ω) | OUTPUT |
| 7 | Push button (INPUT_PULLUP, button → GND) | INPUT |
| 9 | Brightness LED (via 220Ω) | OUTPUT |
| A0 | Potentiometer wiper | INPUT (analog) |
| 5V | Potentiometer left terminal | — |
| GND | Potentiometer right terminal | — |

### Task 9.1 — Potentiometer Selects Which LED Lights

**Objective:** Divide the ADC range into three zones and use the zone to determine which of the three traffic light LEDs is on.

```cpp
// Task 9.1 — Potentiometer position determines which LED lights.
// The 0–1023 ADC range is divided into three equal zones.

const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;
const int POT_PIN    = A0;

// Zone boundary values. Adjust these if you want unequal zones.
const int ZONE1_MAX = 340;   // RED:    ADC 0   to 340
const int ZONE2_MAX = 681;   // YELLOW: ADC 341 to 681
                               // GREEN:  ADC 682 to 1023 (anything above ZONE2_MAX)

String lastZone = "";  // Track the last active zone to avoid printing on every loop.

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Turn potentiometer to select LED.");
    Serial.println("Zone boundaries: RED<341, YELLOW<682, GREEN≥682");
}

void turnAllOff() {
    // Always turn all LEDs off before turning a specific one on.
    // This function is called before every state change to ensure
    // no two LEDs are ever simultaneously on.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(YELLOW_PIN, LOW);
    digitalWrite(GREEN_PIN,  LOW);
}

void loop() {
    int adcValue = analogRead(POT_PIN);   // 0 to 1023

    String zone;

    if (adcValue <= ZONE1_MAX) {
        // Zone 1: Lower third of knob range — RED LED
        turnAllOff();
        digitalWrite(RED_PIN, HIGH);
        zone = "RED";
    } else if (adcValue <= ZONE2_MAX) {
        // Zone 2: Middle third of knob range — YELLOW LED
        turnAllOff();
        digitalWrite(YELLOW_PIN, HIGH);
        zone = "YELLOW";
    } else {
        // Zone 3: Upper third of knob range — GREEN LED
        // No upper bound check needed — anything above ZONE2_MAX is green.
        turnAllOff();
        digitalWrite(GREEN_PIN, HIGH);
        zone = "GREEN";
    }

    // Print only when the zone changes — prevents flooding the Serial Monitor.
    if (zone != lastZone) {
        Serial.print("ADC: ");
        Serial.print(adcValue);
        Serial.print(" → Zone: ");
        Serial.println(zone);
        lastZone = zone;
    }
}
```

**Expected Serial Monitor output (knob swept slowly from min to max):**

```
Turn potentiometer to select LED.
Zone boundaries: RED<341, YELLOW<682, GREEN≥682
ADC: 0    → Zone: RED
ADC: 341  → Zone: YELLOW
ADC: 682  → Zone: GREEN
```

### Task 9.2 — Full Combined Circuit: Traffic Light + Brightness Control + Zone Selection

**Objective:** Combine the millis()-based traffic light from Step 3, the debounced button toggle from Step 6, the LED brightness PWM from Step 8, and the zone-based LED selection into a single sketch. All systems operate simultaneously.

```cpp
// Task 9.2 — Full combined circuit.
// ALL systems run simultaneously, none blocking any other:
//   - Traffic light cycles automatically using millis()
//   - Push button (debounced) pauses/resumes the traffic light
//   - Potentiometer controls brightness of the standalone LED (pin 9)
//   - Potentiometer zone selection runs in the background and prints zone changes

const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;
const int BUTTON_PIN = 7;
const int BRIGHT_LED = 9;
const int POT_PIN    = A0;

// Traffic light timing
const unsigned long RED_TIME    = 5000;
const unsigned long GREEN_TIME  = 4000;
const unsigned long YELLOW_TIME = 1500;

// Debounce
const unsigned long DEBOUNCE_MS = 50;

// Zone boundaries
const int ZONE1_MAX = 340;
const int ZONE2_MAX = 681;

// Traffic light state machine
enum TrafficState { STATE_RED, STATE_GREEN, STATE_YELLOW };
TrafficState currentState = STATE_RED;
unsigned long phaseStartTime = 0;
bool trafficPaused = false;   // Button toggles this to pause/resume the traffic light.

// Button debounce state
int lastRawState      = HIGH;
int stableButtonState = HIGH;
unsigned long lastDebounceTime = 0;

// Zone tracking
String lastZone = "";

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    pinMode(BRIGHT_LED, OUTPUT);
    pinMode(BUTTON_PIN, INPUT_PULLUP);

    Serial.begin(9600);
    Serial.println("Full circuit running. Button pauses/resumes traffic light.");

    enterTrafficState(STATE_RED);  // Start on red.
}

void enterTrafficState(TrafficState newState) {
    currentState   = newState;
    phaseStartTime = millis();

    // Turn all traffic LEDs off before turning the correct one on.
    // The brightness LED (pin 9) is independent and NOT touched here.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);

    switch (currentState) {
        case STATE_RED:
            digitalWrite(RED_PIN, HIGH);
            Serial.println("[Traffic] PHASE: RED");
            break;
        case STATE_GREEN:
            digitalWrite(GREEN_PIN, HIGH);
            Serial.println("[Traffic] PHASE: GREEN");
            break;
        case STATE_YELLOW:
            digitalWrite(YELLOW_PIN, HIGH);
            Serial.println("[Traffic] PHASE: YELLOW");
            break;
    }
}

void handleTrafficLight() {
    // Only advance the traffic light if it is not paused by the button.
    if (trafficPaused) return;

    unsigned long now = millis();
    switch (currentState) {
        case STATE_RED:
            if (now - phaseStartTime >= RED_TIME)    enterTrafficState(STATE_GREEN);
            break;
        case STATE_GREEN:
            if (now - phaseStartTime >= GREEN_TIME)  enterTrafficState(STATE_YELLOW);
            break;
        case STATE_YELLOW:
            if (now - phaseStartTime >= YELLOW_TIME) enterTrafficState(STATE_RED);
            break;
    }
}

void handleButton() {
    int rawState = digitalRead(BUTTON_PIN);

    if (rawState != lastRawState) {
        lastDebounceTime = millis();  // State changed — restart debounce timer.
    }

    if ((millis() - lastDebounceTime) >= DEBOUNCE_MS) {
        if (rawState != stableButtonState) {
            stableButtonState = rawState;

            if (stableButtonState == LOW) {
                // Genuine press detected — toggle pause state.
                trafficPaused = !trafficPaused;
                Serial.print("[Button] Traffic light ");
                Serial.println(trafficPaused ? "PAUSED" : "RESUMED");

                // When pausing, hold red. When resuming, restart from red.
                if (trafficPaused) {
                    digitalWrite(RED_PIN,    HIGH);
                    digitalWrite(GREEN_PIN,  LOW);
                    digitalWrite(YELLOW_PIN, LOW);
                } else {
                    enterTrafficState(STATE_RED);
                }
            }
        }
    }

    lastRawState = rawState;
}

void handleBrightness() {
    int adcValue = analogRead(POT_PIN);
    int pwmValue = map(adcValue, 0, 1023, 0, 255);
    analogWrite(BRIGHT_LED, pwmValue);
}

void handleZoneSelection() {
    int adcValue = analogRead(POT_PIN);
    String zone;

    if (adcValue <= ZONE1_MAX)      zone = "RED";
    else if (adcValue <= ZONE2_MAX) zone = "YELLOW";
    else                            zone = "GREEN";

    if (zone != lastZone) {
        Serial.print("[Pot] Zone: ");
        Serial.print(zone);
        Serial.print(" (ADC: ");
        Serial.print(adcValue);
        Serial.println(")");
        lastZone = zone;
    }
}

void loop() {
    handleTrafficLight();    // Check if it is time to advance the traffic light phase.
    handleButton();          // Check for a debounced button press.
    handleBrightness();      // Update brightness LED from potentiometer.
    handleZoneSelection();   // Print zone changes from potentiometer.
    // loop() returns immediately and runs again. No delays anywhere.
    // All four systems operate concurrently.
}
```

**Expected Serial Monitor output:**

```
Full circuit running. Button pauses/resumes traffic light.
[Traffic] PHASE: RED
[Pot] Zone: RED (ADC: 0)
[Pot] Zone: YELLOW (ADC: 355)
[Pot] Zone: GREEN (ADC: 700)
[Traffic] PHASE: GREEN
[Traffic] PHASE: YELLOW
[Traffic] PHASE: RED
[Button] Traffic light PAUSED
[Button] Traffic light RESUMED
[Traffic] PHASE: RED
...
```

**Exercise Questions — Step 9**

1. In Task 9.2, four `handle___()` functions are called in sequence inside `loop()`. Does the order matter? Would calling `handleButton()` before `handleTrafficLight()` produce different behaviour?
2. If you wanted four equal zones instead of three, what boundary values would you use, and what would you connect the fourth zone to?
3. The `lastZone` variable prevents repeated Serial printing. What would happen visually if you removed it and printed on every loop pass?
4. In `handleBrightness()`, `analogRead()` is called, and in `handleZoneSelection()`, `analogRead()` is called again. Between those two calls, could the knob position have changed enough to produce different ADC values? Is this a problem for this application?
5. The button now pauses the traffic light and holds it at red. How would you modify the code so that when the button is pressed, the traffic light immediately advances to the NEXT state rather than pausing?
