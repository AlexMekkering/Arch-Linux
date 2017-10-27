# Using pacman
This page describes how to use `pacman`, the package manager for Arch Linux.

## Install a package
The syntax for this is `pacman -Syu <package>` where `<package>` shall be replaced by the name of the package you'd like to install.
For unprivileged users this should be preceded by `sudo `.

i.e. with the following command, an unprivileged user will install package `grep` (assuming sudo is installed and the user is part of the `wheel` group):
```bash
sudo pacman -Syu grep
```
> **Always** do a full upgrade (`pacman -Syu`) when installing packages. Arch Linux **doesn't support** [partial upgrades](https://wiki.archlinux.org/index.php/System_maintenance#Partial_upgrades_are_unsupported).
