#include <SPI.h>
#include <mcp_can.h>
#include <DHT.h>

#define DHTPIN 2     // Pin for DHT11 sensor
#define DHTTYPE DHT11

// Pin for CAN Bus
const int SPI_CS_PIN = 10;
MCP_CAN CAN(SPI_CS_PIN);

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);

  // Initialize CAN bus at 500 kbps
  if (CAN.begin(CAN_500KBPS) == CAN_OK) {
    Serial.println("CAN Bus Initialized.");
  } else {
    Serial.println("CAN Bus Initialization Failed.");
    while (1);
  }

  dht.begin();
  CAN.init_Mask(0, 0, 0);
  CAN.init_Filt(0, 0, 0);
}

void loop() {
  float temperature = dht.readTemperature();  // Read temperature from DHT11
  byte tempData[] = { (byte)temperature };    // Convert float to byte for CAN
  CAN.sendMsgBuf(0x300, 0, sizeof(tempData), tempData);  // Send temperature to CAN bus
  delay(2000);  // Send data every 2 seconds
}
