### Idun v1.2.2 Release

This release brings a new shell interface (`shell.app`) which is fully integrated with Linux. In fact, it makes the experience much closer to simply running modern Linux on your Commodore! Unlike the previous versions, this one boots directly to the shell by default. The previous DOS-work-a-like shell is still available, but no longer needed for running Commodore programs. `shell.app` can run Commodore programs directly, simply, and right alongside Linux programs.

Additionally, this release adds support for the new Commodore 64 Ultimate. Along with all the features present in the C64 version, the C64U includes Turbo Boost and easily launching content files (CRT images, MODs, SIDs, etc.) that are supported by the C64U using the new `run` command.

On the C64(U), the `shell.app` also defaults to the software 80 columns text mode (requires cart with ERAM support). This mode runs smooth and fast on the Ultimate, thanks to Turbo Boost support. On the original C64, it is quite slow, but serviceable. It is recommended for heavy text usage that users take advantage of an HDMI secondary display. *Bonus* the HDMI Linux console, as well as any remote Linux shell, can run all the same commands as `shell.app` and launch Commodore programs directly, simply, and right alongside Linux programs.

_Installation_ should be done as follows from a Linux prompt: `pacman -Sy idun idun-defaults idun-base idun-filebrowser`. Alternatively, download the latest [SD card image](https://drive.google.com/file/d/1tYmL1zDgZj2KxWQS5eT54sy9L9NNPKM7/view?usp=sharing) to replace the one you are using.

If you have a C64U, you should also copy the file `/etc/xdg/idun/Idun_c64u_run_first.cfg` to your C64U "Flash" directory. Run it once to setup your C64U correctly for Idun, then save the settings as the default configuration.

- **New** Linux default shell. This consists of the `shell.app` Commodore program and various Linux software found in the new `idun-defaults` package. `idun-defaults` includes the new default configuration, which makes booting to the shell possible. This can be changed by modifying your configuration file in `.config/idunrc.toml`. So, if you like booting to BASIC or using the old DOS Shell, you can still do that. From BASIC, you can use the command `go "shell"` to start `shell.app`, just as with any other Idun App. There is a `help` command, and you should start there to see the list of commands supported. Because this is built on standard Linux foundations, most of the commands can be run from _any_ Linux shell which you open locally or remotely too!
- **New** C64 Ultimate Turbo CPU support. This is fantastic for the 80 column VIC-II text display, and useful for many Idun programs. The Turbo is normally enabled for running any Idun programs and normally disabled when launching native Commodore programs. It can be controlled in software with the `aceTurboCtl` command, which is documented in the ACE API reference and used in the `liner` and `boxer` screensaver tools.
- **New** Smart `run` command. In `shell.app`, one of the key commands is `run`. This replaces the `load` command from the old DOS Shell and is used to load & run native PRG files on any Commodore. Additionally, `run` is C64 Ultimate "aware". It can be used to run C64U specific files like .CRT cartridges. It is designed to "do the right thing" based on filename extensions. *Bonus* `run` command can control the C64U from another Commodore computer across the network!
- **New** `drives` command. In `shell.app`, the old commands: "mount" and "assign" are replaced with a single command `drives`. It is used to list the current mounts/assigns and to change the disk images (mounts) or default directories (assigns). See `help drives`.
- **New** Machine type detection. The type of Commodore machine is recognized by the cartridge and reported by the `ffetch` program in `shell.app`. It is also stored in the `$IDUN_SYS` variable in a compressed form. See code for `ffetch` to understand more.
- **New** Startup configuration. The `[start]` config setting lets you change the firmware and booter selection when the Mode switch is disabled. Allows two completely independent boot sequences based on the Mode switch and what you configure in `[start]` and `[mode]`.
- **New** The tool extension for high-resolution graphics has been extended to provide a common API for both VDC and VIC-II. Now, graphical program can easily be created that detect the video in use and adjust to it automatically. The new API is `toolx/gfx.asm`.
- **New** The tool extension for 1351 mouse support has been extended to provide a common API for both VDC and VIC-II. It can be found in `toolx/pointer.asm`.
- **New** `boxer` tool is a simple screensaver that draws rectangles using `toolx/gfx`. Runs on VDC and VIC-II displays.
- **Update** liner, mousetest, and cube.app graphical programs now run on C64/VIC-II, in addition to C128/VDC.
- **Update** `koala.app` is removed, and replaced with the `koala` tool instead. The new version can take filenames to display as command arguments.
- **Update** Keyboard switching using Com+k now works in Linux shell.
- **Update** `basic` command. This command now resets the cartridge into BASIC mode with Idun activated on all machines. For C128, use `go64` to switch modes as well.
- **Update** Idun's Linux terminal now identifies itself as a "vt100" term. Previously used "ansi", and still does for virtual modem (M: device).
- **Update** `io.devsock` renamed to `io.socket` in configuration file.
- **Update** Issue #31; `go64` command now works from BASIC on the C128 to switch into C64 mode _with the cartridge activated_.
- **Update** Filebrowser shell window works with `idunsh` to launch programs remotely.
- **Fix** Scrolling issues with some Linux terminal programs.
- **Fix** ERAM garbage collection was not tracked correctly causing crash.
- **Fix** Issue #33. Plugging in a gamepad crashes `idunio` process.
- **Fix** Issue #26. `diskcopy` is functional again.
- **Fix** `del` command failing to delete file in DOS shell.
- **Fix** `copy` command not copying files between virtual drives.
- **Fix** All prior hotfixes to v1.2.1 are included.

### Idun v1.2.1 Release

This release adds support for the CP/M operating system and applications on both the C128 & C64. Additionally, there are some new features for the Idun Shell and the Arcade.app, and several other updates. See list below:

- **New** Support for CP/M. This is provided by the new package [idun-cpem](https://github.com/idun-project/idun-cpem). The *CPem* emulator runs standard Z80 CP/M programs on a super-fast Z80 that is emulated by the RPi. This works for C64/128. On the C128 only, you can also launch native Z80 programs on the CPU in your C128. Check out the README at the link above.
- **New**  Software 80-column text display is available on the C64 (or C128 in 64 mode). This is only officially supported for CPem and is the default display you get when you start it with `go "cpm"` from Basic. *Note:* For both C128 and C64, you can toggle 40/80 column mode in CP/M using `Com+4` and `Com+8` hotkeys.
- **New** `assign` command in the shell lets you change paths for virtual drives and updates those changes in the configuration file, These changes persist across resets and power-cycles. Virtual drives (E:, G:, additional ones you add) can thus be setup easily to point to different paths. `help assign` will show the command usage.
- **New** Switching between drives in the shell can now be done more simply by just type `a:`, `c:`, etc. You used to have to type `cd a:`, for example.
- **New** You can send IEC device commands from the shell by using the `@` symbol. You have to select the target device first. For example, to format a disk in drive A: from the shell, first do `a:` to switch drives, followed by `@n0:mydisk,1a`. All CBM-DOS supported drive commands (and whatever "extended" commands your IEC device allows) should work in this manner.
- **Update** The searching for games by name in `arcade.app` now uses "fuzzy" search. Just start typing any letters in the game's name, and fuzzy search will quickly locate the game you thought you wanted.
- **Update** `mount` command now updates the configuration file when you mount a Virtual Floppy, so that the mounted drive persists across resets and power-cycles. `help mount` will show the command usage.
- **Update** `cd` command with no arguments now shows the current working directory.
- **Update** `makerom.lua` script can now make a patched 8KiB kernal ROM for the C64. Just run it from the shell as `lua makerom.lua /8`. _Note:_ For the newer C64c 16KiB ROM, you can simply use the U32 patched ROM file that is normally created by the script.

How to update: If you are already on at least v1.1.7, then you can update from the Linux prompt with `sudo pacman -Sy idun idun-base idun-cpem`. Alternatively, you can just download the new [SD card image](https://drive.google.com/file/d/1tYmL1zDgZj2KxWQS5eT54sy9L9NNPKM7/view?usp=sharing) to replace the one you are using.

Full Changelog: https://github.com/idun-project/idun-cartridge/compare/v1.2.0...v1.2.1

### Idun v1.2.0 Release
This is a significant release that adds support for the Commodore 64 and the addition of 4 Megabytes of external, expansion memory ("ERAM").

- **New** From this version onward, the idun-cartridge works on the C128 and C64. The Mode switch is the toggle. When ON (switch toward the expansion port), the cart boots on a C64 or in C64 mode on the C128. When OFF, the cart is partially invisible to the C64. It can still be accessed if you load software that uses the cart, but there is nothing loaded into the C64 RAM and it will behave as if there is no cartridge. All software is cross-platform except for those programs that require the VDC or z80 CPU.
- **New** The cart and its software now provide a 4M RAM expansion called "ERAM". This additional memory is used by the `resident` command in the shell and is a major enabler for future software. If you have the **2023 edition** of the cart, then ERAM will not work unless you install a bodge wire, which is described on the Discord. See [ERAM doc](https://github.com/idun-project/idun-cartridge/blob/main/doc/eram.md)
- **New** `go` command added to BASIC wedge allows launching any Idun app directly. The command by itself ("go") launches the default shell set in the config file. With unmodified config, this replaces the old `dos` command. You can also specify the app, such as by using `go "dos"` or `go "arcade"`. The quotes are required. The ".app" extension is NOT required. _Note_: This is the new way to start arcade mode if you do not want it to start automatically (see configuration file!).
- **New** partial implementation for issue #18 allows direct commands to IEC devices using the built-in wedge. Start by selecting an IEC device using `@#n` where n is the device number. Then, any command following `@` is sent to the device (i.e. `@n:mydisk,4d` to format a diskette).
- **Fix** issue #17 was preventing use of `pacman` to update the idun software from the idun terminal. Works now!
- **Fix** issue #19 so you can again use the <STOP> key to exit from a `mode debug vdc` command.
- **Update** Soft-switches ($de7e & $de7f) that turn EXROM on/off improved and working on C64.
- **Update** A few changes in default configuration for [mode] settings and ERAM. You should merge or copy `idun-sys/idunrc.toml` to your `.config/idunrc.toml` to be up-to-date.
- **Fix** A broader array of VIC-IIe revisions are working reliably. I have tested R4-R7. Thanks to Chad for the loaner VIC chips.
- **Fix** When exiting the shell, the current virtual device selection is preserved. This makes it simple to continue to access virtual drives from BASIC if you have the ROM patch.
- **Fix** Launch an app from the shell no longer requires providing the ".app" extension.
- **Fix** aceFileStat routine had a bug that crashed the cart.
- **Fix** Disk image directories causing crash if the disk was empty.
- **Fix** Filesystem ownership for all the files in `idun-base` (should be owner/group idun/idun, NOT root!).

How to update: If you are already on at least v1.1.7, then you can update from the Linux prompt with `sudo pacman -Sy idun idun-base`. Alternatively, you can just download the new [SD card image](https://drive.google.com/file/d/1tYmL1zDgZj2KxWQS5eT54sy9L9NNPKM7/view?usp=sharing) to replace the one you are using. 

Full Changelog: https://github.com/idun-project/idun-cartridge/compare/v1.1.9...v1.2.0
