## Retro-approved Packager (`rap`) & Builder (`rapper`)

Welcome to the guide for **`rap`** (**R**etro-**a**pproved **P**ackager) and its companion build tool, **`rapper`**. If you're tired of software that insists on living on someone else's server, you're in the right place. 

Built for retro computing enthusiasts and software archivists, `rap` is a package management ecosystem designed specifically for retro content collections. The goal is refreshingly old-fashioned: keep your collection local, avoid wasting bandwidth, and don't download gigabytes just to discover the package was never going to install anyway. Whether you're curating CP/M disks, Amiga utilities, console ROMs, or an absurdly large archive of binaries, your CPU, disk, and Internet connection have better things to do. 

## How it works

At the core of `rap` is a high-speed, "fail-fast" streaming architecture. Too many modern package systems cheerfully download an entire archive first and ask questions later. `rap` does the sensible thing instead: verify first, move bytes second.

### 1. Sequential Stream Ordering
When `rapper` builds a package (`.tar.zst`), it explicitly enforces `RAPPER.LUA` as the very first 512-byte block in the archive. Hidden files, dot-directories, and previous build artifacts are stripped out automatically.

### 2. Decompression Interception & TCP Flow Control
When `rap` initiates a package download, it pipes the incoming network stream directly through `zstd` and acts as an inline streaming tar parser.

- It reads the first 512-byte header.
- It extracts `RAPPER.LUA` into memory.
- It **pauses reading from the network socket**.

Because `rap` stops pulling network data, standard TCP flow control naturally pauses the remote HTTP server from sending the rest of the payload.

```
[Remote Server] ---> (TCP Stream) ---> [rap Stream Parser]
                                             |
                                   (Intercepts RAPPER.LUA)
                                             |
                                  [Pause & Evaluate Gate]
                                    /                 \
                           (Passes) /                 \ (Fails / Aborted)
                                   v                   v
                     Resume Download & Install      Sever Connection Dropped
```

### 3. Fail-Fast Gatekeeping
While the connection is safely paused, `rap` sandboxes and executes `RAPPER.LUA`. It evaluates target installation paths, checks minimum release requirements, and inspects dependencies.

* **Rapper Dependencies** Ensures required `rappers` are already present in `$HOME`.
* **System Dependencies** Detects your native Linux package manager (`apk` or `pacman`) and offers to install any missing software.

If any check fails—or if you reject a system dependency—`rap` severs the connection cleanly. You save bandwidth, time, and unnecessary downloads without downloading a single unnecessary byte of the payload. If everything checks out, `rap` unpauses the stream, hands control back to the downloader, and continues installation. After this it runs an optional post-install routine that package maintainers write using simple Lua script.

## Hosting rappers on Internet Archive

The Internet Archive is one of the few corners of the modern Internet still focused on preservation instead of recurring revenue. That's why `rap` treats it as a first-class package host. Publish once, let people download directly, and skip worrying about hosting bills, bandwidth quotas, or whether your favorite free tier survives next quarter. 

When you pass a standard IA page URL like `archive.org/details/my-retro-collection` to `rap`, it understands how to locate and download the rapper package file, and seamlessly streams it to your local machine.

### How to Host Your Package

Hosting your own retro-approved packages is pleasantly boring. No VPS to rent, no database to babysit, no Kubernetes cluster, and no monthly invoice reminding you that your files apparently need a subscription now.

1. Create a Free Account -
Head over to [archive.org](https://archive.org) and click **Sign Up**. It is completely free for everyone and only requires a valid email address. 

2. Upload Your Package -
Once logged in, click the **Upload** button (the icon shaped like an up-arrow in the top navigation bar) and select the "Upload Files" option. 
    * Drag and drop your built package (e.g., `my-retro-collection-r1.tar.zst`) into the upload area.

3. Fill Out the Metadata -
The Internet Archive requires a few standard fields to publish your item:
    * **Page Title:** The display name of your package.
    * **Description:** Explain what your package is.
    * **Subject Tags:** Add appropriate tags like `retro`, `software`, `cpm`, `amiga`, or `homebrew` to help others discover your work.
    * **Creator / Mediatype:** Ensure the mediatype is set to `software`.

4. Publish and Share -
Click **Upload and Create Your Item**. Once the Internet Archive finishes its automated processing tasks (deriving metadata and generating the item page), your package is officially live on the internet!

You can now share your retro software with the world. Anyone with `rap` installed can pull it down immediately using your item URL:

```bash
## Users can install your package directly from the Archive
rap install archive.org/details/my_retro_collection
```

## Using rap

`rap` is your client CLI tool for downloading, installing, upgrading, querying, and managing packages.

### Command Syntax

```bash
rap <command> [arguments]
```

| Command | Arguments | Description |
| :--- | :--- | :--- |
| `install` / `add` | `<url\|file>` `[--path <dest>]` | Downloads/verifies metadata, checks dependencies, and installs the package. |
| `uninstall` / `remove` | `<name>` | Runs pre-remove hooks, purges the package directory, and updates the cache. |
| `upgrade` | `[name]` | Upgrades the named package, or all packages, when new revisions exist. |
| `info` | `<name>` | Displays local metadata and fetches live remote Archive.org metadata if available. |
| `list` | `[--rebuild]` | Lists all installed `rappers` and their release numbers. |

### Installing Packages

`rap` accepts local files, raw package URLs, and direct Archive.org item details links.

```bash
## Install from a local package file
rap install ./cpem-disks-r0.tar.zst

## Install directly from a remote web server
rap install https://example.com/packages/cpem-disks-r1.tar.zst

## Install directly using an Archive.org details URL (rap auto-resolves metadata!)
rap install archive.org/details/cpm_the_basics_5_drives
```

Destination Path Heuristics

All packages **must** be installed within `$HOME`. `rap` determines the destination path using the following priority:

1. **Explicit Path Flag:** `--path ~/Games` installs the package to `~/Games/<name>`.
2. **Category Key:** If `category = "Games"` is defined in RAPPER.LUA, it installs to `$HOME/Games/<name>`.
3. **Current Working Directory:** If no flag or category exists, it installs to `./<name>`.

Clean Overwrites & Downgrade Protection

* **Reinstalls / Upgrades:** Installing an updated release cleans out old files before extracting the new stream, preventing orphaned files from corrupting your environment.
* **Downgrades:** Accidental downgrades are blocked. If you attempt to install release `0` over release `1`, `rap` halts execution and instructs you to run `rap remove <name>` first.

### Managing Installed Packages

Upgarding Packages -
The `upgrade` command is used to upgrade a single package when you provide its name, or all of your installed packages. `rap` will query the metadata from the original installation URL to determine if a newer revision is available.
```bash
rap upgrade
```

Viewing Package Details -
The `info` command parses local metadata and natively queries Archive.org APIs for extra metadata (uploader, title, curation date) if applicable:
```bash
rap info cpem-disks
```

Listing Installed Packages -
`rap` maintains a fast lookup cache at `~/.cache/rapper/installed.list`. If the cache is deleted, `rap list --rebuild` can be used to automatically search `$HOME` for installed `.rapper` manifests, and rebuild the cache.
```bash
rap list
```

Removing Packages -
Executing `remove` invokes any custom `remove()` Lua hooks (to clean up custom symlinks or configs) and safely purges the package directory:
```bash
rap remove cpem-disks
```

## Building a rapper

The `rapper` CLI tool handles creating, updating, and auditing your `.tar.zst` retro packages.

### Command Syntax

```bash
rapper <command>
```

| Command | Description |
| :--- | :--- |
| `new` | Generates a fresh `RAPPER.LUA` template or recovers one from an installed package directory. |
| `build` | Validates syntax, strips hidden files, and builds `<name>-r<release>.tar.zst`. |
| `update` | Checks for directory changes, bumps `release` by +1, and rebuilds if changes exist. |
| `check` | Performs a non-destructive diff against the latest release archive and reports deltas. |

### Anatomy of `RAPPER.LUA`

Every package directory must contain a `RAPPER.LUA` file. This file defines package variables and optional lifecycle hooks.

```lua
-- ============================================================================
-- RAPPER.LUA - Package Metadata
-- ============================================================================

-- REQUIRED FIELDS
name = "cpem-disks"
release = 0
description = "CP/M software binary collection for retro drives A-E"

-- OPTIONAL FIELDS
category = "Emulation"
-- For archive.org hosted packages, this can be determined automatically
-- during `rap install`.
url = "https://archive.org/details/cpm_the_basics_5_drives"

-- DEPENDENCIES (Optional)
depends = {  }          -- Requires this rap package in $HOME
uses = { "idun" }       -- Requires these native host packages (apk/pacman)

-- LIFECYCLE HOOKS (Optional)
function install()
    -- Provided with global 'pkgpath' pointing to the destination directory
    print("Post-install setup for: " .. pkgpath)
end

function remove()
    print("Cleaning up before removal of: " .. pkgpath)
end
```

Security & Sandboxing Rules

`rapper` automatically validates `RAPPER.LUA` before building:

* **Privilege Escalation Block:** References to `su` or `sudo` inside `RAPPER.LUA` trigger an immediate build failure.
* **Function Restrictions:** No custom functions are permitted other than `install()` and `remove()`.
* **Data Structure Restrictions:** Complex nested tables are disallowed. Metadata keys must be strings, integers, or simple string arrays (like `depends` and `uses`).

### Packaging Workflow

1.Initialize a Package -
Run `rapper new` inside your payload directory to generate a boilerplate `RAPPER.LUA`:
```bash
cd ~/Development/my-retro-collection
rapper new
```
2.Build the Package -
Run `rapper build`. `rapper` includes all visible files, strips out dotfiles/hidden directories, and compiles the archive:
```bash
rapper build    # Creates my-retro-collection-r0.tar.zst
```
3.Audit Changes (`check`) -
To inspect what files have changed compared to your last built archive:
```bash
rapper check
```
4.Automatic Release Bumping (`update`) -
When you add, delete, or modify files in your collection, run `rapper update`. If deltas are detected, `rapper` automatically increments `release` inside `RAPPER.LUA` and compiles the new `my-retro-collection-r1.tar.zst`:
```bash
rapper update
```

## Retro-Approved Certification

Congratulations. You now have everything you need to build, distribute, and install lightweight, fail-fast software packages that live where they belong: on your own machines and in archives that are meant to outlast the latest startup. Keep your archives tidy, your bandwidth intact, and your retro software genuinely under your control.
