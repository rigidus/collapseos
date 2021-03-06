# Z80-MBC2

The [Z80-MBC2][link] combines a Z80 and an ATMEGA32A to provide a CP/m capable
computing environment. It features a SD card bootloader which makes running
Collapse OS on it rather simple.

## Recipe

In this recipe, we're going to run Collapse OS on the Z80-MBC2, interfacing
through its serial port. We're going to use the MBC's API to implement BLK on
the SD card.

### Gathering parts

* A Z80-MBC2 computer with its SD card module and a properly flashed "IOS" on
  the ATMega32A.
* A FTDI-to-TTL cable to connect to the serial port.

### Building the binary

Running `make` will yield `os.bin` which is what we want.

### Running on the Z80-MBC2

Mount the SD card on your modern computer and copy `os.bin` as `autoboot.bin`,
overwriting the binary that was previously there.

We also have to copy the blkfs over. This is done by using IOS' drive system.
Each `DSxNyy.DSK` file on the card is a drive, each drive has 512 track of 32
sectors of 512 bytes, so one drive is plenty for our needs. Collapse OS
hardcodes drive 0.

Each drive is part of a set. IOS theoretically supports up to 10 sets, but the
binary shipped by default only accepts 4. You have to overwrite an existing set.
I used set 3. So, copy `blkfs` to file `DS3N00.DSK`. If you want, you can change
the name of the set by changing the contents of `DS3NAM.DAT`.

Put back the SD card in the Z80-MBC2 and power it up by connecting the FTDI
adapter to it (red: VCC, black: GND, green: TX, white: RX).

The FTDI adapter will show up as something like `ttyUSB0` (or `ttyU0` on
OpenBSD). Connect to it with `screen` or `cu` or whatever you like. Baud rate of
the Z80-MBC2 appears to be hardcoded to 115200.

Then, enable IOS program selection by holding RESET and USER at the same time,
wait 2 seconds, releasing RESET, wait 2 seconds, releasing USER. You should then
be given a 1-8 choice.

You begin by selecting the proper disk set, which is through choice 8, then you
select the Autoboot binary through choice 4.

You are now in Collapse OS.

[link]: https://hackaday.io/project/159973-z80-mbc2-a-4-ics-homebrew-z80-computer
