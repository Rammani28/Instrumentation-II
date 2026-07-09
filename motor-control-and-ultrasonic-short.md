---
Subject: Instrumentation II
Lab: LAB 02 (Short Form)
Title: Controlling a DC Motor and Measuring Distance with Arduino UNO
Level: Bachelor of Engineering
Prerequisites: LAB 01 completed — familiarity with Arduino UNO pins, ` pinMode() `, ` digitalWrite() ` , Serial Monitor
Hardware: Arduino UNO, L298N Motor Driver Module, DC Motor (6V–12V), 9V Battery + connector, HC-SR04 Ultrasonic Sensor, Breadboard, Jumper Wires(male-to-male)
Software: Arduino IDE 1.8.x or 2.x
Author: Er. Rammani Acharya

---

## Table of Contents

- [Precautions](#precautions)
- [Objectives](#objectives)
- [Part 1 — DC Motor Control with the L298N Motor Driver](#part-1--dc-motor-control-with-the-l298n-motor-driver)
  - [Task 1: Confirm the Motor Spins](#task-1-confirm-the-motor-spins)
  - [Task 2: Direction Control](#task-2-direction-control)
- [Part 2 — Distance Measurement with HC-SR04 Ultrasonic Sensor](#part-2--distance-measurement-with-hc-sr04-ultrasonic-sensor)
  - [Task 3: Measuring Distance](#task-3-measuring-distance)
- [Consolidated Observation Tables](#consolidated-observation-tables)
- [Exercise Questions](#exercise-questions)
- [Quick Reference](#quick-reference)

---

## Precautions

1. Never power the motor from the Arduino's 5V pin — the motor's stall current can overheat or damage the onboard regulator. Power the motor only from the 9V battery.
2. Always connect Arduino GND and L298N GND together (shared ground) — without it, IN1/IN2/ENA will behave unpredictably.
3. Never set IN1 and IN2 both HIGH at the same time — this causes a shoot-through short circuit that can destroy the L298N.
4. The L298N has built-in flyback diodes to absorb back-EMF; if you ever drive a motor without a driver module, add a diode across the motor yourself.
5. Check the 9V battery's polarity every time before connecting it — reversing it on VCC/GND can destroy the L298N instantly.
6. The HC-SR04 runs on 5V logic only — never connect it to a 3.3V board without a level shifter.
7. Never wire HC-SR04 Trig or Echo to D0/D1 — these are the serial RX/TX lines and will conflict with uploads and the Serial Monitor. Use D2 and D3 instead.
8. Always disconnect the 9V battery before rewiring anything — the motor circuit stays live even when the Arduino's USB cable is unplugged.

---

## Objectives

- Wire an L298N motor driver to an Arduino UNO and control a DC motor's direction.
- Wire an HC-SR04 ultrasonic sensor to an Arduino UNO and measure distance to an object.

---

## PART 1 — DC Motor Control with the L298N Motor Driver

An Arduino pin cannot drive a motor directly — a motor draws far more current than a pin can safely supply — so an L298N driver module sits between the Arduino and the motor.

A DC motor works when we supply voltage across it — this creates a magnetic field around its internal coil, and due to the force between this field and the motor's permanent magnets, the coil (and shaft) rotates.

On the L298N, **ENA** enables the motor (and can control its speed), while **IN1** and **IN2** control its direction:

| IN1 | IN2 | Motor behavior |
|---|---|---|
| HIGH | LOW | Rotates forward |
| LOW | HIGH | Rotates in reverse |
| LOW | LOW | Coasts freely (stopped) |
| HIGH | HIGH | **Never do this** — damages the driver |

### Component Wiring — L298N Logic Side

| L298N Pin | Connect To | Notes |
|---|---|---|
| ENA | Arduino D9 | Remove the ENA jumper cap first |
| IN1 | Arduino D8 | Direction control line 1 |
| IN2 | Arduino D7 | Direction control line 2 |
| GND | Arduino GND | Shared ground — required |
| ENB, IN3, IN4 | Not connected | Unused Motor B channel |

### Component Wiring — L298N Power Side

| Connection | To | Notes |
|---|---|---|
| 9V battery (+) | L298N VCC screw terminal | Motor supply — never through Arduino |
| 9V battery (–) | L298N GND screw terminal | Same ground as Arduino GND |
| L298N OUT1 | Motor terminal 1 | Defines "forward" in code |
| L298N OUT2 | Motor terminal 2 | Swap if motor spins backward |

The motor is powered entirely by the 9V battery through the L298N — the Arduino only ever sends it 5V logic signals on ENA, IN1, and IN2. The battery's GND and the Arduino's GND must be the same shared ground for those logic signals to be read correctly.

### Task 1: Confirm the Motor Spins

**Objective:** Verify the wiring by running the motor at fixed full speed, one direction, no PWM.

```cpp
// Task 1 — Confirm the motor spins (no PWM, no direction switching yet)

// 1. Begin Serial communication at 9600 baud (Serial.begin)

// 2. Set pinMode() for ENA (9), IN1 (8), IN2 (7) as OUTPUT

// 3. Use digitalWrite() to set IN1 HIGH and IN2 LOW — this picks one direction

// 4. Use digitalWrite() to set ENA HIGH — turns the motor on at full drive

// 5. Use delay() to keep it running for a few seconds (e.g. 3000 ms)

// 6. Use digitalWrite() to set ENA LOW — stops the motor
```

### Task 2: Direction Control

**Objective:** Make the motor run forward, stop, run in reverse, then stop — repeating continuously.

```cpp
// Step 1: In setup(), begin Serial and set pinMode() for ENA, IN1, IN2 as OUTPUT

// write code here



// Step 2: In loop() — set IN1 HIGH, IN2 LOW (forward), then set ENA HIGH, wait a few seconds

// write code here



// Step 3: Set ENA LOW to stop the motor, wait briefly before changing direction

// write code here



// Step 4: Set IN1 LOW, IN2 HIGH (reverse), then set ENA HIGH again, wait a few seconds

// write code here



// Step 5: Set ENA LOW to stop again before the loop repeats

// write code here
```

---

## PART 2 — Distance Measurement with HC-SR04 Ultrasonic Sensor

The HC-SR04 measures distance by sending a short ultrasonic pulse on **Trig** and timing how long it takes for the echo to return on **Echo**. That round-trip time, combined with the speed of sound, gives the distance to the nearest object in front of the sensor.

### Component Wiring — HC-SR04

| HC-SR04 Pin | Connect To | Notes |
|---|---|---|
| VCC | Arduino 5V | Never connect to 3.3V without a level shifter |
| GND | Arduino GND | Shared ground |
| Trig | Arduino D3 | Output — starts each measurement |
| Echo | Arduino D2 | Input — pulse width is the round-trip time |

⚠️ **Warning:** Never wire Trig or Echo to D0 or D1 — those are the hardware serial RX/TX lines and will conflict with uploads and the Serial Monitor. Use D2 and D3 only, as shown above.

### pulseIn() and the Distance Formula

`pulseIn(pin, HIGH, timeout)` waits for the Echo pin to go HIGH, times how long it stays HIGH, and returns that duration in microseconds — or 0 if no echo returns within the timeout. Always pass an explicit timeout (e.g. `30000UL`) so the sketch doesn't freeze for a full second, which is the default.

**Distance formula:** distance (cm) = (duration × 0.0343) ÷ 2 — divided by 2 because the timed duration covers the round trip to the object and back, not just one way.

Before calling `pulseIn()`, the sensor must be triggered: set Trig LOW briefly, then HIGH for at least 10 microseconds, then LOW again — this tells the sensor to send its ultrasonic burst.

### Task 3: Measuring Distance

**Objective:** Trigger the HC-SR04 and print the calculated distance in centimeters.

```cpp
// Step 1: In setup(), begin Serial and set pinMode() — Trig (3) as OUTPUT, Echo (2) as INPUT

// write code here



// Step 2: Send the trigger pulse — Trig LOW briefly, then HIGH for 10 microseconds (delayMicroseconds), then LOW again

// write code here



// Step 3: Use pulseIn() on the Echo pin (HIGH, with an explicit timeout like 30000UL) and store the result

// write code here



// Step 4: If the result is 0, print "No echo detected" — otherwise calculate distance using the formula and print it

// write code here
```

---

## Consolidated Observation Tables

### Task 1 — Confirm the Motor Spins

| Trial | IN1 | IN2 | ENA | Observed motor behavior |
|---|---|---|---|---|
| 1 | HIGH | LOW | HIGH | |
| 2 | HIGH | LOW | LOW | |

### Task 2 — Direction Control

| Direction | IN1 | IN2 | Observed motor behavior |
|---|---|---|---|
| Forward | HIGH | LOW | |
| Reverse | LOW | HIGH | |

### Task 3 — Measuring Distance

| Object distance (actual, ruler) | Pulse duration (µs) | Calculated distance (cm) | Error (cm) |
|---|---|---|---|
| 10 cm | | | |
| 50 cm | | | |
| 100 cm | | | |
| No object in range | | | Should read "No echo detected" |

**Formula:** distance (cm) = (duration × 0.0343) ÷ 2, Error (cm) = calculated distance − actual distance

---

## Exercise Questions

1. Why does this task use `digitalWrite(PIN_ENA, HIGH)` instead of PWM, given both should produce full speed?
2. Why is the raw pulse duration divided by 2 before it's converted into a distance?
3. What does a `pulseIn()` return value of exactly 0 mean, and why shouldn't it be treated as "0 cm away"?
4. Why must the HC-SR04's Trig and Echo pins avoid D0 and D1?
5. If the ambient temperature changed significantly, how would that affect a distance reading calculated with a fixed 0.0343 cm/µs constant?

---

## Quick Reference

### Arduino Functions Used in This Lab

| Function | Syntax | Purpose |
|---|---|---|
| `pinMode()` | `pinMode(pin, mode)` | Sets a pin as INPUT or OUTPUT |
| `digitalWrite()` | `digitalWrite(pin, value)` | Sets a pin HIGH or LOW |
| `delay()` | `delay(ms)` | Pauses for a number of milliseconds |
| `delayMicroseconds()` | `delayMicroseconds(us)` | Pauses for a number of microseconds |
| `pulseIn()` | `pulseIn(pin, value, timeout)` | Measures pulse width in microseconds (0 on timeout) |
| `Serial.begin()` | `Serial.begin(rate)` | Starts Serial communication |
| `Serial.print()` / `Serial.println()` | `Serial.print(value)` | Prints to the Serial Monitor |

### Formula Sheet

**Distance from Pulse Duration**
distance (cm) = (duration × 0.0343) ÷ 2

**Speed of Sound (20°C reference)**
343 m/s = 0.0343 cm/µs

### Pin Assignment Summary

| Signal | Arduino Pin | Direction |
|---|---|---|
| ENA | D9 | Output |
| IN1 | D8 | Output |
| IN2 | D7 | Output |
| Trig | D3 | Output |
| Echo | D2 | Input |

### L298N Logic Truth Table

| IN1 | IN2 | Motor Effect |
|---|---|---|
| HIGH | LOW | Rotates forward |
| LOW | HIGH | Rotates in reverse |
| LOW | LOW | Coasts freely |
| HIGH | HIGH | **Forbidden — shoot-through risk** |
