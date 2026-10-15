# Am5x86 Flow

Plan for behavioural reverse engineering of the AMD Am5x86-P75 (Am486DX5-133 V16BCG). The Arty A7-35T acts as the whole motherboard: it supplies clock and reset, serves memory, and records every bus cycle. The Olimex ARM-USB-TINY-H handles JTAG bring-up.

Pin numbers, voltages and timing below are from memory. Everything must be checked against the AMD Am5x86 datasheet before the carrier board is designed.

## Why this chip first

- **No level shifters.** The chip runs at about 3.45 V, close enough to wire to the Arty's 3.3 V I/O through series resistors.
- **JTAG boundary scan.** It can be checked with tools already owned, before any FPGA logic exists.
- **Stallable bus.** Holding RDY# and BRDY# inactive stalls any bus cycle indefinitely, which gives single-cycle stepping.
- **x86.** Results can be compared directly against software emulators.

## Key facts to confirm in the datasheet

| Item                                | Expected                                                            | Why it matters                                                                                                                                                             |
| ----------------------------------- | ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Package                             | 168-pin PGA                                                         | Socket choice.                                                                                                                                                             |
| Core supply                         | 3.45 V (about 3.3 to 3.6 V)                                         | Bench supply setting.                                                                                                                                                      |
| 5 V reference pin (VCC5 or similar) | May exist                                                           | Must be connected as the datasheet says, even with no 5 V parts on the bus.                                                                                                |
| Clock multiplier                    | Set by a CLKMUL-type pin, 3x or 4x                                  | Tie-off value.                                                                                                                                                             |
| Minimum bus clock                   | Unknown. The internal PLL probably needs a minimum input frequency. | If it does, the bus must run near rated speed and stepping is done with RDY#, not by slowing the clock. That sets the signal-integrity requirements for the carrier board. |
| Cache mode                          | WB/WT# pin selects write-back or write-through                      | Tie to write-through for simpler bus behaviour.                                                                                                                            |
| JTAG pins                           | TCK, TMS, TDI, TDO                                                  | JTAG header on the carrier.                                                                                                                                                |
| IDCODE and BSDL file                | Unknown                                                             | Without a BSDL file, boundary scan needs the cell order worked out by hand.                                                                                                |
| Internal pull-ups                   | Some inputs have them                                               | Affects which tie-offs are needed.                                                                                                                                         |

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

**Tied off through resistors, at their inactive levels:** HOLD, AHOLD, BOFF#, FLUSH#, A20M#, BS8#, BS16#, EADS#, IGNNE#, STPCLK#, SMI#, SRESET, INV, plus the clock multiplier and cache mode pins. Check each pin's polarity in the datasheet.

**Left unconnected or on test points:** parity (DP0 to DP3, PCHK#), HLDA, BREQ, FERR#, PCD, PWT, CACHE#, HITM#, SMIACT#.

**KEN# held inactive** makes every memory read non-cacheable, so more of the CPU's activity shows up on the bus. The prefetch queue still hides some of it.

## Carrier board

A small two-layer PCB:

- 168-pin PGA socket, or a Socket 3 ZIF socket (Socket 3 accepts 168-pin 486 parts)
- 3.45 V power input, with decoupling at every VCC pin
- series resistors (33 to 100 Ω) on every FPGA-connected signal, for signal integrity
- tie-off resistors as above
- 2x10 ARM JTAG header for the Olimex adapter
- connectors that plug straight onto the Arty's shield and Pmod headers

Plugging straight onto the Arty, rather than using jumper wires, matters if the bus has to run near 33 MHz.

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

1. Fit the chip, power it at 3.45 V with a current limit, and supply a clock.
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
