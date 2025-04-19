# esp32-IRremote
esp32-IRremote
```cpp
#include <IRremote.hpp>

#define IR_RECEIVE_PIN 15  // Jetzt definitiv korrekt!

void setup() {
  Serial.begin(115200);
  delay(200);

  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);
  Serial.println("IR-Empfänger bereit. Bitte Taste auf Fernbedienung drücken.");
}

void loop() {
  if (IrReceiver.decode()) {
    Serial.print("Empfangen: 0x");
    Serial.println(IrReceiver.decodedIRData.decodedRawData, HEX);

    Serial.print("Protokoll: ");
    Serial.println(getProtocolString(IrReceiver.decodedIRData.protocol));

    IrReceiver.resume();
  }
}
```
