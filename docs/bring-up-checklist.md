# Hardware Bring-Up Checklist

Use this after fabrication/assembly. It is intentionally written as a validation plan, not as a claim that testing has already been completed.

## Before power

- [ ] Visual inspection for solder bridges and reversed polarized parts.
- [ ] Check resistance from 5V_SYS to GND for a hard short.
- [ ] Check resistance from 3.3 V to GND for a hard short.
- [ ] Confirm LDO orientation and EN/BOOT resistor/capacitor placement.
- [ ] Confirm ESP32 antenna keep-out is unobstructed.

## First power-up

- [ ] Apply current-limited 5 V.
- [ ] Verify 5V_SYS at the head connector.
- [ ] Measure LDO output: ~3.3 V.
- [ ] Check for abnormal heating.
- [ ] Verify EN is HIGH during normal operation.

## ESP32 bring-up

- [ ] RESET button pulls EN LOW.
- [ ] BOOT button pulls GPIO0 LOW.
- [ ] Native USB enumerates correctly.
- [ ] Flash a minimal LED/serial test firmware.

## Peripheral bring-up

- [ ] I2C scan finds ADXL345.
- [ ] TFT responds on SPI.
- [ ] microSD initializes and reads/writes a test file.
- [ ] INMP441 produces I2S microphone samples.
- [ ] MAX98357A produces speaker output.
- [ ] TTP223 changes GPIO16 state on touch.
- [ ] RGB LED responds through GPIO15.
- [ ] Servo 1 responds to GPIO10 PWM.
- [ ] Servo 2 responds to GPIO11 PWM.

## Power-integrity checks

- [ ] Monitor 3.3 V while moving servos.
- [ ] Monitor 5V_SYS during servo start/stall transients.
- [ ] Verify ESP32 does not reset during servo activity.
- [ ] Check audio peaks do not collapse the supply.
- [ ] Inspect USB, SPI and I2S signals if a scope/logic analyzer is available.

## Validation evidence to add after testing

- board photographs
- 5V_SYS and 3.3 V measured values
- oscilloscope screenshots during servo/audio transients
- USB programming screenshot
- TFT/microSD test output
- I2C scan result
- short demo video
