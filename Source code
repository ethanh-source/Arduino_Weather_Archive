//TX complete source code v1.2

#include<VirtualWire.h>
#include <Wire.h>
#include "rgb_lcd.h"

rgb_lcd lcd;

int Rainpin = A0;
int LDRpin = A1;
float rainlevel = 0;
float lightlevel = 0;

const int colorR = 148;
const int colorG = 0;
const int colorB = 220;

float hum = 0;
float voltage = 0;
float percentRH = 0;
int Humidity = A2;
int tempPin = A3;
          
int buzzer = 5;
int ledAlarm = 6;


void setup()
{
  Serial.begin(9600);
  vw_setup(2000); 

  pinMode(ledAlarm,OUTPUT);
  pinMode(buzzer,OUTPUT);

  lcd.begin(16, 2);
  lcd.setRGB(colorR, colorG, colorB);

}

void loop()
{
  float rainlevel = analogRead(A0);
  float lightlevel = analogRead(A1);
  Serial.print("rain level =");
  Serial.print(rainlevel);
  Serial.print(", light level =");
  Serial.print(lightlevel);

  if(rainlevel<250 && lightlevel<600){
    Serial.println(", Heavy thunderstorm with risk of flooding, 'sent:5'");
    send("5");
    display();
    lcd.print("Heavy thunderstorm");
    readings();
    alarm();
    delay(1000);  
    }
  else if(rainlevel<1000 && rainlevel>250 && lightlevel<600){
    Serial.println(", Light to moderate rain detected, 'sent:4'");
    send("4");
    display();
    lcd.print("Light rain");
    readings();
    delay(1000);
  }
  else if(rainlevel<1000 && rainlevel>250 && lightlevel>600){
    Serial.println(", Light Sunshower, 'sent:3'");
    send("3");
    display();
    lcd.print("Light sunshower");
    readings();
    delay(1000);
  }
  else if(rainlevel<1000 && lightlevel>600){
    Serial.println(", Heavy sunshower with risk of flooding, 'sent:2'");
    send("2");
    display();
    lcd.print("Heavy sunshower");
    readings();
    alarm();
    delay(1000);
  }
  else if(rainlevel>=1000 && lightlevel<600){
    Serial.println(", Sky overcast with no rain detected, 'sent:1'");
    send("1");
    display();
    lcd.print("Overcast,no rain");
    readings();
    delay(1000);
  }
  else if(rainlevel>=1000 && lightlevel>600){
    Serial.println(", Clear skies, 'sent:0'");
    send("0");
    display();
    lcd.print("Clear skies");
    readings();
    delay(1000);
  }
}

void send (char *message)
{
  vw_send((uint8_t *)message, strlen(message));
  // Wait until the whole message is transmitted 
  vw_wait_tx(); 
}

void display(){
  lcd.clear();
  lcd.setCursor(0,0);
}

void readings(){
  hum = analogRead(A2); // Read voltage coming from sensor (value will be between 0-1023)
  voltage = hum * 5.0 / 1024.0; // Translate value into a voltage value
  percentRH = (voltage - 0.788) / 0.0314; // Translate voltage into percent relative humidity

//read the value of A0 and store it in a variable
  int tempVal = analogRead(tempPin);

  //convert the ADC reading to voltage
  float voltage = (tempVal/1024.0)*5.0;

  //Convert the voltage to temperature in degree C
  //The sensor (TMP37) changes 20mV per degree
  //voltage/0.02 = voltage*50
  float temperature = voltage*50;

    // set the cursor to column 0, line 1
    // (note: line 1 is the second row, since counting begins with 0):
    lcd.setCursor(0, 1);

    lcd.print("RH:");
    lcd.print(percentRH,1);
    lcd.print("%");
    lcd.print(" T:");
    lcd.print(temperature,1);
    lcd.print("C");

    delay(1000);
}

void alarm(){
  tone(buzzer,4500,500);
  digitalWrite(ledAlarm,HIGH);
  delay(500);
  digitalWrite(ledAlarm,LOW);
  delay(500);
}

  







//RX complete source code

#include <VirtualWire.h>
#include <ServoTimer2.h>

// a buffer to store the incoming messages
byte message[VW_MAX_MESSAGE_LEN]; 
// size of the message
byte messageLength = VW_MAX_MESSAGE_LEN; 

int ledPin=6;
int servoPin=3;

ServoTimer2 servo1;

void setup()
{
  // Initialize the serial monitor
  Serial.begin(9600);
  Serial.println("Device is ready");
  
  //Initialize the IO and ISR
  vw_setup(2000); // Bits per sec
  vw_rx_start(); // Start the receiver

  
  pinMode(ledPin,OUTPUT);  //set led pin as an output
  servo1.attach(servoPin);  //set pin as servo output
  //delay(500); 
}

void loop()
{
  if (vw_get_message(message, &messageLength)) 
  {
    Serial.print("Received: ");
    for (int i = 0; i < messageLength; i++)
    {    
      Serial.write(message[i]);
        if(message[i]=='0')
        {
        digitalWrite(ledPin,LOW);     
        servo1.write(900); 
        delay(300);
        Serial.print(", clear skies");
        }
       else if(message[i]=='1')
       {
        digitalWrite(ledPin,HIGH); 
        servo1.write(900); 
        delay(300);
        Serial.print("House Lights ON, sky overcast with no rain");
       }
       else if(message[i]=='2')
       {
        digitalWrite(ledPin,LOW);
        servo1.write(1600); 
        delay(300);
        //tone(buzzer,4500,500);
        //digitalWrite(ledAlarm,HIGH);
        Serial.print(", Windows closed, flood warning activated, Heavy sunshower");
       }
       else if(message[i]=='3')
       {
        digitalWrite(ledPin,LOW);
        servo1.write(1600);
        delay(300);
        Serial.print(", Windows closed, Light sunshower");
       }
       else if(message[i]=='4')
       {
        digitalWrite(ledPin,HIGH);
        servo1.write(1600);
        delay(300);
        Serial.print(", Windows closed, House Lights ON, Light to moderate rain detected");
       }
       else if(message[i]=='5')
       {
        digitalWrite(ledPin,HIGH);  
        servo1.write(1600);
        delay(300);
        
        Serial.print(", Windows closed, House Lights On, flood warning activated, Heavy thunderstorm");
       }
    }
    Serial.println();
  }
}



