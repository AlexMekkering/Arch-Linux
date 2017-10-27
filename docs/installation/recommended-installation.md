# Recommended supplements for new installations
This page describes some supplementary but strongly recommended packages and their configuration for [new installations](../README.md).
It is assumed that all of the [necessary essential configuration](essentials-installation.md) has been done.
> To reduce dependencies, the main recommended packages on this page are all part of the `core` repository. In some cases additional notes have been added which refer to possibly useful packages in either the `extra` or the `community` repository.

## Configure unprivileged administrative user
### Install prerequisites
As user `root`:
* [Install][1] package [`sudo`](https://www.archlinux.org/packages/core/x86_64/sudo/)
* Call `visudo` and remove the `# ` of rule `# %wheel ALL=(ALL) ALL`.

### Add an administrative user
```bash
useradd -m -G wheel <username>
passwd <username>
```

## Command line editor
[Install][1] package [`vi`](https://www.archlinux.org/packages/core/x86_64/vi/) or [`nano`](https://www.archlinux.org/packages/core/x86_64/nano/).

> [Install][1] package [`gvim`](https://www.archlinux.org/packages/extra/x86_64/gvim/) or [`vim`](https://www.archlinux.org/packages/extra/x86_64/vim/) for an extended vi editor (see https://wiki.archlinux.org/index.php/Vim).

## Utilities for monitoring your system and its processes
[Install][1] package [`procps-ng`](https://www.archlinux.org/packages/core/x86_64/procps-ng/) which includes `ps`, `top` and `uptime`.

> [Install][1] package [`htop`](https://www.archlinux.org/packages/extra/x86_64/htop/) and/or [`sysstat`](https://www.archlinux.org/packages/community/x86_64/sysstat/) for extended system and performance monitoring.

## Additional network tools
[Install][1] package [`iputils`](https://www.archlinux.org/packages/core/x86_64/iputils/) which includes i.e. `ping`.

## Reading Manpages
[Install][1] packages [`man-db`](https://www.archlinux.org/packages/core/x86_64/man-db/) and [`man-pages`](https://www.archlinux.org/packages/core/x86_64/man-pages/).

[1]: ../using-pacman.md#install-a-package
