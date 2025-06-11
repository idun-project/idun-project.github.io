 This repository contains CPem; a modified version of the excellent "RunCPM" emulator by Marcelo Dantas. CPem has been improved to work with the idun-cartridge and to support the real z80 CPU when running programs on a Commodore 128.

## Getting Started

You can install this software on your idun-cartridge using the following pacman command from the Linux prompt: `sudo pacman -Sy idun-cpem`. You also need the "cpm.app" that is part of the cartridge project. This is included from v1.2.1 of idun-cartridge onward.

You can launch directly into CPem from the Commodore BASIC prompt using the command `go "cpm"`. You can also start CPem from the idun-shell by simply typing `cpm`.

Once started, you will be in a CP/M v2.2 compatible OS that can run "vanilla" CP/M text programs. The environment is emulated on the Raspberry Pi's ARM processor, so CP/M programs run super fast. You can use all the standard CP/M commands, plus some additional built-in commands. To see the built-in commands, enter `?`. To see what CP/M software is available, you can enter `drives` to get a list of disk drives, then enter `vol a:`, `vol b:`, etc. to see what is on each drive. You can switch to drive B, for example, by entering `b:` and to user area 1 by entering either `user 1` or `b1:`.

The terminal is VT-100 compatible. Therefore, CP/M programs that are setup to use VT-100 will work best. You can use CPem in 40-column mode and on the Commodore 64. However, be aware that many CP/M programs expect an 80-column text display. If you have weird looking text output from a CP/M program, you should investigate what kind of terminal it is setup to use. CP/M software compatibility problems that can be improved by modifying the CP/M software, its setup, or the idun-cartridge VT-100 emulation abilities can be reported here using GitHub's "Issues" reporting.

## Running programs on real Z80

If you are using the idun-cartridge on a Commodore 128, then you have the best of both worlds- fast, emulated performance along with the ability to launch programs on the C128's Z80 CPU. CPem's command processor will assume that any command with a ".Z80" extension is a native program intended to run on the C128. Such programs are identical to the programs also supported by the `zload` command used to launch them from idun-shell. These programs are most easily created using the ZCC cross-compiler from [idun-zcc](https://github.com/idun-project/idun-zcc). Some sample programs are available on drive `c1:`.

## Development Tools

CP/M might be best remembered for the large selection of software development tools it offered. Default drive `A0:` includes Z80 assemblers and `mbasic`. Other drives include C, Pascal, and BASIC compilers. Plus there is BBCBASIC and a Forth language compiler. All of these CP/M dev tools run very fast and can thus provide an enjoyable way to learn as much as you ever wanted to know about CP/M programming.

The ZCC cross-compiler that is also installed on the idun-cartridge can build both native c128 and CP/M programs. ZCC is based on the excellent Z88DK project. It has an extensive library and provides a more "modern" C programming experience. 

## Access CPem remotely

When you installed the idun-cpem package, it will have created a new `cpm` user account. The reason this account exists is to turn the idun-cartridge into a remote CP/M terminal that you can access from your PC using ssh. Just login using `ssh cpm@idunpi` (default password `idun`). This user account drops you directly into CPem. If you only have a 40-column display for your Commodore, then you might find the PC terminal to be a good alternative. It's also very convenient since you can leave the cartridge powered up and use it this way when your Commodore is turned off.

## Future possibilities...

BIOS extensions and/or driver libraries to allow CP/M programs running on the emulated (accelerated) Z80 to directly access the Commodore's VIC-II or VDC to display text and graphics.
