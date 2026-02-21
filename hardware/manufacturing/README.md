# Manufacturing Outputs

This folder is reserved for **tool-independent fabrication files** generated from the final verified PCB revision.

## Files to export from EasyEDA Pro

- Gerber copper layers
- solder mask layers
- silkscreen layers
- board outline
- NC drill files
- pick-and-place / CPL file, if assembly is planned
- final BOM used for ordering

## Verification before committing

1. Export the Gerber/NC drill package from the final PCB revision.
2. Open the package in an independent Gerber viewer.
3. Confirm board outline, holes, copper, solder mask and silkscreen alignment.
4. Check that the ESP32 antenna keep-out and connector footprints match the PCB layout.
5. Only then commit the verified fabrication archive here.

> Gerber files are not included yet because a verified final export has not been provided. This repository does not claim manufacturability from unverified or placeholder fabrication files.
