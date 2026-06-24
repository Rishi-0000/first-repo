# 🤖 Line Following Robot with Speed Control

A simple autonomous robot built using **Arduino Uno** that follows a black line on a white surface using two IR sensors and controls two DC motors via the L293D motor driver IC with PWM-based speed control.

---

## 📸 Demo

> 📹 *Add your demo video link here after uploading to YouTube (Unlisted)*
> 
> [![Demo Video]- https://youtube.com/shorts/udUnPPfsYcw?feature=share

---

## 📋 Table of Contents

- [Overview](#overview)
- [Components Required](#components-required)
- [Circuit & Pin Configuration](#circuit--pin-configuration)
- [Working Principle](#working-principle)
- [Code Explanation](#code-explanation)
- [Assembly Steps](#assembly-steps)
- [Results](#results)
- [How to Use](#how-to-use)
- [Future Improvements](#future-improvements)

---

## Overview

This robot autonomously follows a black line on a white surface by continuously reading two IR sensors and adjusting motor speed and direction in real time. PWM is used on the motor driver enable pins to achieve smooth forward motion and differential turning without abrupt stops.

**Key concepts demonstrated:**
- Digital I/O and analog PWM on Arduino Uno
- H-bridge motor driving with L293D
- Closed-loop sensor-to-actuator control
- Differential drive steering

---

## Components Required

| S.No | Component | Specification | Quantity |
|------|-----------|---------------|----------|
| 1 | Arduino Uno | ATmega328P microcontroller | 1 |
| 2 | IR Sensor Module | Digital output, detects black/white surface | 2 |
| 3 | L293D Motor Driver IC | Dual H-bridge, 600mA per channel | 1 |
| 4 | DC Gear Motor (TT Motor) | 3–6V, drives left and right wheels | 2 |
| 5 | Breadboard | Full-size for prototyping | 1 |
| 6 | Jumper Wires | Male-to-male & male-to-female | As needed |
| 7 | 9V Battery / Power Bank | Powers Arduino + motor driver | 1 |

---

## Circuit & Pin Configuration

| Arduino Pin | Connected To | Type | Function |
|-------------|-------------|------|----------|
| 10 | Left IR Sensor OUT | Digital Input | Reads left sensor |
| 11 | Right IR Sensor OUT | Digital Input | Reads right sensor |
| Pin 6 (PWM~) | L293D EN1 | PWM Output | Left motor speed |
| Pin 9 (PWM~) | L293D EN2 | PWM Output | Right motor speed |
| Pin 2 | L293D IN1 | Digital Output | Left motor direction A |
| Pin 3 | L293D IN2 | Digital Output | Left motor direction B |
| Pin 4 | L293D IN3 | Digital Output | Right motor direction A |
| Pin 5 | L293D IN4 | Digital Output | Right motor direction B |

> ⚠️ **Note:** EN pins MUST be connected to PWM-capable pins (~) on Arduino Uno for speed control to work.

---

## Working Principle

Each IR sensor emits infrared light downward:
- **White surface** → light reflected back → sensor output **LOW** (on line)
- **Black surface** → light absorbed → sensor output **HIGH** (off line)

### Sensor Logic Table

| Left IR | Right IR | Action | Speed |
|---------|----------|--------|-------|
| LOW | LOW | Move Forward | 150 |
| LOW | HIGH | Turn Left | 120 |
| HIGH | LOW | Turn Right | 120 |
| HIGH | HIGH | Stop | 0 |

---

## Code Explanation

### Speed Constants
```cpp
int normalSpeed = 150;   // Straight forward
int turnSpeed   = 120;   // Dominant motor during a turn
int slowSpeed   = 80;    // Slow/reverse motor during a turn
```

### Core Motor Function
All movement routes through a single `setMotors()` function:
```cpp
void setMotors(int leftSpeed, int leftDir, int rightSpeed, int rightDir)
```
- Direction controlled by `HIGH`/`LOW` on IN pins
- Speed controlled by `analogWrite()` on EN pins (0–255)

### Main Loop
```cpp
void loop() {
  int left  = digitalRead(leftSensor);
  int right = digitalRead(rightSensor);

  if (left == LOW && right == LOW)        moveForward(normalSpeed);
  else if (left == LOW && right == HIGH)  turnLeft(turnSpeed);
  else if (left == HIGH && right == LOW)  turnRight(turnSpeed);
  else                                    stopMotors();
}
```

### Turning Logic
During a turn, the dominant motor runs forward at `turnSpeed` while the other motor runs slowly in reverse at `slowSpeed` — creating a sharp differential turn without losing the line.

---

## Assembly Steps

1. Mount DC gear motors on chassis and attach wheels
2. Place L293D on breadboard
3. Connect IN1/IN2/EN1 (left motor) and IN3/IN4/EN2 (right motor) from Arduino to L293D
4. Connect DC motor terminals to L293D output pins (OUT1/OUT2, OUT3/OUT4)
5. Mount left IR sensor at front-left and right IR sensor at front-right (~5–10mm above surface)
6. Connect sensor OUT pins to Arduino 10 (left) and 11 (right); connect VCC and GND
7. Power Arduino via USB or 9V battery
8. Upload code via Arduino IDE and test on a black-line track on white surface

---

## Results

- ✅ Robot successfully followed a black line at `normalSpeed = 150`
- ✅ Differential turning produced sharp, controlled turns without losing the line
- ✅ Stop condition (both sensors off-line) worked as a reliable safety fallback
- ✅ Serial monitor confirmed correct sensor-to-action mapping in all 4 states
- ✅ Speed constants were easily tunable for different track types

---

## How to Use

1. **Clone this repo**
   ```bash
   git clone https://github.com/YourUsername/line-following-robot.git
   ```

2. **Open in Arduino IDE**
   - Open `line_following_robot.ino`

3. **Upload to Arduino Uno**
   - Connect via USB
   - Select **Board:** Arduino Uno
   - Select correct **Port**
   - Click **Upload**

4. **Open Serial Monitor** (9600 baud) to watch real-time sensor states

5. **Place robot on track** — black line on white surface, minimum line width ~2cm

---

## Future Improvements

- [ ] Add PID control for smoother line tracking
- [ ] Bluetooth module (HC-05) for manual speed override
- [ ] 3rd center IR sensor for better line recovery
- [ ] OLED display for real-time speed and sensor status
- [ ] Battery level indicator

---

## 🛠️ Built With

![Arduino](https://img.shields.io/badge/Arduino-Uno-00979D?logo=arduino&logoColor=white)
![C++](https://img.shields.io/badge/Language-C%2B%2B-blue)

---

## 👤 Author

**Rishikesh Shukla**  
B.E. Mechatronics Engineering — Terna College of Engineering, Navi Mumbai  
📧 rishikesh001k@gmail.com

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).










// Line Following Robot with Speed Control
// EN pins must be on PWM pins (~) on Arduino Uno

int leftSensor  = 10;
int rightSensor = 11;

// Motor A (Left)
int EN1 = 6;   // PWM pin ~ for speed control
int IN1 = 2;
int IN2 = 3;

// Motor B (Right)
int EN2 = 9;   // PWM pin ~ for speed control
int IN3 = 4;
int IN4 = 5;

// Speed values (0–255)
int normalSpeed  = 150;
int turnSpeed    = 120;
int slowSpeed    = 80;

void setup() {
  pinMode(leftSensor,  INPUT);
  pinMode(rightSensor, INPUT);

  pinMode(EN1, OUTPUT);
  pinMode(EN2, OUTPUT);
  pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);

  Serial.begin(9600);
  Serial.println("Robot ready!");
}

// Core motor control function
void setMotors(int leftSpeed, int leftDir, int rightSpeed, int rightDir) {
  // Left motor
  analogWrite(EN1, abs(leftSpeed));
  digitalWrite(IN1, leftDir == 1 ? HIGH : LOW);
  digitalWrite(IN2, leftDir == 1 ? LOW  : HIGH);

  // Right motor
  analogWrite(EN2, abs(rightSpeed));
  digitalWrite(IN3, rightDir == 1 ? HIGH : LOW);
  digitalWrite(IN4, rightDir == 1 ? LOW  : HIGH);
}

void moveForward(int speed) {
  setMotors(speed, 1, speed, 1);
  Serial.print("Forward, speed: "); Serial.println(speed);
}

void turnLeft(int speed) {
  setMotors(slowSpeed, -1, speed, 1);  // left motor slow reverse, right forward
  Serial.println("Turning left");
}

void turnRight(int speed) {
  setMotors(speed, 1, slowSpeed, -1);  // left forward, right motor slow reverse
  Serial.println("Turning right");
}

void stopMotors() {
  analogWrite(EN1, 0);
  analogWrite(EN2, 0);
  Serial.println("Stopped");
}

void loop() {
  int left  = digitalRead(leftSensor);
  int right = digitalRead(rightSensor);

  if (left == LOW && right == LOW) {
    moveForward(normalSpeed);

  } else if (left == LOW && right == HIGH) {
    turnLeft(turnSpeed);

  } else if (left == HIGH && right == LOW) {
    turnRight(turnSpeed);

  } else {
    stopMotors();
  }
}





# 🗑️ Smart Automatic Dustbin with Lid Control

An Arduino-based smart dustbin that **automatically opens its lid** when a hand is detected nearby, monitors how full the bin is, and **alerts you with a buzzer and red LED** when it needs to be emptied — no touching required.

---

## 📸 Demo

> 📹 *Add your demo video link here after uploading to YouTube (Unlisted)*
>
> [![Demo Video]https://youtu.be/aVsg32IgqwE

---

## 📋 Table of Contents

- [Overview](#overview)
- [Components Required](#components-required)
- [Circuit & Pin Configuration](#circuit--pin-configuration)
- [Working Principle](#working-principle)
- [Code Explanation](#code-explanation)
- [Assembly Steps](#assembly-steps)
- [Results](#results)
- [How to Use](#how-to-use)
- [Future Improvements](#future-improvements)

---

## Overview

This project implements a **contactless smart dustbin** using an ultrasonic sensor, servo motor, buzzer, and LED indicators. The ultrasonic sensor serves a dual purpose — detecting a hand approaching the bin to trigger the lid, and also measuring the fill level of the bin to warn when it is nearly full.

**Key concepts demonstrated:**
- Ultrasonic distance measurement using HC-SR04
- Servo motor control with the Arduino Servo library
- PWM and digital I/O for LEDs and buzzer
- Time-based state management using `millis()` (non-blocking)
- Multi-purpose sensor usage in a single embedded system

---

## Components Required

| S.No | Component | Specification / Purpose | Quantity |
|------|-----------|------------------------|----------|
| 1 | Arduino Uno | ATmega328P MCU — main controller | 1 |
| 2 | HC-SR04 Ultrasonic Sensor | Measures distance for hand detection & fill level | 1 |
| 3 | Servo Motor (SG90) | Opens and closes the dustbin lid | 1 |
| 4 | Buzzer | Beeps at 1000 Hz when bin is full | 1 |
| 5 | Green LED | Indicates bin is OK / not full | 1 |
| 6 | Red LED | Indicates bin is full — needs emptying | 1 |
| 7 | Resistor (220Ω) | Current limiting for LEDs | 2 |
| 8 | Breadboard | Prototyping base for all connections | 1 |
| 9 | Jumper Wires | Male-to-male & male-to-female connections | As needed |

---

## Circuit & Pin Configuration

| Arduino Pin | Connected To | Type | Function |
|-------------|-------------|------|----------|
| Pin 10 | HC-SR04 TRIG | Digital Output | Sends ultrasonic pulse |
| Pin 11 | HC-SR04 ECHO | Digital Input | Receives echo pulse |
| Pin 9 | Servo Motor Signal | PWM Output | Controls lid angle |
| Pin 8 | Buzzer (+) | Digital Output | Beeps when bin is full |
| Pin 7 | Green LED (via 220Ω) | Digital Output | ON = bin is fine |
| Pin 6 | Red LED (via 220Ω) | Digital Output | ON = bin is full |

> ⚠️ **Note:** Always connect LEDs through a 220Ω resistor to protect them from excess current. Connect resistor between Arduino pin and LED anode (+).

---

## Working Principle

### Dual Role of the Ultrasonic Sensor

The HC-SR04 is used for **two separate tasks** in one project:

**1. Hand Detection (Lid Control)**
The sensor is mounted on top of the bin facing outward. When a hand comes within 10 cm, the sensor detects it and sends a signal to open the lid via the servo motor. The lid stays open for 3 seconds then automatically closes.

**2. Fill Level Monitoring**
The same sensor (or a second one mounted inside) measures distance from the sensor to the garbage. A shorter distance means the bin is fuller. The fill percentage is calculated using Arduino's `map()` function.

### Fill Level Calculation
```
Fill % = map(distance, BIN_DEPTH, 0, 0, 100)
```
- If `distance = BIN_DEPTH (20cm)` → bin is 0% full (empty)
- If `distance = 0cm` → bin is 100% full

### System Logic Flow

```
Loop starts
    │
    ▼
Measure distance (ultrasonic)
    │
    ├──► Calculate fill percentage
    │
    ├──► Hand detected (< 10cm)?
    │         YES → Open lid, start 3s timer
    │
    ├──► Lid open > 3 seconds?
    │         YES → Close lid automatically
    │
    └──► Bin ≥ 80% full?
              YES → Red LED ON, Green LED OFF, Buzzer ON
              NO  → Green LED ON, Red LED OFF, Buzzer OFF
```

### Sensor-to-Action Logic

| Distance Reading | Condition | Action |
|-----------------|-----------|--------|
| < 10 cm | Hand detected near lid | Open lid via servo |
| > 10 cm, lid open > 3s | Timeout | Close lid via servo |
| Fill ≥ 80% | Bin nearly full | Red LED + Buzzer ON |
| Fill < 80% | Bin has space | Green LED ON |
| No echo (999 cm) | Sensor error / out of range | Ignore, continue loop |

---

## Code Explanation

### Key Settings (Easy to Customize)
```cpp
#define BIN_DEPTH       20   // Depth of your bin in cm
#define OPEN_LID_IF     10   // Open lid if hand is within 10 cm
#define FULL_AT_PERCENT 80   // Alert when bin is 80% full
#define LID_OPEN        90   // Servo angle for open lid (degrees)
#define LID_CLOSE        0   // Servo angle for closed lid (degrees)
#define KEEP_LID_OPEN 3000   // Keep lid open for 3 seconds
```

### Distance Measurement
```cpp
long measureDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);   // Send 10µs pulse
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 30000);  // Wait for echo
  if (duration == 0) return 999;                    // No echo = out of range

  return duration * 0.034 / 2;   // Convert time to cm
}
```
The formula `duration × 0.034 / 2` converts microseconds to centimeters using the speed of sound (340 m/s). Dividing by 2 accounts for the pulse travelling to the object and back.

### Non-Blocking Lid Timer using millis()
```cpp
// When lid opens — record the time
timeWhenOpened = millis();

// Each loop — check how long lid has been open
long timeSinceOpen = millis() - timeWhenOpened;
if (timeSinceOpen > KEEP_LID_OPEN) {
  lid.write(LID_CLOSE);   // Close after 3 seconds
  lidIsOpen = false;
}
```
`millis()` is used instead of `delay()` so the Arduino can keep checking sensors while waiting — this is a non-blocking approach and is best practice in embedded systems.

### LED and Buzzer Control
```cpp
if (fullPercent >= FULL_AT_PERCENT) {
  digitalWrite(GREEN_LED, LOW);
  digitalWrite(RED_LED, HIGH);
  tone(BUZZER_PIN, 1000);      // 1000 Hz beep
} else {
  digitalWrite(GREEN_LED, HIGH);
  digitalWrite(RED_LED, LOW);
  noTone(BUZZER_PIN);
}
```

---

## Assembly Steps

1. Mount the servo motor on top of the dustbin lid hinge position
2. Attach the servo arm to the lid so it opens when servo rotates to 90°
3. Mount the HC-SR04 ultrasonic sensor on the front of the bin facing outward (for hand detection)
4. Place the Arduino Uno and breadboard on the side or base of the bin
5. Connect TRIG (Pin 10) and ECHO (Pin 11) to the HC-SR04
6. Connect servo signal wire to Pin 9; VCC to 5V; GND to GND
7. Connect Green LED through a 220Ω resistor to Pin 7; cathode (–) to GND
8. Connect Red LED through a 220Ω resistor to Pin 6; cathode (–) to GND
9. Connect Buzzer positive (+) to Pin 8; negative (–) to GND
10. Upload the code via Arduino IDE and open Serial Monitor at 9600 baud to verify

---

## Results

- ✅ Lid opened automatically within 0.3 seconds of hand detection
- ✅ Lid closed reliably after the 3-second timeout with no manual input
- ✅ Fill level percentage correctly calculated and displayed on Serial Monitor
- ✅ Red LED and buzzer triggered when bin exceeded 80% full threshold
- ✅ Green LED confirmed normal operation when bin had space
- ✅ `millis()`-based timer allowed sensor polling to continue while lid was open (non-blocking)

---

## How to Use

1. **Clone this repo**
   ```bash
   git clone https://github.com/YourUsername/smart-automatic-dustbin.git
   ```

2. **Open in Arduino IDE**
   - Open `smart_dustbin.ino`

3. **Adjust settings if needed** (top of the code)
   ```cpp
   #define BIN_DEPTH       20   // Change to your bin's actual depth in cm
   #define FULL_AT_PERCENT 80   // Change alert threshold if needed
   ```

4. **Upload to Arduino Uno**
   - Connect via USB
   - Select **Board:** Arduino Uno
   - Select correct **Port**
   - Click **Upload**

5. **Open Serial Monitor** (9600 baud) to see real-time fill percentage and lid status

6. **Place hand within 10 cm** of the sensor to test lid opening

---

## Future Improvements

- [ ] Add Wi-Fi module (ESP8266) to send bin-full alerts to a phone
- [ ] OLED display showing fill percentage on the bin itself
- [ ] Second ultrasonic sensor inside dedicated to fill level only
- [ ] Solar panel for outdoor bin power supply
- [ ] Data logging to track how frequently bin is used

---

## 🛠️ Built With

![Arduino](https://img.shields.io/badge/Arduino-Uno-00979D?logo=arduino&logoColor=white)
![C++](https://img.shields.io/badge/Language-C%2B%2B-blue)

---

## 👤 Author

**Rishikesh Shukla**  
B.E. Mechatronics Engineering — Terna College of Engineering, Navi Mumbai  
📧 rishikesh001k@gmail.com

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

