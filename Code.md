# ECE-387-Individual-Final
//const int flexThresh = 250;
#include <LiquidCrystal.h>
#include<Wire.h>
const int MPU = 0x68;
int16_t AcX, AcY, AcZ, Tmp, GyX, GyY, GyZ;
boolean properForm;
//I use GyX and GyY
int buttonState;
int countCurl = 0;
int ledPin = 11;
int buttonPin = 10;
int piezo = 9;

unsigned long lastDebounceTime = 0;
unsigned long debounceDelay = 50;
const int rs = 3, en = 4, d4 = 5, d5 = 6, d6 = 7, d7 = 8;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup() {
  Wire.begin();
  Wire.beginTransmission(MPU);
  Wire.write(0x6B);
  Wire.write(0);
  Wire.endTransmission(true);
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
  pinMode(piezo, OUTPUT);
  digitalWrite(ledPin, LOW);
  pinMode(buttonPin, INPUT);
  lcd.begin(16, 2);
  lcd.setCursor(0, 0);
  lcd.print("Begin Curling");
  lcd.setCursor(0, 1);
  lcd.print("Curl # ");
}
void loop() {
  lcd.setCursor(7, 1);
  int buttonState = digitalRead(buttonPin);

  //  int sensorValue = analogRead(A3);
  //
  //  Serial.println(sensorValue);
  //  delay(1);
  //  if (sensorValue < flexThresh) {
  //    digitalWrite(led, HIGH);
  //
  //  }
  //  else
  //  {
  //    digitalWrite(led, LOW);
  //
  //  }

  // above was a portion of code used with the muscle sensor.

  Wire.beginTransmission(MPU);
  Wire.write(0x3B);
  Wire.endTransmission(false);
  Wire.requestFrom(MPU, 12, true);
  AcX = Wire.read() << 8 | Wire.read();
  AcY = Wire.read() << 8 | Wire.read();
  AcZ = Wire.read() << 8 | Wire.read();
  GyX = Wire.read() << 8 | Wire.read();
  GyY = Wire.read() << 8 | Wire.read();
  GyZ = Wire.read() << 8 | Wire.read();

  Serial.print("Accelerometer: ");
  Serial.print("X = "); Serial.print(AcX); ///////////////////**********************
  Serial.print(" | Y = "); Serial.print(AcY); ///////////////////**********************
  Serial.print(" | Z = "); Serial.println(AcZ);

  Serial.print("Gyroscope: ");
  Serial.print("X = "); Serial.print(GyX);
  Serial.print(" | Y = "); Serial.print(GyY);
  Serial.print(" | Z = "); Serial.println(GyZ);
  Serial.println(" ");
  Serial.print(countCurl);
  Serial.println(" ");
  delay(333);
  if (AcX < 8500 && AcX > -4000 && AcY > -5000 && AcY < 6000) { //check arm level
    digitalWrite(ledPin, HIGH);
    properForm == true;
  }

  else {
    digitalWrite(ledPin, LOW);
    properForm == false;


  }
  if (buttonState == HIGH && properForm == true) { //actively flexing and doing well
    //what is actively missing is a value from a second accelerometer saying that the arm has gone up and down and succesfully completed the flex
    countCurl++;
    noTone(piezo);
    delay(1000);
    digitalWrite(ledPin, HIGH);
  }
  else if (buttonState == HIGH && properForm == false){
    digitalWrite(ledPin, LOW);
    delay(100);
    digitalWrite(ledPin, HIGH);
    delay(100);
  }
}
