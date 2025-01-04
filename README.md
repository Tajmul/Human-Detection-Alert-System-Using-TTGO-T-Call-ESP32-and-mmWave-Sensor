# Human-Detection-Alert-System-Using-TTGO-T-Call-ESP32-and-mmWave-Sensor
Human Detection Alert System Using TTGO T-Call ESP32 and mmWave Sensor
This project integrates a TTGO T-Call ESP32 module, SIM800L GSM module, and mmWave C4001 sensor to create a human detection alert system. It detects human presence using the mmWave sensor and sends real-time alerts via SMS while logging data to the Serial Monitor. This system is ideal for remote monitoring and automation in IoT applications.

Features
Human Detection: Utilizes mmWave C4001 sensor for precise human presence detection.
SMS Alerts: Sends automated SMS notifications using the SIM800L GSM module.
Serial Logging: Outputs sensor data and system status for debugging and monitoring.
Customizable: Includes settings for APN, phone numbers, and sensor configurations.
Robust Error Handling: Ensures stable operation with features like GPRS reconnection, SMS throttling, and signal strength validation.
Hardware Requirements
TTGO T-Call ESP32 (with SIM800L module)
mmWave C4001 sensor
GSM-enabled SIM card with SMS capability
Stable 5V 2A power supply for the SIM800L module
Jumper wires and breadboard for connections
Setup
Connect the hardware:
mmWave sensor TX/RX to ESP32 GPIOs
SIM800L module powered and connected to ESP32 UART pins
Update the APN, phone number, and other settings in the code.
Upload the code to the ESP32 using Arduino IDE.
Open the Serial Monitor (115200 baud rate) to observe logs and debug.
Usage
Detect human presence in a monitored area.
Receive SMS alerts remotely when human activity is detected.
Use for security systems, remote monitoring, or smart home automation.
License
This project is open-source and licensed under the MIT License. Feel free to use, modify, and distribute the code as needed.
