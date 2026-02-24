#include<LiquidCrystal.h>
const int rs=13,en=12,d4=11,d5=10,d6=9,d7=8; 
LiquidCrystal lcd(rs,en,d4,d5,d6,d7);
#include <stdio.h> 
#include <stdlib.h> 
#include <string.h>
#include <SoftwareSerial.h>
SoftwareSerial GSMSerial(6, 7);/* (Rx, Tx) */
int Right_IR=14;
int Left_IR=15;
int IN1=16;
int IN2=17;
int IN3=18; 
int IN4=19;
#define trigPin 4
#define echoPin 5
void SEND_SMS(String num, String str );
 long duration;
int distance; void setup()
{
 pinMode(IN1, OUTPUT);
 pinMode(IN2, OUTPUT);
 pinMode(IN3, OUTPUT);
 pinMode(IN4, OUTPUT);

 pinMode(Left_IR, INPUT);
 pinMode(Right_IR,INPUT);

 digitalWrite(IN1,LOW);
 digitalWrite(IN2,LOW); 
 digitalWrite(IN3,LOW); 
 digitalWrite(IN4,LOW);
   pinMode(trigPin, OUTPUT);

   pinMode(echoPin, INPUT); 
  Serial.begin(9600); 
 GSMSerial.begin(9600); 
 lcd.begin(16,2);

Serial.begin(9600); 
lcd.clear(); 
lcd.setCursor(0,0);
lcd.print("Railway Crack");
 lcd.setCursor(0,1);
 lcd.print(" Monitoring");
 Serial.println("Railway Monitoring");
 delay(500);

SEND_SMS("+916362375548","Railway Monitoring System.."); 
delay(2000);
}
void loop()
{
 FORWARD();
 Ultra_Check(); 
 CRACK_CHECK();
}
void CRACK_CHECK()
{

if(digitalRead(Left_IR)==HIGH)
{
 lcd.clear();
 lcd.print("Left Side Crack");
 lcd.setCursor(0,1); 
 lcd.print(" Detected");
  Serial.println("$Left Side Crack Detected#"); 
  STOP();
  String one ="Left Side Crack Detected AT:";

  String two ="http://maps.google.com/maps?&z=15&mrt=yp&t=k&q=";
  String message= one + two +"12.8251583,77.5114552";
  // Convert String to char array
  int str_len = message.length() + 1; 
  char textmessage[str_len];A
  message.toCharArray(textmessage,str_len);
  Serial.println('$'+String(textmessage)+'#'); 
  SEND_SMS("+916362375548", textmessage);
 delay(5000); 
 while(digitalRead(Left_IR)==HIGH);
}
if(digitalRead(Right_IR)==HIGH)
{
 lcd.clear();
 lcd.print("Right Side Crack");
 lcd.setCursor(0,1); 
 lcd.print(" Detected");
 Serial.println("$Right Side Crack Detected#");
 STOP();
  String one ="Right Side Crack Detected AT:"; 

    String two ="http://maps.google.com/maps?&z=15&mrt=yp&t=k&q=";

    String message= one + two +"12.8251583,77.5114552";
    // Convert String to char array
    int str_len = message.length() + 1; 
    char textmessage[str_len];
    message.toCharArray(textmessage,str_len); 
    Serial.println('$'+String(textmessage)+'#'); 
    SEND_SMS("+916362375548", textmessage);
    delay(5000); 
while(digitalRead(Right_IR)==HIGH);
}

}
void Ultra_Check()
{
 digitalWrite(trigPin, LOW); 
 delayMicroseconds(5);
 // Trigger the sensor by setting the trigPin high for 10 microseconds: 
 digitalWrite(trigPin, HIGH);
 delayMicroseconds(10); 
 digitalWrite(trigPin, LOW);
// Read the echoPin, pulseIn() returns the duration (length of the pulse) in microseconds: 
duration = pulseIn(echoPin, HIGH);
// Calculate the distance:
distance = duration * 0.034 / 2;

// Print the distance on the Serial Monitor (Ctrl+Shift+M): 
Serial.print("Distance = ");
Serial.print(distance); 
Serial.println(" cm"); 
lcd.clear(); 
lcd.print("Distance= ");
lcd.print(distance); 
lcd.print("cm");

delay(500); 
if(distance<50)
{
// Serial.println("$Vehicle Detected Distance of 50cm#"); 
lcd.clear();
lcd.print("Object Detected "); 
lcd.setCursor(0,1); 
lcd.print("Distance of 50cm.."); 
Serial.println("$Object Detected#"); 
STOP();
SEND_SMS("+916362375548", "Object Detected"); 
delay(2000);

}

}
void FORWARD()
{
lcd.clear(); lcd.print("Moving..");
digitalWrite(IN1,HIGH); digitalWrite(IN2,LOW);
digitalWrite(IN3,HIGH); digitalWrite(IN4,LOW);
}
void STOP()
{
 digitalWrite(IN1,LOW);
 digitalWrite(IN2,LOW); 
 digitalWrite(IN3,LOW); 
 digitalWrite(IN4,LOW); 
 delay(100);
}
void SEND_SMS(String num, String str )
{
 GSMSerial.println("AT+CMGF=1"); //Sets the GSM Module in Text Mode 
 delay(1000); // Delay of 1000 milli seconds or 1 second

GSMSerial.print("AT+CMGS=\""); // Send the SMS number 
GSMSerial.print(num);
GSMSerial.println("\""); 
delay(1000);

GSMSerial.println(str);// The SMS text you want to send 
delay(100);
GSMSerial.println((char)26);// ASCII code of CTRL+Z
delay(1000); Serial.println("SMS Sent..");
}
