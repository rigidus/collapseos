# TI-84+

The TI-84+ is a machine with many advantages, one being that it's very popular.
It also has a lot of flash memory and RAM.

Its builtin keyboard and screen, however, are hard to use, especially the
screen. With a tiny font, the best we can get is a 24x10 console.

There is, however, a built-in USB controller that might prove very handy.

I haven't opened one up yet, but apparently, they have limited scavenging value
because its z80 CPU is packaged in a TI-specific chip. Due to its sturdy design,
and its ample RAM and flash, we could imagine it becoming a valuable piece of
equipment if found intact.

The best pre-collapse ressource about it is
[WikiTI](http://wikiti.brandonw.net/index.php).

As it is now, with its tiny screen and cumbersome keyboard, Collapse OS is
not really usable on the TI-84+. One could imagine a scenario where one has a
terminal and uses the TI-84+ through the link for its large amount of flash and
RAM. But using it standalone? Nah, not even post-collapse.

Therefore, this recipe is more of a "look, I run!" demo.

## Recipe

This recipe gets the Collapse OS interpreter to run on the TI-84+, using its LCD
screen as output and its builtin keyboard as input.

## Gathering parts

* [zasm][zasm]
* A TI-84+ (TI-83+ compatibility is being worked on. See issue #41)
* A USB cable
* [tilp][tilp]
* [mktiupgrade][mktiupgrade]

## Build the ROM

Running `make` will result in `os.rom` being created.

## Emulate

Collapse OS has a builtin TI-84+ emulator using XCB for display in `emul/hw/ti`.
You can invoke it with `make emul`.

You will start with a blank screen, it's normal, you haven't pressed the "ON"
key yet. This key is mapped to tilde (~) in the emulator. Once you press it, the
Collapse OS prompt will appear. See `emul/hw/ti/README.md` for details.

## Upload to the calculator

### Background notes

Getting software to run on it is a bit tricky because it needs to be signed
with TI-issued private keys. Those keys have long been found and are included
in `keys/`. With the help of the
[mktiupgrade](https://github.com/KnightOS/mktiupgrade), an upgrade file can be
prepared and then sent through the USB port with the help of
[tilp](http://lpg.ticalc.org/prj_tilp/).

That, however, requires a modern computing environment. As of now, there is no
way of installing Collapse OS on a TI-8X+ calculator from another Collapse OS
system.

Because it is not on the roadmap to implement complex cryptography in Collapse
OS, the plan is to build a series of pre-signed bootloader images. The
bootloader would then receive data through either the Link jack or the USB port
and write that to flash (I haven't verified that yet, but I hope that data
written to flash this way isn't verified cryptographically by the calculator).

As modern computing fades away, those pre-signed binaries would become opaque,
but at least, would allow bootstrapping from post-modern computers.

### Instructions

**WARNING: the instructions below will wipe all the contents of your calculator,
including TI-OS.**

To send your ROM to the calculator, you'll need two more tools:
[mktiupgrade][mktiupgrade] and [tilp][tilp].

Once you have them, you need to place your calculator in "bootloader mode",
that is, in a mode where it's ready to receive a new binary from its USB cable.
To do that you need to:

1. Shut down the calculator by removing one of the battery.
2. Hold the DEL key
3. But the battery back.
4. A "Waiting... Please install operating system now" message will appear.

Once this is done, you can plug the USB cable in your computer and run
`make send`. This will create an "upgrade file" with `mktiupgrade` and then
push that upgrade file with `tilp`. `tilp` will prompt you at some point.
Press "1" to continue.

When this is done, you can press the ON button to see Collapse OS' prompt!

## Validation errors

Sometimes, when uploading an upgrade file to your calculator, you'll get a
validation error. You can always try again, but in my own experience, some
specific binaries will simply always be refused by the calculator. Adding
random `nop` or reordering lines (when it makes sense, of course) should fix
the problem.

I'm not sure whether it's a bug with the calculator or with `mktiupgrade`.

## Usage

The shell works like a normal BASIC shell, but with very tight screen space.

When pressing a "normal" key, it spits the symbol associated to it depending
on the current mode. In normal mode, it spits the digit/symbol. In Alpha mode,
it spits the letter. In Alpha+2nd, it spits the uppercase letter.

Special keys are Alpha and 2nd. Pressing them toggles the associated mode.
Alpha and 2nd mode don't persist for more than one character. After the
character is spit, mode reset to normal.

Pressing 2nd then Alpha will toggle the A-Lock mode, which is a persistent mode.
The A-Lock mode makes Alpha enabled all the time. While A-Lock mode is enabled,
you have to enable Alpha to spit a digit/symbol.

Simultaneous keypresses have undefined behavior. One of the keys will be
registered as pressed. Mode key don't work by simultaneously pressing them with
a "normal" key. The presses must be sequential.

Keys that aren't a digit, a letter, a symbol that is part of 7-bit ASCII or one
of the two mode key have no effect.

[zasm]: ../../tools/emul
[mktiupgrade]: https://github.com/KnightOS/mktiupgrade
[tilp]: http://lpg.ticalc.org/prj_tilp/
