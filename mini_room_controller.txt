#include <Servo.h>

#define LED1 4
#define LED2 5
#define SWITCH1 2
#define SWITCH2 3
#define TRIG 9
#define ECHO 10
#define SERVO_PIN 6

Servo doorServo;

long duration;
int distance;

bool autoLightState = false;

void setup() {
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);

  pinMode(SWITCH1, INPUT_PULLUP);
  pinMode(SWITCH2, INPUT_PULLUP);

  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);

  doorServo.attach(SERVO_PIN);
  doorServo.write(0); // Door closed

  Serial.begin(9600);
}

void loop() {

  // -------- Manual Switch Control --------
  if (digitalRead(SWITCH1) == LOW) {
    digitalWrite(LED1, HIGH);
  } else {
    digitalWrite(LED1, LOW);
  }

  if (digitalRead(SWITCH2) == LOW) {
    digitalWrite(LED2, HIGH);
  } else {
    digitalWrite(LED2, LOW);
  }

  // -------- Ultrasonic Sensor --------
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);

  duration = pulseIn(ECHO, HIGH);
  distance = duration * 0.034 / 2;

  Serial.print("Distance: ");
  Serial.println(distance);

  // -------- Automatic Control --------
  if (distance > 0 && distance < 20) {
    // Person detected
    digitalWrite(LED1, HIGH);
    digitalWrite(LED2, HIGH);

    doorServo.write(90); // Open door
    autoLightState = true;
  } 
  else if (autoLightState) {
    // No person
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);

    doorServo.write(0); // Close door
    autoLightState = false;
  }

  delay(300);
}
