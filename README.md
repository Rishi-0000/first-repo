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
