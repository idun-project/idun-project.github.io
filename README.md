# idun-project.github.io

### Documentation and Package Repository
Arch Linux packages for the idun-cartridge are hosted on this site in the `/packages` directory. In order to perform updates using the `pacman` command, the repository must be properly configured. If you simply download the default SD image for the idun-cartridge, then this repository will be used, beginning with v1.1.7. In order to configure the repository manually on an existing SD card, perform the following steps.

1. Update the file `/etc/pacman.conf` adding the following at the end of the file.
```
[idun-repo]
SigLevel = Optional TrustAll
Server = https://idun-project.github.io/packages
```
2. Download the public key used for package signing
`wget https://idun-project.github.io/repo_pub.gpg`
3. Install the key
`sudo pacman-key -a repo_pub.gpg`
4. Update package databases
`sudo pacman -Sy`

After this, the idun-repo should be available and you can install/update idun packages with a command such as `pacman -S idun idun-base idunsid idun-filebrowser`. Also, you can shorten this to `pacman -Suy` to update ALL packages you have installed. BEWARE: This has the _potential_ to introduce changes that could break compatibility with the idun software. In particular, new versions of the Linux kernel which are "bleeding-edge" might have compatibility issues. If you find this is happening, please post your findings as an "Issue".
