The file `dos.app`, with source in `cbm/dos.app.s`, is the **Idun Shell**, a command-line interface for the Idun cartridge that emulates the behavior of MS-DOS. It is the primary application that loads on startup if you just type the `go` command from BASIC, or if you specify it using `go "dos"`.

Here is a summary of its key features and structure:

*   **Core Functionality:** It provides a classic command-line environment where users can enter commands at a prompt. It parses these commands, handles arguments, and executes them.

*   **Startup Process:**
    *   It includes a resident **Toolbox** (`sys/toolbox.asm`), making a set of common routines available for other tools and applications that are launched from the shell.
    *   It supports an `autoexec.bat`-like script (`z:autoexec.bat`) which runs automatically on startup to configure the environment.
    *   It features a "fast reload" capability, caching parts of itself in ERAM to speed up subsequent launches.

*   **Command Dispatcher:**
    *   The shell has a table of **internal commands** for common tasks (e.g., `dir`, `cd`, `type`, `mem`).
    *   If a command is not found internally, it attempts to load and execute an **external program** from a disk or virtual drive.

*   **Key Features:**
    *   **I/O Redirection:** Supports redirecting input and output using `<`, `>`, and `>>`, similar to DOS and Unix shells.
    *   **Wildcard Support:** Allows the use of `*` in filenames for commands that operate on multiple files.
    *   **Batch File Execution:** Can run scripts of shell commands using the `exec` command.
    *   **Drive Management:** Includes `mount` to attach disk images and `assign` to map host directories to virtual drive letters.
    *   **System Interaction:** Provides commands like `mem` to view memory usage and `info` to display information about system devices.
    *   **Host Integration:** Features powerful commands like `lua`, `nix`, and `linux` that pass commands through a `tty` interface to be executed on the host system (the Raspberry Pi).
    *   **Macros:** Implements a `doskey`-like macro system for creating command aliases.
    *   **Screen Saver:** Includes a simple countdown timer that launches a `blanker` application after a period of inactivity.
