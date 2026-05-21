# Smart Energy Meter Monitoring System
## What it does
* Measure current
* Measure voltage
* Calculate power
* Show units consumed
* Send data to cloud/mobile
* Alert if usage too high

## Components Required
### Hardware
* ESP8266 (NodeMCU)
* ACS712 Current Sensor
* Voltage Sensor Module (ZMPT101B often used)
* LCD (optional)
* Breadboard
* Jumper wires
* Power supply
### Software
* Arduino IDE or STM32CubeIDE (if STM32 path)
* Blynk / ThingSpeak / web dashboard
* Embedded C / Arduino C++
## Flow Diagram 
<img width="1600" height="639" alt="WhatsApp Image 2026-05-21 at 10 28 59 AM" src="https://github.com/user-attachments/assets/5a013e32-d7f0-461f-96dd-e8774f552c3d" />


## Block Diagram 
<img width="1200" height="836" alt="image" src="https://github.com/user-attachments/assets/3ec0c79d-df06-41f8-84a0-a5bba01cdae2" />

## Program 
```
#define BLYNK_PRINT Serial

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

char auth[] = "YOUR_BLYNK_TOKEN";
char ssid[] = "YOUR_WIFI_NAME";
char pass[] = "YOUR_PASSWORD";

BlynkTimer timer;

const int sensorPin = A0;

// Fixed voltage (can replace later with voltage sensor)
float voltage = 230.0;

void sendSensorData()
{
  int adcValue = analogRead(sensorPin);

  // Convert ADC to voltage
  float sensorVoltage = adcValue * (3.3 / 1023.0);

  // ACS712 center point
  float offsetVoltage = 2.5;

  // Sensitivity (ACS712 5A = 185mV/A)
  float sensitivity = 0.185;

  // Calculate current
  float current = (sensorVoltage - offsetVoltage) / sensitivity;

  if (current < 0)
    current = -current;

  // Calculate power
  float power = voltage * current;

  Serial.println("----- Energy Meter -----");
  Serial.print("ADC Value: ");
  Serial.println(adcValue);

  Serial.print("Current: ");
  Serial.print(current);
  Serial.println(" A");

  Serial.print("Voltage: ");
  Serial.print(voltage);
  Serial.println(" V");

  Serial.print("Power: ");
  Serial.print(power);
  Serial.println(" W");
  Serial.println("------------------------");

  Blynk.virtualWrite(V0, current);
  Blynk.virtualWrite(V1, power);
  Blynk.virtualWrite(V2, voltage);
}

void setup()
{
  Serial.begin(9600);

  Blynk.begin(auth, ssid, pass);

  timer.setInterval(2000L, sendSensorData);
}

void loop()
{
  Blynk.run();
  timer.run();
}
```
## Output 
<img width="750" height="500" alt="image" src="https://github.com/user-attachments/assets/aa70c641-2fe3-4074-b5c3-8747229611f3" />
