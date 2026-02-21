# ESP32-S3 Pin Mapping

This table documents the pin assignments used by the HEAD PCB design.

| GPIO | Net / Function | Peripheral |
|---:|---|---|
| 0 | BOOT | Boot strap / push-button to GND |
| 4 | I2S DOUT | MAX98357A audio amplifier |
| 5 | I2S BCLK | MAX98357A / INMP441 |
| 6 | I2S LRCLK / WS | MAX98357A / INMP441 |
| 7 | Microphone SD | INMP441 |
| 8 | I2C SDA | ADXL345 |
| 9 | I2C SCL | ADXL345 |
| 10 | SERVO_1 PWM | SG90 servo 1 |
| 11 | SERVO_2 PWM | SG90 servo 2 |
| 15 | RGB DIN | Addressable RGB via 330 Ω |
| 16 | TOUCH_OUT | TTP223 |
| 19 | USB D− | Native USB |
| 20 | USB D+ | Native USB |
| 33 | TFT_CS | TFT display |
| 34 | TFT_DC | TFT display |
| 36 | SPI MOSI | TFT + microSD shared SPI |
| 37 | SPI SCK | TFT + microSD shared SPI |
| 38 | SPI MISO | microSD shared SPI |
| 39 | SD_CS | microSD |
| 42 | TFT_RST | TFT display |

## I2C pull-ups

- SDA → 4.7 kΩ → 3.3 V
- SCL → 4.7 kΩ → 3.3 V

## BOOT / RESET

### BOOT

```text
3V3 --- 10 kΩ --- GPIO0
                  |
                  SW
                  |
                 GND
```

The pull-up gives GPIO0 a defined HIGH level during normal boot. Pressing BOOT pulls it LOW.

### RESET / EN

The ESP32 EN/CHIP_PU node is held HIGH for normal operation and can be pulled LOW using the RESET switch. A local capacitor to ground is included to improve startup stability.

## Servo headers

Each servo connector exposes:

1. GND
2. 5V_SYS
3. PWM signal (GPIO10 or GPIO11)

Servo power is intentionally **not** taken from the 3.3 V rail.
