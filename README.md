# esp32-IRremote

Dieses Projekt zeigt, wie man mit einem **ESP32** und dem **AZ-Delivery KY-022 IR Receiver Modul (CHQ1838)** IR-Codes einer Fernbedienung auslesen kann – insbesondere zum Steuern einer PTZ-Kamera. Die erfassten IR-Codes können später mit einem **KY-005 IR-Sendemodul** wieder gesendet werden.
Hauptsächlich geht es mir um das Aus- und Einschalten des **"AI Trackings"** meiner **HDKATOV PTZ Kamera**. Diese Funktion kann derzeit nur über einer Fernbedienung, nicht aber über die Weboberfläche remote erfolgen. Die chinesische Herstellerfirma war trotz mehrmaliger Nachfrage bisher nicht imstande, uns eine Auskunft zu geben, wie diese AI Tracking Funktion remote ohne Fernbedienung aktiviert und deaktiviert werden kann. Deswegen will ich mir eine Modul bauen, welches an der Kamera befestigt wird, um aus der Ferne, per Wifi, das AI Tracking ein- und auszuschalten.

---

## 📦 Hardware

- ESP32 DevKit (z. B. von AZ-Delivery)
- KY-022 IR-Empfänger (CHQ1838-basiert)
- Standard-Fernbedienung (z. B. PTZ-Remote)
- (später: KY-005 IR-Sender-LED)

---

## 🔌 Anschluss KY-022 an ESP32

| KY-022 Pin | Funktion | Verbindung zum ESP32 |
|------------|----------|----------------------|
| S          | Signal   | GPIO **15**          |
|   | 3,3 Volt | externe Spannung |
| –          | GND      | GND                  |

> Das Modul wurde von mir mit 3.3 V über einen separater Anschluss versorgt. Geht vieleicht auch anders.

---

## 📟 Beispielsketch: IR-Codes empfangen

```cpp
#include <IRremote.hpp>

#define IR_RECEIVE_PIN 15  // Pin für KY-022 OUT

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

## 📋 Erfasste IR-Codes (PTZ-Fernbedienung)

|Taste|Code|Protokoll|
|-----|----|---------|
|Links|0x51D1D|Sony|
|Rechts|0x51D1C|Sony|
|Hoch|0x51D1A|Sony|
|Runter|0x51D1B|Sony|
|AI Toggle (An/Aus)|0x6A49|Sony|

**Die gleiche Taste auf der Fernbedienung wird verwendet, um die AI-Tracking-Funktion ein- und auszuschalten. Das entsprechende Signal (0x6A49, Sony-Protokoll) wird von der Kamera als Toggle-Befehl interpretiert.**

## 🧪 Weiteres
Mit diesen Codes kann z. B. per **IrSender.sendSony(0x6A49, 15)**; das AI-Tracking ausgelöst werden.
Das nächste Projekt ist die Wiedergabe dieser Codes per IR-Sender (KY-005).

## 🔧 Abhängigkeiten
- Arduino IDE ≥ 2.x
- IRremote Bibliothek (aktuelle Version von Armin Joachimsmeyer)
- ESP32-Boardmanager installiert (via Boardverwalter)

