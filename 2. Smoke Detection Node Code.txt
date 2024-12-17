#include <SPI.h>
#include <mcp_can.h>

// Pin for CAN Bus
const int SPI_CS_PIN = 10;
MCP_CAN CAN(SPI_CS_PIN);

const int smokeSensorPin = A0;  // Assuming smoke sensor connected to analog pin A0

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
  int smokeLevel = analogRead(smokeSensorPin); // Read smoke sensor value
  if (smokeLevel > 800) {  // If smoke is detected above threshold
    byte smokeAlert[] = {0x01}; // 0x01 indicates smoke detected
    CAN.sendMsgBuf(0x200, 0, sizeof(smokeAlert), smokeAlert); // Send alert to CAN bus
    Serial.println("Smoke Detected!");
  }
  delay(1000); // Check every 1 second
}
