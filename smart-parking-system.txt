#include <Servo.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

Servo enterServo, exitServo;

#define ir_enter 2
#define ir_back 4

#define ir_enter_exit 12
#define ir_back_exit 0

#define ir_car1 5
#define ir_car2 6
#define ir_car3 7
#define ir_car4 8
#define ir_car5 9
#define ir_car6 10
// const int ledPin = 13;

LiquidCrystal_I2C lcd(0x27, 20, 4);

int S1 = 0, S2 = 0, S3 = 0, S4 = 0, S5 = 0, S6 = 0;
int flag1 = 0, flag2 = 0;
int slot = 6;

void setup() {
  Serial.begin(9600);

  // pinMode(ledPin, OUTPUT);
  pinMode(ir_car1, INPUT);
  pinMode(ir_car2, INPUT);
  pinMode(ir_car3, INPUT);
  pinMode(ir_car4, INPUT);
  pinMode(ir_car5, INPUT);
  pinMode(ir_car6, INPUT);

  pinMode(ir_enter, INPUT);
  pinMode(ir_back, INPUT);

  pinMode(ir_enter_exit, INPUT);
  pinMode(ir_back_exit, INPUT);

  enterServo.attach(3);
  enterServo.write(90);

  exitServo.attach(11);
  exitServo.write(90);

  lcd.begin(20, 4);
  lcd.init();
  lcd.backlight();

  lcd.setCursor(0, 1);
  lcd.print("    Car  parking  ");
  lcd.setCursor(0, 2);
  lcd.print("       System     ");
  delay(800);
  lcd.clear();

  Read_Sensor();

  int total = S1 + S2 + S3 + S4 + S5 + S6;
  slot = 6 - total;
}

void loop() {
  // digitalWrite(ledPin, HIGH);
  // delay(1000);
  // digitalWrite(ledPin, LOW);
  // delay(1000);
  Read_Sensor();

  lcd.setCursor(0, 1);
  lcd.print("   Have Slot: ");
  lcd.print(slot);
  lcd.print("    ");

  lcd.setCursor(0, 0);
  if (S1 == 1) {
    lcd.print("S1:Fill ");
  } else {
    lcd.print("S1:Empty");
  }

  lcd.setCursor(10, 0);
  if (S2 == 1) {
    lcd.print("S2:Fill ");
  } else {
    lcd.print("S2:Empty");
  }

  lcd.setCursor(0, 3);
  if (S3 == 1) {
    lcd.print("S3:Fill ");
  } else {
    lcd.print("S3:Empty");
  }

  lcd.setCursor(10, 3);
  if (S4 == 1) {
    lcd.print("S4:Fill ");
  } else {
    lcd.print("S4:Empty");
  }

  lcd.setCursor(0, 2);
  if (S5 == 1) {
    lcd.print("S5:Fill ");
  } else {
    lcd.print("S5:Empty");
  }

  lcd.setCursor(10, 2);
  if (S6 == 1) {
    lcd.print("S6:Fill ");
  } else {
    lcd.print("S6:Empty");
  }

  if (digitalRead(ir_enter) == 0 && flag1 == 0) {
    if (slot > 0) {
      flag1 = 1;
      if (flag2 == 0) {
        enterServo.write(180); // Use enterServo for ir_enter
        slot = slot - 1;
      }
    } else {
      lcd.setCursor(0, 0);
      lcd.print(" Sorry Parking Full ");
      delay(800);
    }
  }

  if (digitalRead(ir_back) == 0 && flag2 == 0) {
    flag2 = 1;
    if (flag1 == 0) {
      enterServo.write(90); // Use enterServo for ir_back
      // slot = slot + 1;  // Remove this line to avoid increasing the counter
      flag2 = 0;  // Reset flag2 after handling exit operation
    }
  }

  if (digitalRead(ir_enter_exit) == 0 && flag1 == 0) {
    if (slot > 0) {
      flag1 = 1;
      if (flag2 == 0) {
        exitServo.write(180); // Use exitServo for ir_enter_exit
        slot = slot + 1;
      }
    } else {
      lcd.setCursor(0, 0);
      lcd.print(" Sorry Parking Full ");
      delay(800);
    }
  }

  if (digitalRead(ir_back_exit) == 0 && flag2 == 0) {
    flag2 = 1;
    if (flag1 == 0) {
      exitServo.write(90); // Use exitServo for ir_back_exit
      slot = slot - 1;
      flag2 = 0;  // Reset flag2 after handling exit operation
    }
  }

  if (flag1 == 1 && flag2 == 1) {
    delay(800);
    enterServo.write(90);
    exitServo.write(90);
    flag1 = 0, flag2 = 0;
  }

  delay(500);  // Adjusted delay value, use a larger value for better performance
}

void Read_Sensor() {
  S1 = 0, S2 = 0, S3 = 0, S4 = 0, S5 = 0, S6 = 0;

  if (digitalRead(ir_car1) == 0) { S1 = 1; }
  if (digitalRead(ir_car2) == 0) { S2 = 1; }
  if (digitalRead(ir_car3) == 0) { S3 = 1; }
  if (digitalRead(ir_car4) == 0) { S4 = 1; }
  if (digitalRead(ir_car5) == 0) { S5 = 1; }
  if (digitalRead(ir_car6) == 0) { S6 = 1; }
}
