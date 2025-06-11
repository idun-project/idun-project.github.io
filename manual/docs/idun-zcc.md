## idun-zcc

This repository contains packaging scripts and C library patches to easily install the Z80 compiler, assembler, and C128 libs from the [Z88DK project](https://z88dk.org) on the idun-cartridge.

Using the installed tools, you can easily build C and Z80 asm programs for the Z80 directly on the cartridge, then launch those Z80 programs on your C128 from the idun Dos shell with the `zload` command.

[<img src="https://img.youtube.com/vi/EeYdJ8bXxik/maxresdefault.jpg" width="400">](https://youtu.be/EeYdJ8bXxik?feature=shared)


How to Use
----------
The main tool for using the Z80 compiler/assembler is `zcc`. This runs on the Raspberry Pi in the idun-cart and cross-compiles Z80 code for the C128. In most cases, you just need a command line like this to build your program:

```
zcc +c128 <.c files> <.asm files> -o <progname>
```

Any number or mix of both C and asm files is supported, and the .c files also support inline assembly code too. The entry point to your program is the `main()` function in any of the .c files. Once built, you can directly launch the Z80 program from the idun shell using `zload <progname>`.

How to Install
--------------
Grab the latest SD card image for the idun-cart, which includes this package by default. Or, download the release from this repository using `wget` and install it on your cart using `sudo pacman -U idun-zcc-*`. You'll also need to update the main idun package (adds `zload`) and the content from the `idun-base` repository to get the sample code files.

What's Included
---------------
The C and asm source code for targeting the C128 is included in this repository with the patches needed to make binaries that are compatible with the idun-cart. Additionally, this provides a place to add/contribute new library code that works with the idun-cart.

You also get all the supporting tools and documentation from the Z88DK project installed from the package. Technically, you can use it to build for other Z80 systems (about 100 systems supported). To learn more, definitely check out both the Z88DK [home page](https://z88dk.org) and their [git repository](https://github.com/z88dk).

Important Technical Notes
-------------------------
This works really well on the idun-cart, allowing near seamless and fast launching/switching between 6502 and Z80 programs. It is (imo) a far better user experience than CP/M ever was, at least on the C128. This is mainly accomplished with adherence to memory partitioning.

- The 6502 and idun software owns RAM bank 0, and uses RAM bank 1 for dynamically allocated high-speed cache.
- The Z80 programs run out of RAM bank 1, and can use 52K for code and data starting from $3000.
- The `zload` command does all the setup necessary to load and launch the Z80 program plus cleanly return to the shell when the program exits.
- An additional 8K of RAM 1 ($1000-$2FFF) is set aside for VIC-II use by the Z80 program. This is enough to support text mode with custom character sets, bitmap mode, or hardware sprites.
- The Z80 ROM BIOS is "just there" from $0000-$0fff. This might be useful for accessing peripherals such as the 1571 floppy drive, etc.
- Of course, the Z80 program has full control over the VDC, VIC-II, SID, MMU, and CIA chips while the program runs. It is likely possible to put these things in a state that causes the shell to crash on exit, though.

What about CP/M?
----------------
You can (in theory) use this package as installed to also build Z80 programs for use with C128 CP/M. Just modify the compile command to use `zcc +cpm ...` This has not been tested.

TODOs
-----
Helpful enhancements include, but aren't limited to:

- [ ] Standard C library functions for file access (e.g. open, read, write, close). These would give Z80 programs easy access to virtual drives, files, and devices on the idun-cart.
- [ ] Better VIC-II support, such as hardware sprite usage from C.
- [ ] Porting over some reasonable text mode UI support.
- [ ] Porting over or creating more demo and application sample code.
