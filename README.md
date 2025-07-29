#include <LiquidCrystal.h>

// LCD pins: RS, E, D4, D5, D6, D7
LiquidCrystal led(7, 6, 5, 4, 3, 2);

// Ultrasonic sensor pins
#define TRIG_PIN 9
#define ECHO_PIN 8

// Bin configuration
#define BIN_DEPTH 100.0 // Bin height in cm
#define BIN_ID 12345UL
Const float LATITUDE = -12.80629;
Const float LONGITUDE = 28.23835;

void setup() {
  lcd.begin(16, 2);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  lcd.setCursor(0, 0);
  lcd.print("Smart Bin System");
  delay(2000);
  lcd.clear();
}

void loop() {
  float distance = getDistance();
  int fullness = (int)((1.0 - (distance / BIN_DEPTH)) * 100.0);
  if (fullness < 0) fullness = 0;
  if (fullness > 100) fullness = 100;

  // Line 1: Bin ID and Fill %
  lcd.setCursor(0, 0);
  lcd.print("ID:");
  lcd.print(BIN_ID);
  lcd.setCursor(11, 0);
  lcd.print(fullness);
  lcd.print("%");

  // Line 2: GPS or Warning
  lcd.setCursor(0, 1);
  if (fullness >= 80) {
    lcd.print("Status: BIN FULL  ");
  } else {
    lcd.print("Lat:");
    lcd.print(LATITUDE, 1); // Only 1 decimal to fit
  }

  delay(3000);

  // Optional second screen: Longitude
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Lon:");
  lcd.print(LONGITUDE, 1); // Limited decimals for LCD width

  lcd.setCursor(0, 1);
  lcd.print("Level: ");
  lcd.print(fullness);
  lcd.print("%");

  delay(3000);
  lcd.clear();
}

float getDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH);
  float distance = duration * 0.034 / 2;
  if (distance > BIN_DEPTH || distance <= 0) {
    distance = BIN_DEPTH;
  }
  return distance;
}
Payload decoding code in The Things Network Console
{
  "f_port": 1,
  "frm_payload": "qqqqqqqquE6PTw==",
  "decoded_payload": {
    "Distance": 43690
  },
  "rx_metadata": [
    {
      "gateway_ids": {
        "gateway_id": "test"
      },
      "rssi": 42,
      "channel_rssi": 42,
      "snr": 4.2
    }
  ],
  "settings": {
    "data_rate": {
      "lora": {
        "bandwidth": 125000,
        "spreading_factor": 7
      }
    },
    "frequency": "868000000"
  }
}
