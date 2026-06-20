### Idun v1.3.1 Release

This release marks the first "official" inclusion of modern graphical Linux applications running directly on the cartridge via the HDMI output. These are desktop applications specifically chosen and custom-configured for Idun, and launchable through a Kiosk mechanism that creates a GPU-accelerated Wayland display.

Before you can use the applications, you need to connect the cartridge HDMI to a suitable display. You will also want sound, which can be either through HDMI or the 3.5mm stereo audio out (see Notes below about configuring the audio). The applications use the Commodore keyboard and mouse (mouse optional, not required).

Graphical applications use the kiosk functionality so that you can launch them from the Linux shell and be automatically logged in with the same account. You **must** initialize this functionality by running the command **"kiosk"** from the shell at least one time. It will show the output below.
```
Available kiosk applications:

  xterm              cross-platform, GPU-accelerated terminal emulator
  thor               Blazing fast terminal file manager written in Rust, based on async I/O
  syncterm           SyncTERM retro bbs terminal emulator
  ocp                Open Cubic Player (for retro chip tunes)
  vice               (description unavailable)

5 application(s) available.
```

Launch the application by typing its name in the shell. If you need to recall the list, just run "kiosk" again at any time.

There is plenty to say about each application, but they all have their own documentation online. I will mention here that "thor" is based on "yazi" and is highly-extensible. I have already integrated it with the cartridge features and will continue to add to this in future releases. It is called "thor" because it will give you "God-like control" over the cartridge as the integration matures and you master its usage.

#### Installation

It is highly recommend you install this upgrade by downloading the [SD card image](https://drive.google.com/file/d/1j5v-0p9-eopSoVMuX6h2P_U8w21OJF-Q/view?usp=drive_link) and flashing to your SD card. Future releases will be more automated, but this release is backfilling some "holes" in v1.3.0.

If you really, really want to go the manual update route, follow this recipe. I make no promises...
1. `sudo setup-devd udev`
2. `sudo apk add seatd`
3. `sudo rc-update add seatd default`
4. Edit `/etc/apk/repositories` and replace both "v3.23" paths with "v3.24".
5. `sudo apk update`
6. `sudo apk upgrade --available`
7. `sudo reboot`

#### Notes and Fixes

- **New** Linux applications/kiosk.
- **Update** Improved ANSI graphics handling in shell, especially for Mystic BBS systems. Use `mode ans` before you connect with `telnet`.
- **Update** Improved configuration settings around Mode switch and boot mode selection. Check the comments in `idunrc.toml` under the [firmware], [booter], and [shell] sections.
- **Update** The C= key registers as ALT for Linux apps.
- **Update** Added 75ms keypress delay to internal KVM. Helps with slow responders.
- **Fix** Not explicitly disabling Ultimate Turbo when launching games from `arcade.app`.
- **Fix** Very long output to shell causing hangs.
= **Fix** Broken file permissions on `idunrc.toml`.
- **Fix** "!" key not working for internal KVM.
- **Fix** `resize-fs` command required Internet connection.

**Known Issues**
- USB keyboards controlling the Commodore is broken and needs to be modified for Alpine. It will work fine as a Linux keyboard.

**Notes**
- Don't forget to run `kiosk` first. Only required once after an update.
- `ocp` can be used to play SID files too. BUT, you **must** change the sidconfig under the setup options to use "crSID" mode for the Pi Zero 2, lest it choke itself on the computations with the default "residfp" mode.
- Analog audio is the default. You can change between analog and hdmi out with a simple file edit. Use `sudo nano /etc/asound.conf`.
- Check the "Setup" documentation for resizing your SD card filesystem and setting up the network.

### Idun v1.3.0 Release

This release includes the migration to 64-bit Alpine Linux OS, plus enhancements, fixes, and some new features. Because of the new OS, Idun now works on the full range of Raspberry Pi devices, and with slightly better performance.

For those interested in development, the Lua integration has been made more powerful and easy to use. It is now possible to write a shell tool for Idun in assembly language that can call the functions in a companion Lua module using a simple RPC-style approach. Thus, it is easy to separate what needs to run on the Raspberry Pi and just write functions in Lua that receive arguments and return results to the Commodore program. The `sidplay` tool, as one example, has been updated to take advantage of this new approach.

Another improvement for developers in this release is more complete support for Idun functionality in the Vice emulator (using idun-vice). Support for Windows users has finally been added, along with a pre-built binary for Windows! Many thanks to user **Dwaco** for these contributions! If you are wanting to connect to your cartridge from Vice, you should definitely update to the latest versions. Check out the idun-project/idun-vice repository on Github and the [emulator docs](https://idun-project.github.io/docs/idun-vice/) for more info.
Another area that has been reworked is the internal KVM switching- which allows the keyboard to be toggled between controlling the Commodore or controlling a Linux console or application directly. The new implementation works more reliably, has low latency, AND fully supports a mouse connected to the Commodore too! If you connect a display to the HDMI output of your cartridge you can have full access to applications on the RasPi and easy switching between the two computers. And, remember, the HDMI Linux console can also control your Commodore using the same commands!

Additionally, there are a few new convenience commands that make Linux setup and file sharing easy for new users, or new installs on your SD card.

1. `resize-fs` - This command can be used to expand the root filesystem to fill your SD card. It runs in two phases with an automated reboot.
2. `setup-interfaces` - An Alpine command for setting up your wifi connection with little typing required.
3. `share` - A command for enabling any of the 3 file-sharing options avaialble (Web, WebDAV, and Windows). Web-based sharing is still the default, but is now connected on port 80 by using  `http://idunpi.local` in your browser. The other two servers are easily enabled if you want integration with Windows Explorer or the macOS Finder.

The documentation site has also received a refresh to include all the enhancements in this release.

#### Installation

Since this version is built on an entirely new OS, you must install this upgrade by downloading the [SD card image](https://drive.google.com/file/d/1j5v-0p9-eopSoVMuX6h2P_U8w21OJF-Q/view?usp=drive_link) and flashing to your SD card.

#### Notes and Fixes

- **New** Holding down the C= key during reset causes the cartridge to boot into Idun's BASIC interface instead of the Idun shell. Use the `go` comamnd from BASIC to launch shell or other Idun apps (Issue #32).
- **New** Internal KVM (activation using C=+k) now supports the Commodore mouse (1351 style).
- **New** Moving between virtual drives in the shell is now supported and works the same as from the BASIC wedge. For example, use `@e` in the shell to CD to the E: virtual drive.
- **New** `turbo` command simply reports the clock speed in use; mainly for C64U, but works on all Commodore machines.
- **Update** `sidplay` rewritten to use the `usrcall` interface. `sidplay.lua` is now a regular Lua module with functions callable from the Commodore.
- **Update** Cartridge mapper API updated with more consistent routine names. Also options to avoid double-copy of Lua responses.
- **Update** Vice emulator for Idun Windows compatibility and pre-built Windows binary release.
- **Update** Linux service refactoring. The low-level hardware service is now called `cartmon`. The higher level I/O manager and Lua engine is all contained in the `idun` service, and runs as service under the `idun` user account.
- **Update** The Commodore 64 Backarrow key is now mapped to the Escape key in all terminals.
- **Update** Initial cartridge poweron boots the Commodore to vanilla BASIC. You *must* press the red reset button to activate any Idun software. This results in a more consistent user experience when turning on the Commodore.
- **Fix** Issue #37. ANSI charcters (selected using `mode ans`) were not working in the Soft80 display mode.
- **Fix** Issue #35. `tmux` installation should work again; solved with upgrade to newest Linux.
- **Fix** Issue #34. `dir` command works correctly with virtual drives (e.g. `dir d:`)
- **Fix** Issue #13. $HOME env variable not available in Lua.
- **Removed** The `idun-handler` Lua module is removed; replaced by `usrcall` API instead.

**Known Issues**
- USB keyboards controlling the Commodore is broken and needs to be modified for Alpine. It will work fine as a Linux keyboard.

**Note**: I have worked with the MEGA65 developers on their C64 core. Their next release of the core works perfectly with Idun, just like a C64. More news soon on native MEGA65 support!

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
