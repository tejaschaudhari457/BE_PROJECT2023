#include <SPI.h>
#include <mcp_can.h>

// Pin for CAN Bus
const int SPI_CS_PIN = 10;
MCP_CAN CAN(SPI_CS_PIN);

// Assume an analog input for battery voltage
const int batteryVoltagePin = A1;

void setup() {
  Serial.begin(9600);

  // Initialize CAN bus at 500 kbps
  if (CAN.begin(CAN_500KBPS) == CAN_OK) {
    Serial.println("CAN Bus Initialized.");
  } else {
    Serial.println("CAN Bus Initialization Failed.");
    while (1);
  }

  CAN.init_Mask(0, 0, 0);
  CAN.init_Filt(0, 0, 0);
}

void loop() {
  int batteryVoltage = analogRead(batteryVoltagePin); // Read battery voltage
  byte socData[] = { (byte)(batteryVoltage / 4) };  // Map analog reading to percentage
  CAN.sendMsgBuf(0x400, 0, sizeof(socData), socData);  // Send SOC data to CAN bus
  delay(2000);  // Send data every 2 seconds
}
