# Boss RC-10R notes

## Disassembly

Bottom black "foot" case fastened with 5 larger screws and 1 smaller: two at the back, two
underneath, and one (plus the small) on the side. Once removed, the black foot case can be removed,
revealing the interior.

In addition, three larger screws reachable from the outside still remains. Two attaches the topmost
"I/O board" to the outer red case, one attaches the lower main board.

TODO: Now what? The main board probably has to come off, requiring removal of the five nuts + discs
fastening the 1/4" instrument jacks. The main board can't be pulled back though, a PCB labelled
"RC-10R PANEL SHEET ASSY 5100065874" is in the way, and nearby there's a parallel flat cable that
connects the main board with the I/O board. Best bet might be a rather large screw that looks like
it fastens the main board to the I/O board, and possibly also the red casing...

## Hardware notes

### RC-10R MAIN BOARD ASSY 5100065873

* CPU covered by QR code sticker labelled "A190422", and in smaller text "65873A190401104".
* Underneath reads "MIMXRT1051DVL6B". Arm Cortex M7: http://www.farnell.com/datasheets/2603846.pdf

* Two ISSI DRAM circuits: http://www.issi.com/WW/pdf/42-45S83200J-16160J.pdf

* A smaller chip. TODO: can't make out the text. Probably on-board flash memory.

* A SD card slot with what looks like a old chewing gum stuck to it (glued shut).

* TODO: No JTAG? Maybe the USB port can be used for debug access somehow.

### RC-10R "I/O BOARD"

* TODO: Hard to make out without further disassembly. This upper case looks attached to every upper
peripheral, like knobs, buttons, LCDs, etc minus the two foot switches. Connects to main board
through some parallel flat cable.

### RC-10R "PANEL SHEET"

* Looks like super simply circuitry for the two footswitches. A set of three cables (1 red, 2 white)
connects the panel sheet with the upper I/O board.
