# HEAD PCB — ESP32-S3-WROOM-1 Embedded Controller

A custom 2-layer head/controller PCB built around the **ESP32-S3-WROOM-1**, designed in EasyEDA Pro for a larger robot system.

## What this board does

The **HEAD PCB** is the low-voltage control and interface board of a two-board robot architecture. A separate BODY PCB handles the battery, charging and system-power generation; the HEAD PCB receives **5V_SYS**, regulates it to **3.3 V** for the ESP32-S3 and logic, and integrates user-interface, sensing, storage, audio and servo-control hardware.

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

## Key hardware

| Block | Device / Interface | Role |
|---|---|---|
| MCU | **ESP32-S3-WROOM-1-N16R8** | Main controller |
| 3.3 V regulation | **ME6211C33M5G** | 5V_SYS to 3.3 V logic rail |
| USB protection | **USBLC6-2SC6** | ESD protection on native USB data lines |
| Display | SPI TFT | Local user interface |
| Storage | microSD, SPI | Local data / asset storage |
| Motion sensor | **ADXL345**, I2C | 3-axis acceleration sensing |
| Microphone | **INMP441**, I2S | Digital audio input |
| Audio amplifier | **MAX98357A**, I2S | 5 V class-D speaker drive |
| Touch | **TTP223** | Capacitive-touch input |
| Actuation | 2 × SG90 servo headers | PWM control from ESP32 |
| RGB | WS2812B / SK6812-class LED | Addressable status indication |
| Programming | USB + BOOT + RESET | Native USB programming/debug path |

## Power architecture

The board separates high-current and logic domains:

- **5V_SYS:** servo motors and MAX98357A audio power.
- **3.3 V:** ESP32-S3 and logic/peripherals.
- **Ground:** one continuous ground reference/plane where possible.
- **LDO:** ME6211C33M5G with local input/output bulk and high-frequency decoupling.
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
- **TFT connector:** 3.3 V, GND and SPI/control signals.
- **microSD socket:** 3.3 V SPI storage interface.
- **USB connector:** native ESP32-S3 USB programming/data with ESD protection.

## PCB/layout choices

- 2-layer FR-4 design, nominal 1.6 mm board thickness and 1 oz copper.
- Bottom layer used as a continuous GND reference where possible.
- 45° routing preferred over 90° corners.
- High-current 5V_SYS routes kept short and wide.
- High-speed SPI/I2S/USB routes kept over a solid return path and away from servo power.
- ESP32 antenna keep-out is treated as a placement constraint.
- Decoupling capacitors are placed close to IC supply pins.
- ESD devices are placed near exposed connectors/signals.

## Repository structure

```text
.
├── hardware/
│   ├── easyeda/             # Editable EasyEDA Pro source archive
│   └── 3d/                  # GLB, STL and compressed STEP model
├── docs/
│   ├── architecture.md
│   ├── pin-mapping.md
│   ├── design-decisions.md
│   └── design-specification.txt
├── website/                 # Interactive browser-based 3D model viewer
└── .github/workflows/       # GitHub Pages deployment
```

## Open the hardware project

1. Log in to **EasyEDA Pro**.
2. Import `hardware/easyeda/HEAD_PCB.epro2` as an EasyEDA Professional project.
3. Open the schematic and `PCB_HEAD_PCB` board document.
4. Use EasyEDA's 3D view for assembly/mechanical inspection.

## Interactive 3D viewer

The `website/` folder provides a browser-based viewer of the exported GLB model. The GitHub Pages workflow publishes it automatically after Pages is enabled for **GitHub Actions** in the repository settings.

For local viewing:

```bash
cd website
python -m http.server 8000
```

Then open `http://localhost:8000`.

## Current project status

**Available in this repository:** schematic/PCB project source, layout data, 3D CAD export, design specification and interface documentation.

**Not claimed here:** physical fabrication/assembly, measured bench results, or production firmware. Those items are intentionally not presented as completed because the provided project material does not document them.

---

**EDA:** EasyEDA Pro  
**MCU:** ESP32-S3-WROOM-1-N16R8  
**Board:** 2-layer embedded controller / head PCB
