---
Subject: Instrumentation II
Lab: LAB 02
Title: Controlling a DC Motor and Measuring Distance with Arduino UNO
Level: Bachelor of Engineering
Prerequisites: LAB 01 completed — familiarity with Arduino UNO pins, PWM, analogWrite(), Serial Monitor, pinMode(), digitalWrite(), map(), millis()
Hardware: Arduino UNO, L298N Motor Driver Module, DC Motor (6V–12V), 9V Battery + connector, HC-SR04 Ultrasonic Sensor, Breadboard, Jumper Wires
Software: Arduino IDE 1.8.x or 2.x
Author: Er. Rammani Acharya

---

## 0. Table of Contents

- [1. Precautions](#1-precautions)
- [2. Objectives](#2-objectives)
- [3. Global Overview](#3-global-overview)

---

- [PART 1 — DC Motor Control with the L298N Motor Driver](#part-1--dc-motor-control-with-the-l298n-motor-driver)
  - [Step 1: Why Arduino Pins Cannot Drive a DC Motor Directly](#step-1-why-arduino-pins-cannot-drive-a-dc-motor-directly) - *why a dedicated driver sits between the Arduino and the motor*
    - [Background — Step 1](#background--step-1)

---

  - [Step 2: The DC Motor — How It Works](#step-2-the-dc-motor--how-it-works) - *electromagnetic rotation, commutation, and the origin of back-EMF*
    - [Background — Step 2](#background--step-2)

---

  - [Step 3: The H-Bridge Concept](#step-3-the-h-bridge-concept) - *four switches, two safe states, and one forbidden state*
    - [Background — Step 3](#background--step-3)

---

  - [Step 4: The L298N Motor Driver Module](#step-4-the-l298n-motor-driver-module) - *every pin on the driver board explained before you wire it*
    - [Background — Step 4](#background--step-4)
    - [Component Wiring — L298N Logic Side](#component-wiring--l298n-logic-side)

---

  - [Step 5: Connecting the 9V Battery](#step-5-connecting-the-9v-battery) - *giving the motor its own independent power supply*
    - [Background — Step 5](#background--step-5)
    - [Component Wiring — L298N Power Side](#component-wiring--l298n-power-side)
    - [Task 5.1: Confirm the Motor Spins](#task-51-confirm-the-motor-spins)
      - [Observation Table — Task 5.1](#observation-table--task-51)
      - [Exercise Questions — Task 5.1](#exercise-questions--task-51)

---

  - [Step 6: Speed Control with PWM](#step-6-speed-control-with-pwm) - *duty cycle, the friction threshold, and three named speed levels*
    - [Background — Step 6](#background--step-6)
    - [Component Wiring — Step 6](#component-wiring--step-6)
    - [Task 6.1: Three Fixed Speed Levels](#task-61-three-fixed-speed-levels)
    - [Task 6.2: Variable Speed Ramp](#task-62-variable-speed-ramp)
      - [Observation Table — Tasks 6.1 & 6.2](#observation-table--tasks-61--62)
      - [Exercise Questions — Tasks 6.1 & 6.2](#exercise-questions--tasks-61--62)

---

  - [Step 7: Direction Control](#step-7-direction-control) - *IN1/IN2 logic combined with PWM into one complete motor system*
    - [Background — Step 7](#background--step-7)
    - [Component Wiring — Step 7](#component-wiring--step-7)
    - [Task 7.1: Switching Direction at a Fixed Speed](#task-71-switching-direction-at-a-fixed-speed)
    - [Task 7.2: Combined Variable Speed and Direction Control](#task-72-combined-variable-speed-and-direction-control)
      - [Observation Table — Task 7.2](#observation-table--task-72)
      - [Exercise Questions — Task 7.2](#exercise-questions--task-72)

---

- [PART 2 — Distance Measurement with HC-SR04 Ultrasonic Sensor](#part-2--distance-measurement-with-hc-sr04-ultrasonic-sensor)
  - [Step 8: The HC-SR04 Ultrasonic Sensor](#step-8-the-hc-sr04-ultrasonic-sensor) - *how an ultrasonic pulse is emitted, reflected, and received*
    - [Background — Step 8](#background--step-8)
    - [Component Wiring — Step 8](#component-wiring--step-8)

---

  - [Step 9: Time-of-Flight Principle](#step-9-time-of-flight-principle) - *deriving the distance formula from the speed of sound*
    - [Background — Step 9](#background--step-9)

---

  - [Step 10: Reading the Echo Pulse with pulseIn()](#step-10-reading-the-echo-pulse-with-pulsein) - *timing the echo safely using an explicit timeout value*
    - [Background — Step 10](#background--step-10)
    - [Component Wiring — Step 10](#component-wiring--step-10)
    - [Task 10.1: Read and Print Raw Pulse Duration](#task-101-read-and-print-raw-pulse-duration)
    - [Task 10.2: Explicitly Handle the Timeout Case](#task-102-explicitly-handle-the-timeout-case)
      - [Observation Table — Tasks 10.1 & 10.2](#observation-table--tasks-101--102)
      - [Exercise Questions — Tasks 10.1 & 10.2](#exercise-questions--tasks-101--102)

---

  - [Step 11: Displaying Distance and Threshold Detection](#step-11-displaying-distance-and-threshold-detection) - *converting timing into centimeters and adding a proximity alert*
    - [Background — Step 11](#background--step-11)
    - [Component Wiring — Step 11](#component-wiring--step-11)
    - [Task 11.1: Formatted Distance Output](#task-111-formatted-distance-output)
    - [Task 11.2: Threshold-Based Proximity Alert](#task-112-threshold-based-proximity-alert)
      - [Observation Table — Task 11.2](#observation-table--task-112)
      - [Exercise Questions — Task 11.2](#exercise-questions--task-112)

---

- [Consolidated Observation Tables](#consolidated-observation-tables)
  - [Task 5.1 — Confirm the Motor Spins](#task-51--confirm-the-motor-spins)
  - [Task 6.1 / 6.2 — Speed Control](#task-61--62--speed-control)
  - [Task 7.2 — Combined Speed and Direction](#task-72--combined-speed-and-direction)
  - [Task 10.1 / 10.2 — Raw Pulse Duration](#task-101--102--raw-pulse-duration)
  - [Task 11.2 — Distance and Threshold Detection](#task-112--distance-and-threshold-detection)
- [Troubleshooting](#troubleshooting)
- [Post-Lab Questions](#post-lab-questions)
  - [DC Motor and Back-EMF](#dc-motor-and-back-emf)
  - [H-Bridge and L298N](#h-bridge-and-l298n)
  - [PWM and Motor Speed Control](#pwm-and-motor-speed-control)
  - [Motor Direction Control](#motor-direction-control)
  - [HC-SR04 and Time-of-Flight](#hc-sr04-and-time-of-flight)
  - [pulseIn() and Timing](#pulsein-and-timing)
  - [Distance Calculation and Accuracy](#distance-calculation-and-accuracy)
  - [Cross-topic and Design Questions](#cross-topic-and-design-questions)
- [Quick Reference](#quick-reference)
  - [Arduino Functions Used in This Lab](#arduino-functions-used-in-this-lab)
  - [Formula Sheet](#formula-sheet)
  - [Pin Assignment Summary](#pin-assignment-summary)
  - [L298N Logic Truth Table](#l298n-logic-truth-table)

---

## 1. Precautions

This lab carries more electrical risk than LAB 01, because you are now introducing a second, independent power source and a motor that can push current back into the circuit on its own. Read every paragraph below before you connect anything, and come back to it if you rewire the circuit partway through the lab.

Never power the motor from Arduino's 5V pin. The 5V pin on the Arduino UNO is fed through the onboard voltage regulator, and that regulator is only rated for a few hundred milliamps of continuous current, most of which is already spoken for by the board itself and anything else you have wired to 5V. A DC motor, even a small one, draws a stall current far beyond that when it starts moving or when it is blocked, and pulling that current through the Arduino's regulator will overheat it, brown out the board mid-sketch, or damage the regulator permanently. This is why the motor gets its own 9V battery in this lab, connected directly to the L298N, never routed through the Arduino at all.

Always connect Arduino GND and L298N GND to the same point — this is called a shared ground, and it is not optional. The Arduino reads IN1, IN2, and ENA as HIGH or LOW by comparing the voltage on those pins against its own ground reference. If the L298N's ground and the Arduino's ground are not tied together, the L298N has no common reference point to interpret those signals against, and the logic pins will behave unpredictably — the motor may run backwards, twitch, ignore your code entirely, or refuse to start. Every wiring diagram in this lab includes a jumper wire from Arduino GND to the L298N GND terminal for exactly this reason, and if you ever add or remove a battery, check that this jumper is still in place before you power anything up again.

Never allow IN1 and IN2 to both be HIGH at the same time. Internally, the L298N uses transistor switches arranged so that one side of the H-bridge conducts when IN1 is HIGH and the other conducts when IN2 is HIGH. If both are driven HIGH simultaneously, both halves of that bridge try to conduct at once, creating what is called a shoot-through condition — a near-direct short from the motor supply to ground through the driver's internal switches. The L298N is designed to tolerate brief switching transients, but sustained shoot-through from a bug in your code — for example, setting both pins HIGH before you've thought through the logic table — can overheat and destroy the chip inside the module. Every direction-control sketch in this lab is written so that IN1 and IN2 are never set HIGH in the same statement block; check your own code against this rule before uploading.

Back-EMF is the reason freewheeling diodes matter, and the reason you should never wire a bare DC motor straight to a microcontroller pin even through a transistor without protection. A spinning motor is also a generator — its magnetic field and its coil are still doing the same physics in reverse, and the moment you cut power or reverse direction, the collapsing magnetic field induces a voltage spike in the coil that can be many times higher than the motor's rated supply voltage. Without a path for that spike to safely dissipate, it flows backward into whatever is driving the motor and can destroy transistors or microcontroller pins almost instantly. The L298N has freewheeling (flyback) diodes built into the module across each output, so this lab already has that protection in place, but the reason this precaution exists is so that if you ever build a DC motor circuit without a driver module — using a single transistor, for instance — you know you must add a diode across the motor yourself, or you will destroy the transistor the first time the motor changes state.

The 9V battery connection has a polarity, and reversing it on the L298N's power input is not a small mistake. The L298N's VCC and GND power-terminal screws are not interchangeable; VCC expects the higher potential and GND expects the return path, and internally the chip's protection circuitry assumes current will flow in that direction. Connecting the battery backwards forces current through internal paths that were never meant to carry it in reverse, and can destroy the driver chip on the module immediately, sometimes before you even see the motor twitch. Check the battery connector and the screw terminal labels every single time before you clip the battery in, especially after you've disconnected it to make a wiring change.

The HC-SR04 ultrasonic sensor operates at 5V logic levels, and you must not connect it to a 3.3V system without a level shifter. This lab uses an Arduino UNO, which is a 5V board throughout, so this precaution does not affect you today — but it matters for anything you build later on a 3.3V board such as an ESP32 or a 3.3V-only sensor node. On a 3.3V board, the HC-SR04's 5V Trig and Echo signals would exceed the microcontroller's input voltage tolerance and can damage the input pin over repeated exposure. Keep this in mind as a standing rule for future projects, not just this one.

Never place the HC-SR04's Trig or Echo pins on D0 or D1. Those two pins are the Arduino UNO's hardware serial RX and TX lines, the same lines the Serial Monitor uses to talk to your computer over USB. If Trig or Echo is wired to D0 or D1, the sensor's signals collide with the serial communication the moment you open the Serial Monitor or try to upload new code, and you will see garbled output, upload failures, or a sensor that appears to work only when the USB cable is unplugged. This lab uses D2 and D3 for exactly this reason — keep those assignments as given.

Disconnect the 9V battery when you rewire anything, not just the USB cable. It is easy to assume that unplugging the Arduino's USB cable makes the whole breadboard "safe" to touch and rearrange, but the 9V battery powers the L298N and the motor completely independently of the Arduino's power state. If the battery is still connected while you move jumper wires around IN1, IN2, or the motor terminals, you risk momentarily shorting the motor supply through a wire you're holding, or creating exactly the shoot-through condition described above with no code running to prevent it. Unclip the battery first, every time, before you touch the wiring.

---

## 2. Objectives

By the end of this lab, you will have built two independent physical systems around the same Arduino UNO and understood the electrical reasoning behind every connection in both. In Part 1, you will move from a simple question — why can't a microcontroller pin just drive a motor directly — through the internal logic of an H-bridge, into wiring and commanding a real L298N motor driver module powered from its own 9V battery, controlling both the speed and the direction of a DC motor with PWM and digital logic working together.

In Part 2, you will shift from moving something to measuring something, using the HC-SR04 ultrasonic sensor to time a physical event — the round trip of a sound pulse — and convert that timing into a distance using nothing but the speed of sound and a formula you derive yourself. Along the way you will get comfortable reading and protecting against the failure modes specific to each system: back-EMF and shoot-through on the motor side, and echo timeouts and inconsistent readings on the sensor side, so that by the final task in each part you are not just running example code, but debugging and reasoning about real embedded hardware the way an instrumentation engineer would on the job.

---

## 3. Global Overview

Before you pick up a single jumper wire, it helps to see the shape of the whole lab, because the two parts you are about to build do not depend on each other at all — you could build them on two entirely separate breadboards if you wanted to — but they do share two ground rules that apply no matter which part you're working on.

The first shared rule is the one you already read in the precautions above: every ground in this circuit is the same ground. The Arduino's GND, the L298N's GND, and later the HC-SR04's GND all connect back to one common point. This matters for a subtle reason worth restating in plain terms — the Arduino doesn't measure voltage in absolute terms, it measures it relative to its own GND pin. The moment any part of your circuit has a different idea of what "zero volts" means, every HIGH and LOW signal the Arduino sends or reads becomes ambiguous. You will tie grounds together once, early, and then stop thinking about it — but get it right at the start.

The second shared rule is that the motor gets its own power supply, separate from the Arduino's own 5V and separate from your USB cable. This is not a convenience choice — it is the same current-limiting reasoning from the precautions section, just stated as a design principle: anything that can draw a large, sudden, or reversing current, like a motor spinning up, stalling, or changing direction, must never share a power rail with the microcontroller that's trying to run stable logic on that same rail. The 9V battery in Part 1 exists entirely because of this rule.

With those two rules fixed in your head, here's the shape of what's ahead. Part 1 builds a motor control system: you'll wire the L298N driver between the Arduino and the motor, power it from the 9V battery, and write code that controls speed with PWM and direction with a pair of digital logic pins, finishing with both working together. Part 2 sets the motor circuit aside entirely and builds a distance-measuring system: you'll wire the HC-SR04 sensor directly to the Arduino, and write code that times an ultrasonic echo and turns that timing into a distance reading in centimeters, finishing with a threshold-based proximity alert. Nothing from Part 2 depends on anything wired in Part 1, and the two are never combined into a single task.

---

## PART 1 — DC Motor Control with the L298N Motor Driver

### Step 1: Why Arduino Pins Cannot Drive a DC Motor Directly

You're starting here rather than with the motor itself because the very first question a driver like the L298N answers is "why do I need this at all?" — and you can't appreciate what the L298N does for you until you've seen what happens without it.

#### Background — Step 1

An Arduino UNO's digital output pins are built to drive logic-level loads: LEDs, transistor bases, other chips' input pins — things that need only a few milliamps to work correctly. Each I/O pin on the ATmega328P (the microcontroller at the heart of the UNO) is rated for a maximum continuous current of about 20 mA, with an absolute maximum of 40 mA before you risk damaging the pin's internal driver circuitry. The entire chip also has a total current budget shared across all pins combined, typically around 200 mA, which limits how many pins you can push near their individual maximum at the same time.

A small hobby DC motor, even a modest 6V one, draws nowhere near that little. Under normal spinning load it might pull 100–250 mA, but the current that matters most here is the stall current — the current the motor draws the instant it's asked to start turning from a standstill, or if its shaft is physically blocked. Stall current for a small DC motor is commonly 3 to 10 times its running current, easily reaching several hundred milliamps to over an amp. If you wired that motor directly to an Arduino digital pin and called `digitalWrite(motorPin, HIGH)`, the motor would try to pull all of that current through a pin designed for 20 mA. In practice one of two things happens: either the pin's internal transistor overheats and fails within seconds, silently disabling that pin (and sometimes damaging neighboring circuitry on the same power rail), or the sudden current draw drags down the 5V rail enough to reset or brown out the entire board mid-sketch.

There's a second problem even if you could somehow supply enough current: a spinning DC motor generates its own back-EMF (you'll see exactly why in the next step), and an Arduino pin has no protection against that reverse voltage spike at all. A dedicated motor driver like the L298N exists specifically to solve both problems at once — it sits between the Arduino's low-current logic signals and the motor's high-current, back-EMF-generating world, acting as a current amplifier and a protective buffer in one package. Your Arduino pins will only ever tell the L298N what to do; they will never carry motor current directly.

💡 **Note:** This is the same reason you never drive a relay coil, a solenoid, or a speaker directly from a digital pin either — any load that draws meaningfully more current than a few LEDs, or that stores energy in a magnetic field, needs a driver stage between it and the microcontroller.

---

### Step 2: The DC Motor — How It Works

With the "why you need a driver" question settled, it's worth spending a moment on the motor itself, because the internal physics of how it spins is exactly what produces the back-EMF hazard you'll be protecting against for the rest of Part 1.

#### Background — Step 2

A brushed DC motor converts electrical energy into rotational mechanical energy using electromagnetism. Inside the motor casing is a fixed set of permanent magnets (the stator) surrounding a rotating coil of wire wound around an iron core (the rotor, or armature). When you apply a voltage across the motor's two terminals, current flows through the rotor's coil, and that current-carrying coil sitting inside the stator's magnetic field experiences a force — this is the same Lorentz force principle you'd have seen in a first electromagnetism course, F = BIL, where a current-carrying conductor in a magnetic field feels a force perpendicular to both the current and the field. That force is what pushes the rotor to turn.

If the rotor simply turned until it aligned with the magnetic field and stopped, that would be useless as a continuous motor — so brushed DC motors use a clever mechanical trick called commutation. A pair of spring-loaded contacts (the brushes) press against a segmented ring on the rotor shaft (the commutator), and as the shaft turns, the commutator automatically reverses the direction of current flowing through the coil at just the right moment, so the magnetic force keeps pushing the rotor in the same rotational direction instead of letting it settle into equilibrium. This is entirely mechanical — you don't control commutation from your code at all; it happens automatically inside the motor the instant current flows, which is part of why brushed DC motors are so simple to drive from the outside: you only decide how much voltage to apply and in which overall direction, and the motor handles the rest internally.

Real-world applications of small brushed DC motors like the one in this lab include RC car drivetrains, cheap cordless drills, electric toothbrushes, camera autofocus mechanisms, and countless conveyor and pump systems in industrial automation — anywhere you need continuous rotation from a DC supply without needing to know the shaft's exact angular position.

Now, the part that matters for the rest of this lab: because the rotor's coil is spinning inside a magnetic field, the motor is simultaneously acting as a generator, by the same electromagnetic induction principle you'd use to explain how a bicycle dynamo or an AC alternator works. While the motor is under power and accelerating, this generated voltage (called back-EMF, or counter-electromotive force) opposes the applied voltage and is small compared to it, which is part of what naturally limits the motor's running current once it's up to speed. But the moment you suddenly cut power, reverse the applied voltage, or otherwise force the motor to change state abruptly, the rotor is still spinning and its magnetic field is still collapsing or changing, and that changing field induces a voltage spike across the coil that can momentarily exceed the motor's rated supply voltage by a significant margin. That spike has nowhere useful to go, and if there's no protective path for it, it discharges back into whatever circuit is connected to the motor terminals — which is precisely why the L298N you're about to wire up has protective diodes built in, and precisely why you should never drive a bare motor from a transistor or pin without similar protection.

⚠️ **Warning:** Never touch the motor's terminals with your fingers while it's spinning down after power is cut, especially on larger motors than the one used here — the back-EMF voltage, while low-current and not typically dangerous to a person on a small hobby motor, is exactly the mechanism you'll be protecting your electronics from in the next few steps, and it's worth respecting even at this scale.

---

### Step 3: The H-Bridge Concept

You now know two things: a motor needs more current than an Arduino pin can safely provide, and a motor produces a dangerous voltage spike of its own. The H-bridge is the circuit topology that solves both problems while also giving you something you haven't had yet — the ability to reverse the motor's direction using only logic-level signals.

#### Background — Step 3

A DC motor spins in one direction when current flows through it one way, and in the opposite direction when current flows the other way. To reverse a motor electrically, you don't change the supply voltage's magnitude — you swap which of the motor's two terminals is connected to the positive supply and which is connected to ground. Doing that with a single on/off switch isn't possible; you need four switches arranged in a specific pattern called an H-bridge, named for the shape the circuit traces when you draw it: two vertical switch-pairs on the left and right, with the motor sitting horizontally between them like the crossbar of the letter H.

Label the four switches S1 (top-left), S2 (bottom-left), S3 (top-right), and S4 (bottom-right), with the motor connected between the midpoint of the left pair and the midpoint of the right pair. Close S1 and S4 together, leaving S2 and S3 open, and current flows from the supply through S1, through the motor from left to right, through S4, and back to ground — the motor spins, say, forward. Close S2 and S3 together instead, leaving S1 and S4 open, and current now flows through S3, through the motor from right to left, through S2, back to ground — the motor spins in reverse. Notice that in both valid states, exactly one top switch and the diagonally-opposite bottom switch are closed, while the other two are open.

The dangerous state is closing both switches on the same side at once — S1 and S2 together, or S3 and S4 together. That connects the supply rail directly to ground through only the switches themselves, with no motor coil in between to limit the current. This is the shoot-through condition mentioned in the precautions section, and it's the electrical reason IN1 and IN2 must never both be HIGH — those two Arduino-facing logic pins are what tell the L298N's internal circuitry which pair of switches to close, and setting both HIGH essentially asks the chip to attempt this same-side-closed condition internally.

In a real H-bridge chip like the one inside the L298N, these four mechanical "switches" are implemented as transistors (specifically, a configuration of bipolar power transistors in the L298N's case), switched on and off by internal logic that reads your IN1/IN2 (and IN3/IN4 for a second motor channel) signals and translates them into the correct transistor states — including built-in dead-time and protection logic that makes momentary shoot-through during switching transitions survivable, even though sustained shoot-through from a code bug is not. This is also where the freewheeling diodes from Step 2 physically live: one diode across each transistor switch, oriented to give the back-EMF spike a safe path to discharge through instead of blasting through the transistor itself.

💡 **Note:** The letter H in "H-bridge" refers purely to the shape of the switch arrangement in a schematic — it has nothing to do with any brand name or the word "high."

---

### Step 4: The L298N Motor Driver Module

You've seen the H-bridge as a concept — four switches, one motor. Now you're going to meet the real chip that implements it, learn every pin on the breakout board in front of you, and wire the logic side of it to your Arduino. Power to the motor itself comes in the next step; this step only sets up the signals that will command it.

#### Background — Step 4

The L298N motor driver module is a small breakout board built around the L298N integrated circuit, which contains two complete H-bridges inside a single chip — enough to control two separate DC motors independently, labeled Motor A and Motor B on the board. This lab only uses one motor, so you'll wire and use only the Motor A channel, leaving the Motor B pins unconnected.

Walking across the board, you'll find the following pins and terminals. The **power screw terminal block** has three connections: **VCC** (sometimes labeled 12V), which accepts the motor's supply voltage — in this lab, the 9V battery — and can typically accept anywhere from about 5V up to 35V depending on the exact module; **GND**, the ground return for that same supply; and a middle terminal that connects internally to **OUT1** and **OUT2** are the two motor output terminals for channel A, where the actual motor wires screw in, and **OUT3**/**OUT4** are the equivalent pair for channel B.

On the logic side, you'll find **ENA** (Enable A), **IN1**, and **IN2**, which together control Motor A — ENA enables (and via PWM, sets the speed of) that channel, while IN1 and IN2 set its direction, exactly matching the switch logic from the H-bridge discussion in Step 3. **ENB**, **IN3**, and **IN4** are the mirror-image controls for Motor B, unused here. There is also a **5V pin**, which is the output of an onboard linear voltage regulator that can derive a clean 5V logic supply from the VCC input — useful if you wanted to power the Arduino itself from this same regulator, though in this lab you will leave that 5V pin unconnected, since the Arduino stays powered from its own USB connection.

Two small removable jumpers matter here. The first, near the 5V pin, enables or disables that onboard regulator; it should stay in place for this lab, since your 9V battery is within the regulator's input range and you are not drawing logic power from it anyway. The second is the **ENA jumper**, a small cap sitting directly on the ENA pin header. When it's in place, it ties ENA permanently HIGH through an onboard pull-up, which enables Motor A at a fixed, full-drive state with no speed control available at all — useful only if you never intend to vary speed. Because Step 6 asks you to control speed with PWM, you must **remove the ENA jumper** before wiring ENA to an Arduino pin; leaving it in place while also connecting an external signal to ENA creates a conflict between the jumper's fixed HIGH and whatever your Arduino is trying to drive, and the motor will not respond correctly to your code.

⚠️ **Warning:** Do this wiring with the 9V battery disconnected. You are only connecting logic-side signals in this step, but it's good practice to never have any external power connected to a driver module while you're placing or removing jumpers and headers.

#### Component Wiring — L298N Logic Side

| L298N Pin | Connect To | Notes |
|---|---|---|
| ENA | Arduino D9 | Remove the ENA jumper cap first — D9 is a PWM-capable pin |
| IN1 | Arduino D8 | Direction control line 1 for Motor A |
| IN2 | Arduino D7 | Direction control line 2 for Motor A |
| GND | Arduino GND | Shared ground — required for IN1/IN2/ENA logic to be read correctly |
| 5V pin | Not connected | Onboard regulator jumper stays in place; Arduino is powered separately by USB |
| ENB, IN3, IN4 | Not connected | Motor B channel is unused in this lab |

💡 **Note:** D9 is chosen for ENA specifically because it's one of the Arduino UNO's six PWM-capable pins (3, 5, 6, 9, 10, 11) — you'll need that in Step 6. D8 and D7 are ordinary digital pins, which is all IN1 and IN2 need since they only ever need to be HIGH or LOW, never a variable value.

---

### Step 5: Connecting the 9V Battery

The logic side is wired. Now you'll give the L298N — and through it, the motor — its own independent source of power, and run your very first motor test.

#### Background — Step 5

Recall from the global overview that the motor must never share a power rail with the Arduino's own logic supply. The 9V battery connects to the L298N's power screw terminals — VCC and GND — and from there the L298N's internal H-bridge switches route that 9V to the motor's OUT1/OUT2 terminals according to whatever IN1/IN2 state your code sets. The Arduino never sees this 9V directly at any point; it only ever sends 5V logic signals to ENA, IN1, and IN2, and the L298N does the work of switching the higher-voltage, higher-current battery supply on the motor's behalf. This separation — low-power logic signals in, high-power motor current out — is the entire reason a driver module exists, and it's worth pausing to notice you've now built exactly that separation on your breadboard.

The GND terminal on the L298N's power block is not a separate ground from the one you already wired to the Arduino in Step 4 — it must be the same shared ground point, which is why a single jumper wire from Arduino GND is often extended to both the L298N's logic GND pin and its power GND terminal, or the two are bridged directly on the module (many L298N boards internally tie these together already, but always verify with a continuity check if you're unsure, rather than assume).

#### Component Wiring — L298N Power Side

| Connection | To | Notes |
|---|---|---|
| 9V battery (+) | L298N VCC screw terminal | Motor supply voltage — never route through Arduino |
| 9V battery (–) | L298N GND screw terminal | Same ground as Arduino GND and L298N logic GND |
| L298N OUT1 | Motor terminal 1 | Polarity here defines which direction is "forward" in your code |
| L298N OUT2 | Motor terminal 2 | If the motor spins backward from what you expect later, swap OUT1/OUT2, not your code |

#### Task 5.1: Confirm the Motor Spins

**Objective:** Verify every connection made so far by running the motor at a single fixed speed in one direction, with no PWM and no direction switching yet — the simplest possible proof that the wiring is correct before you add any complexity.

```cpp
// Task 5.1 — Confirm the motor spins at a fixed speed, one direction only.
// This sketch deliberately avoids PWM and direction switching so that if
// something doesn't work, you know the problem is in the basic wiring,
// not in more advanced code you haven't written yet.

const int PIN_ENA = 9;   // Enables Motor A channel and (later) sets its speed
const int PIN_IN1 = 8;   // Direction control line 1
const int PIN_IN2 = 7;   // Direction control line 2

const int RUN_TIME_MS = 3000;  // How long the motor stays on for this test

void setup() {
  Serial.begin(9600);

  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);

  // IN1 HIGH, IN2 LOW selects one rotation direction — we're calling this
  // "forward" for now, but which physical direction it actually is depends
  // on how OUT1/OUT2 are wired to the motor terminals.
  digitalWrite(PIN_IN1, HIGH);
  digitalWrite(PIN_IN2, LOW);

  Serial.println("Starting motor at fixed full speed...");

  // Driving ENA HIGH with digitalWrite (not analogWrite) enables the channel
  // at its maximum drive — equivalent to 100% duty cycle, but written as a
  // plain digital signal since we aren't varying speed in this task yet.
  digitalWrite(PIN_ENA, HIGH);

  delay(RUN_TIME_MS);

  // Disabling ENA cuts the motor off regardless of what IN1/IN2 are set to —
  // this is the safe way to stop the motor between tests.
  digitalWrite(PIN_ENA, LOW);
  Serial.println("Motor stopped.");
}

void loop() {
  // Nothing to repeat for this confirmation test — it runs once in setup().
}
```

**Expected Serial Monitor Output**

```
Starting motor at fixed full speed...
Motor stopped.
```

The motor shaft should spin steadily for 3 seconds at full speed the moment the sketch uploads, then stop completely. If the motor doesn't spin at all, doesn't stop, or spins only weakly, do not move on to Step 6 — work through the Troubleshooting table at the end of this manual first, since every later task in Part 1 builds directly on this wiring.

##### Observation Table — Task 5.1

| Trial | IN1 | IN2 | ENA | Observed motor behavior |
|---|---|---|---|---|
| 1 | HIGH | LOW | HIGH | |
| 2 | HIGH | LOW | LOW | |

##### Exercise Questions — Task 5.1

1. If you swapped the wires on OUT1 and OUT2 before running this task, what would change in the observed behavior, and what would stay exactly the same?
2. Why does this task use `digitalWrite(PIN_ENA, HIGH)` instead of `analogWrite(PIN_ENA, 255)`, given that both should produce full speed?

---

### Step 6: Speed Control with PWM

The motor runs. Now you'll take control of exactly how fast it runs, using the same PWM technique from LAB 01, applied to the ENA pin instead of an LED.

#### Background — Step 6

`analogWrite()` and the duty-cycle concept behind PWM were covered in detail in LAB 01, so this section only covers what's specific to using PWM on a motor rather than an LED. When you dim an LED with PWM, your eye simply averages the flickering light into a perceived brightness. A motor does something physically closer to true averaging: because the motor's rotor has real mechanical inertia (it resists sudden changes in speed) and the ENA switching happens fast — the Arduino's default PWM frequency on pin 9 is about 490 Hz — the motor's shaft doesn't have time to speed up and slow down with each individual pulse. Instead, it settles into a speed roughly proportional to the *average* voltage the PWM signal delivers, which is set by the duty cycle: `analogWrite(PIN_ENA, 128)` delivers roughly 50% duty cycle and the motor spins at roughly half its full-speed RPM, while `analogWrite(PIN_ENA, 255)` delivers 100% duty cycle for full speed.

The word "roughly" matters here, and it's worth being explicit about why the relationship between PWM value and actual shaft RPM is not perfectly linear. A motor has to overcome static friction and the mechanical resistance of its own bearings and brushes before it turns at all, so there's a minimum PWM value — often somewhere in the range of 40–80 out of 255 for a small hobby motor, though this varies by motor — below which the motor won't turn at all, or will only hum and vibrate without spinning (you'll meet this exact symptom in the Troubleshooting table). Above that threshold, speed does increase roughly with duty cycle, but factors like motor load, bearing wear, and the battery's remaining charge all shift the exact curve, which is why this relationship is described qualitatively here rather than with an exact formula — unlike the distance calculation in Part 2, which is precise because it's based on the physical speed of sound rather than a mechanical system with friction and variable load.

#### Component Wiring — Step 6

No new wiring is needed for this step — you're reusing the exact connections from Steps 4 and 5. Confirm the 9V battery is still connected and the ENA jumper is still removed before continuing.

#### Task 6.1: Three Fixed Speed Levels

**Objective:** Run the motor at three distinct, named PWM levels in sequence — slow, medium, and full — so you can directly observe and record how duty cycle relates to perceived motor speed.

```cpp
// Task 6.1 — Run the motor at three fixed speed levels in sequence.
// Named constants make it obvious what each speed represents and make the
// sketch easy to retune later without hunting for magic numbers in the code.

const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int SPEED_SLOW   = 80;   // Just above the minimum needed to overcome static friction
const int SPEED_MEDIUM = 160;  // Roughly 63% duty cycle
const int SPEED_FULL   = 255;  // 100% duty cycle — maximum available speed

const int RUN_TIME_MS = 3000;  // How long to hold each speed before switching

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);

  // Direction is fixed for this task — only speed is being tested here.
  digitalWrite(PIN_IN1, HIGH);
  digitalWrite(PIN_IN2, LOW);
}

void runAtSpeed(int pwmValue, const char* label) {
  Serial.print("Running at ");
  Serial.print(label);
  Serial.print(" (PWM = ");
  Serial.print(pwmValue);
  Serial.println(")");

  // analogWrite on ENA sets the duty cycle, which sets the average voltage
  // reaching the motor through the L298N — this is the only line that
  // actually changes between the three speed levels.
  analogWrite(PIN_ENA, pwmValue);
  delay(RUN_TIME_MS);
}

void loop() {
  runAtSpeed(SPEED_SLOW, "SLOW");
  runAtSpeed(SPEED_MEDIUM, "MEDIUM");
  runAtSpeed(SPEED_FULL, "FULL");

  // Stop briefly between full cycles so the three speeds are clearly
  // distinguishable when you're observing and timing the motor by eye.
  analogWrite(PIN_ENA, 0);
  Serial.println("Pausing before next cycle...");
  delay(2000);
}
```

**Expected Serial Monitor Output**

```
Running at SLOW (PWM = 80)
Running at MEDIUM (PWM = 160)
Running at FULL (PWM = 255)
Pausing before next cycle...
Running at SLOW (PWM = 80)
...
```

#### Task 6.2: Variable Speed Ramp

**Objective:** Instead of jumping between three fixed levels, sweep the PWM value smoothly from 0 to 255 and back down, so you can observe the motor accelerating and decelerating continuously rather than stepping between speeds.

```cpp
// Task 6.2 — Smoothly ramp motor speed up and back down.
// This builds directly on Task 6.1 by replacing the three fixed speed
// levels with a continuous sweep, using a for loop instead of named
// constants for each step.

const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int PWM_MIN = 0;
const int PWM_MAX = 255;
const int RAMP_STEP_DELAY_MS = 15;  // Delay between each PWM increment — controls ramp speed

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);

  digitalWrite(PIN_IN1, HIGH);
  digitalWrite(PIN_IN2, LOW);
}

void loop() {
  // Ramp up from stopped to full speed.
  for (int pwmValue = PWM_MIN; pwmValue <= PWM_MAX; pwmValue++) {
    analogWrite(PIN_ENA, pwmValue);
    Serial.println(pwmValue);
    delay(RAMP_STEP_DELAY_MS);
  }

  // Ramp back down from full speed to stopped — note the loop condition
  // and decrement direction are the only differences from the ramp-up above.
  for (int pwmValue = PWM_MAX; pwmValue >= PWM_MIN; pwmValue--) {
    analogWrite(PIN_ENA, pwmValue);
    Serial.println(pwmValue);
    delay(RAMP_STEP_DELAY_MS);
  }
}
```

**Expected Serial Monitor Output**

```
0
1
2
3
...
254
255
254
...
1
0
```

💡 **Note:** You'll notice the motor doesn't visibly start moving until the printed PWM value climbs past whatever its static-friction threshold happens to be — the ramp itself is linear in code, but the motor's response to it is not, exactly as described in the background above.

##### Observation Table — Tasks 6.1 & 6.2

| PWM value | Duty cycle (%) | Observed motor behavior | Serial Monitor output |
|---|---|---|---|
| 80 | | | |
| 160 | | | |
| 255 | | | |

**Formula:** Duty cycle (%) = (PWM value ÷ 255) × 100

##### Exercise Questions — Tasks 6.1 & 6.2

1. At what PWM value did you first observe the motor actually begin turning, and how does that compare to its fully-stopped value of 0?
2. If you needed the motor to run more slowly than SPEED_SLOW allows without stalling, what would you need to change about the motor or mechanical load rather than the code?
3. Why does Task 6.2 use a `for` loop instead of three separate `analogWrite()` calls like Task 6.1 did?

---

### Step 7: Direction Control

Speed is under control. The last piece of Part 1 is direction — and once you have both working together, you'll have a fully functional motor control system.

#### Background — Step 7

Direction is set entirely by IN1 and IN2, independent of whatever PWM value is on ENA. Since ENA determines *whether and how fast* the enabled channel drives current, and IN1/IN2 determine *which way* that current flows, the two work together but control genuinely separate things — you could, in principle, change IN1/IN2 while the motor is at any speed, though it's safer practice to briefly reduce speed near zero before reversing direction, to reduce mechanical and electrical stress on both the motor and the driver.

The logic table below captures every combination and is worth memorizing rather than looking up each time, since it comes directly from the H-bridge switch logic in Step 3:

| IN1 | IN2 | Motor behavior |
|---|---|---|
| HIGH | LOW | Rotates in one direction ("forward") |
| LOW | HIGH | Rotates in the opposite direction ("reverse") |
| LOW | LOW | Coasts freely — both bridge halves open, motor spins down under its own friction with no active braking |
| HIGH | HIGH | **Never do this** — shoot-through condition described in the precautions and in Step 3 |

The LOW/LOW "coast" state and the HIGH/HIGH forbidden state are easy to confuse conceptually, so it's worth being precise about the difference: LOW/LOW opens both switch pairs, so the motor is electrically disconnected from the supply entirely and simply decelerates on its own mechanical friction, the same way a bicycle wheel slows down when you stop pedaling. HIGH/HIGH does the opposite — it tries to close both switch pairs on the same side simultaneously, which doesn't disconnect anything; it creates a short circuit path through the switches themselves. Some L298N-based projects implement active "braking" (deliberately shorting the motor's own terminals together through the bridge to use its back-EMF to slow it rapidly) but that is a different, deliberate state achieved differently, not something you get by accident from HIGH/HIGH — and this lab does not implement active braking.

#### Component Wiring — Step 7

No new wiring for this step either — IN1 and IN2 are already connected from Step 4. This step is entirely about the code logic applied to pins you've already wired.

#### Task 7.1: Switching Direction at a Fixed Speed

**Objective:** Run the motor forward, stop it, run it in reverse, and stop it again, all at one fixed speed, to isolate direction switching from speed control before combining both.

```cpp
// Task 7.1 — Switch motor direction at a single fixed speed.
// Direction changes only happen while ENA is at 0 (motor stopped), which
// is safer practice even though the L298N can technically survive a live
// direction change — isolating the two reduces mechanical and electrical stress.

const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int FIXED_SPEED = 200;
const int RUN_TIME_MS = 2500;
const int STOP_TIME_MS = 1000;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);
}

void stopMotor() {
  // Bringing ENA to 0 before changing IN1/IN2 avoids switching direction
  // while current is actively flowing through the bridge.
  analogWrite(PIN_ENA, 0);
  delay(STOP_TIME_MS);
}

void loop() {
  Serial.println("Direction: FORWARD");
  digitalWrite(PIN_IN1, HIGH);  // IN1 HIGH, IN2 LOW — forward, per the logic table
  digitalWrite(PIN_IN2, LOW);
  analogWrite(PIN_ENA, FIXED_SPEED);
  delay(RUN_TIME_MS);

  stopMotor();

  Serial.println("Direction: REVERSE");
  digitalWrite(PIN_IN1, LOW);   // IN1 LOW, IN2 HIGH — reverse, per the logic table
  digitalWrite(PIN_IN2, HIGH);
  analogWrite(PIN_ENA, FIXED_SPEED);
  delay(RUN_TIME_MS);

  stopMotor();
}
```

**Expected Serial Monitor Output**

```
Direction: FORWARD
Direction: REVERSE
Direction: FORWARD
Direction: REVERSE
...
```

#### Task 7.2: Combined Variable Speed and Direction Control

**Objective:** Bring everything in Part 1 together — the motor should ramp up smoothly to full speed forward, ramp back down, reverse direction, ramp up to full speed in reverse, and ramp back down again, repeating indefinitely, with the Serial Monitor reporting both the current direction and PWM value at all times.

```cpp
// Task 7.2 — Final Part 1 task: variable PWM speed AND direction control
// working together. This combines the ramp logic from Task 6.2 with the
// direction-switching logic from Task 7.1 into one continuous cycle.

const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int PWM_MIN = 0;
const int PWM_MAX = 255;
const int RAMP_STEP_DELAY_MS = 12;

// Setting direction here in one place, rather than repeating digitalWrite
// calls throughout loop(), keeps the forward/reverse logic in a single
// spot that's easy to check against the IN1/IN2 table if something looks wrong.
void setDirection(bool forward) {
  digitalWrite(PIN_IN1, forward ? HIGH : LOW);
  digitalWrite(PIN_IN2, forward ? LOW : HIGH);
}

void rampSpeed(int fromValue, int toValue, const char* directionLabel) {
  int step = (toValue >= fromValue) ? 1 : -1;
  for (int pwmValue = fromValue; pwmValue != toValue + step; pwmValue += step) {
    analogWrite(PIN_ENA, pwmValue);
    Serial.print(directionLabel);
    Serial.print(" | PWM = ");
    Serial.println(pwmValue);
    delay(RAMP_STEP_DELAY_MS);
  }
}

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);
}

void loop() {
  // Forward: ramp up, then ramp back down to a full stop before reversing.
  setDirection(true);
  rampSpeed(PWM_MIN, PWM_MAX, "FORWARD");
  rampSpeed(PWM_MAX, PWM_MIN, "FORWARD");

  // Only change IN1/IN2 once ENA has been ramped down to 0 above — this
  // is the same live-direction-change caution from Task 7.1, still applied
  // here even though the ramp makes it easy to forget.
  setDirection(false);
  rampSpeed(PWM_MIN, PWM_MAX, "REVERSE");
  rampSpeed(PWM_MAX, PWM_MIN, "REVERSE");
}
```

**Expected Serial Monitor Output**

```
FORWARD | PWM = 0
FORWARD | PWM = 1
...
FORWARD | PWM = 255
FORWARD | PWM = 254
...
FORWARD | PWM = 0
REVERSE | PWM = 0
REVERSE | PWM = 1
...
REVERSE | PWM = 255
REVERSE | PWM = 254
...
REVERSE | PWM = 0
FORWARD | PWM = 0
...
```

##### Observation Table — Task 7.2

| Direction | PWM value | Duty cycle (%) | Observed motor behavior |
|---|---|---|---|
| Forward | 80 | | |
| Forward | 255 | | |
| Reverse | 80 | | |
| Reverse | 255 | | |

**Formula:** Duty cycle (%) = (PWM value ÷ 255) × 100

##### Exercise Questions — Task 7.2

1. In `rampSpeed()`, why is the loop condition written as `pwmValue != toValue + step` instead of the more familiar `pwmValue <= toValue`, given that the function needs to ramp both up and down?
2. What would happen — electrically, not just in terms of code correctness — if `setDirection()` were called partway through a ramp, while ENA was still well above 0?
3. Sketch out, in words, how you would modify Task 7.2 so that instead of an automatic ramp, the direction and speed were controlled live by two characters typed into the Serial Monitor.

---

## PART 2 — Distance Measurement with HC-SR04 Ultrasonic Sensor

### Step 8: The HC-SR04 Ultrasonic Sensor

Set the motor circuit aside completely — nothing from Part 1 is reused here, and nothing in Part 2 will be combined back into it. You're starting a second, independent system: measuring distance using sound you can't hear.

#### Background — Step 8

Ultrasound is simply sound above the range of human hearing, generally taken to start around 20 kHz. The HC-SR04 uses this because ultrasonic pulses at its operating frequency of about 40 kHz travel predictably through air, reflect cleanly off most solid surfaces, and — critically for this lab — are completely silent to you while you're testing it repeatedly on your desk.

Physically, the module has two silver cylindrical transducers on its face that look identical but do different jobs: one is a transmitter that converts an electrical pulse into a burst of 40 kHz sound, and the other is a receiver that converts an incoming sound wave back into an electrical signal. The measurement cycle works like this: you send a short electrical trigger pulse to the module, the transmitter emits a burst of eight 40 kHz sound pulses, that burst travels outward until it strikes an object and reflects back, and the receiver detects the returning echo. The module handles the actual sound generation and detection entirely on its own — your job, and the Arduino's job, is only to start the process and time how long the whole round trip takes, which is exactly what Step 9 and Step 10 cover.

The module has four pins: **VCC**, which expects 5V (this is the precaution from earlier in the manual — never connect this pin to a 3.3V supply without a level shifter); **GND**; **Trig** (trigger), a digital input on the sensor that you pulse HIGH briefly to start a measurement; and **Echo**, a digital output from the sensor that goes HIGH the instant the ultrasonic burst is sent and returns LOW the instant the echo is detected — the width of that HIGH pulse is the round-trip time you'll be measuring.

The HC-SR04's rated range is typically about 2 cm to 400 cm, with a stated accuracy around 3 mm under ideal conditions — a flat, hard, perpendicular reflecting surface, in still air, at a known temperature. Real-world accuracy is often worse than that, because soft or angled surfaces absorb or deflect sound rather than reflecting it straight back, and because the speed of sound itself (which Step 9 relies on directly) changes with air temperature and humidity — you'll see this surface again as a specific troubleshooting entry and post-lab question later in this manual.

Real-world applications of this exact sensing principle include robot obstacle avoidance, automotive parking-assist sensors, non-contact liquid level sensing in tanks, and automatic hand sanitizer or soap dispensers that detect a hand nearby.

#### Component Wiring — Step 8

| HC-SR04 Pin | Connect To | Notes |
|---|---|---|
| VCC | Arduino 5V | Never connect to a 3.3V rail without a level shifter |
| GND | Arduino GND | Same shared ground as the rest of the circuit |
| Trig | Arduino D3 | Output from Arduino — starts each measurement |
| Echo | Arduino D2 | Input to Arduino — pulse width is the round-trip time |

⚠️ **Warning:** Do not wire Trig or Echo to D0 or D1 under any circumstances — those are the hardware serial RX/TX lines used by the Serial Monitor, and this exact mistake is one of the entries in the Troubleshooting table at the end of this manual.

---

### Step 9: Time-of-Flight Principle

You now know the HC-SR04 hands you a pulse width on the Echo pin. This step is pure math — turning that pulse width into an actual distance, worked all the way from first principles so you understand exactly where the formula you'll use in code comes from.

#### Background — Step 9

The core idea is called time-of-flight: sound travels at a known, roughly constant speed through air, so if you know how long a sound pulse took to travel somewhere and back, and you know its speed, you can calculate the distance it covered using nothing more than the basic relationship distance = speed × time.

Start with the speed of sound in air, which is approximately 343 meters per second at 20°C. Converting that into units that match what you'll actually be working with in code — microseconds and centimeters — makes the arithmetic much easier later:

343 m/s = 34,300 cm/s = 0.0343 cm/µs

That number, 0.0343 cm per microsecond, is the speed of sound expressed in exactly the units your Arduino sketch will use, since `pulseIn()` returns a duration in microseconds and you want a result in centimeters.

Now, the pulse width you measure on the Echo pin is not the one-way travel time to the object — it's the *round-trip* time: the sound travels from the sensor to the object, reflects, and travels back to the sensor before Echo goes LOW again. That means the raw duration corresponds to *twice* the distance you actually want:

duration (µs) × 0.0343 (cm/µs) = total distance traveled by the sound = 2 × (distance to object)

Solving for the distance to the object, you divide by 2:

**distance (cm) = (duration × 0.0343) ÷ 2**

which simplifies to:

**distance (cm) = duration × 0.01715**

This is the exact formula the code in Step 11 will implement — and it's worth walking through two worked numerical examples so the formula isn't just an abstract line of algebra by the time you type it into a sketch.

**Worked Example 1:** Suppose `pulseIn()` returns a duration of 1160 microseconds.

distance = 1160 × 0.01715 = 19.894 cm ≈ **19.9 cm**

**Worked Example 2:** Suppose you place an object exactly 50 cm from the sensor and want to predict what duration you should expect, working the formula in reverse.

duration = (2 × distance) ÷ 0.0343 = (2 × 50) ÷ 0.0343 = 100 ÷ 0.0343 ≈ **2,915 µs**

If your measured duration for an object you've physically placed at 50 cm comes out noticeably different from ~2915 µs, that's a real, useful diagnostic signal — not just measurement noise — and you'll use exactly this kind of check in the observation tables in Step 10 and Step 11.

💡 **Note:** The 0.0343 cm/µs figure assumes 20°C air. The speed of sound increases by roughly 0.6 m/s for every 1°C rise in temperature, which is why the exact same sensor, aimed at the exact same object, can report slightly different distances on a hot afternoon versus a cool morning — a real physical effect, not sensor error, and one you'll meet again in the Troubleshooting table under "HC-SR04 reads correctly indoors but fails outdoors."

---

### Step 10: Reading the Echo Pulse with pulseIn()

The math is settled. Now you'll write the code that actually triggers a measurement and captures the raw pulse width, before any distance conversion happens at all.

#### Background — Step 10

Triggering the HC-SR04 follows an exact sequence given in its datasheet: set Trig LOW briefly to ensure a clean starting state, then set it HIGH for at least 10 microseconds, then set it LOW again. That short HIGH pulse is what tells the sensor "start a measurement now" — internally, the module responds by emitting its 40 kHz burst and immediately setting Echo HIGH, holding it HIGH until the echo returns or its own internal timeout is reached.

`pulseIn()` is the Arduino function built exactly for measuring a pulse like this. Its full signature is `pulseIn(pin, value, timeout)`. The first parameter is the pin to read. The second parameter tells it which state to measure the duration of — `HIGH` in this case, since that's the state Echo holds for the duration you care about. The third parameter, timeout, is optional but you should never omit it in this lab: it's the maximum time, in microseconds, that `pulseIn()` will wait for the pulse to start and complete before giving up. If you omit it, the default timeout is a full second (1,000,000 microseconds) — which, if the sensor never receives an echo at all, would freeze your entire sketch for a full second on every single measurement, making anything else in `loop()` sluggish or unresponsive.

Choosing a sensible timeout means working backward from the sensor's maximum range. From the formula in Step 9, an object at the HC-SR04's rated maximum range of about 400 cm produces a round-trip duration of roughly 400 × 2 ÷ 0.0343 ≈ 23,300 microseconds. Rounding up generously for margin, a timeout of 30,000 microseconds (30 milliseconds) comfortably covers the sensor's entire usable range without making your sketch wait unnecessarily long when there's genuinely nothing in range.

That last point matters for how you interpret the return value: if `pulseIn()` reaches its timeout without ever seeing the expected pulse, it returns **0**. It is critical not to treat a returned 0 as "the object is 0 cm away" — plugging 0 into the Step 9 formula would say the object is touching the sensor, which is almost never what a 0 actually means. A 0 means one of: no object is within range, the ultrasonic burst reflected off at an angle steep enough that the echo never returned to the receiver, or the sensor itself isn't wired or triggered correctly. Every sketch from here onward in this lab explicitly checks for a 0 return and reports it as "no echo detected" rather than silently computing a nonsense distance.

#### Component Wiring — Step 10

No new wiring — Trig on D3 and Echo on D2 were already connected in Step 8.

#### Task 10.1: Read and Print Raw Pulse Duration

**Objective:** Trigger the sensor repeatedly and print only the raw duration returned by `pulseIn()`, in microseconds, with no distance conversion yet — confirming the sensor and wiring respond correctly before adding any math.

```cpp
// Task 10.1 — Trigger the HC-SR04 and print the raw echo pulse duration.
// No distance conversion here yet — this task only confirms the sensor
// and its wiring are working before Step 11 adds the formula from Step 9.

const int PIN_TRIG = 3;   // Output — starts each measurement
const int PIN_ECHO = 2;   // Input — pulse width is the round-trip time

const unsigned long ECHO_TIMEOUT_US = 30000UL;  // ~30ms covers the sensor's full rated range
const int MEASUREMENT_INTERVAL_MS = 500;         // Time between successive measurements

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

unsigned long triggerAndMeasure() {
  // Force Trig LOW first — this guarantees a clean, known starting state
  // before we pulse it HIGH, rather than assuming it was already LOW.
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);

  // A HIGH pulse of at least 10 microseconds on Trig tells the sensor to
  // emit its ultrasonic burst — this timing comes directly from the
  // HC-SR04 datasheet, not from Arduino convention.
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);

  // pulseIn with an explicit timeout avoids the default 1-second wait,
  // which would make the whole sketch feel frozen if no echo ever returns.
  return pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);
}

void loop() {
  unsigned long duration = triggerAndMeasure();

  Serial.print("Raw pulse duration: ");
  Serial.print(duration);
  Serial.println(" microseconds");

  delay(MEASUREMENT_INTERVAL_MS);
}
```

**Expected Serial Monitor Output**

```
Raw pulse duration: 1160 microseconds
Raw pulse duration: 1148 microseconds
Raw pulse duration: 0 microseconds
Raw pulse duration: 2912 microseconds
...
```

#### Task 10.2: Explicitly Handle the Timeout Case

**Objective:** Extend Task 10.1 so that a returned duration of 0 is reported clearly as "no echo detected," rather than being printed as a raw number that could be mistaken for a real (very short) measurement.

```cpp
// Task 10.2 — Explicitly detect and report the pulseIn() timeout case.
// This builds directly on Task 10.1 by adding one conditional check before
// printing, so a 0 is never confused with a genuinely short pulse duration.

const int PIN_TRIG = 3;
const int PIN_ECHO = 2;

const unsigned long ECHO_TIMEOUT_US = 30000UL;
const int MEASUREMENT_INTERVAL_MS = 500;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

unsigned long triggerAndMeasure() {
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);

  return pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);
}

void loop() {
  unsigned long duration = triggerAndMeasure();

  // A duration of exactly 0 means pulseIn() hit its timeout without ever
  // seeing the echo — treating that as "0 cm away" would be physically
  // wrong, so it gets its own explicit message instead.
  if (duration == 0) {
    Serial.println("No echo detected (out of range or no reflecting surface).");
  } else {
    Serial.print("Raw pulse duration: ");
    Serial.print(duration);
    Serial.println(" microseconds");
  }

  delay(MEASUREMENT_INTERVAL_MS);
}
```

**Expected Serial Monitor Output**

```
Raw pulse duration: 1160 microseconds
No echo detected (out of range or no reflecting surface).
Raw pulse duration: 2912 microseconds
...
```

##### Observation Table — Tasks 10.1 & 10.2

| Object distance (actual, ruler) | Pulse duration (µs) | Notes |
|---|---|---|
| 10 cm | | |
| 50 cm | | |
| 100 cm | | |
| No object in range | | Should read 0 / "no echo detected" |

##### Exercise Questions — Tasks 10.1 & 10.2

1. Why is `delayMicroseconds(2)` included before the trigger pulse in `triggerAndMeasure()`, given that Trig should already be LOW from the previous loop iteration?
2. If you angled the sensor 45° away from a flat wall instead of pointing it straight on, would you expect a duration reading, a 0, or something else — and why?
3. Task 10.2 checks `duration == 0` after calling `pulseIn()`. Would checking the condition *before* calling `triggerAndMeasure()` make any sense here? Explain why or why not.

---

### Step 11: Displaying Distance and Threshold Detection

The raw timing works. This final step of Part 2 converts that timing into an actual distance using the formula you derived in Step 9, and finishes with a practical proximity-alert feature — the same building block a real parking sensor or obstacle-avoidance robot would use.

#### Background — Step 11

There's nothing conceptually new here — this step is where Step 9's formula and Step 10's timeout-safe pulse reading come together in code. The one addition worth calling out is the idea of a **threshold**: rather than only ever printing a raw distance number, you'll compare that distance against a fixed value you choose in advance (a named constant, not a magic number) and change the sketch's behavior — printing an alert — whenever the distance drops below it. This exact pattern (measure something continuously, compare it to a threshold, react when the threshold is crossed) is one of the most common structures in real instrumentation and embedded systems, whether the threshold is a distance, a temperature, or a pressure reading.

#### Component Wiring — Step 11

No new wiring — this step is entirely a code-side extension of Step 10.

#### Task 11.1: Formatted Distance Output

**Objective:** Convert the raw pulse duration into a distance in centimeters using the Step 9 formula, and print both the raw duration and the calculated distance together in one clearly labeled line.

```cpp
// Task 11.1 — Convert raw pulse duration into a distance in centimeters,
// using the formula derived in Step 9, and print both values together.

const int PIN_TRIG = 3;
const int PIN_ECHO = 2;

const unsigned long ECHO_TIMEOUT_US = 30000UL;
const int MEASUREMENT_INTERVAL_MS = 500;

// Speed of sound at 20°C, expressed in cm per microsecond — see Step 9
// for the full derivation of this constant and the divide-by-2 reasoning.
const float SPEED_OF_SOUND_CM_PER_US = 0.0343;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

unsigned long triggerAndMeasure() {
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);
  return pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);
}

float durationToDistanceCm(unsigned long duration) {
  // Divided by 2 because the raw duration covers the round trip to the
  // object and back, but we only want the one-way distance — see Step 9.
  return (duration * SPEED_OF_SOUND_CM_PER_US) / 2.0;
}

void loop() {
  unsigned long duration = triggerAndMeasure();

  if (duration == 0) {
    Serial.println("No echo detected (out of range or no reflecting surface).");
  } else {
    float distanceCm = durationToDistanceCm(duration);

    Serial.print("Duration: ");
    Serial.print(duration);
    Serial.print(" us | Distance: ");
    Serial.print(distanceCm);
    Serial.println(" cm");
  }

  delay(MEASUREMENT_INTERVAL_MS);
}
```

**Expected Serial Monitor Output**

```
Duration: 1160 us | Distance: 19.89 cm
Duration: 2912 us | Distance: 49.94 cm
No echo detected (out of range or no reflecting surface).
Duration: 583 us | Distance: 10.00 cm
...
```

#### Task 11.2: Threshold-Based Proximity Alert

**Objective:** Extend Task 11.1 so that whenever the calculated distance falls within a chosen threshold, the Serial Monitor prints a clear alert in addition to the normal distance reading — completing the full Part 2 system.

```cpp
// Task 11.2 — Final Part 2 task: formatted distance output plus a
// threshold-based proximity alert. Builds directly on Task 11.1 by adding
// one comparison against a named threshold constant after the distance
// has already been calculated.

const int PIN_TRIG = 3;
const int PIN_ECHO = 2;

const unsigned long ECHO_TIMEOUT_US = 30000UL;
const int MEASUREMENT_INTERVAL_MS = 500;

const float SPEED_OF_SOUND_CM_PER_US = 0.0343;
const float ALERT_THRESHOLD_CM = 10.0;  // Any object closer than this triggers the alert

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

unsigned long triggerAndMeasure() {
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);
  return pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);
}

float durationToDistanceCm(unsigned long duration) {
  return (duration * SPEED_OF_SOUND_CM_PER_US) / 2.0;
}

void loop() {
  unsigned long duration = triggerAndMeasure();

  if (duration == 0) {
    Serial.println("No echo detected (out of range or no reflecting surface).");
    delay(MEASUREMENT_INTERVAL_MS);
    return;  // Nothing to compare against a threshold if there's no valid reading
  }

  float distanceCm = durationToDistanceCm(duration);

  Serial.print("Duration: ");
  Serial.print(duration);
  Serial.print(" us | Distance: ");
  Serial.print(distanceCm);
  Serial.print(" cm");

  // Comparing against a named constant, rather than a bare number, means
  // changing the alert distance later only ever requires editing one line.
  if (distanceCm < ALERT_THRESHOLD_CM) {
    Serial.println("  <<< OBJECT DETECTED WITHIN THRESHOLD");
  } else {
    Serial.println();
  }

  delay(MEASUREMENT_INTERVAL_MS);
}
```

**Expected Serial Monitor Output**

```
Duration: 2912 us | Distance: 49.94 cm
Duration: 1160 us | Distance: 19.89 cm
Duration: 480 us | Distance: 8.23 cm  <<< OBJECT DETECTED WITHIN THRESHOLD
Duration: 340 us | Distance: 5.83 cm  <<< OBJECT DETECTED WITHIN THRESHOLD
No echo detected (out of range or no reflecting surface).
...
```

##### Observation Table — Task 11.2

| Object distance (actual, ruler) | Pulse duration (µs) | Calculated distance (cm) | Error (cm) |
|---|---|---|---|
| 5 cm | | | |
| 10 cm | | | |
| 25 cm | | | |
| 50 cm | | | |
| 100 cm | | | |

**Formula:** distance (cm) = (duration × 0.0343) ÷ 2, Error (cm) = calculated distance − actual distance

##### Exercise Questions — Task 11.2

1. Why does Task 11.2 use `return;` inside the `duration == 0` branch instead of wrapping the rest of `loop()` in an `else` block?
2. If you wanted two thresholds instead of one — a "caution" zone and a much closer "danger" zone, each printing a different message — how would you restructure the `if` statement in `loop()`?
3. `ALERT_THRESHOLD_CM` is a `float`, while `distanceCm` is also a `float`. What would go wrong, if anything, if both were declared as `int` instead, given the formula in `durationToDistanceCm()`?

---

## Consolidated Observation Tables

All observation tables from throughout this lab are repeated here in one place for easy reference and for submission alongside your completed sketches.

### Task 5.1 — Confirm the Motor Spins

| Trial | IN1 | IN2 | ENA | Observed motor behavior |
|---|---|---|---|---|
| 1 | HIGH | LOW | HIGH | |
| 2 | HIGH | LOW | LOW | |

### Task 6.1 / 6.2 — Speed Control

| PWM value | Duty cycle (%) | Observed motor behavior | Serial Monitor output |
|---|---|---|---|
| 80 | | | |
| 160 | | | |
| 255 | | | |

**Formula:** Duty cycle (%) = (PWM value ÷ 255) × 100

### Task 7.2 — Combined Speed and Direction

| Direction | PWM value | Duty cycle (%) | Observed motor behavior |
|---|---|---|---|
| Forward | 80 | | |
| Forward | 255 | | |
| Reverse | 80 | | |
| Reverse | 255 | | |

**Formula:** Duty cycle (%) = (PWM value ÷ 255) × 100

### Task 10.1 / 10.2 — Raw Pulse Duration

| Object distance (actual, ruler) | Pulse duration (µs) | Notes |
|---|---|---|
| 10 cm | | |
| 50 cm | | |
| 100 cm | | |
| No object in range | | Should read 0 / "no echo detected" |

### Task 11.2 — Distance and Threshold Detection

| Object distance (actual, ruler) | Pulse duration (µs) | Calculated distance (cm) | Error (cm) |
|---|---|---|---|
| 5 cm | | | |
| 10 cm | | | |
| 25 cm | | | |
| 50 cm | | | |
| 100 cm | | | |

**Formula:** distance (cm) = (duration × 0.0343) ÷ 2, Error (cm) = calculated distance − actual distance

---

## Troubleshooting

| Problem observed | Likely cause | How to fix it |
|---|---|---|
| Motor doesn't spin at all, no hum | ENA jumper still in place while ENA is also wired to D9, or GND not shared between Arduino and L298N | Remove the ENA jumper cap; verify a jumper wire runs from Arduino GND to L298N GND |
| Motor spins in the wrong direction from what code expects | OUT1/OUT2 wiring to the motor terminals is physically reversed relative to what "forward" assumes | Swap the two motor wires on OUT1/OUT2 rather than rewriting IN1/IN2 logic, or simply relabel which state you call "forward" |
| Motor hums or vibrates but does not turn | PWM value is below the motor's static-friction threshold, or the motor is mechanically stalled/blocked | Increase the PWM value above roughly 80–100; check the shaft isn't physically obstructed |
| Motor spins only one direction regardless of IN1/IN2 values | One of IN1 or IN2 is not actually connected, or that pin is stuck HIGH/LOW due to a loose jumper wire | Reseat the jumper wires on IN1 and IN2; confirm each with `digitalRead()` printed to Serial before assuming the code is wrong |
| Motor runs briefly then stops on its own | 9V battery voltage has sagged under load, or a loose connection is intermittently breaking contact | Test battery voltage with a multimeter under load; check every screw terminal and jumper connection is firmly seated |
| L298N module gets noticeably hot to the touch | Sustained shoot-through from a code bug (IN1 and IN2 both HIGH), or the module is driving a motor beyond its rated current | Audit your code for any statement setting both IN1 and IN2 HIGH; confirm the motor's rated current is within the L298N's continuous current rating |
| Arduino resets or browns out when the motor starts | Motor's stall current is being drawn through a shared or undersized power path, or grounds are not properly shared, causing voltage transients | Confirm the Arduino is powered independently via USB, not sharing the 9V battery's supply; check shared ground wiring |
| Motor direction reverses briefly on its own during a speed ramp | IN1/IN2 are being changed while ENA is still at a nonzero PWM value, causing a brief live direction change | Always ramp ENA down to 0 before changing IN1/IN2, as shown in Task 7.2 |
| Serial Monitor shows only garbled characters | Serial Monitor's baud rate doesn't match the `Serial.begin()` value in the sketch | Set the Serial Monitor's baud rate dropdown to match the sketch, 9600 in every example in this lab |
| Serial Monitor shows no output at all despite the code running (motor or sensor still visibly working) | Serial Monitor window is set to the wrong COM port, or `Serial.begin()` is missing or was called after the first `Serial.print()` | Confirm the correct port is selected in the IDE; make sure `Serial.begin()` is the first line inside `setup()` |
| 9V battery drains unusually fast | Motor is being left running continuously at full speed between tests, or the battery is a low-capacity type unsuited for repeated motor stall currents | Power down (unclip the battery) between test runs rather than leaving sketches looping indefinitely; use a fresh or higher-capacity battery for extended testing |
| HC-SR04 always reads 0 / "no echo detected" | Trig and Echo wires are swapped, sensor isn't receiving 5V, or nothing is within the sensor's minimum/maximum range | Double check Trig is on D3 and Echo is on D2, not reversed; confirm VCC reads 5V with a multimeter; test with an object 20–50 cm directly in front of the sensor |
| HC-SR04 reads wildly inconsistent values between consecutive measurements | Reflecting surface is soft, angled, or too small, scattering the ultrasonic burst instead of reflecting it straight back | Test against a large, flat, hard surface held perpendicular to the sensor before trusting readings from irregular objects |
| HC-SR04 reads correctly indoors but fails or drifts outdoors | Air temperature outdoors differs from the 20°C assumption baked into the 0.0343 cm/µs constant, and wind or ambient ultrasonic noise can also interfere | Recalculate the speed-of-sound constant for the actual ambient temperature if outdoor accuracy matters; shield the sensor from wind where possible |
| Sketch appears to freeze for about a second on every measurement | `pulseIn()` is being called without an explicit timeout, falling back to its default 1-second maximum wait | Always pass an explicit timeout value, such as `ECHO_TIMEOUT_US` in this lab's sketches |
| Uploading new code fails or the IDE reports a port/upload error | Trig or Echo is wired to D0 or D1, colliding with the hardware serial lines used for uploading | Move Trig and Echo off D0/D1 permanently, as instructed in Step 8 — D2 and D3 are used throughout this lab specifically to avoid this |
| Distance readings are consistently a fixed amount higher or lower than the actual measured distance | Ruler measurement was taken from the wrong reference point (e.g., from the sensor's front edge rather than the transducer face itself), or the speed-of-sound constant doesn't match actual room temperature | Always measure from the same reference point on the sensor consistently; consider recalculating the constant if the room is unusually hot or cold |
| Distance calculation prints as 0.00 cm even though `pulseIn()` clearly returned a nonzero duration | Integer division was used somewhere in the formula instead of floating-point division, truncating the result to zero | Ensure `SPEED_OF_SOUND_CM_PER_US` and the division by 2.0 are written as floating-point values, exactly as shown in Task 11.1 |
| Motor and HC-SR04 sketches both seem to interfere with each other when combined on one Arduino | Both systems were wired and coded onto the same breadboard/sketch despite the manual's instruction to keep them separate | This lab intentionally keeps Part 1 and Part 2 as separate systems and separate sketches — do not merge them into one combined circuit for this lab |

---

## Post-Lab Questions

### DC Motor and Back-EMF

1. Explain, in your own words, why a spinning DC motor also behaves like a generator, and name the physical principle responsible.
2. What is back-EMF, and at what point during a motor's operation is it at its most dangerous to nearby electronics?
3. Why does the L298N need built-in freewheeling diodes if the module is only ever used to spin a motor forward and backward, never to power something else back through it?
4. If you replaced the L298N in this lab with a single bare transistor switching the motor on and off directly, what additional component would you need to add, and why?
5. Describe, using the commutator concept from Step 2, why you don't need to write any code that manages exactly when current reverses inside the motor's own coil.
6. A motor's stall current is often much higher than its running current. Explain physically why stall current is higher, using the back-EMF concept from Step 2.

### H-Bridge and L298N

7. Describe, in words, the four-switch H-bridge arrangement from Step 3, and explain which pair of switches must close to spin a motor in each of its two directions.
8. What specifically is shoot-through, and which two switches in the H-bridge model correspond to IN1 and IN2 both being HIGH at once?
9. Why is the L298N able to survive brief shoot-through-like transients during normal PWM switching, but not sustained shoot-through caused by a code bug?
10. The L298N contains two complete H-bridges. Why does this lab only use one of them, and what would change in the wiring if you wanted to control a second, independent motor?
11. What is the purpose of the ENA jumper cap on the L298N module, and why must it be removed before this lab's PWM speed-control sketches will work correctly?
12. If you accidentally reversed the 9V battery's polarity on the L298N's VCC/GND screw terminals, what part of the circuit is most at risk, and why?

### PWM and Motor Speed Control

13. Explain, using the concept of duty cycle, why `analogWrite(PIN_ENA, 128)` produces roughly half of the motor's maximum speed rather than exactly a fixed voltage.
14. An LED's brightness under PWM and a motor's speed under PWM both depend on an averaging effect, but for two physically different reasons. What are they?
15. **Calculation:** If the Arduino's PWM resolution is 8-bit (0–255) and you want a duty cycle of exactly 40%, what integer value should you pass to `analogWrite()`?
16. **Identify the bug:** The following snippet is meant to run the motor at half speed forward, using the pin assignments from this lab. What is wrong with it?
    ```cpp
    digitalWrite(PIN_IN1, HIGH);
    digitalWrite(PIN_IN2, LOW);
    digitalWrite(PIN_ENA, 128);
    ```
17. Why does a small hobby DC motor typically have a minimum PWM value below which it only hums instead of turning, and what mechanical property is responsible?
18. If two identical motors were wired to two separate L298N channels and given the exact same PWM value, would you expect them to spin at exactly the same RPM? Explain your reasoning.

### Motor Direction Control

19. Write out the full IN1/IN2 logic table from memory, including the forbidden state, without looking back at Step 7.
20. What is the practical difference between the LOW/LOW "coast" state and stopping the motor by setting ENA to 0 while leaving IN1/IN2 unchanged?
21. Why does this lab's code always ramp ENA down to 0 before changing IN1 and IN2, even though the L298N can technically survive a live direction change?
22. **Identify the bug:** The function below is a modification of Task 7.1's `stopMotor()`. Explain why this version is unsafe to call right before a direction change.
    ```cpp
    void stopMotor() {
      digitalWrite(PIN_IN1, LOW);
      digitalWrite(PIN_IN2, LOW);
    }
    ```
23. If IN1 and IN2 were both wired to the exact same Arduino pin instead of two separate pins, which states from the logic table would become impossible to produce?
24. Explain why "forward" and "reverse" in this lab are simply labels you chose, rather than something physically fixed by the L298N or the motor itself.

### HC-SR04 and Time-of-Flight

25. Explain what physically happens inside the HC-SR04 between the moment Trig goes HIGH and the moment Echo goes LOW again, for a successful measurement.
26. Why is the HC-SR04's operating principle described as "time-of-flight," and what other everyday technologies use the same basic principle, whether with sound or light?
27. Why does the sensor's rated minimum range (around 2 cm) exist at all — what physically limits how close an object can be and still be measured correctly?
28. Explain, in terms of transmitter and receiver transducers, why the HC-SR04 needs two separate silver cylinders rather than one component that does both jobs.
29. Why does the HC-SR04 rely on a physical sound reflection at all, rather than detecting an object's presence some other way?
30. Two identical HC-SR04 sensors are aimed at the same flat wall from the same distance, one indoors and one outdoors on a windy day. Would you expect their readings to match exactly? Explain.

### pulseIn() and Timing

31. Explain, using this lab's chosen timeout of 30,000 microseconds, why that specific value was chosen rather than a much smaller or much larger number.
32. What does a `pulseIn()` return value of exactly 0 mean, and why is it dangerous to treat that value as "the object is 0 cm away"?
33. Why must `Serial.begin()` be called before any `Serial.print()` statement in `setup()`, and what specifically goes wrong if it isn't?
34. **Identify the bug:** The following code is meant to measure and print the pulse duration, but it never prints a correct value. Find the mistake.
    ```cpp
    void loop() {
      unsigned long duration;
      pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);
      Serial.println(duration);
    }
    ```
35. Why does this lab always call `pulseIn()` with an explicit third argument, rather than relying on the function's default timeout behavior?
36. If you wanted to average three consecutive `pulseIn()` readings together before printing a distance, how would you restructure `loop()` to do that?

### Distance Calculation and Accuracy

37. Derive, starting from the speed of sound in meters per second, the exact value of the constant used in this lab's distance formula (0.0343 cm/µs), showing every unit conversion step.
38. **Calculation:** An HC-SR04 returns a raw pulse duration of 2,000 microseconds. Using this lab's formula, what distance in centimeters does that correspond to?
39. **Calculation:** You want to detect an object at exactly 75 cm away. What raw pulse duration, in microseconds, should you expect `pulseIn()` to return?
40. Why is the raw pulse duration divided by 2 before being converted into a distance, and what would the calculated result represent if this division were accidentally left out of the code?
41. Explain why the HC-SR04's stated accuracy figure (around 3 mm) is best thought of as a best-case number rather than a guarantee under all conditions.
42. If the ambient temperature rose from 20°C to 30°C during a long outdoor test, would your calculated distances read slightly too high, slightly too low, or stay the same, assuming the constant in your code is never updated? Explain using the speed-of-sound relationship from Step 9.

### Cross-topic and Design Questions

43. **Design question:** Describe how you would combine the motor system from Part 1 and the distance sensor from Part 2 into a single robot that automatically stops before hitting a wall — describe the overall control logic, not just the wiring.
44. **Design question:** An automatic parking barrier needs to stop a gate motor the instant a vehicle is detected within a set distance. Using only the concepts from this lab — PWM speed control, direction control, and threshold-based distance detection — describe how you would structure that system.
45. **Comparison question:** Compare the HC-SR04 ultrasonic sensor to an infrared (IR) distance sensor for the same obstacle-detection task. Describe one situation where the HC-SR04 would clearly outperform the IR sensor, and one situation where the reverse would be true.
46. Both the motor system and the distance sensor system in this lab rely on precise timing — PWM duty cycle in one case, pulse width in the other. Explain how these two uses of timing are conceptually similar, and how they are fundamentally different.
47. If you had to run only one of the two systems in this lab continuously inside `loop()` while checking the other only every few seconds, which would you prioritize running more frequently, and why?
48. This lab deliberately keeps Part 1 and Part 2 as separate circuits and separate sketches. Describe one real engineering reason, beyond simply following instructions, why keeping unrelated subsystems electrically and logically separate during development and testing is good practice.

---

## Quick Reference

### Arduino Functions Used in This Lab

| Function | Syntax | Parameters | Returns | Example |
|---|---|---|---|---|
| `pinMode()` | `pinMode(pin, mode)` | pin: pin number; mode: INPUT or OUTPUT | None | `pinMode(PIN_ENA, OUTPUT);` |
| `digitalWrite()` | `digitalWrite(pin, value)` | pin: pin number; value: HIGH or LOW | None | `digitalWrite(PIN_IN1, HIGH);` |
| `analogWrite()` | `analogWrite(pin, value)` | pin: PWM-capable pin; value: 0–255 duty cycle | None | `analogWrite(PIN_ENA, 160);` |
| `delay()` | `delay(ms)` | ms: milliseconds to pause | None | `delay(2000);` |
| `delayMicroseconds()` | `delayMicroseconds(us)` | us: microseconds to pause | None | `delayMicroseconds(10);` |
| `pulseIn()` | `pulseIn(pin, value, timeout)` | pin: input pin; value: HIGH or LOW state to time; timeout: max wait in microseconds | Pulse duration in microseconds (unsigned long), or 0 on timeout | `pulseIn(PIN_ECHO, HIGH, 30000UL);` |
| `Serial.begin()` | `Serial.begin(rate)` | rate: baud rate | None | `Serial.begin(9600);` |
| `Serial.print()` | `Serial.print(value)` | value: data to print, no newline after | None | `Serial.print("Distance: ");` |
| `Serial.println()` | `Serial.println(value)` | value: data to print, with newline after | None | `Serial.println(distanceCm);` |

### Formula Sheet

**PWM Duty Cycle**
Duty cycle (%) = (PWM value ÷ 255) × 100

**Distance from Pulse Duration**
distance (cm) = (duration × speed of sound in cm/µs) ÷ 2
Using the speed of sound at 20°C: distance (cm) = duration × 0.01715

**Speed of Sound (20°C reference)**
343 m/s = 34,300 cm/s = 0.0343 cm/µs

**pulseIn() Timeout Sizing**
timeout (µs) ≳ (2 × maximum expected distance in cm) ÷ speed of sound (cm/µs), with margin added. For this lab's 400 cm rated maximum range: (2 × 400) ÷ 0.0343 ≈ 23,300 µs, rounded up to 30,000 µs.

**L298N Direction Logic**
See truth table below.

### Pin Assignment Summary

**Part 1 — Motor Control**

| Signal | Arduino Pin | Direction |
|---|---|---|
| ENA | D9 (PWM) | Output |
| IN1 | D8 | Output |
| IN2 | D7 | Output |
| GND | — | Shared with L298N GND |

**Part 2 — Distance Measurement**

| Signal | Arduino Pin | Direction |
|---|---|---|
| Trig | D3 | Output |
| Echo | D2 | Input |
| VCC | 5V | Power |
| GND | — | Shared ground |

### L298N Logic Truth Table

| IN1 | IN2 | Motor Effect |
|---|---|---|
| HIGH | LOW | Rotates forward |
| LOW | HIGH | Rotates in reverse |
| LOW | LOW | Coasts freely (no active drive) |
| HIGH | HIGH | **Forbidden — shoot-through risk** |
