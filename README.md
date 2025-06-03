# ARDUINO BASED ANTI THEFT SECURITY SYSTEM
The Arduino-Based Anti-Theft Security System is a simple yet effective project that detects unauthorized movement using sensors and triggers an audible alarm using a buzzer. This system is ideal for protecting homes, shops, lockers, or restricted areas from theft or intrusions
## Features

- Detects unauthorized item removal or pressure changes
- Triggers a **buzzer** alarm instantly
- Simple hardware setup using Arduino
- Low power and cost-efficient

---

## Components Used

- Arduino UNO/Nano
- FSR (Force Sensitive Resistor)
- Buzzer
- Resistor (10kΩ)
- Breadboard and jumper wires
- USB cable or 9V power supply

---

## How It Works

1. The **FSR sensor** is placed under a valuable item.
2. When the item is lifted or disturbed, the pressure on the FSR changes.
3. The **Arduino** reads this change and **activates the buzzer**.
4. After a short delay, the system resets and resumes monitoring.

---

## Arduino Code

#define FSRTHD 100        // Change based on your object

#include "OneButton.h"   // OneButton library for easy button functionality

//Pin declarations
int sesnorPin = A0;
int buzPin = 11;
int led = 2;
int btnPin = 7;

OneButton button(btnPin, true); 

int sensorData;
static int State;

void setup() 
{
  Serial.begin(9600);
  pinMode(led, OUTPUT);
  pinMode(buzPin, OUTPUT);
    
  button.attachClick(singleclick_Func);   // link the function to be called on a singleclick event
  button.attachDoubleClick(doubleclick_Func); // link the function to be called on a doubleclick event
  button.attachLongPressStart(longclick_Func); // link the function to be called on a longclick event
}

void loop() 
{
   sensorData = analogRead(sesnorPin); // Read sensor data
   Serial.print(sensorData); 
   Serial.print("  \t");

   button.tick();     // check the status of the button
   delay(10); 
   
   switch(State)
   {
    case 1:         // Activated by button
    {
      digitalWrite(led, HIGH);
      Serial.println("Anti-theft mode ACTIVE");
      if(sensorData < FSRTHD)
      {
        buz(true);
      } 
      break;
    }
    default:   //Not activated by default
    {
      buz(false);
      digitalWrite(led, LOW);
      Serial.println("Anti-theft mode INACTIVE");
      break;
    }
   }
}

//Functions
//Callback for single click
void singleclick_Func()
{  
  if(sensorData >= FSRTHD)
  {                               
   State = 1; 
  }      
}

//Callback for Double click
void doubleclick_Func()
{                             
  if(sensorData >= FSRTHD)
  { 
    State = 0;
  }    
}

//Callback for long click 
void longclick_Func()
{ 
  if(sensorData < FSRTHD)
  { 
    State = 0;
  }
}

//Buzzer control
void buz(boolean x)
{
  if(x == true)
  {
   analogWrite(buzPin, 200);
   delay(110);
   analogWrite(buzPin, 100);
   delay(110);
  }
  else if(x == false)
  {
    digitalWrite(buzPin, LOW);
  }
}
