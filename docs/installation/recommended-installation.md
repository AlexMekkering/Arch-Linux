# Recommended supplements for new installations
This page describes some supplementary but strongly recommended packages and their configuration for [new installations](../README.md).
It is assumed that all of the [necessary essential configuration](essentials-installation.md) has been done.
> To reduce dependencies, the main recommended packages on this page are all part of the [`core`](https://www.archlinux.org/packages/?repo=Core) repository. In some cases additional notes have been added which refer to possibly useful packages in either the [`extra`](https://www.archlinux.org/packages/?repo=Extra) or the [`community`](https://www.archlinux.org/packages/?repo=Community) repository.

## Command line editor
[Install][1] package [`vi`](https://www.archlinux.org/packages/core/x86_64/vi/).

> Additionally, to have a more intuitive kind of editor, you could [install][1] package [`nano`](https://www.archlinux.org/packages/core/x86_64/nano/).

> For an extended vi editor, you could [install][1] package [`gvim`](https://www.archlinux.org/packages/extra/x86_64/gvim/) or [`vim`](https://www.archlinux.org/packages/extra/x86_64/vim/) (see https://wiki.archlinux.org/index.php/Vim).

## Add an administrative user
### Install prerequisites
As user `root`:
* [Install][1] package [`sudo`](https://www.archlinux.org/packages/core/x86_64/sudo/)
* Call `visudo` and remove the `# ` of rule `# %wheel ALL=(ALL) ALL`.
  > Package [`vi`](https://www.archlinux.org/packages/core/x86_64/vi/) is required for this to work (see the previous [Command line editor](#command-line-editor) section)

### Add the user
```bash
useradd -m -G wheel <username>
passwd <username>
```

## Utilities for monitoring your system
[Install][1] package [`procps-ng`](https://www.archlinux.org/packages/core/x86_64/procps-ng/) which includes `ps`, `top` and `uptime`.

> For extended system and performance monitoring, you could [install][1] package [`htop`](https://www.archlinux.org/packages/extra/x86_64/htop/) and/or [`sysstat`](https://www.archlinux.org/packages/community/x86_64/sysstat/).

## Additional network tools
[Install][1] package [`iputils`](https://www.archlinux.org/packages/core/x86_64/iputils/) which includes i.e. `ping`.

## Reading manual pages
[Install][1] packages [`man-db`](https://www.archlinux.org/packages/core/x86_64/man-db/) and [`man-pages`](https://www.archlinux.org/packages/core/x86_64/man-pages/).

## Additional archivers
[Install][1] packages [`tar`](https://www.archlinux.org/packages/core/x86_64/tar/), [`gzip`](https://www.archlinux.org/packages/core/x86_64/gzip/), [`bzip2`](https://www.archlinux.org/packages/core/x86_64/bzip2/), [`xz`](https://www.archlinux.org/packages/core/x86_64/xz/) and/or [`lz4`](https://www.archlinux.org/packages/core/x86_64/lz4/).
> For much higher compression and decompression speed (at the cost of some compression ratio), you could [install][1] package [`lzop`](https://www.archlinux.org/packages/extra/x86_64/lzop/).

[1]: ../using-pacman.md#install-a-package
