### Adding Software

The idun-cartridge SD card image includes everything necessary for using the cartridge's "official" features. However, it remains a pretty minimalist Alpine Linux system underneath that can be hugely expanded, if you want to. The Alpine Linux software repositories are extensive; including all of the most popular Linux applications with easy installation through the `apk` tool.

There are lots of ways to discover software for Linux that may be of interest to you. Once you are aware of the software's name, you can usually find the package name that it uses on typical Linux distros, including Alpine. You then use `apk search <package>` to find it from the idun-cartridge. Or, you can access the [alpinelinux.org](https://alpinelinux.org/) site and use the "packages" tool to search there.

When you have located a package you want to install, the commands are pretty easy:
```
sudo apk update         # Ensures your local catalog has the latest releases
sudo apk add <package>
```

Those are just the 3 most basic commands for `apk`. You can learn much more about its capabilities from the [Alpine User Handbook](https://docs.alpinelinux.org/user-handbook/0.1a/Working/apk.html).

### Adding Kiosk Software

A lot of modern Linux software is not going to be able to run using just the shell terminal that Idun provides on your Commodore's display. Graphical programs, and even modern text programs that use a sophisticated TUI, need to run on a second display connected to the cartridge HDMI output. This is the purpose of the `kiosk` command that is included, and provides an easy way to add programs that you can start from your Commodore display, but that actually run full-screen on the HDMI display. You can add new apps to the Kiosk using:
```
sudo apk update
kiosk app <package>
```

The `kiosk` command will fetch and install the package, and add its name the the list of Kiosk-compatible apps, so that you can start it from the Commodore shell. This is what you should do for all graphical applications. For TUI applications, you can start them on the HDMI display by using `xterm <tui-app-name>`. The Xterm program is already part of the Kiosk, and it will launch and then launch the app within. If you want to make it even simpler, you can also use `kiosk app xterm <package>` to add the TUI app to Kiosk and allow you to launch it from the Commodore shell using simply the app's name.

In case you want to remove an app, you can use `kiosk remove <package>`. This will not actually delete the software from your system. It only removes it from the Kiosk list, and you will need to use `apk` to remove it from your system.

### Adding Retro Content

As you know, there's a lot more than just Linux software that you want to install on your idun-cartridge. Many Commodore programs, games, demos, music, documents, art work, and whatever related multimedia files can be stored and accessed as content. Idun even provides a means to manage and organize all this retro-content. For starters, you will want to learn to use the `rap` program. It allows you to easily install full content collections on your system, manage updates, locate them, remove them. etc. There is [full documentation](../rapper.md) for this system that includes instructions for creating your own packages too! Here's a quick summary for using `rap`.

Installing Content -
The `install` command takes an HTTPS URL identifying the package location, and downloads it to your cartridge.
```bash
# Install directly from a remote web server
rap install https://example.com/packages/cpem-disks-r1.tar.zst

# Install directly using an Archive.org details URL (rap auto-resolves metadata!)
rap install archive.org/details/cpm_the_basics_5_drives
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

You will notice that `rap` usually works from a URL. These URL's are published by the community as they become available. For the latest details, check the `#rappers` channel on the Discord.