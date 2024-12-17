#include <SPI.h>
#include <mcp_can.h>

// Pin for CAN Bus
const int SPI_CS_PIN = 10;
MCP_CAN CAN(SPI_CS_PIN);

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
  // Example TPMS data (tire pressure readings in PSI)
  byte tirePressureData[] = { 32, 34, 30, 33 };  // Pressure for 4 tires
  CAN.sendMsgBuf(0x100, 0, sizeof(tirePressureData), tirePressureData); // Sending data to CAN bus
  delay(1000); // Send data every 1 second
}
