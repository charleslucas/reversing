# Am5x86 Flow

Plan for behavioural reverse engineering of the AMD Am5x86-P75 (Am486DX5-133 V16BCG). The Arty A7-35T acts as the whole motherboard: it supplies clock and reset, serves memory, and records every bus cycle. The Olimex ARM-USB-TINY-H handles JTAG bring-up.

Facts marked as confirmed come from AMD's Enhanced Am486DX Microprocessor Family datasheet (publication 20736, preliminary), which covers the Am486DX5-133: <https://www.amd.com/content/dam/amd/en/documents/archived-tech-docs/datasheets/20736.pdf>. The Am5x86 has its own datasheet (publication 19751) and is described there as functionally identical apart from the CLKMUL pin. Pin numbers still need to be taken from the pinout before the carrier board is designed.

## Why this chip first

- **No level shifters.** The chip runs at 3.3 V, the same as the Arty's I/O, so they connect through series resistors.
- **JTAG boundary scan.** It can be checked with tools already owned, before any FPGA logic exists.
- **Stallable bus.** Holding RDY# and BRDY# inactive stalls any bus cycle indefinitely, which gives single-cycle stepping.
- **x86.** Results can be compared directly against software emulators.

## Key facts

| Item                 | Datasheet says                                                                                       | Consequence                                                                                                                                                   |
| -------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Package              | 168-pin PGA, with 23 VCC and 28 VSS pins                                                             | Every VCC and VSS pin must be connected.                                                                                                                      |
| Core supply          | 3.3 V ±0.3 V generally; 3.15 to 3.6 V for the Am486DX5-133 V16 parts. Confirmed.                     | Run at 3.3 V from the bench supply.                                                                                                                           |
| VCC5 pin             | Not used: the pin is internally not connected. Confirmed.                                            | Nothing to wire.                                                                                                                                              |
| I/O levels           | Inputs high from 2.0 V, low below 0.8 V, and 5 V tolerant. Outputs high at least 2.4 V. Confirmed.   | Direct connection to the Arty works.                                                                                                                          |
| Supply current       | 7 mA per MHz of core clock, maximum. Confirmed.                                                      | About 225 mA at an 8 MHz bus (32 MHz core); about 930 mA at full speed.                                                                                       |
| Clock multiplier     | CLKMUL sampled at RESET. Low gives 4x on the DX5-133; high is undefined. Confirmed.                  | Tie CLKMUL low.                                                                                                                                               |
| Bus clock range      | 8 to 33 MHz, through an internal PLL. Confirmed.                                                     | The clock cannot be slowed below 8 MHz or single-stepped. Stepping is done with RDY#, and the carrier board must have good signal integrity at 8 MHz or more. |
| Static design        | The core is static, but the PLL needs at least 1 ms of stable clock after a clock change. Confirmed. | Stopping the clock is only possible through the Stop Grant sequence (STPCLK#).                                                                                |
| Cache mode           | WB/WT# sampled at RESET. Low makes the whole cache write-through. Confirmed.                         | Tie WB/WT# low to start.                                                                                                                                      |
| Unused inputs        | Active-low inputs pulled up to VCC (about 20 kΩ); active-high inputs tied to GND. Confirmed.         | Tie-off values.                                                                                                                                               |
| Parity               | DP0 to DP3 pulled up to VCC when parity is unused. Confirmed.                                        | Four more pull-ups.                                                                                                                                           |
| JTAG                 | IEEE 1149.1 TAP on TCK, TMS, TDI, TDO, with no TRST#. Confirmed.                                     | 2x10 ARM JTAG header on the carrier.                                                                                                                          |
| IDCODE and BSDL file | Not covered in this datasheet; no BSDL file found online.                                            | Probe the TAP with OpenOCD, and expect to work out the boundary scan cell order by hand if needed.                                                            |

## Pin plan

The chip has roughly 100 signal pins and the Arty about 80 usable I/O, so not everything goes to the FPGA.

**To the FPGA (about 65 pins to start):**

| Group          | Pins                                     | Count |
| -------------- | ---------------------------------------- | ----- |
| Data           | D0 to D31                                | 32    |
| Address        | A2 to A17, more as pins allow            | 16    |
| Byte enables   | BE0# to BE3#                             | 4     |
| Cycle outputs  | ADS#, W/R#, D/C#, M/IO#, BLAST#, LOCK#   | 6     |
| Control inputs | CLK, RESET, RDY#, BRDY#, KEN#, INTR, NMI | 7     |

Partial address decode means memory aliases, which is fine: the reset fetch at FFFFFFF0 lands at the top of whatever window is decoded. Any spare FPGA pins go to more address bits.

**Tied off through resistors, at their inactive levels:** HOLD, AHOLD, BOFF#, FLUSH#, A20M#, BS8#, BS16#, EADS#, IGNNE#, STPCLK#, SMI#, SRESET, INV, plus DP0 to DP3 pulled up, and CLKMUL and WB/WT# tied low. Active-low inputs get about 20 kΩ to VCC; active-high inputs go to GND.

**Left unconnected or on test points:** PCHK#, HLDA, BREQ, FERR#, PCD, PWT, CACHE#, HITM#, SMIACT#.

**KEN# held inactive** makes every memory read non-cacheable, so more of the CPU's activity shows up on the bus. The prefetch queue still hides some of it.

## Carrier board

A small two-layer PCB:

- 168-pin PGA socket, or a Socket 3 ZIF socket (Socket 3 accepts 168-pin 486 parts)
- 3.3 V power input, with decoupling at every VCC pin
- series resistors (33 to 100 Ω) on every FPGA-connected signal, for signal integrity
- tie-off resistors as above
- 2x10 ARM JTAG header for the Olimex adapter
- connectors that plug straight onto the Arty's shield and Pmod headers

Plugging straight onto the Arty, rather than using jumper wires, matters because the bus never runs below 8 MHz.

## Flow

### Step 1: paperwork

1. Get the Am5x86 datasheet and the Am486 enhanced-family documentation.
2. Search for a BSDL file.
3. Build a pin map, one row per CPU pin: FPGA pin, tie-off, power, or unconnected. This becomes the source for both the PCB and the FPGA constraints file.

### Step 2: carrier board

1. Design in KiCad from the pin map.
2. Order from a cheap PCB fab.
3. Assemble and check power with no chip fitted.

### Step 3: JTAG bring-up, no FPGA logic yet

1. Fit the chip, power it at 3.3 V with a current limit of about 400 mA, and supply an 8 MHz clock.
2. Read the IDCODE with OpenOCD and the Olimex adapter. If the chip has no IDCODE, check the length of the instruction register instead.
3. Use boundary scan SAMPLE while the FPGA toggles each pin, to prove every socket-to-FPGA connection.

### Step 4: bus responder

1. FPGA design: clock generator, reset sequencing, ADS# cycle decode, RDY# response, memory served from block RAM, KEN# inactive.
2. Trace buffer: log address, data, byte enables and cycle type for every cycle.
3. UART link to the PC.

### Step 5: first code

1. Put a far jump at the reset vector, then a short loop that writes to an I/O port.
2. Confirm the trace shows the fetch at FFFFFFF0, the jump, and the I/O writes.

### Step 6: stepping and tooling

1. Hold RDY# inactive to stall the CPU on chosen cycles.
2. Python on the PC: load a memory image, run, stop, dump the trace.

### Step 7: experiments

- Register values after reset, including the CPU signature in DX.
- CPUID results.
- Opcode sweeps compared against an emulator (Bochs or 86Box), looking for undocumented behaviour.
- Bus timing and cycle ordering compared with Intel 486 documentation.
- Write-back cache protocol (WB/WT# tied to write-back, HITM#, INV), once write-through works.
