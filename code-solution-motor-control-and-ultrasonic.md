LAB 02 (Short Form) — Answer Key
Companion to: LAB_02_Short.md

---

## Task 1: Confirm the Motor Spins

```cpp
const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int RUN_TIME_MS = 3000;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);

  digitalWrite(PIN_IN1, HIGH);
  digitalWrite(PIN_IN2, LOW);

  digitalWrite(PIN_ENA, HIGH);
  delay(RUN_TIME_MS);
  digitalWrite(PIN_ENA, LOW);
}

void loop() {
  // Nothing to repeat — this test runs once in setup()
}
```

---

## Task 2: Direction Control

```cpp
const int PIN_ENA = 9;
const int PIN_IN1 = 8;
const int PIN_IN2 = 7;

const int RUN_TIME_MS = 2500;
const int STOP_TIME_MS = 1000;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_ENA, OUTPUT);
  pinMode(PIN_IN1, OUTPUT);
  pinMode(PIN_IN2, OUTPUT);
}

void loop() {
  // Forward
  digitalWrite(PIN_IN1, HIGH);
  digitalWrite(PIN_IN2, LOW);
  digitalWrite(PIN_ENA, HIGH);
  delay(RUN_TIME_MS);

  digitalWrite(PIN_ENA, LOW);
  delay(STOP_TIME_MS);

  // Reverse
  digitalWrite(PIN_IN1, LOW);
  digitalWrite(PIN_IN2, HIGH);
  digitalWrite(PIN_ENA, HIGH);
  delay(RUN_TIME_MS);

  digitalWrite(PIN_ENA, LOW);
  delay(STOP_TIME_MS);
}
```

---

## Task 3: Measuring Distance

```cpp
const int PIN_TRIG = 3;
const int PIN_ECHO = 2;

const unsigned long ECHO_TIMEOUT_US = 30000UL;
const int MEASUREMENT_INTERVAL_MS = 500;
const float SPEED_OF_SOUND_CM_PER_US = 0.0343;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

void loop() {
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);

  unsigned long duration = pulseIn(PIN_ECHO, HIGH, ECHO_TIMEOUT_US);

  if (duration == 0) {
    Serial.println("No echo detected");
  } else {
    float distanceCm = (duration * SPEED_OF_SOUND_CM_PER_US) / 2.0;
    Serial.print("Distance: ");
    Serial.print(distanceCm);
    Serial.println(" cm");
  }

  delay(MEASUREMENT_INTERVAL_MS);
}
```
