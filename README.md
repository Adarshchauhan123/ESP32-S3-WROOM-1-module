# HEAD PCB — ESP32-S3-WROOM-1 Embedded Controller

A custom **2-layer embedded controller PCB** built around the **ESP32-S3-WROOM-1-N16R8** and designed in **EasyEDA Pro** as the head/control board of a larger robot system.

**Status:** Schematic and PCB layout complete. Fabrication, assembled-board bring-up and measured bench validation are not yet documented in this repository. DRC-clean status is not claimed without an exported DRC report.

## PCB layout

![HEAD PCB layout](docs/images/pcb-layout-overview.png)

## What this board does

The **HEAD PCB** is the low-voltage control and interface board of a two-board robot architecture. A separate BODY PCB handles the battery, charging and system-power generation. The HEAD PCB receives **5V_SYS**, regulates it to **3.3 V** for the ESP32-S3 and logic, and integrates sensing, storage, audio, user-interface and servo-control hardware.

```text
Samsung 21700 battery
        |
        v
BODY PCB
(charging + protection + 5V_SYS)
        |
        | JST-PH-8 neck cable
        v
HEAD PCB
        |
        +--> ME6211C33M5G --> 3.3 V --> ESP32-S3 + logic
        +--> 5V_SYS ----------------> servos + audio amplifier
        |
        +--> TFT / microSD / ADXL345 / INMP441 / TTP223 / RGB LED
```

## Why ESP32-S3?

The ESP32-S3 was selected because it combines **Wi-Fi/BLE connectivity, native USB, multiple SPI/I2C/I2S/PWM peripherals and substantial processing headroom** in a single module. The N16R8 variant also provides external-memory capacity suitable for a controller that may need to handle display buffers, audio data, sensor processing and larger firmware without adding a separate application processor. Native USB simplifies programming/debugging, while the available peripheral interfaces allow the TFT, microSD, microphone, accelerometer, touch input, RGB LED and servo outputs to be integrated around one MCU.

## Key hardware

| Block | Device / Interface | Role |
|---|---|---|
| MCU | **ESP32-S3-WROOM-1-N16R8** | Main controller |
| 3.3 V regulation | **ME6211C33M5G-N** | 5V_SYS to 3.3 V logic rail |
| USB protection | **D1213A-04TS-7** | ESD protection on USB data lines |
| Display | SPI TFT | Local user interface |
| Storage | microSD, SPI | Local data / asset storage |
| Motion sensor | **ADXL345BCCZ-RL**, I2C | 3-axis acceleration sensing |
| Microphone | **INMP441ACEZ**, I2S | Digital audio input |
| Audio amplifier | **MAX98357AETE+T**, I2S | 5 V class-D speaker drive |
| Touch | **TTP223E-BA6** | Capacitive-touch input |
| Actuation | 2 × servo headers | PWM control from ESP32 |
| RGB | **WS2812B** | Addressable status indication |
| Programming | USB + BOOT + RESET | Native USB programming/debug path |

## Power architecture

The board separates high-current and logic domains:

- **5V_SYS:** servo motors and MAX98357A audio power.
- **3.3 V:** ESP32-S3 and low-voltage peripherals.
- **Ground:** one continuous ground reference/plane where possible.
- **LDO:** ME6211C33M5G-N with local input/output bulk and high-frequency decoupling.
- **Servo rail:** wide 5 V routing and local bulk capacitance to reduce brownouts caused by current transients.

### Boot and supply-stability additions

The design includes reliability changes intended to avoid unstable startup and random resets:

- **100 nF capacitor from ESP32 EN to GND**.
- **10 kΩ pull-up from GPIO0/BOOT to 3.3 V**; BOOT switch still pulls GPIO0 to GND.
- **100 nF LDO VIN decoupler** close to the regulator.
- **100 nF LDO VOUT decoupler** close to the regulator.

## ESP32-S3 interface map

| ESP32 GPIO | Function |
|---|---|
| GPIO36 | SPI MOSI |
| GPIO37 | SPI SCK |
| GPIO38 | SPI MISO |
| GPIO39 | microSD CS |
| GPIO33 | TFT CS |
| GPIO34 | TFT DC |
| GPIO42 | TFT RESET |
| GPIO8 | I2C SDA |
| GPIO9 | I2C SCL |
| GPIO5 | I2S BCLK |
| GPIO6 | I2S LRCLK |
| GPIO4 | I2S audio DOUT |
| GPIO7 | INMP441 microphone SD |
| GPIO10 | Servo 1 PWM |
| GPIO11 | Servo 2 PWM |
| GPIO15 | RGB LED DIN through 330 Ω |
| GPIO16 | TTP223 touch output |
| GPIO19 | USB D− |
| GPIO20 | USB D+ |

## External connections

- **JST-PH-8 Body → Head:** 2 × GND, 2 × 5V_SYS, I2S_BCLK, I2S_LRCLK, I2S_DIN, one NC pin.
- **2 × 3-pin servo headers:** GND, 5V_SYS, PWM.
- **Speaker output:** differential SPK+ / SPK− from the MAX98357A stage.
- **Touch-pad connector:** GND, TOUCH_PAD, 3.3 V.
- **TFT connector/header:** 3.3 V, GND and SPI/control signals.
- **microSD socket:** 3.3 V SPI storage interface.
- **USB-C connector:** native ESP32-S3 USB programming/data with ESD protection.

## PCB/layout choices

- 2-layer FR-4 design, nominal 1.6 mm board thickness and 1 oz copper.
- Bottom layer used as a continuous GND reference where possible.
- 45° routing preferred over 90° corners.
- High-current 5V_SYS routes kept short and wide.
- High-speed SPI/I2S/USB routes kept over a solid return path and away from servo power.
- ESP32 antenna keep-out treated as a placement constraint.
- Decoupling capacitors placed close to IC supply pins.
- ESD devices placed close to exposed connectors/signals.
- Bulk capacitance placed near servo/audio loads to handle transient current demand.

## Bill of Materials

A source-derived BOM is included at:

[`hardware/BOM_HEAD_PCB.csv`](hardware/BOM_HEAD_PCB.csv)

It contains reference designators, quantity, part/value, manufacturer part number, package/footprint, supplier and supplier part number where that metadata exists in the EasyEDA project. Generic resistor/capacitor sourcing fields remain marked `TBD` rather than inventing a specific supplier part.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── hardware/
│   ├── BOM_HEAD_PCB.csv
│   ├── easyeda/
│   └── 3d/
└── docs/
    ├── architecture.md
    ├── pin-mapping.md
    ├── design-decisions.md
    ├── bring-up-checklist.md
    └── images/
        └── pcb-layout-overview.png
```

## Hardware validation plan

The repository includes [`docs/bring-up-checklist.md`](docs/bring-up-checklist.md) for the intended post-fabrication validation sequence: power-rail checks, BOOT/RESET verification, USB enumeration, peripheral bring-up and power-integrity testing under servo/audio transients.

## 3D model / live viewer

The board has been exported as a 3D CAD model. A public GitHub Pages URL is **not listed here yet because a working Pages deployment has not been verified**. Once the viewer is published and tested, its live URL can be added at the top of this README.

## License

This repository is released under the [MIT License](LICENSE).

---

**EDA:** EasyEDA Pro  
**MCU:** ESP32-S3-WROOM-1-N16R8  
**Board:** 2-layer embedded controller / head PCB
