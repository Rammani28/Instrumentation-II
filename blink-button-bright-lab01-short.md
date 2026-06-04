**Subject:** Instrumentation II | **Lab:** LAB 01
**Title:** Blink, Button, Bright: Learning Digital and Analog Control with Arduino UNO
**Hardware:** Arduino UNO, 3× LEDs (red, yellow, green), 3× 220Ω resistors, 1× potentiometer (10kΩ), 1× push button, 2× 10kΩ resistors, Breadboard, Jumper Wires
**Software:** Arduino IDE 1.8.x or 2.x

---

## Precautions

Never connect an LED directly between an Arduino output pin and GND without a current-limiting resistor. An Arduino digital pin can source a maximum of 40 mA. A typical LED's forward voltage is only 1.8–2.2 V, so with a 5 V supply and no resistor, the current rises to a destructive level instantly — the LED burns out and the pin may be permanently damaged. Always verify a 220Ω resistor is in series before powering on.

Never connect a voltage above 5 V to any Arduino I/O pin. The ATmega328P is a 5 V device. Even brief overvoltage can latch a pin permanently or corrupt adjacent logic. When using the potentiometer, always connect its fixed terminals to 5 V and GND as specified — never to an external supply.

Always use a data-capable USB cable, not a charge-only cable. A charge-only cable carries power but omits the D+ and D− data lines. The board will appear to power on normally but the IDE cannot upload sketches or open the Serial Monitor.

Check every breadboard connection against the wiring table before powering on. A wire off by one row is electrically invisible but will make your circuit behave in ways that seem impossible. Develop the habit of verifying each connection before pressing Upload.

---

## Objectives

You are going to build a complete multi-output control circuit from scratch — starting from a bare Arduino UNO — and by the end you will understand exactly why every wire and every line of code is where it is. You will master both fundamental modes of Arduino I/O: digital, where pins are fully on or fully off, and analog, where you read a continuously varying voltage and write a simulated one using pulse-width modulation.

Along the way you will understand how a microcontroller differs from a general-purpose processor, how to sequence multiple timed outputs without freezing the processor, how to read digital inputs reliably by eliminating floating-pin errors, how to eliminate false triggers from a mechanical switch in software, and how to translate a physical knob position into LED brightness or LED selection. When you power off at the end, all of these systems will be running simultaneously in a single sketch.

---

## Section 1 — Foundations: The Arduino UNO and How It Works

Before touching a single wire, you need a clear picture of what you are working with.

### What Is a Microcontroller?

A **microcontroller** (MCU) is a complete computer system on a single chip. Unlike a **microprocessor** (MPU) — which is only a processor core requiring external RAM, storage, and support chips — an MCU integrates the processor, Flash program memory, SRAM, EEPROM, and peripherals (ADC, timers, UART, PWM generators) all on the same piece of silicon. This makes it compact, low-power, and capable of directly interfacing with the physical world without any operating system.

The Arduino UNO uses the **ATmega328P**: an 8-bit AVR MCU running at 16 MHz, with 32 KB Flash, 2 KB SRAM, and 1 KB EEPROM. The UNO development board adds a USB-to-serial bridge, a 5 V regulator, a crystal oscillator, and pin headers that expose the MCU's I/O in a breadboard-friendly layout.

### Pin Types

**Power pins:** `5V` (regulated output from USB), `3.3V`, `GND` (multiple, all connected internally), and `VIN` (for barrel jack input).

**Digital I/O pins (0–13):** Configured as input or output via `pinMode()`. When HIGH they source 5 V; when LOW they sink to 0 V. Pins 0 (RX) and 1 (TX) are shared with USB serial — avoid using them for other purposes.

**PWM-capable pins (3, 5, 6, 9, 10, 11):** A subset of digital pins connected to hardware timer/compare units. Only these pins support `analogWrite()`. They are marked with ~ on the board.

**Analog input pins (A0–A5):** Connected to the ATmega328P's 10-bit ADC. They read 0–5 V and convert it to a number 0–1023. They can also function as digital I/O when needed.

### Sketch Structure

Every Arduino sketch must contain exactly two functions:

```cpp
void setup() {
    // Runs once at power-on or reset.
    // Configure pins, initialise Serial, set initial states.
}

void loop() {
    // Runs repeatedly, forever, after setup() completes.
    // Your main logic lives here.
}
```

There is no `main()` in a sketch — the Arduino core library provides a hidden `main()` that calls `setup()` once then calls `loop()` in an infinite loop.

### The Serial Monitor

`Serial.begin(baud)` opens the USB-serial connection. `Serial.print()` and `Serial.println()` send text to the Serial Monitor in the IDE (Tools → Serial Monitor, or Ctrl+Shift+M). The baud rate in the Serial Monitor dropdown must match the number passed to `Serial.begin()` — a mismatch produces garbled characters. Use 9600 baud throughout this lab.

💡 The Serial Monitor is your primary debugging tool. When you cannot see what a pin is reading or what a calculation produces, printing it to the monitor makes the invisible visible.

---

## Step 1 — Internal LED Blink: Verifying Your Board

Before connecting any external components, make the Arduino do something visible using only the board itself. The goal is to confirm that your board, cable, IDE installation, and driver are all working before you build anything else.

### Theory

`pinMode(pin, mode)` sets a pin's direction: `OUTPUT` to drive voltage, `INPUT` to read voltage. You must call this before using `digitalWrite()` or `digitalRead()`.

`digitalWrite(pin, value)` sets an OUTPUT pin to `HIGH` (5 V) or `LOW` (0 V).

`delay(ms)` pauses execution for the given number of milliseconds. The processor does nothing else during this wait — it cannot read pins, respond to button presses, or run any other logic. This is acceptable for simple blinking. For multi-event timing you will use `millis()` from Step 3 onward.

Pin 13 on the UNO has an LED soldered directly to the board (labelled "L" on the silkscreen), connected through a series resistor. Writing HIGH turns it on; writing LOW turns it off.

### Wiring

No external wiring required.

### Task 1.1 — Blink the Onboard LED

Upload the sketch below. The onboard LED should blink at 1-second intervals and the Serial Monitor should print its state.

```cpp
const int ONBOARD_LED = 13;  // Pin 13 has a built-in LED on every UNO board.

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);  // Must declare OUTPUT before driving it.
    Serial.begin(9600);
    Serial.println("Blink sketch started.");
}

void loop() {
    digitalWrite(ONBOARD_LED, HIGH);   // Drive pin 13 to 5V — LED turns on.
    Serial.println("LED ON");
    delay(1000);                       // Hold for 1 second. Processor does nothing else.

    digitalWrite(ONBOARD_LED, LOW);    // Drive pin 13 to 0V — LED turns off.
    Serial.println("LED OFF");
    delay(1000);
}
```

Expected Serial Monitor output:

```
Blink sketch started.
LED ON
LED OFF
LED ON
LED OFF
...
```

### Task 1.2 — Variable Blink Rate

Declare `int onTime` and `int offTime` as named variables at the top of the sketch (outside `setup()` and `loop()`). Replace the hardcoded `1000` values in both `delay()` calls with these variables. Upload and verify the timing changes when you modify the values.

**Observation Table 1 — Fill in the Visual Character column by watching the LED:**

| `onTime` (ms) | `offTime` (ms) | Visual character of the blink (describe what you see) |
|---|---|---|
| 1000 | 1000 | |
| 200 | 800 | |
| 50 | 950 | |
| 900 | 100 | |

Each row: change both values in the code, re-upload, and describe whether the LED appears to flash briefly, pulse slowly, appear almost continuously on, etc.

---

## Step 2 — External LED Blink: Your First Real Circuit

The onboard LED proved the board works. Now you wire an LED on the breadboard yourself. This is where Ohm's Law stops being a formula on paper and becomes something you calculate before touching a wire.

### Theory

An LED's current rises exponentially once its forward voltage (V_f) is exceeded — typically 1.8–2.2 V for standard red/yellow/green LEDs. Without a resistor, a 5 V pin drives destructive current through it. A series resistor limits current to a safe value.

**Resistor value calculation:**

The resistor carries the voltage that the LED does not:

```
V_R = V_supply − V_f = 5 V − 2 V = 3 V
```

For a target current of 15 mA:

```
R = V_R / I = 3 V / 0.015 A = 200 Ω
```

The nearest standard value above 200 Ω is **220 Ω**, which gives:

```
I = 3 V / 220 Ω = 13.6 mA  — safe for both the LED and the pin.
```

⚠️ Never omit the series resistor. Never substitute a lower value "just to see if it works."

💡 The longer lead of an LED is the anode (+). Current flows from anode to cathode. If your LED does not light up and your code is correct, try reversing it.

### Wiring

| From | To | Notes |
|---|---|---|
| Arduino pin 8 | 220Ω resistor leg 1 | Start of series connection |
| 220Ω resistor leg 2 | LED anode (longer leg) | Resistor before LED |
| LED cathode (shorter leg) | GND rail | |
| Arduino GND | GND rail | Complete the circuit |

### Task 2.1 — Blink the External LED

Reproduce the Task 1.1 blink behaviour using pin 8. Change `ONBOARD_LED` to `EXT_LED = 8` and update `pinMode()` accordingly.

Expected Serial Monitor output:

```
External LED blink started.
External LED: ON
External LED: OFF
External LED: ON
...
```

### Task 2.2 — Alternating Blink

Blink both the onboard LED (pin 13) and the external LED (pin 8) in alternating fashion — when one is ON the other is OFF. Declare both pins with `pinMode()` in `setup()`. In `loop()`, use two pairs of `digitalWrite()` calls separated by delays.

Expected Serial Monitor output:

```
Alternating blink started.
Onboard: ON  |  External: OFF
Onboard: OFF |  External: ON
...
```

---

## Step 3 — Traffic Light: Sequencing Multiple Outputs with millis()

You have one external LED working. Now you wire three — red, yellow, green — and sequence them as a traffic light. This step introduces the most important timing concept in Arduino programming.

### Theory

#### Why delay() Is Wrong for Multi-Event Timing

`delay()` freezes the processor for its entire duration. While it waits, your program cannot read a pin, respond to a button, or do anything else. For a traffic light that must also respond to a button press (added in later steps), this is fatal — a button press during `delay(5000)` is completely ignored.

`millis()` returns the number of milliseconds elapsed since the board was powered on, incremented by a background hardware timer regardless of what your code does. Instead of freezing and waiting, you record when a phase started and check whether enough time has passed on every pass through `loop()`.

The pattern:

```cpp
unsigned long previousMillis = 0;
unsigned long interval = 5000;

void loop() {
    unsigned long currentMillis = millis();
    if (currentMillis - previousMillis >= interval) {
        previousMillis = currentMillis;
        // time is up — change state
    }
    // loop() returns in microseconds and runs again immediately.
}
```

`loop()` runs thousands of times per second. Most passes do nothing. When the condition is finally true, you act and reset the timer. The processor is never frozen.

💡 Always use `unsigned long` for millis() variables. Using `int` causes the subtraction to produce a negative result after the 49-day rollover, breaking your timing permanently.

#### State Machine

A traffic light cycles through states: RED → GREEN → YELLOW → RED. Each state has a duration. Implement this with an `enum` and a `switch` statement. When the timer expires, call a helper function that turns all LEDs off, turns the correct one on, and resets the timer.

### Wiring

| From | To | Notes |
|---|---|---|
| Arduino pin 2 | 220Ω → Red LED anode | Red LED |
| Red LED cathode | GND rail | |
| Arduino pin 3 | 220Ω → Yellow LED anode | Yellow LED |
| Yellow LED cathode | GND rail | |
| Arduino pin 4 | 220Ω → Green LED anode | Green LED |
| Green LED cathode | GND rail | |
| Arduino GND | GND rail | Common ground |

### Task 3.1 — Traffic Light with delay() (For Comparison)

Implement the traffic light using `delay()` so you can feel the processor freeze. RED = 5 s, GREEN = 4 s, YELLOW = 1.5 s. Notice that during each phase, the Serial Monitor does not update and the board cannot respond to anything.

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;

const int RED_TIME    = 5000;
const int GREEN_TIME  = 4000;
const int YELLOW_TIME = 1500;

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Traffic light — delay version. Processor freezes each phase.");
}

void loop() {
    digitalWrite(RED_PIN, HIGH); digitalWrite(GREEN_PIN, LOW); digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: RED");
    delay(RED_TIME);

    digitalWrite(RED_PIN, LOW); digitalWrite(GREEN_PIN, HIGH); digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: GREEN");
    delay(GREEN_TIME);

    digitalWrite(RED_PIN, LOW); digitalWrite(GREEN_PIN, LOW); digitalWrite(YELLOW_PIN, HIGH);
    Serial.println("PHASE: YELLOW");
    delay(YELLOW_TIME);
}
```

### Task 3.2 — Traffic Light with millis() (The Correct Approach)

Rewrite the traffic light using `millis()`. Use an `enum` for states and a `enterState()` helper function. This is the version you keep for all future steps.

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;

const unsigned long RED_TIME    = 5000;
const unsigned long GREEN_TIME  = 4000;
const unsigned long YELLOW_TIME = 1500;

enum TrafficState { STATE_RED, STATE_GREEN, STATE_YELLOW };
TrafficState currentState = STATE_RED;

unsigned long phaseStartTime = 0;

void enterState(TrafficState newState) {
    currentState   = newState;
    phaseStartTime = millis();  // Record the exact moment this phase began.

    // Turn all off first — ensures only one LED is ever on at a time.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);

    switch (currentState) {
        case STATE_RED:    digitalWrite(RED_PIN,    HIGH); Serial.println("PHASE: RED");    break;
        case STATE_GREEN:  digitalWrite(GREEN_PIN,  HIGH); Serial.println("PHASE: GREEN");  break;
        case STATE_YELLOW: digitalWrite(YELLOW_PIN, HIGH); Serial.println("PHASE: YELLOW"); break;
    }
}

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    enterState(STATE_RED);
}

void loop() {
    unsigned long now = millis();

    switch (currentState) {
        case STATE_RED:
            if (now - phaseStartTime >= RED_TIME)    enterState(STATE_GREEN);
            break;
        case STATE_GREEN:
            if (now - phaseStartTime >= GREEN_TIME)  enterState(STATE_YELLOW);
            break;
        case STATE_YELLOW:
            if (now - phaseStartTime >= YELLOW_TIME) enterState(STATE_RED);
            break;
    }
    // loop() returns here — microseconds elapsed — processor is free.
}
```

Expected Serial Monitor output:

```
PHASE: RED
PHASE: GREEN
PHASE: YELLOW
PHASE: RED
...
```

**Observation Table 3 — Time each phase with a stopwatch and record:**

| Phase | Configured duration (ms) | Measured duration (s) | Do they match? (Yes/No) |
|---|---|---|---|
| RED | 5000 | | |
| GREEN | 4000 | | |
| YELLOW | 1500 | | |

For each row: start the stopwatch the moment the phase name appears in the Serial Monitor, stop it when the next phase name appears, and record the elapsed seconds.

---

## Step 4 — Push Button Input: The Floating Pin Problem

Your traffic light runs automatically. Now you add human input: a push button. This step introduces `digitalRead()` and the floating pin problem — one of the most common sources of mysterious behaviour in digital circuits.

### Theory

`digitalRead(pin)` reads the voltage on a pin configured as `INPUT` and returns `HIGH` or `LOW`. The ATmega328P threshold is approximately 2.5 V.

#### The Floating Pin

When a button connects a pin to 5 V on press but leaves the pin connected to nothing when released, the pin floats. A floating pin picks up interference from nearby conductors and mains frequency fields. Its reading bounces randomly between HIGH and LOW thousands of times per second with no pattern. Your code will think the button is being pressed and released continuously when nobody is touching it.

#### The Pull-Down Resistor

A pull-down resistor connects between the input pin and GND. When the button is open, it holds the pin firmly at 0 V — `digitalRead()` returns LOW. When the button is pressed (connecting the pin to 5 V), the resistor carries 5 V / 10 kΩ = 0.5 mA to GND (harmless), and the pin reads HIGH.

10 kΩ is the standard value: low enough to firmly override interference, high enough that pressing the button does not waste significant current.

### Wiring

All LEDs from Step 3 remain connected. Add the push button.

| From | To | Notes |
|---|---|---|
| Arduino 5V | Button terminal 1 | One side of button to 5V |
| Button terminal 2 | Arduino pin 7 | Other side to input pin |
| Button terminal 2 | 10kΩ → GND rail | Pull-down: same node as pin 7 |

💡 A standard 4-pin tactile button has pins connected in pairs. Pins on the same side are always shorted together. Orient the button so it bridges across the breadboard's centre gap.

### Task 4.1 — Read the Button and Print Its State

Upload the sketch below. Press and release the button while watching the Serial Monitor. Then temporarily disconnect the 10 kΩ resistor and observe how the reading behaves without it.

```cpp
const int BUTTON_PIN = 7;

void setup() {
    pinMode(BUTTON_PIN, INPUT);
    Serial.begin(9600);
    Serial.println("Press and release the button.");
}

void loop() {
    int buttonState = digitalRead(BUTTON_PIN);

    if (buttonState == HIGH) {
        Serial.println("Button: PRESSED");
    } else {
        Serial.println("Button: RELEASED");
    }

    delay(100);  // Limit print rate — not debouncing, just slowing the output.
}
```

**Observation Table 4.1 — Read and record `digitalRead()` result for each condition:**

| Condition | Expected result | Observed result |
|---|---|---|
| Button released, pull-down connected | LOW | |
| Button pressed, pull-down connected | HIGH | |
| Button released, pull-down disconnected | Undefined (floating) | |
| Button pressed, pull-down disconnected | HIGH (mostly) | |

For each row: set up the condition, observe what the Serial Monitor prints, and write the actual observed value (LOW, HIGH, or describe the erratic behaviour for the floating case).

### Task 4.2 — Button Toggles the Red LED

Toggle the red LED (pin 2) on each button press using edge detection. The LED changes state once per press — not continuously while the button is held.

```cpp
const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;
int lastButtonState = LOW;  // Used to detect the LOW→HIGH transition (the press moment).

void setup() {
    pinMode(BUTTON_PIN, INPUT);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("Press button to toggle red LED.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);

    // Act only at the moment of press — when state changes from LOW to HIGH.
    if (currentButtonState == HIGH && lastButtonState == LOW) {
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);
        Serial.print("Toggled → Red LED: ");
        Serial.println(ledState == HIGH ? "ON" : "OFF");
    }

    lastButtonState = currentButtonState;  // Save for next loop to detect transitions.
    delay(20);
}
```

💡 You may notice a single press occasionally toggles the LED more than once. This is contact bounce — the subject of Step 6.

---

## Step 5 — Pull-Up Resistors and INPUT_PULLUP

You have used a pull-down resistor to fix the floating pin. Now you will see the alternative: a pull-up resistor. Then you will discover that the ATmega328P has pull-up resistors built into the chip itself, eliminating the need for any external resistor.

### Theory

A pull-up resistor connects between the input pin and 5 V. The button connects between the input pin and GND. When the button is open, the pull-up holds the pin at 5 V — `digitalRead()` returns HIGH. When pressed, GND pulls the pin to 0 V — `digitalRead()` returns LOW.

This is the opposite logic to pull-down. With pull-down: pressed = HIGH. With pull-up: pressed = LOW. Forgetting to invert your comparison when switching between the two is a very common bug.

`pinMode(pin, INPUT_PULLUP)` enables the ATmega328P's internal pull-up resistor (approximately 20–50 kΩ) without any external component. The logic is identical to an external pull-up: HIGH when open, LOW when pressed, button connects pin to GND.

| Approach | Button connects to | Idle state | Pressed state | External resistor needed? |
|---|---|---|---|---|
| External pull-down | 5V | LOW | HIGH | Yes — 10kΩ to GND |
| External pull-up | GND | HIGH | LOW | Yes — 10kΩ to 5V |
| Internal INPUT_PULLUP | GND | HIGH | LOW | No |

### Wiring

Remove the 10 kΩ pull-down resistor. Rewire the button so that it connects pin 7 to GND when pressed.

| From | To | Notes |
|---|---|---|
| Arduino pin 7 | Button terminal 1 | Input pin to button |
| Button terminal 2 | GND rail | Button now connects pin to GND (not 5V) |

⚠️ With `INPUT_PULLUP`, the button must connect pin to GND. If you keep the button connected to 5 V, pressing it connects 5 V to a pin already pulled to 5 V — nothing happens and the button appears broken.

### Task 5.1 — Button with Internal INPUT_PULLUP

Change `pinMode(BUTTON_PIN, INPUT)` to `pinMode(BUTTON_PIN, INPUT_PULLUP)`. Update the edge detection to detect a `HIGH → LOW` transition (falling edge) rather than a rising edge. Toggle the red LED as before.

```cpp
const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;
int lastButtonState = HIGH;  // Idle state with INPUT_PULLUP is HIGH — initialise accordingly.

void setup() {
    pinMode(BUTTON_PIN, INPUT_PULLUP);  // Internal pull-up enabled. No external resistor needed.
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("INPUT_PULLUP active. LOW = pressed, HIGH = released.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);

    // With INPUT_PULLUP, press is a HIGH→LOW transition (falling edge).
    if (currentButtonState == LOW && lastButtonState == HIGH) {
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);
        Serial.print("Press detected → Red LED: ");
        Serial.println(ledState == HIGH ? "ON" : "OFF");
    }

    lastButtonState = currentButtonState;
    delay(20);
}
```

---

## Step 6 — Contact Bounce and Software Debouncing

You have noticed a single physical press occasionally triggers the LED toggle more than once. This is contact bounce — a real physical phenomenon that every system interacting with mechanical switches must handle.

### Theory

When a button's metal contacts close, they are elastic — they bounce apart and make contact again multiple times in the first 5–20 ms before settling. During those milliseconds, the pin voltage looks like a burst of pulses rather than a single clean transition. Your edge-detection code sees each bounce as a separate press event.

`delay(50)` after a press is a common but flawed fix: it blocks the processor and its duration is an arbitrary guess that may be wrong for a different button or temperature. The correct approach is a millis()-based debounce window: only accept a transition as genuine if the pin has been stable in the new state for at least 50 ms. If it bounces within the window, the timer resets. No blocking occurs — the processor checks on every loop pass and moves on immediately.

### Wiring

No changes. Button remains on pin 7 with `INPUT_PULLUP`.

### Task 6.1 — Observe Contact Bounce

Remove all delays from the loop. Press the button once and count how many events are detected per press.

```cpp
const int BUTTON_PIN = 7;
const int RED_PIN    = 2;

int ledState        = LOW;
int lastButtonState = HIGH;

void setup() {
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("No debounce — press once and count events.");
}

void loop() {
    int currentButtonState = digitalRead(BUTTON_PIN);
    if (currentButtonState == LOW && lastButtonState == HIGH) {
        ledState = (ledState == LOW) ? HIGH : LOW;
        digitalWrite(RED_PIN, ledState);
        Serial.println("Event detected!");
    }
    lastButtonState = currentButtonState;
    // No delay — loop runs as fast as possible to catch all bounce events.
}
```

**Observation Table 6.1 — Press the button once per row and count the "Event detected!" lines:**

| Press number | Number of "Event detected!" lines observed |
|---|---|
| 1 | |
| 2 | |
| 3 | |
| 4 | |
| 5 | |

Each row: press the button exactly once, count how many lines appear in the Serial Monitor before it stops, and write the count.

### Task 6.2 — Debounce with millis()

Implement the standard millis()-based debounce algorithm. Each physical press must produce exactly one event regardless of bounce.

```cpp
const int BUTTON_PIN            = 7;
const int RED_PIN               = 2;
const unsigned long DEBOUNCE_MS = 50;  // Only accept a state as genuine after 50 ms of stability.

int ledState           = LOW;
int lastRawState       = HIGH;
int stableButtonState  = HIGH;
unsigned long lastDebounceTime = 0;

void setup() {
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    pinMode(RED_PIN,    OUTPUT);
    Serial.begin(9600);
    Serial.println("Debounced. Each press = one event.");
}

void loop() {
    int rawState = digitalRead(BUTTON_PIN);

    if (rawState != lastRawState) {
        lastDebounceTime = millis();  // State changed — restart the stability timer.
    }

    if ((millis() - lastDebounceTime) >= DEBOUNCE_MS) {
        if (rawState != stableButtonState) {
            stableButtonState = rawState;

            if (stableButtonState == LOW) {  // Stable LOW = genuine press.
                ledState = (ledState == LOW) ? HIGH : LOW;
                digitalWrite(RED_PIN, ledState);
                Serial.print("Debounced press → Red LED: ");
                Serial.println(ledState == HIGH ? "ON" : "OFF");
            }
        }
    }

    lastRawState = rawState;
}
```

**Observation Table 6.2 — Compare event counts before and after debouncing:**

| Press number | Events before debounce (from Table 6.1) | Events after debounce |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |

Copy your counts from Table 6.1 into the middle column. Then run Task 6.2 and press the button the same five times, recording the new event count (should be 1 each time) in the right column.

---

## Step 7 — Potentiometer and Analog Input

Everything so far has been digital — signals either fully on or off. Now you work with an analog signal: a voltage that varies smoothly across a continuous range.

### Theory

A **potentiometer** is a resistor with a sliding wiper contact. Connect its two fixed ends to 5 V and GND, and the wiper delivers a voltage proportional to its position — 0 V at one extreme, 5 V at the other, 2.5 V at the midpoint. It is a mechanically-adjustable voltage divider.

`analogRead(pin)` reads the voltage on an analog pin and converts it to an integer using the ATmega328P's 10-bit ADC. The result spans 0 to 1023:

```
ADC_value = (V_in / V_ref) × 1023       where V_ref = 5.0 V
```

Rearranging to recover the voltage from an ADC reading:

```
V_in = (ADC_value / 1023) × V_ref
V_in = (ADC_value / 1023.0) × 5.0
```

The `1023.0` must include the decimal point. In C/C++, dividing two integers performs integer division: `500 / 1023` evaluates to `0`, not `0.488`. Writing `1023.0` forces the compiler to use floating-point division, which gives the correct fractional result.

**Example:** ADC reads 682.

```
V_in = (682 / 1023.0) × 5.0 = 0.6667 × 5.0 = 3.333 V
```

**Voltage resolution:** The smallest detectable voltage change is one ADC step:

```
Resolution = 5.0 V / 1023 ≈ 4.89 mV per step
```

`map(value, fromLow, fromHigh, toLow, toHigh)` rescales a value linearly from one range to another. To convert ADC range (0–1023) to PWM range (0–255):

```
map(adcValue, 0, 1023, 0, 255)
```

`map()` uses integer arithmetic internally — it truncates, not rounds.

### Wiring

All previous components remain. Add the potentiometer.

| From | To | Notes |
|---|---|---|
| Potentiometer left pin | Arduino 5V | Fixed end — full supply voltage |
| Potentiometer right pin | Arduino GND | Fixed end — ground reference |
| Potentiometer centre pin (wiper) | Arduino A0 | Output: varies 0V–5V with knob |

💡 Swapping the two fixed-end connections only reverses the knob direction — turning clockwise will decrease rather than increase the reading. Either orientation is electrically correct.

### Task 7.1 — Read ADC Value and Voltage

Read A0 and display both the raw ADC value and the calculated voltage.

```cpp
const int POT_PIN = A0;  // Analog pins need no pinMode() — they are inputs by default.

void setup() {
    Serial.begin(9600);
    Serial.println("Turn the knob and observe the values change.");
    Serial.println("ADC_Value | Voltage (V)");
    Serial.println("----------|------------");
}

void loop() {
    int adcValue = analogRead(POT_PIN);  // Returns 0–1023.

    // Divide by 1023.0 (not 1023) to force floating-point division.
    // Without the decimal, integer division would truncate and give wrong results.
    float voltage = (adcValue / 1023.0) * 5.0;

    Serial.print(adcValue);
    Serial.print("       | ");
    Serial.println(voltage, 3);  // 3 decimal places.

    delay(200);
}
```

**Observation Table 7 — Turn the knob to each position, wait for the reading to stabilise, and record the values:**

| Knob position | ADC reading (0–1023) | Calculated voltage displayed (V) | Expected voltage (V) |
|---|---|---|---|
| Fully counter-clockwise | | | 0.000 |
| ~25% turned clockwise | | | ~1.250 |
| ~50% turned (midpoint) | | | ~2.500 |
| ~75% turned clockwise | | | ~3.750 |
| Fully clockwise | | | 5.000 |

The "ADC reading" and "Calculated voltage displayed" columns come from what appears in the Serial Monitor. The "Expected voltage" column is provided for comparison — your measured values may differ slightly due to potentiometer tolerance.

### Task 7.2 — Map to Multiple Ranges

Display the ADC value, a percentage (0–100), and the PWM range (0–255) simultaneously.

```cpp
const int POT_PIN = A0;

void setup() {
    Serial.begin(9600);
    Serial.println("ADC | Percent | PWM");
}

void loop() {
    int adcValue = analogRead(POT_PIN);
    int percent  = map(adcValue, 0, 1023, 0, 100);
    int pwmValue = map(adcValue, 0, 1023, 0, 255);

    Serial.print(adcValue);
    Serial.print(" | ");
    Serial.print(percent);
    Serial.print("%  | ");
    Serial.println(pwmValue);

    delay(200);
}
```

---

## Step 8 — PWM and LED Brightness Control

The potentiometer gives you a number from 0 to 1023. Now you use it to produce a continuously varying output — LED brightness — using pulse-width modulation.

### Theory

A digital output pin can only be fully ON (5 V) or fully OFF (0 V). PWM simulates an analog output by switching rapidly between ON and OFF at a fixed frequency (approximately 490 Hz on most UNO PWM pins) and varying the proportion of each cycle that the signal is HIGH. This proportion is the **duty cycle**.

```
Duty cycle (%) = (time ON / total period) × 100
```

`analogWrite(pin, value)` sets the PWM duty cycle. The value maps to duty cycle as:

```
Duty cycle (%) = (value / 255) × 100
```

So `analogWrite(pin, 0)` = 0% (off), `analogWrite(pin, 127)` ≈ 50%, `analogWrite(pin, 255)` = 100% (full brightness).

The LED and your eye do not perceive the switching at 490 Hz — only the average brightness, which is proportional to duty cycle.

Only pins 3, 5, 6, 9, 10, and 11 support `analogWrite()`. Calling it on any other pin has no effect.

### Wiring

All previous components remain. Add a new LED on pin 9.

| From | To | Notes |
|---|---|---|
| Arduino pin 9 | 220Ω → LED anode | Pin 9 is PWM-capable (marked ~ on board) |
| LED cathode | GND rail | |

### Task 8.1 — Control LED Brightness with the Potentiometer

Map the potentiometer ADC output to the PWM range and drive pin 9. Turn the knob and observe smooth brightness change.

```cpp
const int POT_PIN    = A0;
const int BRIGHT_LED = 9;  // Must be a PWM-capable pin.

void setup() {
    pinMode(BRIGHT_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("ADC | PWM | Duty%");
}

void loop() {
    int adcValue  = analogRead(POT_PIN);
    int pwmValue  = map(adcValue, 0, 1023, 0, 255);  // Scale to analogWrite range.
    analogWrite(BRIGHT_LED, pwmValue);

    float dutyCycle = (pwmValue / 255.0) * 100.0;
    Serial.print(adcValue); Serial.print(" | ");
    Serial.print(pwmValue); Serial.print("  | ");
    Serial.print(dutyCycle, 1); Serial.println("%");

    delay(100);
}
```

### Task 8.2 — Inverted Brightness

Swap the output range in `map()` to `map(adcValue, 0, 1023, 255, 0)`. Verify that turning the knob clockwise now decreases brightness.

**Observation Table 8 — Turn to each position and record the values from the Serial Monitor:**

| Knob position | ADC reading | PWM value (0–255) | Duty cycle (%) | LED appearance (off/dim/half/bright/full) |
|---|---|---|---|---|
| Fully CCW | | | | |
| ~25% | | | | |
| ~50% | | | | |
| ~75% | | | | |
| Fully CW | | | | |

Calculate the duty cycle for each row using: Duty cycle (%) = (PWM_value / 255) × 100. Compare your calculated value to what the Serial Monitor prints.

---

## Step 9 — Multi-LED Zone Selection and Full Circuit

This is the final step. The potentiometer's full 0–1023 range is divided into three zones — one for each traffic LED. Turn the knob and you scroll through the three LEDs. Then everything is combined into one sketch running all systems simultaneously.

### Theory

Dividing 0–1023 into three equal zones:

```
Zone 1 (RED):    ADC   0 – 340
Zone 2 (YELLOW): ADC 341 – 681
Zone 3 (GREEN):  ADC 682 – 1023
```

Use `if / else if / else` to determine the zone. Always turn all LEDs off before turning one on — this ensures only one LED is ever active, regardless of the previous state.

### Wiring

No new components. The complete circuit is now fully assembled:

| Pin | Component | Direction |
|---|---|---|
| 2 | Red LED (220Ω) | OUTPUT |
| 3 | Yellow LED (220Ω) | OUTPUT |
| 4 | Green LED (220Ω) | OUTPUT |
| 7 | Push button (INPUT_PULLUP → GND) | INPUT |
| 9 | Brightness LED (220Ω, PWM) | OUTPUT |
| A0 | Potentiometer wiper | ANALOG IN |
| 5V | Potentiometer left terminal | — |
| GND | Potentiometer right terminal | — |

### Task 9.1 — Potentiometer Selects Which LED Lights

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;
const int POT_PIN    = A0;

const int ZONE1_MAX = 340;
const int ZONE2_MAX = 681;

String lastZone = "";

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Turn potentiometer to select LED.");
}

void turnAllOff() {
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(YELLOW_PIN, LOW);
    digitalWrite(GREEN_PIN,  LOW);
}

void loop() {
    int adcValue = analogRead(POT_PIN);
    String zone;

    if (adcValue <= ZONE1_MAX) {
        turnAllOff(); digitalWrite(RED_PIN, HIGH);    zone = "RED";
    } else if (adcValue <= ZONE2_MAX) {
        turnAllOff(); digitalWrite(YELLOW_PIN, HIGH); zone = "YELLOW";
    } else {
        turnAllOff(); digitalWrite(GREEN_PIN, HIGH);  zone = "GREEN";
    }

    // Print only on zone change — avoids flooding the Serial Monitor.
    if (zone != lastZone) {
        Serial.print("ADC: "); Serial.print(adcValue);
        Serial.print(" → Zone: "); Serial.println(zone);
        lastZone = zone;
    }
}
```

### Task 9.2 — Full Combined Circuit

Combine all systems — traffic light, button, brightness control, and zone selection — into one sketch using separate handler functions. No `delay()` anywhere. All four systems run concurrently.

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;
const int BUTTON_PIN = 7;
const int BRIGHT_LED = 9;
const int POT_PIN    = A0;

const unsigned long RED_TIME    = 5000;
const unsigned long GREEN_TIME  = 4000;
const unsigned long YELLOW_TIME = 1500;
const unsigned long DEBOUNCE_MS = 50;
const int ZONE1_MAX = 340;
const int ZONE2_MAX = 681;

enum TrafficState { STATE_RED, STATE_GREEN, STATE_YELLOW };
TrafficState currentState = STATE_RED;
unsigned long phaseStartTime = 0;
bool trafficPaused = false;

int lastRawState      = HIGH;
int stableButtonState = HIGH;
unsigned long lastDebounceTime = 0;

String lastZone = "";

void enterTrafficState(TrafficState newState) {
    currentState   = newState;
    phaseStartTime = millis();
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);
    switch (currentState) {
        case STATE_RED:    digitalWrite(RED_PIN,    HIGH); Serial.println("[Traffic] RED");    break;
        case STATE_GREEN:  digitalWrite(GREEN_PIN,  HIGH); Serial.println("[Traffic] GREEN");  break;
        case STATE_YELLOW: digitalWrite(YELLOW_PIN, HIGH); Serial.println("[Traffic] YELLOW"); break;
    }
}

void handleTrafficLight() {
    if (trafficPaused) return;  // Do nothing if paused by button.
    unsigned long now = millis();
    switch (currentState) {
        case STATE_RED:    if (now - phaseStartTime >= RED_TIME)    enterTrafficState(STATE_GREEN);  break;
        case STATE_GREEN:  if (now - phaseStartTime >= GREEN_TIME)  enterTrafficState(STATE_YELLOW); break;
        case STATE_YELLOW: if (now - phaseStartTime >= YELLOW_TIME) enterTrafficState(STATE_RED);   break;
    }
}

void handleButton() {
    int rawState = digitalRead(BUTTON_PIN);
    if (rawState != lastRawState) lastDebounceTime = millis();

    if ((millis() - lastDebounceTime) >= DEBOUNCE_MS) {
        if (rawState != stableButtonState) {
            stableButtonState = rawState;
            if (stableButtonState == LOW) {
                trafficPaused = !trafficPaused;
                Serial.print("[Button] Traffic ");
                Serial.println(trafficPaused ? "PAUSED (holding RED)" : "RESUMED");
                if (trafficPaused) {
                    digitalWrite(RED_PIN, HIGH);
                    digitalWrite(GREEN_PIN, LOW);
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
    int pwmValue = map(analogRead(POT_PIN), 0, 1023, 0, 255);
    analogWrite(BRIGHT_LED, pwmValue);
}

void handleZoneSelection() {
    int adcValue = analogRead(POT_PIN);
    String zone;
    if      (adcValue <= ZONE1_MAX) zone = "RED";
    else if (adcValue <= ZONE2_MAX) zone = "YELLOW";
    else                            zone = "GREEN";
    if (zone != lastZone) {
        Serial.print("[Pot] Zone: "); Serial.print(zone);
        Serial.print(" (ADC: "); Serial.print(adcValue); Serial.println(")");
        lastZone = zone;
    }
}

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    pinMode(BRIGHT_LED, OUTPUT);
    pinMode(BUTTON_PIN, INPUT_PULLUP);
    Serial.begin(9600);
    Serial.println("Full circuit running. Button pauses/resumes traffic light.");
    enterTrafficState(STATE_RED);
}

void loop() {
    handleTrafficLight();   // Advance traffic light if timer expired.
    handleButton();         // Check for debounced button press.
    handleBrightness();     // Update brightness LED from potentiometer.
    handleZoneSelection();  // Print zone change if potentiometer moved.
    // No delays anywhere. All four systems run on every pass through loop().
}
```

Expected Serial Monitor output:

```
Full circuit running. Button pauses/resumes traffic light.
[Traffic] RED
[Pot] Zone: RED (ADC: 12)
[Pot] Zone: YELLOW (ADC: 355)
[Pot] Zone: GREEN (ADC: 710)
[Traffic] GREEN
[Traffic] YELLOW
[Traffic] RED
[Button] Traffic PAUSED (holding RED)
[Button] Traffic RESUMED
[Traffic] RED
...
```

---

## Troubleshooting Reference

| Problem observed | Likely cause | How to fix |
|---|---|---|
| Board not detected in IDE port menu | Charge-only USB cable | Replace with a data-capable USB cable |
| Sketch uploads but LED never blinks | `pinMode()` not called, or wrong pin number in constant | Verify pin constant matches physical wiring |
| External LED never lights | LED inserted backwards (polarity reversed) | Flip the LED — longer leg (anode) toward the resistor |
| LED very dim or burns out quickly | Wrong resistor value | Recalculate: R = (5 − V_f) / I; use 220Ω |
| LED burns out immediately | No current-limiting resistor in circuit | Always use a 220Ω resistor in series |
| Serial Monitor shows garbled characters | Baud rate mismatch between sketch and monitor | Match `Serial.begin()` value to the dropdown in the Serial Monitor |
| Button reads PRESSED continuously when not touched | No pull-down resistor; pin floating | Add 10kΩ from pin to GND, or switch to `INPUT_PULLUP` |
| Button reads RELEASED when held and PRESSED when released | Pull-up/pull-down logic inverted in code | Check if you are using `INPUT_PULLUP` — if so, LOW = pressed |
| Single button press toggles LED multiple times | Contact bounce; no debouncing implemented | Use the millis()-based debounce from Task 6.2 |
| Traffic light phase never advances | `phaseStartTime` declared as `int` instead of `unsigned long`, or never assigned inside `enterState()` | Use `unsigned long` and assign `phaseStartTime = millis()` on every state entry |
| Traffic light skips the YELLOW phase | `YELLOW_TIME` too short; timer already exceeded when yellow is entered | Increase `YELLOW_TIME`; check `switch` case order |
| `analogRead()` always returns 0 | Wiper not connected to A0; only two pot terminals wired | Connect all three terminals: 5V, GND, wiper → A0 |
| `analogRead()` always returns 1023 | 5V and GND on pot swapped; wiper floating high | Check wiring; the wiper is the centre terminal |
| LED brightness does not change with potentiometer | `analogWrite()` called on a non-PWM pin | Move the LED to pin 3, 5, 6, 9, 10, or 11 |
| Brightness changes in steps, not smoothly | `map()` output used with `digitalWrite()` instead of `analogWrite()` | Use `analogWrite(BRIGHT_LED, pwmValue)` |
| `INPUT_PULLUP` button appears to do nothing | Button still wired to 5V instead of GND | Rewire the button so pressing it connects the pin to GND |

---

## Quick Reference

### Arduino Functions

| Function | Syntax | Returns | Example |
|---|---|---|---|
| `pinMode` | `pinMode(pin, mode)` | void | `pinMode(9, OUTPUT)` |
| `digitalWrite` | `digitalWrite(pin, value)` | void | `digitalWrite(9, HIGH)` |
| `digitalRead` | `digitalRead(pin)` | HIGH / LOW | `int s = digitalRead(7)` |
| `analogRead` | `analogRead(pin)` | 0–1023 | `int v = analogRead(A0)` |
| `analogWrite` | `analogWrite(pin, value)` | void | `analogWrite(9, 128)` |
| `delay` | `delay(ms)` | void | `delay(1000)` |
| `millis` | `millis()` | unsigned long | `unsigned long t = millis()` |
| `map` | `map(val, iL, iH, oL, oH)` | long | `map(512, 0, 1023, 0, 255)` |
| `Serial.begin` | `Serial.begin(baud)` | void | `Serial.begin(9600)` |
| `Serial.print` | `Serial.print(val)` | void | `Serial.print("ADC: ")` |
| `Serial.println` | `Serial.println(val)` | void | `Serial.println(adcValue)` |

### Formula Sheet

| Formula | Expression |
|---|---|
| LED resistor | R = (V_supply − V_f) / I_target |
| ADC to voltage | V_in = (ADC_value / 1023.0) × V_ref |
| ADC voltage resolution | ΔV = V_ref / 1023 ≈ 4.89 mV per step |
| PWM duty cycle | Duty (%) = (analogWrite_value / 255) × 100 |
| map() linear scale | out = outLow + (in − inLow) × (outHigh − outLow) / (inHigh − inLow) |
| Voltage divider | V_out = V_in × R2 / (R1 + R2) |

### Pin Assignment Summary

| Pin | Component | Direction | Notes |
|---|---|---|---|
| 2 | Red LED via 220Ω | OUTPUT | Traffic light |
| 3 | Yellow LED via 220Ω | OUTPUT | Traffic light |
| 4 | Green LED via 220Ω | OUTPUT | Traffic light |
| 7 | Push button | INPUT (INPUT_PULLUP) | Button → GND; no external resistor |
| 9 | Brightness LED via 220Ω | OUTPUT | PWM-capable pin |
| A0 | Potentiometer wiper | ANALOG IN | Fixed ends → 5V and GND |
| 13 | Onboard LED | OUTPUT | Built-in, used in Step 1 only |
