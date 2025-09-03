#include <Servo.h>

#define IN1 8   // Sol motor
#define IN2 9
#define ENA 10

#define IN3 12  // Sağ motor
#define IN4 13
#define ENB 11

#define FRONT_TRIG 6
#define FRONT_ECHO 7

#define RIGHT_TRIG 22
#define RIGHT_ECHO 23

#define LEFT_TRIG 24
#define LEFT_ECHO 25

#define SERVO_PIN 5

Servo myServo;
int lastServoAngle = 90; // servonun son vəziyyətini saxlamaq üçün

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENA, OUTPUT);

  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENB, OUTPUT);

  pinMode(FRONT_TRIG, OUTPUT);
  pinMode(FRONT_ECHO, INPUT);

  pinMode(RIGHT_TRIG, OUTPUT);
  pinMode(RIGHT_ECHO, INPUT);

  pinMode(LEFT_TRIG, OUTPUT);
  pinMode(LEFT_ECHO, INPUT);

  myServo.attach(SERVO_PIN);
  myServo.write(90); // başlanğıc düz

  Serial.begin(9600);

  randomSeed(analogRead(0));
}

long getDistance(int trigPin, int echoPin) {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  return pulseIn(echoPin, HIGH) * 0.034 / 2;
}

// ------------------ Servo təhlükəsiz yazma ------------------
void safeServoWrite(int angle) {
  if (angle != lastServoAngle) {
    myServo.write(angle);
    lastServoAngle = angle;
  }
}
// -------------------------------------------------------------

// ------------------ Hərəkət funksiyaları ------------------
void forward(int speedVal) {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, speedVal);

  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENB, speedVal);

  safeServoWrite(90); // düz
}

void backward(int speedVal) {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  analogWrite(ENA, speedVal);

  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENB, speedVal);

  // Geri gedəndə servonu maksimuma çıxar (random sağ və ya sol)
  if (random(0, 2) == 0) {
    safeServoWrite(0);
  } else {
    safeServoWrite(180);
  }
}

void stopMotors() {
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}
// ----------------------------------------------------------

void loop() {
  long frontDistance = getDistance(FRONT_TRIG, FRONT_ECHO);
  long rightDistance = getDistance(RIGHT_TRIG, RIGHT_ECHO);
  long leftDistance = getDistance(LEFT_TRIG, LEFT_ECHO);

  Serial.print("Front: ");
  Serial.print(frontDistance);
  Serial.print(" cm | Right: ");
  Serial.print(rightDistance);
  Serial.print(" cm | Left: ");
  Serial.println(leftDistance);

  // Limitlər
  bool front = frontDistance <= 15;
  bool right = rightDistance <= 15;
  bool left = leftDistance <= 15;

  if (front) {
    Serial.println("Öndə maneə var! Geri gedir...");
    backward(70);
    delay(1200); // 1.2 saniyə geri
    stopMotors();
  } 
  else if (right) {
    Serial.println("Sağda maneə var! Sağa dönür...");
    // Sağa dön
    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, 55);

    digitalWrite(IN3, LOW);
    digitalWrite(IN4, HIGH);
    analogWrite(ENB, 55);

    safeServoWrite(180); // sağa bax
    delay(500);
    stopMotors();
  } 
  else if (left) {
    Serial.println("Solda maneə var! Sola dönür...");
    // Sola dön
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, HIGH);
    analogWrite(ENA, 55);

    digitalWrite(IN3, HIGH);
    digitalWrite(IN4, LOW);
    analogWrite(ENB, 55);

    safeServoWrite(0); // sola bax
    delay(500);
    stopMotors();
  } 
  else {
    forward(53);  // irəli sürət 51
  }

  delay(50);
}
