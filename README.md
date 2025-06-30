# Arduino---controlling-dor-draw-square


# Code Below:
```cpp
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

int joyX = A1;
int joyY = A0;
byte joyBTN = 2;

byte count = 0;

bool btnState = HIGH;

byte p1[2];
byte p2[2];
byte p3[2];
byte p4[2];
int xRead;
int yRead;
byte posX = 0;
byte posY = 0;
int btnValue;
int lastX;
int lastY;

void setup() {
  pinMode(joyBTN, INPUT_PULLUP);
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print('.');
}

void loop() {
  xRead = analogRead(joyX);
  yRead = analogRead(joyY);
  btnValue = digitalRead(joyBTN);

  posX = map(xRead, 0, 1023, 0, 16);
  posY = map(yRead, 0, 1023, 0, 2);  // تعديل ليلائم الأبعاد في الشاشة (16x2)

  // إذا تغيرت الإحداثيات
  if (posX != lastX || posY != lastY) {
    lcd.setCursor(lastX, lastY);
    lcd.print(' ');  // مسح النقطة السابقة

    // طباعة النقطة الجديدة
    lcd.setCursor(posX, posY);
    lcd.print('.');

    lastX = posX;
    lastY = posY;
  }

  // معالجة الضغط على الزر وتخزين النقاط
  if (btnValue == LOW && btnState == HIGH) {
    count++;

    if (count == 1) {
      p1[0] = posX;
      p1[1] = posY;

    } else if (count == 2) {
      p2[0] = posX;
      p2[1] = posY;

    } else if (count == 3) {
      p3[0] = posX;
      p3[1] = posY;

    } else if (count == 4) {
      p4[0] = posX;
      p4[1] = posY;
    }

  }

    btnState = btnValue;
    
    // طباعة النقاط المخزنة
    if (count >= 1) {
      lcd.setCursor(p1[0], p1[1]);
      lcd.print('.');
    }
    if (count >= 2) {
      lcd.setCursor(p2[0], p2[1]);
      lcd.print('.');
    }
    if (count >= 3) {
      lcd.setCursor(p3[0], p3[1]);
      lcd.print('.');
    }
    if (count >= 4) {
      lcd.setCursor(p4[0], p4[1]);
      lcd.print('.');
    }

    if (count == 5) {
      count = 0;
      lcd.clear();
    }

  delay(50);
}

```
