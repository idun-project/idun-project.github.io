There are two text-based shell applications that allow you to run Idun programs, Linux programs, and native Commodore programs simply and directly. Either one can be used by either launching it with the `go` command and/or by setting it as the default shell in the configuration file.

### Linux Shell (Default)
#### Core Functionality
The default configuration file is setup to use the Linux Bash shell program through the `shell.app`, with source in `cbm/shell.app.s`. For running Linux programs, this works exactly as any shell running on Linux and you can freely run all your favorite text-based Linux programs. If you use this shell to start a graphical Linux program, obviously that cannot be displayed by your Commodore. It will, however, work on a display connected to the cartridge HDMI port.

For running Commodore programs, `shell.app` is integrated with Linux to make this seamless. To see the included commands, simply type `help`.

- **Tools** written for Idun using the ACE/Lua API: These can be started by simply typing their name. The shell will detect they are Commodore programs and start them, at which point the program can continue to interact through the shell or take over the Commodore hardware and display. On exit, the user is returned back to the shell. Such programs may accept command line arguments, and these arguments will usually be processed for wildcard expansion.
- **Apps** written for Idun: These programs have the ".app" suffix and must be started using the `go` command. They do not accept arguments and they will take over the Commodore display. They actually overwrite `shell.app`. Most of them are not designed to return to the shell. They simply run until you reset the machine.
- **Native C64/C128** programs: These are legacy programs that are not aware of Idun. They must be started with the `run` command and don't take arguments. They are simply loaded into memory and started by the cartridge with the Idun software likely being over-written in the process. Most programs only terminate by restting the machine, or they may have an option that exits to BASIC. Either way, you will need to reset the cartridge to return to the shell.

#### Startup Process
When you first boot the shell by pressing the cartridge RESET button, it loads the Idun kernel and `shell.app`, then starts a script called `.newshell` in your HOME directory. This displays the output of the Linux program `ffetch` and drops you into the shell. Importantly, the file `.bashrc`, also in your home directory, is run just as usual for Linux shells. But it has many customizations to support the integration with the Commodore. Commands such as `run` and `show` that are designed to access content on the Commodore are handled by routines in the `.bashrc`.

#### Command Dispatcher
A sequence of software components are necessary to make commands that you type in a Linux shell become programs that execute on the Commodore. First, the routines in `.bashrc` turn the raw command into a set of command-line arguments that are passed to the `idunsh` program also running on Linux. `idunsh` is the interface that controls what instructions can be forwarded on to `shell.app`. You can type `idunsh help` to get an idea how it works. To communicate with the Commodore, `idunsh` turns its arguments into one or more Lua function calls, usually relying on the `sys.shell()` built-in Lua function. Lua functions can in turn cause code to run on the Commodore. In this case, it is code that is part of the `shell.app` that is run to ultimately handle the command originally typed at the Linux prompt. `shell.app` has the ability to spawn other programs too - and that's how any Idun tool or App comes to be started. When the program exits, this can all be unwound to return the user to the Linux shell prompt seamlessly.

#### Key Features
- **C64 Ultimate Support**: In addition to what was described above, the `idunsh` can also forward commands to the C64 Ultimate's network control API. That API includes an interface called "runner", and the `run` command in the shell uses `idunsh` to access this. Based on the filename extension, `idunsh` will push the content to the correct C64 Ultimate "runner" API, causing it to load and run. This means that all native content you'd want to run can be stored on your cartridge and easily moved from machine-to-machine. And, any Commoodore with Idun connected to the LAN with your C64 Ultimate can be used to start content remotely.
- **Lua Integration**: If you create software for Idun using Lua script, these scripts can be easily started from the Linux shell using the command `mlua`, followed by the name of the script, and any arguments. Typing `mlua` by itself will also open an interactive Lua session. _Note:_ This is for the embedded Lua scripting engine that is part of the cartridge software. The standard Lua language is also available and can be used via the `lua` command, but that standard interpreter has no direct access into the Commodore.
- **Device Prefixes**: The mounted disk images and assigned directories that are known to Idun using device prefixes (C:, D:, E:, etc.) are understood by the shell in certain contexts. For example `catalog c:` will show a CBM format directory listing. You can also use prefixes for apps started with the `go` or `zload` commands.
- **Fuzzy Search**: The shell has two commands for quickly locating files and directories. `fcd` finds the best matching directory name to the input given, and changes to that path. `ff` finds the best matching filename and stores it in a buffer. The buffer can be appended to the commands `run`, `show`, and `zload` by pressing TAB (CTRL+I on C64).

### DOS-style Shell
The file `dos.app`, with source in `cbm/dos.app.s`, is the DOS-work-alike shell, a command-line interface for the Idun cartridge that emulates the behavior of MS-DOS. In the default configuration, this shell can be selected with the `go dos` command.

#### Core Functionality
 It provides a classic command-line environment where users can enter commands at a prompt. It parses these commands, handles arguments, and executes them.

#### Startup Process
- It includes a resident **Toolbox** (`sys/toolbox.asm`), making a set of common routines available for other tools and applications that are launched from the shell.
- It supports an `autoexec.bat`-like script (`z:autoexec.bat`) which runs automatically on startup to configure the environment.
- It features a "fast reload" capability, caching parts of itself in ERAM to speed up subsequent launches.

#### Command Dispatcher
- The shell has a table of **internal commands** for common tasks (e.g., `dir`, `cd`, `type`, `mem`).
- If a command is not found internally, it attempts to load and execute an **external program** from a disk or virtual drive.

#### Key Features
- **I/O Redirection:** Supports redirecting input and output using `<`, `>`, and `>>`, similar to DOS and Unix shells.
- **Wildcard Support:** Allows the use of `*` in filenames for commands that operate on multiple files.
- **Batch File Execution:** Can run scripts of shell commands using the `exec` command.
- **Drive Management:** Includes `mount` to attach disk images and `assign` to map host directories to virtual drive letters.
- **System Interaction:** Provides commands like `mem` to view memory usage and `info` to display information about system devices.
- **Host Integration:** Features powerful commands like `lua`, `nix`, and `linux` that pass commands through a `tty` interface to be executed on the host system (the Raspberry Pi).
- **Macros:** Implements a `doskey`-like macro system for creating command aliases.
- **Screen Saver:** Includes a simple countdown timer that launches a `blanker` application after a period of inactivity.
