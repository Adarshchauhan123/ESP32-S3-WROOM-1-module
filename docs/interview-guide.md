# Interview Guide

## 30-second explanation

This is a custom **ESP32-S3-WROOM-1 head/control PCB** for a robot system. A separate body board handles the battery and charging, while this board receives 5V_SYS, generates 3.3 V for the ESP32 and logic, and integrates a TFT, microSD, accelerometer, digital microphone, audio amplifier, capacitive touch input, RGB status LED and two servo interfaces. I focused on power-domain separation, boot reliability, decoupling, ESD protection and PCB return paths.

## 2-minute technical walkthrough

1. **Power enters as 5V_SYS** from the body board through a JST-PH-8 connector.
2. The **ME6211C33M5G** LDO generates 3.3 V for the ESP32-S3 and low-voltage peripherals.
3. **Servos and the MAX98357A amplifier stay on 5V_SYS** so their current transients do not directly load the ESP32 rail.
4. The TFT and microSD share the **SPI bus** with separate chip-select pins.
5. The ADXL345 uses **I2C** with 4.7 kΩ pull-ups.
6. Audio uses **I2S**: INMP441 provides microphone input and MAX98357A drives the speaker.
7. The board exposes native ESP32-S3 USB through a **USBLC6-2SC6** ESD-protection device.
8. BOOT/RESET circuitry plus EN/BOOT biasing and local decoupling improve reliable startup.

## Questions an interviewer may ask

### Why not power the servos from 3.3 V?
Servos are high-current electromechanical loads and can draw large transient/stall currents. Supplying them from the logic rail could cause voltage droop and reset the ESP32. They therefore use 5V_SYS with local bulk capacitance.

### Why is a capacitor placed on EN?
It helps keep the enable node stable during power-up and suppresses short disturbances that could otherwise cause unwanted resets.

### Why is GPIO0 pulled up?
GPIO0 is a boot-strapping pin. A pull-up defines the normal-boot state. The BOOT push-button can still intentionally pull it LOW for programming/recovery.

### Why can TFT and microSD share SPI?
SPI supports multiple slaves on common clock/data lines as long as each slave has its own chip-select signal. The MCU selects only the desired device at a time.

### Why use a ground plane?
A continuous ground plane provides a low-impedance return path, reduces loop area and improves signal integrity, especially for USB, SPI and I2S.

### Why place ESD devices close to connectors?
The goal is to divert an electrostatic discharge to ground before it travels far into the PCB and reaches sensitive IC pins.

### What would you test after fabrication?
- Check for shorts between 5V_SYS, 3.3 V and GND before power-up.
- Verify the 3.3 V LDO output and startup behavior.
- Confirm USB programming and BOOT/RESET operation.
- Bring up I2C/SPI/I2S peripherals one at a time.
- Test servo operation while monitoring 5 V and 3.3 V for droop/noise.
- Test speaker output and microphone capture.
- Verify TFT and microSD operation.

## Be precise about project status

The repository currently demonstrates the **hardware design, PCB layout and CAD export**. Do not claim measured performance, completed fabrication or final firmware unless you add evidence for those items later.
