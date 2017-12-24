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

## Alias to update and purge
To update everything, including systemd-nspawn containers in `/var/lib/machines`, and purge the pacman cache afterwards, you could create an alias `update` as normal user as follows:
```bash
tee -a ~/.bashrc > /dev/null <<EOF
alias update='sudo pacman -Syu; sudo sh -c "for d in /var/lib/machines/*/; do echo \$d; pacman -Syur \$d; done"; paccache -vrk 2'
EOF
```
