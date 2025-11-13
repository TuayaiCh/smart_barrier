const int buttonPin = 2;        
const int limitSwitchPin = 3;   
const int limitSwitchPin2 = 4;  
const int trigPin = 5;          
const int echoPin = 6;          
#define BUZZER_PIN 12           


// มอเตอร์ตัวที่ 1
const int motor1ForwardPin = 8;
const int motor1BackwardPin = 9;

// มอเตอร์ตัวที่ 2
const int motor2ForwardPin = 10;   
const int motor2BackwardPin = 11;  

bool motorRunning = false;
long duration;
int distance;
//จอ
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);
bool countdownStarted = false;


void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(limitSwitchPin, INPUT_PULLUP);
  pinMode(limitSwitchPin2, INPUT_PULLUP);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  pinMode(motor1ForwardPin, OUTPUT);
  pinMode(motor1BackwardPin, OUTPUT);
  pinMode(motor2ForwardPin, OUTPUT);
  pinMode(motor2BackwardPin, OUTPUT);

  Serial.begin(9600);
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("OLED ไม่พบ"));
    while (true)
      ;
  }

  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(25, 18);
  display.print("Barrier");
  display.setCursor(25, 37);
  display.print("Ready");
  display.display();
  delay(1000);
}

void loop() {
  bool buttonPressed = digitalRead(buttonPin) == LOW;


  if (buttonPressed && !motorRunning) {
    display.clearDisplay();
    display.display();
    delay(500);
    distance = readUltrasonic();
    Serial.print("ระยะเริ่ม: ");
    Serial.println(distance);

    if (distance < 1.5) {
      Serial.println("มีสิ่งกีดขวาง มอเตอร์ไม่ทำงาน");
      while (readUltrasonic() < 1.5) {
        Serial.println("ที่กั้นหยุดทำงาน...");
        delay(200);
        tone(BUZZER_PIN, 1000);
        display.clearDisplay();
        display.setCursor(5, 25);
        display.setTextSize(2);
        display.print("Waiting...");
        display.display();
        delay(500);
      }
      moveForward();
      motorRunning = true;
      Serial.println("เคลียร์!! กำลังเดินหน้า...");
      noTone(BUZZER_PIN);

    } else {
      Serial.println("กำลังขยับแผงกั้น...");
      moveForward();
      motorRunning = true;
    }
  }

  if (motorRunning) {
    distance = readUltrasonic();
    Serial.print("Live Distance: ");
    Serial.println(distance);

    // ถ้าระยะใกล้เกินไปให้หยุดรอ
    if (distance < 1.5) {
      Serial.println("ตรวจพบสิ่งกีดขวาง ที่กั้นกำลังหยุด...");
      tone(BUZZER_PIN, 500);
      stopMotor();

      // รอจนกว่าระยะจะปลอดภัย
      while (readUltrasonic() < 1.5) {
        Serial.println("ที่กั้นหยุดทำงาน...");
        delay(200);
        display.clearDisplay();
        display.setCursor(5, 25);
        display.setTextSize(2);
        display.print("Waiting...");
        display.display();
        delay(500);
      }
      Serial.println("เคลียร์!! กำลังเดินหน้า...");
      noTone(BUZZER_PIN);
      moveForward();
    }

    // ถึง limit switch ให้ถอยหลัง
    if (digitalRead(limitSwitchPin) == LOW) {
      Serial.println("รอคนข้าม...");
      stopMotor();
      countdownStarted = true;
      startCountdown(10);
      while (digitalRead(limitSwitchPin2) == HIGH) {
        Serial.println("กำลังถอยกลับ...");
        moveBackward();
      }
      Serial.println("ทำงานเสร็จสิ้น ");
      stopMotor();
      motorRunning = false;
      display.clearDisplay();
      display.setTextSize(3);
      display.setCursor(40, 25);
      display.print("End.");
      display.display();
      delay(2000);
      display.clearDisplay();
      display.print("");
      display.display();
    }
  }

  delay(100);
}

void moveForward() {
  digitalWrite(motor1ForwardPin, LOW);
  digitalWrite(motor1BackwardPin, HIGH);
  digitalWrite(motor2ForwardPin, LOW);
  digitalWrite(motor2BackwardPin, HIGH);
  display.clearDisplay();
  display.setTextSize(3);
  display.setCursor(40, 25);
  display.print("-->");
  display.display();
}

void moveBackward() {
  digitalWrite(motor1ForwardPin, HIGH);
  digitalWrite(motor1BackwardPin, LOW);
  digitalWrite(motor2ForwardPin, HIGH);
  digitalWrite(motor2BackwardPin, LOW);
}

void stopMotor() {
  digitalWrite(motor1ForwardPin, LOW);
  digitalWrite(motor1BackwardPin, LOW);
  digitalWrite(motor2ForwardPin, LOW);
  digitalWrite(motor2BackwardPin, LOW);
}

int readUltrasonic() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  int cm = duration * 0.034 / 2;
  return cm;
}

void startCountdown(int seconds) {
  for (int i = seconds; i >= 2; i--) {
    display.clearDisplay();
    display.setCursor(50, 25);
    display.setTextSize(3);
    display.print(i);
    display.display();
    delay(500);
  }
  display.clearDisplay();
  display.setTextSize(2);
  display.setCursor(10, 25);
  display.print("pull back");
  display.display();

  // รีเซ็ตสถานะหลังจบ
  delay(2000);
  countdownStarted = false;
}
