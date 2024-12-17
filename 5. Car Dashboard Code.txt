#include <SPI.h>
#include <mcp_can.h>
#include <LiquidCrystal_I2C.h>  // Assuming you are using an LCD with I2C interface

// Pin for CAN Bus
const int SPI_CS_PIN = 10;
MCP_CAN CAN(SPI_CS_PIN);

// LCD setup
LiquidCrystal_I2C lcd(0x27, 16, 2);  // I2C address of LCD

void setup() {
  Serial.begin(9600);
  lcd.begin(16, 2);  // Initialize the LCD
  lcd.print("Initializing...");

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
  long unsigned int rxId;
  byte len = 0;
  byte buf[8];

  if (CAN_MSGAVAIL == CAN.checkReceive()) {
    CAN.readMsgBuf(&rxId, &len, buf);  // Read received CAN message

    if (rxId == 0x100) {  // TPMS Data
      lcd.clear();
      lcd.print("Tire Pressure: ");
      lcd.print(buf[0]);
    }
    else if (rxId == 0x200) {  // Smoke Alert
      lcd.clear();
      lcd.print("Smoke Detected!");
    }
    else if (rxId == 0x300) {  // Temperature Data
      lcd.clear();
      lcd.print("Temp: ");
      lcd.print(buf[0]);
      lcd.print(" C");
    }
    else if (rxId == 0x400) {  // SOC Data
      lcd.clear();
      lcd.print("SOC: ");
      lcd.print(buf[0]);
      lcd.print(" %");
    }
  }
}
