<a href="https://107-systems.org/"><img align="right" src="https://raw.githubusercontent.com/107-systems/.github/main/logo/107-systems.png" width="15%"></a>
`107-Arduino-BMP388`
====================
[![Arduino Library Badge](https://www.ardu-badge.com/badge/107-Arduino-BMP388.svg?)](https://www.ardu-badge.com/107-Arduino-BMP388)
[![Compile Examples](https://github.com/107-systems/107-Arduino-BMP388/workflows/Compile%20Examples/badge.svg)](https://github.com/107-systems/107-Arduino-BMP388/actions?workflow=Compile+Examples)
[![Check Arduino](https://github.com/107-systems/107-Arduino-BMP388/actions/workflows/check-arduino.yml/badge.svg)](https://github.com/107-systems/107-Arduino-BMP388/actions/workflows/check-arduino.yml)
[![Check keywords.txt](https://github.com/107-systems/107-Arduino-BMP388/actions/workflows/check-keywords-txt.yml/badge.svg)](https://github.com/107-systems/107-Arduino-BMP388/actions/workflows/check-keywords-txt.yml)
[![General Formatting Checks](https://github.com/107-systems/107-Arduino-BMP388/workflows/General%20Formatting%20Checks/badge.svg)](https://github.com/107-systems/107-Arduino-BMP388/actions?workflow=General+Formatting+Checks)
[![Spell Check](https://github.com/107-systems/107-Arduino-BMP388/workflows/Spell%20Check/badge.svg)](https://github.com/107-systems/107-Arduino-BMP388/actions?workflow=Spell+Check)

<p align="center">
  <a href="https://github.com/107-systems/Viper-Firmware"><img src="https://github.com/107-systems/.github/raw/main/logo/viper.jpg" width="40%"></a>
</p>

Arduino library for interfacing with the BMP388 barometric pressure sensor which can be used for inferring altitude information.

This library works for
* [ArduinoCore-samd](https://github.com/arduino/ArduinoCore-samd): [`Arduino Zero`](https://store.arduino.cc/arduino-zero), [`MKR 1000`](https://store.arduino.cc/arduino-mkr1000-wifi), [`MKR WiFi 1010`](https://store.arduino.cc/arduino-mkr-wifi-1010), [`Nano 33 IoT`](https://store.arduino.cc/arduino-nano-33-iot), [`MKR GSM 1400`](https://store.arduino.cc/arduino-mkr-gsm-1400-1415), [`MKR NB 1500`](https://store.arduino.cc/arduino-mkr-nb-1500-1413), [`MKR WAN 1300/1310`](https://store.arduino.cc/mkr-wan-1310) :heavy_check_mark:
* [ArduinoCore-mbed](https://github.com/arduino/ArduinoCore-mbed): [`Portenta H7`](https://store.arduino.cc/portenta-h7), [`Nano 33 BLE`](https://store.arduino.cc/arduino-nano-33-ble), [`Nano RP2040 Connect`](https://store.arduino.cc/nano-rp2040-connect), [`Edge Control`](https://store.arduino.cc/edge-control) :heavy_check_mark:
* [arduino-esp32](https://github.com/espressif/arduino-esp32): `ESP32 Dev Module`, `ESP32 Wrover Module`, ... :heavy_check_mark:

## Example
```C++
#include <SPI.h>
#include <ArduinoBMP388.h>
/* ... */
static int const BMP388_CS_PIN  = 2;
static int const BMP388_INT_PIN = 6;
/* ... */
void    spi_select     ()                   { digitalWrite(BMP388_CS_PIN, LOW); }
void    spi_deselect   ()                   { digitalWrite(BMP388_CS_PIN, HIGH); }
uint8_t spi_transfer   (uint8_t const data) { return SPI.transfer(data); }
void    onExternalEvent()                   { bmp388.onExternalEventHandler(); }
void    onSensorData   (double const pressure_hpa, double const temperature_deg) {
  Serial.print(pressure_hpa);
  Serial.print(" hPa / ");
  Serial.print(temperature_deg);
  Serial.print(" °C / ");
  Serial.print(ArduinoBMP388::convertPressureToAltitude(pressure_hpa));
  Serial.println(" m");
}
/* ... */
ArduinoBMP388 bmp388(spi_select, spi_deselect, spi_transfer, onSensorData);
/* ... */
void setup() {
  Serial.begin(9600);
  while(!Serial) { }

  SPI.begin();
  pinMode(BMP388_CS_PIN, OUTPUT);
  digitalWrite(BMP388_CS_PIN, HIGH);

  pinMode(BMP388_INT_PIN, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(BMP388_INT_PIN), onExternalEvent, FALLING);

  bmp388.begin(BMP388::OutputDataRate::ODR_12_5_Hz);
}

void loop() {

}
```
