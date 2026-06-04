
**Subject:** Instrumentation II | **Lab:** LAB 01
**Title:** Blink, Button, Bright: Learning Digital and Analog Control with Arduino UNO
**Level:** Bachelor of Engineering
**Hardware:** Arduino UNO, 3× LEDs (red, yellow, green), 3× 220Ω resistors, 1× potentiometer (10kΩ), Breadboard, Jumper Wires
**Software:** Arduino IDE 1.8.x or 2.x

---

## Precautions

Never connect an LED directly between an Arduino output pin and GND without a current-limiting resistor. An Arduino digital pin can source a maximum of 40 mA. A typical LED's forward voltage is only 1.8–2.2 V, so with a 5 V supply and no resistor, the current rises to a destructive level instantly — the LED burns out and the pin may be permanently damaged. Always verify a 220Ω resistor is in series before powering on.

Always use a data-capable USB cable, not a charge-only cable. A charge-only cable carries power but omits the D+ and D− data lines. The board will appear to power on normally but the IDE cannot upload sketches or open the Serial Monitor.

Check every breadboard connection against the wiring table before powering on. A wire off by one row is electrically invisible but will make your circuit behave in ways that seem impossible. Develop the habit of verifying each connection before pressing Upload.

Never connect a voltage above 5 V to any Arduino I/O pin. When using the potentiometer, always connect its fixed terminals to 5 V and GND as specified — never to an external supply above 5 V.

---

## Objectives

You are going to build a progressively more capable circuit — starting with a single blinking LED and ending with a potentiometer that selects which of three LEDs lights up. Each task builds directly on the previous one. By the end, you will understand how the Arduino reads a continuously varying analog signal, how it produces a simulated analog output using PWM, and how digital and analog I/O work together in a single circuit.

---

## Section 1 — Foundations: The Arduino UNO and How It Works

Before touching a single wire, you need a clear picture of what you are working with.

### What Is a Microcontroller?

A **microcontroller** (MCU) is a complete computer system on a single chip. Unlike a **microprocessor** (MPU) — which is only a processor core and requires external RAM, storage, and support chips to function — an MCU integrates the processor, Flash program memory, SRAM, EEPROM, and peripherals (ADC, timers, UART, PWM generators) all on the same piece of silicon. This makes it compact, low-power, and capable of directly interfacing with the physical world without an operating system.

The Arduino UNO uses the **ATmega328P**: an 8-bit AVR MCU running at 16 MHz, with 32 KB Flash for your program, 2 KB SRAM for runtime variables, and 1 KB EEPROM for data that must survive a power cycle. The UNO development board adds a USB-to-serial bridge, a 5 V regulator, a crystal oscillator, and pin headers that expose the MCU's I/O in a breadboard-friendly layout.

### Pin Types

**Power pins:** `5V` (regulated output from USB), `GND` (multiple, all connected internally), and `VIN` (for barrel jack input).

**Digital I/O pins (0–13):** Configured as input or output via `pinMode()`. When driven HIGH they source 5 V; when LOW they sink to 0 V. Pins 0 (RX) and 1 (TX) are shared with USB serial — avoid using them for other purposes while the Serial Monitor is open.

**PWM-capable pins (3, 5, 6, 9, 10, 11):** A subset of digital pins connected to hardware timer/compare units. Only these pins support `analogWrite()`. They are marked with ~ on the board silkscreen.

**Analog input pins (A0–A5):** Connected to the ATmega328P's 10-bit ADC. They read 0–5 V and convert it to a number 0–1023. They require no `pinMode()` call — they are inputs by default.

### Sketch Structure

Every Arduino sketch must contain exactly two functions:

```cpp
void setup() {
    // Runs once at power-on or reset.
    // Configure pins, initialise Serial, set initial states here.
}

void loop() {
    // Runs repeatedly, forever, after setup() completes.
    // Your main logic lives here.
}
```

The Arduino core library provides a hidden `main()` that calls `setup()` once, then calls `loop()` in an infinite loop. Both functions must be present — the sketch will not compile without them.

### The Serial Monitor

`Serial.begin(baud)` opens the USB-serial connection at the specified baud rate. `Serial.print()` and `Serial.println()` send text to the Serial Monitor in the IDE (Tools → Serial Monitor, or Ctrl+Shift+M). The baud rate selected in the Serial Monitor dropdown must match the number passed to `Serial.begin()` — a mismatch produces garbled characters. Use 9600 baud throughout this lab.

💡 The Serial Monitor is your primary debugging tool. When you cannot see what a pin is doing or what a calculation produces, printing it to the monitor makes the invisible visible.

---

## Step 1 — Internal LED Blink: Verifying Your Board

Before connecting any external components, make the Arduino do something visible using only the board itself. The goal is to confirm that your board, USB cable, IDE installation, and driver are all working correctly before you build anything else.

### Theory

`pinMode(pin, mode)` sets a pin's direction. Pass `OUTPUT` to drive voltage onto the circuit, or `INPUT` to read voltage from it. You must call `pinMode()` before using `digitalWrite()` or `digitalRead()` on that pin.

`digitalWrite(pin, value)` sets an OUTPUT pin to `HIGH` (5 V) or `LOW` (0 V).

`delay(ms)` pauses execution for the given number of milliseconds. During this pause the processor does nothing else — it cannot read pins or respond to any external event. For simple single-LED blinking this is perfectly acceptable.

Pin 13 on the UNO has an LED soldered directly to the board, connected through a series resistor to the pin. It is labelled "L" on the board silkscreen. Writing HIGH turns it on; writing LOW turns it off. This LED exists specifically for testing the board without any external components.

### Wiring

No external wiring required.

### Task 1.1 — Blink the Onboard LED

Upload the sketch below. The onboard LED should blink at 1-second intervals and the Serial Monitor should print its state on every change.

```cpp
const int ONBOARD_LED = 13;  // Pin 13 has a built-in LED on every UNO board.
                              // Using a named constant avoids magic numbers in code.

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);  // Declare as OUTPUT before driving it.
                                   // Without this, digitalWrite() has no effect.
    Serial.begin(9600);
    Serial.println("Blink sketch started.");
}

void loop() {
    digitalWrite(ONBOARD_LED, HIGH);   // Drive pin 13 to 5V — LED turns on.
    Serial.println("LED ON");
    delay(1000);                       // Hold for 1 second.

    digitalWrite(ONBOARD_LED, LOW);    // Drive pin 13 to 0V — LED turns off.
    Serial.println("LED OFF");
    delay(1000);                       // Hold for 1 second.
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

Each pair of lines appears every 2 seconds. The onboard LED changes state in sync with each line.

### Task 1.2 — Variable Blink Rate

Declare `int onTime` and `int offTime` as named variables at the top of the sketch, outside `setup()` and `loop()`. Replace the hardcoded `1000` values in both `delay()` calls with these variables. Try the four combinations in the observation table below.

```cpp
const int ONBOARD_LED = 13;

int onTime  = 200;   // LED on duration in milliseconds — change this to experiment.
int offTime = 800;   // LED off duration in milliseconds — change this to experiment.

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);
    Serial.begin(9600);
    Serial.print("ON time: ");  Serial.print(onTime);  Serial.println(" ms");
    Serial.print("OFF time: "); Serial.print(offTime); Serial.println(" ms");
}

void loop() {
    digitalWrite(ONBOARD_LED, HIGH);
    delay(onTime);   // Use the variable — changing it here changes every place it is used.

    digitalWrite(ONBOARD_LED, LOW);
    delay(offTime);
}
```

Expected Serial Monitor output (for `onTime = 200`, `offTime = 800`):

```
ON time: 200 ms
OFF time: 800 ms
```

**Observation Table 1.2 — For each row, change `onTime` and `offTime` in the code, re-upload, and describe what you see:**

| `onTime` (ms) | `offTime` (ms) | Describe the visual character of the blink |
|---|---|---|
| 1000 | 1000 | |
| 200 | 800 | |
| 50 | 950 | |
| 900 | 100 | |

Write in your own words what the LED looks like for each combination — whether it appears to flash briefly, pulse slowly, seem almost continuously on, etc.

---

## Step 2 — External LED Blink: Your First Real Circuit

The onboard LED proved the board works. Now you wire an LED on the breadboard yourself. This is where Ohm's Law stops being a formula on paper and becomes something you calculate before touching a wire.

### Theory

An LED's current rises exponentially once its forward voltage (V_f) is exceeded — typically 1.8–2.2 V for standard red, yellow, and green LEDs. Without a series resistor, a 5 V pin drives destructive current through it. A series resistor limits current to a safe value by obeying Ohm's Law.

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

⚠️ Never omit the series resistor. Never substitute a lower value to see if it works.

💡 The longer lead of an LED is the anode (+). Current flows from anode to cathode. If your LED does not light up and your code is correct, try reversing it.

### Wiring

| From | To | Notes |
|---|---|---|
| Arduino pin 8 | 220Ω resistor leg 1 | Start of the series connection |
| 220Ω resistor leg 2 | LED anode (longer leg) | Resistor sits before the LED |
| LED cathode (shorter leg) | GND rail | |
| Arduino GND | GND rail | Completes the circuit back to the board |

### Task 2.1 — Blink the External LED

Blink the external LED on pin 8 at 500 ms intervals. The onboard LED on pin 13 is no longer needed — you can leave it connected or remove it.

```cpp
const int EXT_LED = 8;   // External LED on pin 8 through a 220Ω resistor.
                          // Pin 8 is a plain digital output — no PWM capability.

void setup() {
    pinMode(EXT_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("External LED blink started.");
}

void loop() {
    digitalWrite(EXT_LED, HIGH);   // 5V on pin 8 — current flows through
                                   // the 220Ω resistor, through the LED, to GND.
    Serial.println("External LED: ON");
    delay(500);

    digitalWrite(EXT_LED, LOW);    // Pin goes to 0V — no current — LED off.
    Serial.println("External LED: OFF");
    delay(500);
}
```

Expected Serial Monitor output:

```
External LED blink started.
External LED: ON
External LED: OFF
External LED: ON
External LED: OFF
...
```

### Task 2.2 — Alternating Blink

Blink both the onboard LED (pin 13) and the external LED (pin 8) in alternating fashion — when one is ON the other is OFF. Both pins need their own `pinMode()` call in `setup()`.

```cpp
const int ONBOARD_LED = 13;
const int EXT_LED     = 8;

void setup() {
    pinMode(ONBOARD_LED, OUTPUT);
    pinMode(EXT_LED,     OUTPUT);
    Serial.begin(9600);
    Serial.println("Alternating blink started.");
}

void loop() {
    // Both digitalWrite calls execute in microseconds — effectively simultaneous.
    digitalWrite(ONBOARD_LED, HIGH);
    digitalWrite(EXT_LED,     LOW);
    Serial.println("Onboard: ON  |  External: OFF");
    delay(400);

    digitalWrite(ONBOARD_LED, LOW);
    digitalWrite(EXT_LED,     HIGH);
    Serial.println("Onboard: OFF |  External: ON");
    delay(400);
}
```

Expected Serial Monitor output:

```
Alternating blink started.
Onboard: ON  |  External: OFF
Onboard: OFF |  External: ON
Onboard: ON  |  External: OFF
...
```

---

## Step 3 — Traffic Light: Sequencing Multiple LEDs with delay()

You can now control two LEDs independently. The next step is to control three in a fixed sequence — a traffic light. This puts together everything from Steps 1 and 2: multiple output pins, `pinMode()`, `digitalWrite()`, and `delay()` — applied to a real-world pattern.

### Theory

`delay()` pauses the entire program for a set duration. While it waits, nothing else happens — no pins are read, no other logic runs. For a fixed, automatic sequence like a traffic light where no other input is involved, this is a straightforward and effective approach. Each phase simply turns the correct LED on, holds it for the required duration, then turns it off and moves to the next.

### Wiring

The external LED from Step 2 (pin 8) remains on the board but is not used in this step. Add red, yellow, and green LEDs on pins 2, 3, and 4, each with a 220Ω series resistor.

| From | To | Notes |
|---|---|---|
| Arduino pin 2 | 220Ω → Red LED anode | Red LED |
| Red LED cathode | GND rail | |
| Arduino pin 3 | 220Ω → Yellow LED anode | Yellow LED |
| Yellow LED cathode | GND rail | |
| Arduino pin 4 | 220Ω → Green LED anode | Green LED |
| Green LED cathode | GND rail | |
| Arduino GND | GND rail | Common ground for all LEDs |

### Task 3.1 — Automatic Traffic Light

Upload the sketch below. The LEDs should cycle RED → GREEN → YELLOW → RED continuously, each held for the duration specified.

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;

// Phase durations in milliseconds — change these to adjust the timing.
const int RED_TIME    = 5000;   // Red: 5 seconds
const int GREEN_TIME  = 4000;   // Green: 4 seconds
const int YELLOW_TIME = 1500;   // Yellow: 1.5 seconds

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Traffic light started.");
}

void loop() {
    // RED phase — stop.
    digitalWrite(RED_PIN,    HIGH);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: RED");
    delay(RED_TIME);

    // GREEN phase — go.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  HIGH);
    digitalWrite(YELLOW_PIN, LOW);
    Serial.println("PHASE: GREEN");
    delay(GREEN_TIME);

    // YELLOW phase — prepare to stop.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(GREEN_PIN,  LOW);
    digitalWrite(YELLOW_PIN, HIGH);
    Serial.println("PHASE: YELLOW");
    delay(YELLOW_TIME);
}
```

Expected Serial Monitor output:

```
Traffic light started.
PHASE: RED
PHASE: GREEN
PHASE: YELLOW
PHASE: RED
PHASE: GREEN
...
```

💡 Notice that the Serial Monitor does not update during each phase — it only prints when `delay()` finishes and the next line of code runs. This is the processor freeze in action. For this simple sequence it causes no problem, but it would become an issue if you needed the board to respond to anything else during a phase.

---

## Step 4 — Potentiometer and Analog Input

Everything so far has been digital — signals either fully on or fully off. Now you move to analog input: a voltage that varies smoothly across a continuous range. The component is a potentiometer, and the hardware that reads it is the ATmega328P's Analog-to-Digital Converter.

### Theory

A **potentiometer** is a resistor with a sliding wiper contact. Connect its two fixed ends to 5 V and GND, and the wiper delivers a voltage proportional to its rotational position — 0 V at one extreme, 5 V at the other, 2.5 V at the midpoint. It is a mechanically-adjustable voltage divider.

`analogRead(pin)` reads the voltage on an analog input pin and converts it to a 10-bit integer using the ATmega328P's ADC. The result spans 0 to 1023:

```
ADC_value = (V_in / V_ref) × 1023       where V_ref = 5.0 V by default
```

Rearranging to recover the actual voltage from a raw ADC reading:

```
V_in = (ADC_value / 1023.0) × V_ref
V_in = (ADC_value / 1023.0) × 5.0
```

The `1023.0` must include the decimal point. In C/C++, dividing two integers performs integer division and discards the remainder: `682 / 1023` evaluates to `0`, not `0.667`. Writing `1023.0` forces the compiler to treat the division as floating-point, producing the correct fractional result.

**Worked example:** ADC reads 682.

```
V_in = (682 / 1023.0) × 5.0 = 0.6667 × 5.0 = 3.333 V
```

**Voltage resolution:** The smallest voltage change the ADC can distinguish is one step:

```
Resolution = 5.0 V / 1023 ≈ 4.89 mV per step
```

Two voltages that differ by less than ~5 mV will produce the same ADC reading.

`map(value, fromLow, fromHigh, toLow, toHigh)` rescales a value linearly from one range to another without floating-point arithmetic:

```
map(adcValue, 0, 1023, 0, 255)   // scales ADC range to PWM range
map(adcValue, 0, 1023, 0, 100)   // scales ADC range to percentage
```

`map()` uses integer arithmetic internally — it truncates fractional results rather than rounding.

### Wiring

All LEDs from Step 3 remain connected. Add the potentiometer.

| From | To | Notes |
|---|---|---|
| Potentiometer left pin | Arduino 5V | Fixed end — full supply voltage |
| Potentiometer right pin | Arduino GND | Fixed end — ground reference |
| Potentiometer centre pin (wiper) | Arduino A0 | Output voltage: varies 0V–5V with knob position |

💡 Swapping the two fixed-end connections only reverses the knob direction — turning clockwise will decrease rather than increase the reading. Either orientation is electrically correct.

### Task 4.1 — Read ADC Value and Voltage

Read the potentiometer position as a raw ADC value and as a calculated voltage. Display both on the Serial Monitor as you turn the knob.

```cpp
const int POT_PIN = A0;  // Analog pins need no pinMode() — they are inputs by default.

void setup() {
    Serial.begin(9600);
    Serial.println("Turn the knob and observe the values change.");
    Serial.println("ADC_Value | Voltage (V)");
    Serial.println("----------|------------");
}

void loop() {
    // Read A0 — returns an integer from 0 (0V) to 1023 (5V).
    int adcValue = analogRead(POT_PIN);

    // Divide by 1023.0 — the decimal forces floating-point division.
    // Using 1023 (integer) would give 0 for any value below 1023 due to truncation.
    float voltage = (adcValue / 1023.0) * 5.0;

    Serial.print(adcValue);
    Serial.print("       | ");
    Serial.println(voltage, 3);  // 3 decimal places.

    delay(200);  // Update 5 times per second — enough to see changes as you turn the knob.
}
```

Expected Serial Monitor output (knob swept from minimum to maximum):

```
Turn the knob and observe the values change.
ADC_Value | Voltage (V)
----------|------------
0         | 0.000
128       | 0.625
255       | 1.246
511       | 2.496
768       | 3.753
1023      | 5.000
```

**Observation Table 4.1 — Turn the knob to each position, wait for the reading to stabilise, and record what the Serial Monitor displays:**

| Knob position | ADC reading (read from Serial Monitor) | Voltage displayed (V) | Expected voltage (V) |
|---|---|---|---|
| Fully counter-clockwise | | | 0.000 |
| ~25% turned clockwise | | | ~1.250 |
| ~50% turned (midpoint) | | | ~2.500 |
| ~75% turned clockwise | | | ~3.750 |
| Fully clockwise | | | 5.000 |

The "ADC reading" and "Voltage displayed" columns come directly from the Serial Monitor output. The "Expected voltage" column is provided for reference — your measured values may differ slightly due to potentiometer tolerance and ADC noise.

### Task 4.2 — Map to Multiple Ranges

Use `map()` to display the ADC value, a human-readable percentage (0–100), and the PWM range (0–255) simultaneously. You will use the PWM range directly in the next step.

```cpp
const int POT_PIN = A0;

void setup() {
    Serial.begin(9600);
    Serial.println("ADC  | Percent | PWM");
    Serial.println("-----|---------|----");
}

void loop() {
    int adcValue = analogRead(POT_PIN);

    // Map from [0,1023] to [0,100] — human-readable percentage of knob position.
    int percent  = map(adcValue, 0, 1023, 0, 100);

    // Map from [0,1023] to [0,255] — required range for analogWrite() in the next step.
    // analogWrite() only accepts 0–255; values outside this range cause undefined behaviour.
    int pwmValue = map(adcValue, 0, 1023, 0, 255);

    Serial.print(adcValue);
    Serial.print("  | ");
    Serial.print(percent);
    Serial.print("%      | ");
    Serial.println(pwmValue);

    delay(200);
}
```

Expected Serial Monitor output:

```
ADC  | Percent | PWM
-----|---------|----
0    | 0%      | 0
256  | 25%     | 63
511  | 49%     | 127
768  | 75%     | 191
1023 | 100%    | 255
```

---

## Step 5 — PWM and LED Brightness Control

The potentiometer gives you a number from 0 to 1023. Now you use it to produce a continuously varying output — LED brightness — using pulse-width modulation.

### Theory

A digital output pin can only be fully ON (5 V) or fully OFF (0 V). It cannot output 2.5 V or any intermediate value. PWM simulates an analog output by switching rapidly between ON and OFF at a fixed frequency (approximately 490 Hz on most UNO PWM pins) and varying the proportion of each cycle that the signal is HIGH. This proportion is the **duty cycle**:

```
Duty cycle (%) = (time ON / total period) × 100
```

`analogWrite(pin, value)` sets the PWM duty cycle. The value maps to duty cycle as:

```
Duty cycle (%) = (value / 255) × 100
```

So `analogWrite(pin, 0)` is 0% (fully off), `analogWrite(pin, 127)` is approximately 50%, and `analogWrite(pin, 255)` is 100% (full brightness). The LED and your eye do not perceive the switching at 490 Hz — only the average brightness, which is proportional to the duty cycle.

Only pins 3, 5, 6, 9, 10, and 11 support `analogWrite()` on the UNO. Calling it on any other pin has no useful effect.

### Wiring

All previous LEDs remain connected. Add a new LED specifically for brightness control on pin 9, which is PWM-capable.

| From | To | Notes |
|---|---|---|
| Arduino pin 9 | 220Ω → LED anode | Pin 9 is PWM-capable (marked ~ on board) |
| LED cathode | GND rail | |

### Task 5.1 — Control LED Brightness with the Potentiometer

Map the ADC reading directly to the PWM range and drive pin 9. Turn the knob and observe smooth, continuous brightness change.

```cpp
const int POT_PIN    = A0;
const int BRIGHT_LED = 9;   // Must be a PWM-capable pin — pin 9 uses Timer 1.

void setup() {
    pinMode(BRIGHT_LED, OUTPUT);
    Serial.begin(9600);
    Serial.println("Turn knob to control LED brightness.");
    Serial.println("ADC  | PWM | Duty%");
}

void loop() {
    int adcValue = analogRead(POT_PIN);   // Read knob: 0–1023.

    // Scale to PWM range. analogWrite() only accepts 0–255.
    int pwmValue = map(adcValue, 0, 1023, 0, 255);

    analogWrite(BRIGHT_LED, pwmValue);    // Set PWM duty cycle — brightness changes instantly.

    float dutyCycle = (pwmValue / 255.0) * 100.0;

    Serial.print(adcValue); Serial.print("  | ");
    Serial.print(pwmValue); Serial.print("  | ");
    Serial.print(dutyCycle, 1); Serial.println("%");

    delay(100);
}
```

Expected Serial Monitor output (knob swept from minimum to maximum):

```
Turn knob to control LED brightness.
ADC  | PWM | Duty%
0    | 0   | 0.0%
255  | 63  | 24.7%
511  | 127 | 49.8%
768  | 191 | 74.9%
1023 | 255 | 100.0%
```

**Observation Table 5.1 — Turn the knob to each position and record the values from the Serial Monitor:**

| Knob position | ADC reading | PWM value (0–255) | Duty cycle (%) | LED appearance |
|---|---|---|---|---|
| Fully CCW | | | | |
| ~25% | | | | |
| ~50% | | | | |
| ~75% | | | | |
| Fully CW | | | | |

The first four columns come from the Serial Monitor. For the "LED appearance" column, describe in your own words what you see — off, barely visible, dim, half brightness, bright, full brightness, etc.

### Task 5.2 — Inverted Brightness

Swap the output range in `map()` so that turning the knob clockwise decreases brightness instead of increasing it. Only one value in the sketch changes — the `map()` call becomes:

```cpp
int pwmValue = map(adcValue, 0, 1023, 255, 0);  // Output range reversed.
```

Replace the `map()` line in Task 5.1's sketch with this version and re-upload. Verify that the LED is at full brightness when the knob is fully counter-clockwise and off when fully clockwise.

Expected Serial Monitor output:

```
ADC: 0    → PWM: 255   (knob at min → LED fully on)
ADC: 511  → PWM: 127   (mid knob → half brightness)
ADC: 1023 → PWM: 0     (knob at max → LED off)
```

---

## Step 6 — Multi-LED Zone Selection: Potentiometer Selects the Active LED

The final step divides the potentiometer's full 0–1023 range into three zones and uses the zone to determine which of the three traffic light LEDs is on. This combines `analogRead()`, range comparison, and `digitalWrite()` — everything you have practiced — into one complete piece of logic.

### Theory

Dividing 0–1023 into three equal zones:

```
Zone 1 (RED):    ADC   0 – 340
Zone 2 (YELLOW): ADC 341 – 681
Zone 3 (GREEN):  ADC 682 – 1023
```

Use `if / else if / else` to determine which zone the current ADC value falls in. Before turning any LED on, always turn all three off first. This ensures only one LED is ever active at a time, regardless of which zone was active previously.

### Wiring

No new components. The complete circuit is now fully assembled:

| Pin | Component | Direction |
|---|---|---|
| 2 | Red LED (220Ω) | OUTPUT |
| 3 | Yellow LED (220Ω) | OUTPUT |
| 4 | Green LED (220Ω) | OUTPUT |
| 8 | External LED from Step 2 (220Ω) | OUTPUT (unused in this step) |
| 9 | Brightness LED (220Ω, PWM) | OUTPUT |
| A0 | Potentiometer wiper | ANALOG IN |
| 5V | Potentiometer left terminal | — |
| GND | Potentiometer right terminal | — |

### Task 6.1 — Potentiometer Selects Which LED Lights

```cpp
const int RED_PIN    = 2;
const int YELLOW_PIN = 3;
const int GREEN_PIN  = 4;
const int POT_PIN    = A0;

const int ZONE1_MAX = 340;   // RED zone:    ADC 0   to 340
const int ZONE2_MAX = 681;   // YELLOW zone: ADC 341 to 681
                               // GREEN zone:  ADC 682 to 1023

String lastZone = "";  // Track the last zone to avoid printing on every loop pass.

void setup() {
    pinMode(RED_PIN,    OUTPUT);
    pinMode(YELLOW_PIN, OUTPUT);
    pinMode(GREEN_PIN,  OUTPUT);
    Serial.begin(9600);
    Serial.println("Turn potentiometer to select LED.");
}

void turnAllOff() {
    // Always call this before turning a specific LED on.
    // Ensures no two LEDs are ever simultaneously active.
    digitalWrite(RED_PIN,    LOW);
    digitalWrite(YELLOW_PIN, LOW);
    digitalWrite(GREEN_PIN,  LOW);
}

void loop() {
    int adcValue = analogRead(POT_PIN);
    String zone;

    if (adcValue <= ZONE1_MAX) {
        turnAllOff();
        digitalWrite(RED_PIN, HIGH);
        zone = "RED";
    } else if (adcValue <= ZONE2_MAX) {
        turnAllOff();
        digitalWrite(YELLOW_PIN, HIGH);
        zone = "YELLOW";
    } else {
        turnAllOff();
        digitalWrite(GREEN_PIN, HIGH);
        zone = "GREEN";
    }

    // Only print when the zone changes — prevents flooding the Serial Monitor.
    if (zone != lastZone) {
        Serial.print("ADC: ");
        Serial.print(adcValue);
        Serial.print(" → Zone: ");
        Serial.println(zone);
        lastZone = zone;
    }
}
```

Expected Serial Monitor output (knob swept slowly from minimum to maximum):

```
Turn potentiometer to select LED.
ADC: 0    → Zone: RED
ADC: 341  → Zone: YELLOW
ADC: 682  → Zone: GREEN
```

**Observation Table 6.1 — Slowly turn the knob across its full range and record the ADC value at which each zone transition occurs:**

| Transition | ADC value where transition was observed | LED that turned on |
|---|---|---|
| RED → YELLOW | | |
| YELLOW → GREEN | | |
| GREEN → YELLOW (turning back) | | |
| YELLOW → RED (turning back) | | |

Turn the knob slowly in one direction until the LED changes, then read the ADC value from the Serial Monitor and write it down. Repeat turning back in the other direction. The transition values should be close to 341 and 682 — small differences are normal due to potentiometer tolerance and ADC noise.
