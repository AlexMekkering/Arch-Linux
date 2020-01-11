# Recommended supplements for new installations

This page describes some supplementary but strongly recommended packages and their configuration for [new installations].
It is assumed that all of the [necessary essential configuration] has been done.
> To reduce dependencies, the main recommended packages on this page are all part of the [`core`] repository. In some cases additional notes have been added which refer to possibly useful packages in either the [`extra`] or the [`community`] repository.

## Command line editor

[Install] package [`vi`].

> Additionally, to have a more intuitive kind of editor, you could [Install] package [`nano`].
> For an extended vi editor, you could [Install] package [`gvim`] or [`vim`] (see <https://wiki.archlinux.org/index.php/Vim).>

## Add an administrative user

### Install prerequisites

As user `root`:

* [Install] package [`sudo`]
* Execute:

  ```bash
  tee /etc/sudoers.d/wheel <<EOF
  %wheel ALL=(ALL) ALL
  EOF
  chmod 440 /etc/sudoers.d/wheel
  ```

### Add the user

```bash
useradd -m -G wheel <username>
passwd <username>
```

## Utilities for monitoring your system

[Install] package [`procps-ng`] which includes `ps`, `top` and `uptime`.

> For extended system and performance monitoring, you could [Install] package [`htop`] and/or [`sysstat`].

## Additional network tools

[Install] package [`iputils`] which includes i.e. `ping`.

## Reading manual pages

[Install] packages [`man-db`] and [`man-pages`].

## Additional archivers

[Install] package [`tar`].
> For much higher compression and decompression speed for tar (at the cost of some compression ratio), you could [Install] package [`lzop`].
> [`gzip`], [`bzip2`], [`xz`] and [`lz4`] are already installed as dependencies of either [`linux`] or [`pacman`].

## Adding AUR package management with yaourt

### Prepare

```bash
sudo pacman -S --needed base-devel git
```

### Install package-query

```bash
git clone https://aur.archlinux.org/package-query.git
cd package-query
makepkg -sri
cd ..
```

### Install yaourt

```bash
git clone https://aur.archlinux.org/yaourt.git
cd yaourt
makepkg -sri
cd ..
```

Now you can use `yaourt` to update your system, install some packages etc.

### Cleanup

```bash
rm -rf package-query yaourt
```

Please also check [some more optimizations for a new installation].

[new installations]: ../README.md
[necessary essential configuration]: essentials-installation.md
[Install]: ../using-pacman.md#install-a-package
[Command line editor]: #command-line-editor
[some more optimizations for a new installation]: optimizations.md

[`core`]: https://www.archlinux.org/packages/?repo=Core
[`extra`]: https://www.archlinux.org/packages/?repo=Extra
[`community`]: https://www.archlinux.org/packages/?repo=Community

[`linux`]: https://www.archlinux.org/packages/core/x86_64/linux/
[`pacman`]: https://www.archlinux.org/packages/core/x86_64/pacman/
[`vi`]: https://www.archlinux.org/packages/core/x86_64/vi/
[`nano`]: https://www.archlinux.org/packages/core/x86_64/nano/
[`sudo`]: https://www.archlinux.org/packages/core/x86_64/sudo/
[`procps-ng`]: https://www.archlinux.org/packages/core/x86_64/procps-ng/
[`iputils`]: https://www.archlinux.org/packages/core/x86_64/iputils/
[`man-db`]: https://www.archlinux.org/packages/core/x86_64/man-db/
[`man-pages`]: https://www.archlinux.org/packages/core/x86_64/man-pages/
[`tar`]: https://www.archlinux.org/packages/core/x86_64/tar/
[`gzip`]: https://www.archlinux.org/packages/core/x86_64/gzip/
[`bzip2`]: https://www.archlinux.org/packages/core/x86_64/bzip2/
[`xz`]: https://www.archlinux.org/packages/core/x86_64/xz/
[`lz4`]: https://www.archlinux.org/packages/core/x86_64/lz4/

[`htop`]: https://www.archlinux.org/packages/extra/x86_64/htop/
[`gvim`]: https://www.archlinux.org/packages/extra/x86_64/gvim/
[`vim`]: https://www.archlinux.org/packages/extra/x86_64/vim/
[`lzop`]: https://www.archlinux.org/packages/extra/x86_64/lzop/

[`sysstat`]: https://www.archlinux.org/packages/community/x86_64/sysstat/
