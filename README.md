# Arduino---controlling-dor-draw-square


# Code Below:
```cpp
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

int joyX = A1;
int joyY = A0;
byte joyBTN = 2;

byte numberDotInRow = 0;
byte numberDotInCol = 0;
byte count = 0;
byte posX = 0;
byte posY = 0;

bool btnState = HIGH;

byte p1[2];
byte p2[2];
byte p3[2];
byte p4[2];
int btnValue;

int xRead, yRead;
int lastX, lastY;

byte x1, x2, x3, x4, y1, y2, y3, y4;


void setup() {
  Serial.begin(9600);
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

  if (count == 4){

    byte points[4][2] = {
      {x1, y1},
      {x2, y2},
      {x3, y3},
      {x4, y4},
    };

    points[0][0] = p1[0];
    points[0][1] = p1[1];
    points[1][0] = p2[0];
    points[1][1] = p2[1];
    points[2][0] = p3[0];
    points[2][1] = p3[1];
    points[3][0] = p4[0];
    points[3][1] = p4[1];

    // Check row
    for (byte i=0; i<4; i++){
      byte y = points[i][1];

      if (y == 0) {
        numberDotInRow += 1;
      }
    }

    for (byte i=0; i<4; i++){
      byte x = points[i][0];

      for (byte z=0; z<4; z++) {
        byte athorX = points[z][0];

        if (x == athorX){
          numberDotInCol++;
        }
      }

    }


    // check if square
    if (numberDotInRow == 2 && numberDotInCol == 8) {
      Serial.println("correct");
      byte x1ForDis = points[0][0];
      byte x2ForDis;

      for (byte i=0; i<4; i++){
        byte x = points[i][0];
        if (x != x1ForDis){
          x2ForDis = x;
        }
      }

      byte distance = abs(x1ForDis - x2ForDis) - 1;

      byte xMax = max(x1ForDis, x2ForDis);
      byte xMin = min(x1ForDis, x2ForDis);

      for (byte i = xMin + 1; i<xMax; i++){
        lcd.setCursor(i, 0);
        lcd.print('.');
      }
      for (byte i = xMin + 1; i<xMax; i++){
        lcd.setCursor(i, 1);
        lcd.print('.');
      }

      Serial.print(distance);
      
    }

    numberDotInRow = 0;
    numberDotInCol = 0;

  }
  

  delay(50);
}

```
