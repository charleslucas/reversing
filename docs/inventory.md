# Inventory

Equipment already on hand. Parts still to buy are in [bill_of_materials.md](bill_of_materials.md).

## FPGA and controller boards

| Item                                     | Qty | Key specs                                                                                                                | Notes                                                                                                                                    |
| ---------------------------------------- | --- | ------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Digilent Arty A7-35T                     | 1   | Xilinx Artix-7 XC7A35T, 3.3 V I/O, 4 Pmod headers, Arduino-style shield header, USB-UART/JTAG, 256 MB DDR3               | Controller for the pin driver. See [arty_a7_35t_flow.md](arty_a7_35t_flow.md).                                                           |
| MELIFE ESP32 ESP-32S development board   | 2   | ESP-WROOM-32 module, dual core, 2.4 GHz WiFi and Bluetooth, 3.3 V I/O (not 5 V tolerant), USB-UART on board              | Not needed for the pin driver. Could serve as a slow pin poker or a wireless bridge.                                                     |
| DIYmall ESP32 OLED WiFi LoRa kit         | 1   | ESP32, SX1276 LoRa transceiver (868/915 MHz) with antenna, 0.96" OLED, CP2102 USB-UART, 3.3 V I/O                        | Not needed for the pin driver. Many GPIO are taken by the radio and display.                                                             |
| TI Stellaris LaunchPad EK-LM4F120XL      | 1   | LM4F120H5QR (ARM Cortex-M4F, 80 MHz), 256 KB flash, 32 KB SRAM, 3.3 V I/O (not 5 V tolerant), on-board ICDI debug probe  | Not needed for the pin driver. Could serve as a slow pin poker or protocol glue.                                                         |
| TI MSP430 LaunchPad MSP-EXP430G2 Rev 1.5 | 1   | MSP430G2553 in DIP-20 socket, 16 MHz, 16 KB flash, 512 B RAM, 3.3 V I/O (not 5 V tolerant), eZ430 emulator on board      | Not needed for the pin driver. The socketed G2553 can be programmed here and dropped into a breadboard.                                  |
| Seeed Studio XIAO SAMD21                 | 1   | SAMD21G18 (ARM Cortex-M0+, 48 MHz), 256 KB flash, 32 KB SRAM, 11 GPIO, 3.3 V I/O (not 5 V tolerant), USB-C               | Not needed for the pin driver. Tiny breadboard-friendly board for small side tasks.                                                      |
| Intel Galileo Gen 2                      | 1   | Quark X1000 SoC (x86, 400 MHz), 256 MB DDR3, runs Yocto Linux, Arduino Uno headers, 3.3 or 5 V I/O via jumper, Ethernet  | Not needed for the pin driver. GPIO are slow. Could act as a network-attached test controller.                                           |
| Intel Desktop Board FPGA CARD IIA        | 1   | Altera Cyclone IV E EP4CE10F17C8N: 10,320 LEs, 414 Kbit RAM, 2 PLLs, 256-ball FBGA, 1.2 V core, 3.3 V-capable I/O banks  | Intel-internal platform validation/debug card, no public documentation. Board pinout still unknown; confirm the part over JTAG (IDCODE). |
| WeMos D1 Mini (ESP8266)                  | 4   | ESP-12F module (ESP8266, 80/160 MHz), 4 MB flash, 2.4 GHz WiFi, 11 GPIO, 3.3 V I/O (not 5 V tolerant), micro-USB         | Not needed for the pin driver. NodeMCU/Lua or Arduino firmware. Cheap wireless pin pokers.                                               |
| FTDI Morph-IC-II Rev 1.2                 | 1   | Altera Cyclone II EP2C5F256C8N (4,608 LEs), FT2232H USB, 80 external I/O at 3.3 V, 50 MHz oscillator, USB-reconfigurable | Possible second pin-driver FPGA; 3.3 V I/O suits the Am5x86. Cyclone II needs old Quartus (13.0sp1).                                     |
| NodeMCU ESP-12E development board        | 2   | ESP-12E module (ESP8266, 80/160 MHz), 4 MB flash, 2.4 GHz WiFi, 11 GPIO, 3.3 V I/O (not 5 V tolerant), CP2102 USB-UART   | Not needed for the pin driver. Full-size NodeMCU; same role as the D1 Minis with more pins broken out.                                   |

## Chips to reverse engineer

| Item                                | Qty | Key specs                                                                                                   | Notes                                                                                                                                         |
| ----------------------------------- | --- | ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| AMD Am5x86-P75 (Am486DX5-133V16BGC) | 1   | 168-pin PGA, 3.3 V (3.15 to 3.6 V), 8 to 33 MHz bus with 4x internal clock, 16 KB cache, JTAG boundary scan | First target. I/O levels work with the Arty without translators. Markings: 9825DPB, made in Malaysia (date code 9825 suggests 1998, week 25). |
| Motorola MC68030RC40C               | 1   | 128-pin ceramic PGA, 5 V, 40 MHz, asynchronous bus                                                          | Second target. Needs 5 V level shifting.                                                                                                      |
| Motorola MC68EN360RC25C (QUICC)     | 1   | 241-pin PGA, CPU32+ core with Ethernet and comms processor, 25 MHz, background debug mode                   | Later target. Controllable over the few-wire debug port rather than a full pin harness.                                                       |
| AMD Duron                           | 1   | Socket A, EV6 bus                                                                                           | Bus too fast and complex for the Arty. Software-level work on a motherboard only.                                                             |
| AMD Athlon                          | 1   | Socket A or Slot A, EV6 bus                                                                                 | As for the Duron.                                                                                                                             |

## Test and measurement

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Power supplies

| Item                        | Qty | Key specs                                                                                | Notes                                                                                                       |
| --------------------------- | --- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| TekPower HY1803D            | 1   | Linear bench supply, 0 to 18 V, 0 to 3 A, single output, LED V/A meters, CV and CC modes | Low-noise linear regulation. CC mode doubles as a settable current limit to protect chips under test.       |
| LE adapter WT1203000        | 1   | Fixed 12 V DC, 3 A (36 W) switching wall adapter, barrel connector                       | Model number decodes as 12 V, 3000 mA. For fans, LED strips or a 12 V rail; no adjustment or current limit. |
| 5 V wall adapter, unbranded | 2   | Fixed 5 V DC, 2 A (10 W) switching wall adapter, barrel connector                        | Generic 5 V rail for breadboard work; no adjustment or current limit.                                       |
| Inline barrel power switch  | 4   | DC barrel male-to-female extension with inline on/off switch                             | Puts a power switch between a wall adapter and its load.                                                    |

## Programmers and debug adapters

| Item                           | Qty | Key specs                                                                                                                                    | Notes                                                                                                   |
| ------------------------------ | --- | -------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Olimex ARM-USB-TINY-H          | 1   | FT2232H-based high-speed USB JTAG, 20-pin ARM JTAG connector, follows target voltage                                                         | Supported by OpenOCD. Use for boundary scan and in-circuit JTAG.                                        |
| Adafruit FT232H breakout       | 1   | FT232H, USB to JTAG/SPI/I2C/UART, 16 GPIO, 3.3 V logic                                                                                       | Supported by OpenOCD, UrJTAG and flashrom. Loose-wire JTAG, SPI flash dumps, I2C EEPROM reads.          |
| Seeed Studio Bus Pirate BPv3.6 | 1   | PIC24-based USB multi-tool: I2C, SPI, UART, 1-Wire, raw 2/3-wire; 3.3 V logic, 5 V tolerant; on-board 3.3/5 V rails, pull-ups, voltage probe | Interactive terminal for poking unknown buses. Slow JTAG via firmware; flashrom/AVRdude support.        |
| Goodspeed Facedancer11         | 1   | GoodFET-derived USB emulation board: MSP430F2618, MAX3420E USB peripheral controller, FT232RL host link                                      | Emulates USB devices from Python for USB protocol analysis and fuzzing. Not relevant to the pin driver. |

## Sockets, adapters and clips

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Soldering and rework

| Item                            | Qty | Key specs                                                     | Notes                                                                             |
| ------------------------------- | --- | ------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Hakko FX-888D soldering station | 1   | 70 W, digital temperature control 50 to 480 C, T18 tip series | Fine for all through-hole and most SMD hand soldering.                            |
| Wagner heat gun                 | 1   | Paint-stripper class heat gun, high airflow                   | Shrinks tubing and general heating. Too hot and broad for board-level SMD rework. |

## Sensors and modules

| Item                                  | Qty | Key specs                                                                                                                                      | Notes                                                                                         |
| ------------------------------------- | --- | ---------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| GY-521 module (MPU-6050)              | 2   | 6-DOF IMU, 3-axis gyro + 3-axis accelerometer, I2C (address 0x68 or 0x69), on-board 3.3 V regulator so 3.3 to 5 V supply                       | Not relevant to the pin driver. Handy I2C target for exercising the FT232H or a LaunchPad.    |
| Hacromatic HacroCam camera module     | 1   | VGA image sensor on an Arduino shield, I2C control at 3.3 or 5 V, serial image readout, open source design                                     | Discontinued (circa 2012). Arduino library and PC viewer software exist but are unmaintained. |
| HiLetgo TP4056 charger module (USB-C) | 2   | TP4056 linear Li-ion charger, 5 V USB-C in, 1 A charge, 4.2 V termination, DW01A + FS8205A protection (overcharge, overdischarge, overcurrent) | Charges single-cell LiPo such as the Liter 804040. Protection sits between cell and load.     |

## Prototyping, cables, wire and passives

| Item                                         | Qty   | Key specs                                                                                                                               | Notes                                                                                                                                                                                              |
| -------------------------------------------- | ----- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sscon prototype PCB, 2 x 8 cm                | 20    | Double-sided perfboard, 0.1" grid, plated through holes; 6 rows x 28 holes, 4 solder pads per side presumed bussed to the middle 4 rows | Too narrow for the ZIF carrier in [bill_of_materials.md](bill_of_materials.md) (needs about 7 x 9 cm); fine for small breakouts. Confirm the pad-to-row bussing with a meter before relying on it. |
| Plusivo resistor kit, 600 pcs                | 1     | 1/4 W metal film, 1%, through-hole; 30 values, 20 of each, 10 Ω to 1 MΩ                                                                 | About 20 pcs per value, so only partial stock toward the 100x 330 Ω line in [bill_of_materials.md](bill_of_materials.md).                                                                          |
| LEDs, 3-pin, assorted                        | 1 lot | Bicolor LEDs, two dies with a shared common pin                                                                                         | Check whether common anode or common cathode per batch. Handy as two-state status indicators.                                                                                                      |
| Heat shrink tubing, assorted                 | 1 lot | Various diameters and colors                                                                                                            | Shrink with the Wagner heat gun.                                                                                                                                                                   |
| Breadboard jumper wires, assorted            | 1 lot | Various 0.1" prototyping wires                                                                                                          | Covers the jumper wire line in [bill_of_materials.md](bill_of_materials.md).                                                                                                                       |
| Archer Experimenter 300 breadboard           | 1     | Vintage Radio Shack solderless breadboard (cat. 276-174), 48 rows, 0.1" pitch, with bus strips                                          | Larger than the half-size boards. Check spring contacts for age before trusting connections.                                                                                                       |
| Solderless breadboard, half-size             | 2     | 30 rows, 400 tie points, 0.1" pitch                                                                                                     | For side circuits such as level translators and resistors. Not suited to carrying the ZIF socket.                                                                                                  |
| Molence WH148 rotary potentiometer           | 5     | Single-turn carbon film, 3-pin, 15 mm shaft, linear (B) taper, XH2.54-3P cable and knob included                                        | Amazon SKU X002RGRW4X. Resistance value is marked on the body (e.g., B1K = 1 kΩ).                                                                                                                  |
| Adafruit JST SH 4-pin cable, 100 mm          | 2     | JST SH 1.0 mm pitch, 4-pin, female both ends, STEMMA QT / Qwiic compatible                                                              | Daisy-chains I2C breakouts without soldering.                                                                                                                                                      |
| Adafruit JST SH 4-pin to male DuPont, 100 mm | 2     | JST SH 1.0 mm pitch to 4 male 0.1" DuPont pins, STEMMA QT / Qwiic compatible                                                            | Breaks a STEMMA QT / Qwiic port out to a breadboard or female headers.                                                                                                                             |
| Liter 804040 LiPo battery                    | 1     | 3.7 V nominal, 1200 mAh, 804040 size (8 x 40 x 40 mm), single cell                                                                      | Charge via the HiLetgo TP4056 modules. Not a bench supply substitute for chips under test.                                                                                                         |
