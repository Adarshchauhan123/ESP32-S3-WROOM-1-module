# System Architecture

## Two-board robot architecture

The design separates battery/power management from sensing, UI and control.

```text
Samsung INR21700-53G cell (3.6 V, 5300 mAh)
        |
        v
BODY PCB
- reverse-polarity protection
- BQ24075 charging + power-path management
- 5V_SYS generation / distribution
        |
        | JST-PH-8 neck cable
        | 2 x GND
        | 2 x 5V_SYS
        | I2S_BCLK
        | I2S_LRCLK
        | I2S_DIN
        | NC
        v
HEAD PCB
- ME6211C33M5G 3.3 V LDO
- ESP32-S3-WROOM-1-N16R8
- USB programming + ESD
- TFT display (SPI)
- microSD (SPI)
- ADXL345 accelerometer (I2C)
- INMP441 digital microphone (I2S)
- MAX98357A speaker amplifier (I2S)
- TTP223 touch sensor
- two SG90 servo interfaces
- addressable RGB LED
```

## Power-domain strategy

### 5V_SYS
Used for high-current loads:

- servo motors
- MAX98357A audio amplifier

The goal is to prevent high-current load transients from directly disturbing the 3.3 V logic rail.

### 3.3 V
Generated on the HEAD PCB using the ME6211C33M5G and used for:

- ESP32-S3
- logic ICs
- TFT logic
- microSD
- ADXL345
- INMP441
- TTP223

### Ground
The layout is intended to keep a continuous ground reference, with the bottom layer used as a solid GND plane where possible. High-speed signal return paths should remain directly underneath their corresponding traces.

## Major buses

### SPI
Shared by TFT and microSD:

- MOSI: GPIO36
- SCK: GPIO37
- MISO: GPIO38
- microSD CS: GPIO39
- TFT CS: GPIO33
- TFT DC: GPIO34
- TFT RESET: GPIO42

Each peripheral has an independent chip-select signal.

### I2C
Used by ADXL345:

- SDA: GPIO8
- SCL: GPIO9
- 4.7 kΩ pull-ups to 3.3 V

### I2S
Audio-related signals:

- BCLK: GPIO5
- LRCLK: GPIO6
- ESP32 audio DOUT: GPIO4
- INMP441 microphone data: GPIO7

## USB programming path

The HEAD PCB exposes native ESP32-S3 USB:

```text
USB connector
   |
   v
USBLC6-2SC6 ESD protection
   |
   +--> GPIO20 (USB D+)
   +--> GPIO19 (USB D-)
```

BOOT and RESET switches provide manual recovery/programming control.

## Servo path

```text
ESP32 GPIO10 ---> Servo 1 PWM
ESP32 GPIO11 ---> Servo 2 PWM

5V_SYS ----------> servo power
GND --------------> servo ground
```

Local bulk capacitance is used near the servo headers to handle step/stall-current transients and reduce supply droop.
