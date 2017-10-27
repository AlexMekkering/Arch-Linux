# Recommended configuration for new installations
This page describes some supplementary but strongly recommended configurations for [new installations](../README.md)
It is assumed that the [necessary essential configuration](essentials-installation.md) has been done.

## Command line editor
[Install][1] package [`vi`](https://www.archlinux.org/packages/core/x86_64/vi/) or [`nano`](https://www.archlinux.org/packages/core/x86_64/nano/)

### Extended vi(m)
[Install][1] package [`gvim`](https://www.archlinux.org/packages/extra/x86_64/gvim/) or [`vim`](https://www.archlinux.org/packages/extra/x86_64/vim/)
> See https://wiki.archlinux.org/index.php/Vim

## Utilities for monitoring your system and its processes, including `ps`, `top` and `uptime`
`pacman -S procps-ng`
## Additional network tools
### Network monitoring tools, including `ping`
`pacman -S iputils`

## Reading Manpages
```bash
pacman -Syu man-pages man-db
```

## Add extra administrative users
### Use Sudo
#### Installation
`pacman -Syu sudo`
#### Configuration
First call `visudo` and remove the `# ` of rule `# %wheel ALL=(ALL) ALL`.
### Add an administrative user
```
useradd -m -G wheel <username>
passwd <username>
```

[1]: ../using-pacman.md
