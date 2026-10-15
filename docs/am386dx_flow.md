# Am386DX-40 Flow

Plan for behavioural reverse engineering of an AMD Am386DX-40: the Arty A7-35T acts as the whole motherboard (clock, reset, memory) and records every bus cycle. Silicon-level work is covered briefly at the end.

Pin and voltage details are from memory. Check them against the AMD Am386DX/DXL datasheet before building.

## Open question

Which package is the chip? It came in 132-pin ceramic PGA and 132-pin PQFP. PGA-132 sockets are standard catalogue parts. The PQFP version is usually soldered to a motherboard and is much harder to socket.

## Equipment

- **Socket:** PGA-132, off the shelf.
- **Carrier board:** a cheap two-layer PCB holding the socket, buffers and headers for the Arty. This replaces the ZIF and perfboard plan for this chip; hand-wiring 132 pins is possible but miserable.
- **Level shifting:** 74LVC245 buffers powered at 3.3 V. The 386 inputs accept TTL levels, so 3.3 V drives them directly, and the LVC inputs tolerate the 386's 5 V outputs. Bus directions are known in groups of 8, so per-pin translators are not needed.
- **Clock driver:** CLK2 likely needs a full 5 V swing, so it gets its own 5 V buffer.
- **Power:** 5 V bench supply good for about 0.5 A, with decoupling capacitors at the socket.

## Pin budget

The chip has roughly 83 signal pins, slightly more than the Arty's usable I/O. It fits at about 68 by:

- capturing only address bits A2 to A21
- tying unused inputs (coprocessor pins, BS16#, NA#) to fixed levels with resistors

## Flow

1. **Bring-up:** drive CLK2 and RESET, then check that the first fetch appears at address FFFFFFF0.
2. **Bus responder:** serve code and data from block RAM, and log every cycle (address, data, cycle type) to a trace buffer read out over UART.
3. **Stepping:** hold READY# inactive to freeze the CPU mid-cycle. This steps it one bus cycle at a time without stopping the clock.
4. **Clock speed:** run well below 40 MHz, since CLK2 is twice the CPU speed (80 MHz at full rate). Check the datasheet for a minimum clock: the DXL variant is fully static, the plain DX may not be.
5. **Experiments:** script opcode sweeps from Python and compare results against an emulator such as Bochs or 86Box. Targets: reset register values, LOADALL, ICEBP, and bus timing differences from Intel's part.

## Silicon-level (separate track)

Remove the lid (easy on ceramic PGA), image the die with a metallurgical microscope, then delayer to see the lower metal layers. Delayering involves hazardous acids and is a much bigger commitment.
