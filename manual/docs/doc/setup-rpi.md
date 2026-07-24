### Logging in to the Raspberry Pi

From the Idun shell, you can access Linux to setup networking and other services. You will be logged in as user `idun` automatically, and you will have `sudo` ability. The default password is "idun", and the default root password is "Commodore".

The idun-cartridge ships with `ssh` and the idun-filebrowser enabled. You should attach the cartridge to your network using wired Ethernet and/or setup the WiFi connection.

If your network supports `.local` host names, then your cartridge should appear on the network as host "idunpi" or "idunpi.local". If not, then use the Linux prompt to discover the cartridge ip address (`ip addr`). Then, use `ssh` or browse to `http://idunpi.local:80` for the idun-filebrowser.

### Configure Networking after connecting cartridge to Commodore

1. If you have an Ethernet port on your Pi, or use a USB dongle that provides one, the networking over wired Ethernet should "just work." You can check the status and see your DHCP assigned IP with the command `ip addr`.
2. If you intend to use WiFi, you can set it up by running `sudo setup-interfaces` from the shell. The interface you want is `wlan0`. It will show a list of access points and prompt you for your WiFi password. _Note_: You sometimes need to do a reboot (`sudo reboot`) after you complete the setup to ensure you are connected.

### Setup Kiosk Applicatons

Idun is not limited to only Commodore graphics and text-based Linux applications. Modern, graphical, applications that display their output on a second screen connected to the cartridge HDMI are officially supported. Connecting a second screen and using the kiosk is optional, but significantly enhances the usefulness of your Commodore.

If you choose to use kiosk, connect the second display to the cartridge HDMI and ensure the Linux console initial boot screen is displayed. If the screen is blank, you may need to reboot your cartridge with the HDMI connected. Do this with `sudo reboot`. After boot, the second screen will be sitting at a login prompt, but there is no need to login.

The first time you use it, run `kiosk` from the Idun shell. This only needs to be done one time and it will show you the list of available applications that you can now launch on the second screen. From then on, typing any one of these application names in the shell will launch the app in full-screen mode. There are a couple of other times when you will need to run the `kiosk` command itself.

1. You can add your own kiosk apps using the `kiosk app <package>` command. The "package" is the name of any APK package available in the Idun or Alpine Linux package repositories. _Only graphical applications_ should be added in this way. If you want to run a text-based application on the second screen, you can install it with `apk add` and run it by simply typing `xterm <program>` at the Idun shell.
2. If you want to restore the default configuration for the included kiosk apps, or set it to the new defaults after an upgrade, you do this by typing `kiosk --replace-config`. The kiosk is part of the `idun-defaults` APK package; therefore, updates to that package may not be fully configured on your cartridge until you run this command.

### Setup LAN file sharing

Type `share` at the shell prompt to see the list of avaiable commands, and `share status` to see which of the file sharing services are currently active. There are also commands to start/stop the individual services and to set the password to be used when accessing them.

By default, filesystem access to your cartridge is available from other computers on your LAN using a web-based filebrowser. You should be able to connect `http://idunpi.local` and login using the `idun` username and default password. Files and directories can be managed easily through the web interface. The web app also supports Drag & Drop, remote text file editing, and a remote shell to control your Commodore.

If you want to use Windows file sharing, it can be enabled by running `share start windows` from the Idun shell. Windows logins use the same `idun` username and password. If you want to use the WebDAV file sharing protocol, it can also be enabled by running `share start webdav`.

### Resize file system / extend root partition

Idun includes a command to expand your filesystem to use the full space on your SD card. It is a two step process that automatically reboots the RasPi.

1. Run `sudo resize-fs` from the shell.
2. Wait for the reboot to complete, which will leave you at the BASIC prompt.
3. Reset the cartridge. The disk space displayed on the shell screen should reflect the change.

### Build open source code (optional)

1. Clone this repository to your idun home directory: `git clone https://github.com/idun-project/idun-cartridge`
2. `cd idun-cartridge && ./setup.sh` -installs additional packages such as bastext and idun-zcc
3. `cd cbm && make` -builds cartridge software
4. `sudo make install` from `cbm` directory to make it active

### Emulation of the cartridge on the Raspberry Pi (optional)

Install the modified idun-vice emulator using `sudo apk add idun-vice`, and see [idun-vice](https://github.com/idun-project/idun-vice).
