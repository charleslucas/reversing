# Bill of Materials

Hardware for a universal chip pin driver/reader: an FPGA drives and samples every pin of a chip under test through a ZIF socket and off-the-shelf package adapters.

Part numbers are examples of the class of part, not verified orders. Check package, pitch and stock before buying. No prices are listed.

## Phase 1: first build (3.3 V DIP parts, 16 pins)

| Item                                                               | Qty   | Status | Notes                                                                                                       |
| ------------------------------------------------------------------ | ----- | ------ | ----------------------------------------------------------------------------------------------------------- |
| Digilent Arty A7-35T                                               | 1     | Owned  | Controller. See [arty_a7_35t_flow.md](arty_a7_35t_flow.md).                                                 |
| Micro-USB cable                                                    | 1     |        | Power, JTAG programming and USB-UART to the PC.                                                             |
| DIP ZIF socket, 48-pin, universal slot (takes 0.3" and 0.6" parts) | 1     |        | The single universal interface to the chip under test.                                                      |
| Pmod cable or 2x6 pin header breakout                              | 2     |        | Brings Pmods JA and JD (16 I/O, 200 Ω series already fitted) out to the ZIF.                                |
| Solderless breadboard, half-size                                   | 0     | Owned  | Two on hand, see [inventory.md](inventory.md). For side circuits; the ZIF socket goes on perfboard (below). |
| Jumper wires, 0.1" male-male and male-female                       | 1 set |        | Signal wiring and manual VCC/GND routing.                                                                   |
| Current-limited bench supply                                       | 1     |        | Powers the chip under test. Set the limit low before first power-up.                                        |

### ZIF socket and connection: spec and pricing

Prices were looked up on 2026-10-11, mostly through aggregators rather than live distributor pages. Confirm before ordering.

**Socket spec:** 48 positions, 2.54 mm pitch, universal slot (accepts 0.3" to 0.6" row spacing), lever actuated, through-hole solder tails on a 0.6" (15.24 mm) footprint.

| Option             | Part                                       | Price (qty 1)                                                        | Notes                                                    |
| ------------------ | ------------------------------------------ | -------------------------------------------------------------------- | -------------------------------------------------------- |
| Recommended        | Aries 48-6554-11 (gold contacts)           | about $21 Mouser (535-48-6554-11), $33 DigiKey (A309-ND), $38 Newark | Name-brand test socket rated for many insertion cycles.  |
| Cheaper name-brand | Aries 48-6554-10 (tin contacts)            | not confirmed, less than the gold version                            | Fine for hobby use; tin wears faster.                    |
| Alternative        | 3M Textool 248-1281-00-0602J               | not found                                                            | Equivalent part. Price it on DigiKey or Mouser directly. |
| Budget             | Unbranded green-lever 48-pin universal ZIF | about $4 plus shipping (eBay)                                        | Works, but contact quality and lifetime vary.            |

**Connection:** solder the socket to perfboard with a pin header beside each row, then run jumper wires to the Arty. A universal ZIF does not sit well in a solderless breadboard: the body covers the tie points and the tails can stretch the contacts.

| Item                                           | Qty    | Price                          | Notes                                                                                      |
| ---------------------------------------------- | ------ | ------------------------------ | ------------------------------------------------------------------------------------------ |
| Perfboard, 0.1" grid, about 7 x 9 cm           | 1      | about $2 to $5 (estimate)      | Carrier for the socket.                                                                    |
| Male pin header, 1x40, 0.1"                    | 2      | about $1 each (estimate)       | Cut to 1x24, one per socket row.                                                           |
| Dupont jumpers, male to female, 20 cm, 40-pack | 2      | about $4 to $7 each (estimate) | Male end into the Arty's female Pmod and shield headers, female end onto the carrier pins. |
| Digilent Pmod cable kit (240-021-2)            | 0 to 2 | $7.00 at Digilent              | Optional, tidier than loose jumpers for JA and JD.                                         |

**Rough total:** $35 to $55 with the Aries socket, about $20 with the budget socket.

Loose 20 cm jumpers are fine for static poking and stimulus up to a few MHz. Faster work needs shorter wiring and a ground wire alongside the signals.

## Phase 2: full 48-pin coverage

| Item                                | Qty    | Status | Notes                                                                                                                                            |
| ----------------------------------- | ------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Arduino Uno-format protoshield      | 0 to 1 |        | Optional. Reaches only the outer shield rows; the Arty's inner-row I/O needs jumpers. A Mega protoshield does not fit.                           |
| Resistors, 330 Ω, 1/4 W or 0603     | 100    |        | One in series per pin for protection on JB, JC and the shield pins. A second FPGA pin on the chip side of each resistor gives direction sensing. |
| 0.1" header strips, male and female | 1 set  |        | Shield and adapter wiring.                                                                                                                       |

## Phase 3: 5 V and 1.8 V parts

| Item                                                        | Qty       | Status | Notes                                                                                    |
| ----------------------------------------------------------- | --------- | ------ | ---------------------------------------------------------------------------------------- |
| SN74LVC1T45 single-bit level translator                     | 48        |        | One per pin, because each has its own direction control. 1.65 V to 5.5 V on either side. |
| SOT-23-6 / SC70 breakout boards, or a custom translator PCB | as needed |        | The translators are surface-mount only.                                                  |
| Adjustable supply or LDO for the target-side rail           | 1         |        | Sets the chip-side I/O voltage (1.8 V, 2.5 V, 3.3 V, 5 V).                               |
| Decoupling capacitors, 100 nF                               | 100       |        | Two per translator, one on each rail.                                                    |

An 8-bit part such as the SN74LVC8T245 needs fewer chips but shares one direction pin across all 8 bits, so it only suits pins whose direction you already know in groups.

## Package adapters (buy as needed)

| Item                                            | Qty       | Notes           |
| ----------------------------------------------- | --------- | --------------- |
| SOIC to DIP clamshell adapter (narrow and wide) | 1 each    |                 |
| TSSOP / SSOP to DIP clamshell adapter           | 1         |                 |
| TSOP48 to DIP48 adapter                         | 1         | Parallel flash. |
| QFP / TQFP to DIP adapter (per pin count)       | as needed |                 |
| QFN to DIP adapter (per size and pitch)         | as needed |                 |

BGA parts need a part-specific socket or soldering to a breakout.

## In-circuit work (optional)

| Item                                    | Qty       | Notes                                                                                                                                                 |
| --------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| SOIC / DIP test clips (Pomona or 3M)    | as needed | Clip onto chips still on a board.                                                                                                                     |
| Sensepeek PCBite probe kit              | 1         | Hands-free spring probes for fine-pitch pins.                                                                                                         |
| FTDI-based JTAG adapter (FT2232H class) | 0         | Owned: Olimex ARM-USB-TINY-H and Adafruit FT232H, see [inventory.md](inventory.md). Boundary scan with OpenOCD or UrJTAG. Needs the chip's BSDL file. |
