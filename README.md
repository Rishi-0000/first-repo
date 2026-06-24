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
