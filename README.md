#include <Servo.h>

#include <LiquidCrystal.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#define t1 10
#define t2 7

#define t3 13

#define t4 9
#define t5 A0

#define t6 8

Servo myservo;

int distanceThreshold = 100;

int parkingoc = 0;
int barrierState = 0;

void setup() {
lcd.begin(16, 2);
lcd.setCursor(0, 0);
Serial.begin (9600);
myservo.attach(6);
myservo.write(0);
}

long readDistance(int triggerPin, int echoPin)
{
pinMode(triggerPin, OUTPUT);
digitalWrite(triggerPin, LOW);
delayMicroseconds(2);
digitalWrite(triggerPin, HIGH);
delayMicroseconds(10);
digitalWrite(triggerPin, LOW);
pinMode(echoPin, INPUT);
return pulseIn(echoPin, HIGH);
}

void loop()
{
float d1 = 0.01723 * readDistance(t1, t4);
float d2 = 0.01723 * readDistance(t2, t5);
float d3 = 0.01723 * readDistance(t3, t6);
//get all sensor readings

Serial.println("d1 = " + String(d1) + "cm");
Serial.println("d2 = " + String(d2) + "cm");
Serial.println("d3 = " + String(d3) + "cm");
parkingoc = 0;
if (d1<=100 && d2>100)
{
parkingoc +=1;
}
else if(d2<=100 && d1>100)
{
parkingoc +=1;
}
else if(d1<=100 && d2<=100)
{
parkingoc +=2;
}
else
{
parkingoc = 0;
}

if (barrierState == 0)
{
if (d3<=100 && parkingoc<2)
{
barrierState = -1;
myservo.write(90);
}
else
{
barrierState == 0;
}
}
if (barrierState == -1)
{
if (d3>=100 && parkingoc>=2)
{
barrierState = 0;
myservo.write(0);
}
else
{
barrierState == -1;
}
}
if (d1<=100 & d2>100 )
{
lcd.setCursor(0, 0);
lcd.print("Parking left ");
lcd.setCursor(0, 1);
lcd.print(" S2 ");
delay(2);
}
else if (d2<= 100 & d1>100 )
{
lcd.setCursor(0, 0);
lcd.print("Parking left ");
lcd.setCursor(0, 1);
lcd.print(" S1 ");
delay(2);
}
else if (d2>100 && d1>100)
{
lcd.setCursor(0, 0);
lcd.print("Parking left ");
lcd.setCursor(0, 1);
lcd.print(" S1 S2 ");
delay(2);
}
else
{
lcd.setCursor(0, 0);
lcd.print("Parking full ");
lcd.setCursor(0, 1);
lcd.print("Parking full ");
delay(2);
}
}




