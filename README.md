# Fire-Fighting-Rescuers-Eye-Bot
#include <SoftwareSerial.h>  
#include <Servo.h>         
#include <NewPing.h> 
const int HC_06_TXD = 5;  
const int HC_06_RXD = 6;  
SoftwareSerial bluetooth(HC_06_TXD, HC_06_RXD);  
 char datainput,dataoutput;         
const int LeftMotorForward = 9; 
const int LeftMotorBackward = 10; 
const int RightMotorForward = 8; 
const int RightMotorBackward = 7;  
const int pumpPin = 12;
int frontv,leftv,rightv; 
#define trig_pin A1  
#define echo_pin A2 
#define frontf A3 
#define rightf A4 
#define leftf A5 
#define maximum_distance 200 
boolean goesForward = false; 
int distance; 
 int distanceRight = 0; 
  int distanceLeft = 0; 
  char command; 
  int mode; 
   
NewPing sonar(trig_pin, echo_pin, maximum_distance); 
Servo servo_motor;  
 
 
void setup(){ 
 
Serial.begin(9600); 
  bluetooth.begin(9600); 
pinMode(RightMotorForward, OUTPUT); 
  pinMode(LeftMotorForward, OUTPUT); 
  pinMode(LeftMotorBackward, OUTPUT); 
  pinMode(RightMotorBackward, OUTPUT); 
  pinMode(pumpPin, OUTPUT);
  digitalWrite(pumpPin, LOW);
  pinMode(3, OUTPUT);    
    pinMode(frontf, INPUT); 
    pinMode(leftf, INPUT); 
    pinMode(rightf, INPUT); 
  servo_motor.attach(2);  
 
   servo_motor.write(115); 
  delay(2000); 
  distance = readPing(); 
  delay(100); 
  distance = readPing(); 
  delay(100); 
  distance = readPing(); 
  delay(100); 
  distance = readPing(); 
  delay(100); 
} 
int lookRight(){   
  servo_motor.write(50); 
  delay(500); 
  int distance = readPing(); 
  delay(100); 
  servo_motor.write(115); 
  return distance; 
} 
 
int lookLeft(){ 
  servo_motor.write(170); 
  delay(500); 
  int distance = readPing(); 
 delay(100); 
  servo_motor.write(115); 
  return distance; 
  delay(100); 
} 
 
int readPing(){ 
  delay(70); 
  int cm = sonar.ping_cm(); 
  if (cm==0){ 
    cm=250; 
  } 
  return cm; 
} 
 
void moveStop(){ 
   
  digitalWrite(RightMotorForward, LOW); 
  digitalWrite(LeftMotorForward, LOW); 
  digitalWrite(RightMotorBackward, LOW); 
  digitalWrite(LeftMotorBackward, LOW); 
   delay(50); 
} 
 
void moveForward(){ 
 
  if(!goesForward){ 
 
    goesForward=true; 
     
    digitalWrite(LeftMotorForward, HIGH); 
    digitalWrite(RightMotorForward, HIGH); 
   
    digitalWrite(LeftMotorBackward, LOW); 
    digitalWrite(RightMotorBackward, LOW);  
     delay(50); 
  } 
} 
 
void moveBackward(){ 
 
  goesForward=false; 
 
  digitalWrite(LeftMotorBackward, HIGH); 
  digitalWrite(RightMotorBackward, HIGH); 
   
digitalWrite(LeftMotorForward, LOW); 
  digitalWrite(RightMotorForward, LOW); 
  delay(50); 
   
} 
 
void turnRight(){ 
 
  digitalWrite(LeftMotorForward, HIGH); 
  digitalWrite(RightMotorBackward, HIGH); 
   
  digitalWrite(LeftMotorBackward, LOW); 
  digitalWrite(RightMotorForward, LOW); 
   
  delay(250); 
   
  digitalWrite(LeftMotorForward, HIGH); 
  digitalWrite(RightMotorForward, HIGH); 
   
  digitalWrite(LeftMotorBackward, LOW); 
  digitalWrite(RightMotorBackward, LOW); 
  
   
   
} 
 
void turnLeft(){ 
 
  digitalWrite(LeftMotorBackward, HIGH); 
  digitalWrite(RightMotorForward, HIGH); 
   
  digitalWrite(LeftMotorForward, LOW); 
  digitalWrite(RightMotorBackward, LOW); 
 
  delay(250); 
   
  digitalWrite(LeftMotorForward, HIGH); 
  digitalWrite(RightMotorForward, HIGH); 
   
  digitalWrite(LeftMotorBackward, LOW); 
  digitalWrite(RightMotorBackward, LOW); 
} 
void flame(){  
   Serial.print("distance  "); 
   Serial.println(distance); 
 
  frontv=analogRead(frontf); 
  leftv=analogRead(leftf); 
  rightv=analogRead(rightf); 
  Serial.println(frontv); 
    Serial.println(leftv); 
      Serial.println(rightv); 
  if(frontv<500){ 
 
  
    moveStop(); 
     digitalWrite(pumpPin, HIGH);
     delay(5000); 
      moveStop(); 
       goesForward=false; 
      digitalWrite(pumpPin, LOW);
    Serial.println("front fire"); 
  }else if (rightv<500){ 
    
      moveBackward(); 
      turnRight(); 
    moveStop(); 
    digitalWrite(pumpPin, HIGH);
    delay(5000); 
       
       goesForward=false; 
        digitalWrite(pumpPin, LOW);
    Serial.println("right fire"); 
  }else if(leftv<500){ 
 
     moveBackward();
     turnLeft(); 
    moveStop(); 
    digitalWrite(pumpPin, HIGH);
     delay(5000); 
       
       goesForward=false; 
        digitalWrite(pumpPin, LOW);
    Serial.println("left fire"); 
  }else{ 
     digitalWrite(pumpPin, LOW);
    delay(50); 
 
  if (distance <= 35){ 
    moveStop(); 
    delay(300); 
    moveBackward();
delay(400); 
    moveStop(); 
    delay(300); 
    distanceRight = lookRight(); 
    delay(300); 
    distanceLeft = lookLeft(); 
    delay(300); 
 
    if (distance >= distanceLeft){ 
      turnRight(); 
      moveStop(); 
      delay(50); 
    } 
    else{ 
      turnLeft(); 
      moveStop(); 
       delay(50); 
    } 
  } 
  else{ 
    moveForward();  
  } 
    distance = readPing(); 
} 
 
} 
void moveCar(char command) { 
  switch (command) { 
    case 'u': //forward 
      digitalWrite(LeftMotorForward, HIGH); 
      digitalWrite(LeftMotorBackward , LOW); 
      digitalWrite(RightMotorForward, HIGH); 
      digitalWrite(RightMotorBackward, LOW); 
      break; 
    case 'd': //backward 
      digitalWrite(LeftMotorForward, LOW); 
      digitalWrite(LeftMotorBackward , HIGH); 
      digitalWrite(RightMotorForward, LOW); 
      digitalWrite(RightMotorBackward, HIGH); 
      break; 
    case 'r': //right 
      digitalWrite(LeftMotorForward, HIGH); 
      digitalWrite(LeftMotorBackward , LOW); 
      digitalWrite(RightMotorForward, LOW); 
      digitalWrite(RightMotorBackward, LOW); 
      break; 
    case 'l': //left 
      digitalWrite(LeftMotorForward, LOW); 
      digitalWrite(LeftMotorBackward , LOW); 
      digitalWrite(RightMotorForward, HIGH); 
      digitalWrite(RightMotorBackward, LOW); 
      break; 
   
    default: //stop 
      digitalWrite(LeftMotorForward, LOW); 
      digitalWrite(LeftMotorBackward , LOW); 
      digitalWrite(RightMotorForward, LOW); 
      digitalWrite(RightMotorBackward, LOW); 
      break; 
  } 
}  
void loop() {  
  
   analogWrite(3,128); 
 if (bluetooth.available()) {  
    command = bluetooth.read();  
    moveCar(command); 
    Serial.print(command);  
  if(command=='f'){ 
    mode=99; 
  }else if(command=='j'){ 
    mode=88; 
  } 
     
  }  
   else if (Serial.available()) {  
    dataoutput = Serial.read();  
    bluetooth.write(command);  
  }  
   if( mode==99){ 
    flame(); 
  if(mode==88){ 
    moveCar(command); 
  } 
} 
}
