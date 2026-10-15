# Inventory

Equipment already on hand. Parts still to buy are in [bill_of_materials.md](bill_of_materials.md).

## FPGA and controller boards

| Item                                   | Qty | Key specs                                                                                                   | Notes                                                                                |
| -------------------------------------- | --- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Digilent Arty A7-35T                   | 1   | Xilinx Artix-7 XC7A35T, 3.3 V I/O, 4 Pmod headers, Arduino-style shield header, USB-UART/JTAG, 256 MB DDR3  | Controller for the pin driver. See [arty_a7_35t_flow.md](arty_a7_35t_flow.md).       |
| MELIFE ESP32 ESP-32S development board | 2   | ESP-WROOM-32 module, dual core, 2.4 GHz WiFi and Bluetooth, 3.3 V I/O (not 5 V tolerant), USB-UART on board | Not needed for the pin driver. Could serve as a slow pin poker or a wireless bridge. |
| DIYmall ESP32 OLED WiFi LoRa kit       | 1   | ESP32, SX1276 LoRa transceiver (868/915 MHz) with antenna, 0.96" OLED, CP2102 USB-UART, 3.3 V I/O           | Not needed for the pin driver. Many GPIO are taken by the radio and display.         |

## Chips to reverse engineer

| Item                                 | Qty | Key specs                                                                                 | Notes                                                                                   |
| ------------------------------------ | --- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| AMD Am5x86-P75 (Am486DX5-133 V16BCG) | 1   | 168-pin PGA, 3.45 V, 33 MHz bus with 4x internal clock, 16 KB cache, JTAG boundary scan   | First target. I/O levels work with the Arty without translators.                        |
| Motorola MC68030RC40C                | 1   | 128-pin ceramic PGA, 5 V, 40 MHz, asynchronous bus                                        | Second target. Needs 5 V level shifting.                                                |
| Motorola MC68EN360RC25C (QUICC)      | 1   | 241-pin PGA, CPU32+ core with Ethernet and comms processor, 25 MHz, background debug mode | Later target. Controllable over the few-wire debug port rather than a full pin harness. |
| AMD Duron                            | 1   | Socket A, EV6 bus                                                                         | Bus too fast and complex for the Arty. Software-level work on a motherboard only.       |
| AMD Athlon                           | 1   | Socket A or Slot A, EV6 bus                                                               | As for the Duron.                                                                       |

## Test and measurement

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Power supplies

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Programmers and debug adapters

| Item                     | Qty | Key specs                                                                            | Notes                                                                                          |
| ------------------------ | --- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| Olimex ARM-USB-TINY-H    | 1   | FT2232H-based high-speed USB JTAG, 20-pin ARM JTAG connector, follows target voltage | Supported by OpenOCD. Use for boundary scan and in-circuit JTAG.                               |
| Adafruit FT232H breakout | 1   | FT232H, USB to JTAG/SPI/I2C/UART, 16 GPIO, 3.3 V logic                               | Supported by OpenOCD, UrJTAG and flashrom. Loose-wire JTAG, SPI flash dumps, I2C EEPROM reads. |

## Sockets, adapters and clips

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Soldering and rework

| Item | Qty | Key specs | Notes |
| ---- | --- | --------- | ----- |
|      |     |           |       |

## Prototyping, cables, wire and passives

| Item                             | Qty | Key specs                           | Notes                                                                                             |
| -------------------------------- | --- | ----------------------------------- | ------------------------------------------------------------------------------------------------- |
| Solderless breadboard, half-size | 2   | 30 rows, 400 tie points, 0.1" pitch | For side circuits such as level translators and resistors. Not suited to carrying the ZIF socket. |
