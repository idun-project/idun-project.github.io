### Updates to Idun software

The software for idun-cartridge is frequently updated with bug fixes, enhancements, and major new features. This is the beauty of an open and community-driven project. Your contributions are also very welcome!

There is no notification about software updates other than through social media. If you monitor those, then there will be specific update instructions accompanying the release. However, the instructions are usually generic, and all you have to do is utilize the Alpine Linux `apk` tool to update your cartridge.

Before you can know if there are any updates available, you need to first synchronize the catalog of packages to your local system. Do this with `sudo apk update`. Now, the latest software packages available will be visible to your system.

It is possible to update selected idun packages by listing them by name. For example:
```
sudo apk update
sudo apk upgrade idun idun-defaults
```
Will check specifically for new versions of the "idun" and "idun-defaults" software packages. However, there are about 9 packages containing various services, applications, and extras. Unless you specifically know which package you need to update, this is not the best way.

### Full system updates

If you are aware of updates, or just want to check and update to the latest, then the simplest command you can use is:
```
sudo apk -aU upgrade
```
Will combine update/upgrade and upgrade all packages you have installed. Both the Idun-specific software packages and other Alpine Linux software is upgraded. In general, this will be the best command to use, unless you know you only want to update some specific packages.

But there is one **cautionary note**. If you frequently do full system updates, then you will sometimes be installing Alpine Linux software that is ahead of anything that's been officially tested with Idun. The same applies to any new software packages you choose to add to your system from the extensive Alpine Linux repository. While the risk of incompatibility is small, it is not zero, and unforessen bugs or incompatibilities could cause your cartridge to misbehave after an update. If this occurs, you can seek help on Discord or GitHub. You can also download the last SD image for idun-cartridge, and re-image the card you are using.
