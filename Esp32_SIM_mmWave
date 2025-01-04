/*
  This code is open-source and released under the MIT License.

  Copyright (c) [2024] [Md. Tajmul Haque]

  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files (the "Software"), to deal
  in the Software without restriction, including without limitation the rights
  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
  copies of the Software, and to permit persons to whom the Software is
  furnished to do so, subject to the following conditions:

  The above copyright notice and this permission notice shall be included in
  all copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
  THE SOFTWARE.
*/

#define TINY_GSM_MODEM_SIM800 // Define GSM modem model
#include <Wire.h>
#include <TinyGsmClient.h> // https://github.com/vshymanskyy/TinyGSM

// TTGO T-Call Pins for SIM800L
#define MODEM_RST 5
#define MODEM_PWRKEY 4
#define MODEM_POWER_ON 23
#define MODEM_TX 27
#define MODEM_RX 26

// mmWave Sensor Pins
#define SENSOR_TX 12  // mmWave RX connected to ESP32 TX
#define SENSOR_RX 14  // mmWave TX connected to ESP32 RX
#define DETECT_PIN 34 // Optional: GPIO pin for digital detection output

// APN and SIM Settings
#define APN "internet"         // Replace with your network provider's APN
#define GPRS_USER ""           // Leave empty if not required
#define GPRS_PASS ""           // Leave empty if not required
#define PHONE_NUMBER "+8801727266040" // Replace with your phone number

#define SerialMon Serial
#define SerialAT Serial1
#define SensorSerial Serial2 // Hardware Serial for mmWave sensor

TinyGsm modem(SerialAT);

unsigned long lastSmsTime = 0;       // Last SMS sent time
const unsigned long smsInterval = 60000; // Minimum interval between SMS (1 minute)

void setup() {
  // Initialize Serial Monitor
  SerialMon.begin(115200);
  delay(10);

  // Initialize mmWave Sensor Serial
  SensorSerial.begin(115200, SERIAL_8N1, SENSOR_RX, SENSOR_TX);
  pinMode(DETECT_PIN, INPUT); // Configure detect pin if used

  // Set up SIM800L power and communication pins
  pinMode(MODEM_PWRKEY, OUTPUT);
  pinMode(MODEM_POWER_ON, OUTPUT);
  pinMode(MODEM_RST, OUTPUT);

  digitalWrite(MODEM_PWRKEY, LOW);
  digitalWrite(MODEM_POWER_ON, HIGH);
  digitalWrite(MODEM_RST, HIGH);

  // Start communication with SIM800L
  SerialAT.begin(9600, SERIAL_8N1, MODEM_RX, MODEM_TX);
  delay(3000);

  SerialMon.println("Initializing modem...");
  if (!modem.restart()) {
    SerialMon.println("Modem restart failed. Retrying...");
    delay(5000);
    ESP.restart(); // Reboot if the modem fails to restart
  }

  String modemInfo = modem.getModemInfo();
  SerialMon.println("Modem Info: " + modemInfo);

  SerialMon.println("Connecting to network...");
  if (!modem.gprsConnect(APN, GPRS_USER, GPRS_PASS)) {
    SerialMon.println("Failed to connect to GPRS. Restarting...");
    delay(5000);
    ESP.restart(); // Reboot if GPRS connection fails
  }
  SerialMon.println("GPRS connected.");
}

void sendSmsAlert(const char *message) {
  unsigned long currentTime = millis();
  if (currentTime - lastSmsTime >= smsInterval) {
    if (modem.sendSMS(PHONE_NUMBER, message)) {
      SerialMon.println("SMS sent successfully!");
    } else {
      SerialMon.println("Failed to send SMS.");
    }
    lastSmsTime = currentTime;
  } else {
    SerialMon.println("SMS alert skipped to prevent spamming.");
  }
}

void loop() {
  // Reconnect to GPRS if disconnected
  if (!modem.isGprsConnected()) {
    SerialMon.println("GPRS disconnected. Reconnecting...");
    modem.gprsConnect(APN, GPRS_USER, GPRS_PASS);
  }

  // Check for sensor data
  if (SensorSerial.available()) {
    String sensorData = SensorSerial.readStringUntil('\n'); // Parse sensor data
    SerialMon.println("mmWave Data: " + sensorData);

    // Check for human detection (based on sensor's data format)
    if (sensorData.indexOf("Human Detected") >= 0) {
      SerialMon.println("Human detected by mmWave sensor!");
      sendSmsAlert("Alert: Human detected by mmWave sensor!");
    }
  }

  // Check digital detection pin (if used)
  if (digitalRead(DETECT_PIN) == HIGH) {
    SerialMon.println("Human detected by digital pin!");
    sendSmsAlert("Alert: Human detected by mmWave sensor (digital pin)!");
    delay(1000); // Prevent duplicate alerts
  }

  delay(100); // Small delay for stability
}
