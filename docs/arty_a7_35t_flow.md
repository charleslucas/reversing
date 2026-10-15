# Arty A7-35T Flow

How the Digilent Arty A7-35T is used as the controller for the chip pin driver/reader, what it can and cannot do, and the order to build things in. Parts are listed in [bill_of_materials.md](bill_of_materials.md).

## What the board provides

- **About 80 usable I/O:** 32 on the four Pmod headers plus roughly 49 on the Arduino-style shield header. The shield count is from memory, so confirm it against the Digilent reference manual.
- **Easy breakout:** everything is on 0.1" headers, so a protoshield plus Pmod cables reaches a ZIF socket without a custom PCB.
- **Host link:** on-board USB-UART for scripting from a PC.
- **Capture memory:** block RAM, plus 256 MB of DDR3 for deep captures.
- **Tooling:** the 35T is covered by the free Vivado edition. LiteX also supports the board, for driving pins from Python.

## Limits

- **3.3 V only, not 5 V tolerant.** The I/O banks are fixed at 3.3 V. 5 V and 1.8 V chips need level translators. A 5 V output wired straight into the FPGA can damage it.
- **Series resistors differ by header.** Pmods JA and JD have 200 Ω in series on every pin. JB and JC are the high-speed Pmods without it. Add external resistors on JB, JC and the shield pins.
- **Direction sensing costs pins.** Driving one side of a resistor and sensing the other takes two FPGA pins per chip pin, which leaves about 40 sensed pins. Pull-ups and pull-downs are fixed per bitstream on this FPGA, so they cannot be toggled at runtime as a substitute.
- **Power pins are manual.** VCC and GND are jumpered to the right ZIF pins from a current-limited supply.

## Signal path

```
PC (Python) <-- USB-UART --> Arty A7-35T --[series R]--> ZIF socket --> chip under test
                                  |                           ^
                                  +------ sense pin ----------+   (optional, for direction sensing)

Bench supply (current-limited) --> VCC / GND pins on the ZIF, by jumper
```

For 5 V or 1.8 V parts, a per-pin level translator sits between the series resistor and the ZIF socket.

## Build order

### Step 1: 16-pin proof of concept

1. Wire a 3.3 V chip in a DIP ZIF socket to Pmods JA and JD only (16 pins, already protected by 200 Ω).
2. Jumper VCC and GND from the bench supply, with the current limit set low.
3. Write a small UART-controlled design with three registers per pin: drive value, output enable and readback.
4. Script it from Python on the PC.

This proves the whole loop before any money goes on level shifters or adapters.

### Step 2: full 48-pin coverage

1. Add a protoshield and bring out the shield header I/O.
2. Fit a series resistor on every pin from JB, JC and the shield.
3. Extend the design to 48 pins and add a pattern memory so stimulus can be clocked at speed, not just poked over UART.

### Step 3: direction sensing

1. Add a second FPGA pin on the chip side of each series resistor.
2. For each pin, drive weakly high and then low through the resistor and read the sense pin. A pin that does not follow is an output.

### Step 4: other voltages

1. Add per-pin level translators with a settable target-side rail.
2. Add a direction signal per pin to the design.

If the need grows past about 48 pins or to other voltages, add a translator board rather than replacing the Arty.
