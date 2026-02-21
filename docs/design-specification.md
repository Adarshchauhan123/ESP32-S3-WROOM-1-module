# Design Specification

## System scope

This repository documents the **HEAD PCB** of a two-board robot electronics architecture.

```text
Samsung INR21700-53G battery
        |
        v
BODY PCB
(charging + protection + 5V_SYS generation)
        |
        | 8-pin neck cable
        v
HEAD PCB
(3.3 V logic + ESP32-S3 + UI + servos)
```

The design follows three system-level rules:

- use a solid ground reference where possible;
- keep high-current loads on **5V_SYS**;
- power the ESP32-S3 and low-voltage logic from **3.3 V**.

## HEAD PCB power input

The HEAD PCB receives power and audio-related signals from the BODY PCB through an 8-pin JST-PH connector.

| Pin | Signal |
|---:|---|
| 1 | GND |
| 2 | GND |
| 3 | 5V_SYS |
| 4 | 5V_SYS |
| 5 | I2S_BCLK |
| 6 | I2S_LRCLK |
| 7 | I2S_DIN |
| 8 | NC |

## 3.3 V regulator

The logic rail is generated with a **ME6211C33M5G** 3.3 V LDO.

| LDO pin | Connection |
|---|---|
| VIN | 5V_SYS |
| VSS | GND |
| CE | 5V_SYS |
| VOUT | 3V3 |
| NC | NC |

Local bulk and high-frequency decoupling are placed close to the regulator input and output.

## ESP32-S3 boot and reset

The controller is based on the **ESP32-S3-WROOM-1-N16R8** module.

- ESP32 supply: **3V3**
- EN: pulled high to 3V3
- BOOT: GPIO0 switch to GND
- RESET: EN switch to GND

Reliability additions include:

- 100 nF capacitor from EN to GND;
- 10 kΩ pull-up from GPIO0 to 3.3 V;
- 100 nF LDO VIN decoupling;
- 100 nF LDO VOUT decoupling.

## Native USB

The ESP32-S3 native USB connection uses:

- GPIO19: USB D−
- GPIO20: USB D+

The USB data pair is protected by an ESD protection device close to the connector.

## Main interfaces

### SPI

The TFT and microSD share the SPI bus with independent chip-select lines.

| GPIO | Function |
|---:|---|
| 36 | MOSI |
| 37 | SCK |
| 38 | MISO |
| 39 | microSD CS |
| 33 | TFT CS |
| 34 | TFT DC |
| 42 | TFT RESET |

### I2C

| GPIO | Function |
|---:|---|
| 8 | SDA |
| 9 | SCL |

The ADXL345 accelerometer is connected on this bus.

### I2S / audio

| GPIO | Function |
|---:|---|
| 5 | I2S BCLK |
| 6 | I2S LRCLK |
| 4 | Audio DOUT to MAX98357A |
| 7 | INMP441 microphone data |

### Servo control

- GPIO10: Servo 1 PWM
- GPIO11: Servo 2 PWM

Servos remain on the 5V_SYS power domain rather than the 3.3 V logic rail.

### User I/O

- GPIO15: addressable RGB LED data through a series resistor
- GPIO16: TTP223 capacitive-touch output

## BODY PCB relationship

The BODY PCB provides battery charging/protection and generates 5V_SYS. Its system-level electrical specification is part of the overall architecture, but **its editable schematic/PCB layout is not included in this repository**. This repository is intentionally scoped to the HEAD PCB.

## Manufacturing deliverables

The editable EasyEDA Pro source is the native design format. For tool-independent manufacturing review, the intended standard deliverables are:

- Gerber copper/mask/silkscreen files;
- NC drill files;
- BOM;
- pick-and-place/CPL data where applicable.

At present, the BOM is included in the repository. Gerber/drill outputs should only be added after exporting them from the final EasyEDA PCB revision and verifying the generated archive in a Gerber viewer.
