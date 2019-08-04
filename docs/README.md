![Arch Linux](https://www.archlinux.org/static/logos/archlinux-logo-dark-scalable.518881f04ca9.svg)

This page is the starting point for all installation and configuration documentation of my [Arch Linux] setup.
There's already some great [documentation regarding Arch Linux], so please consult that for background information. My focus will be on the bare minimum of necessities to achieve a specific goal and therefor:
* The number of explicitly installed packages will be kept to a minimum
* For filling configuration files, the command `tee` is used instead of a command line editor (like [`vi`] or [`nano`]), mostly because:
  * it's part of package [`coreutils`] which is already pulled in by mandatory top level packages like [`linux`] and [`systemd`]
  * to execute the command using `tee`, it can be copied on the command line or in scripts
  * the part of the command between the `EOF` tags can still be copied into an editor to your taste

# Installation
* [Basic Installation]
  * **...or** [Minimal Installation using systemd-boot]
* [Essential configuration]
* [Recommended supplements]
* [Possible optimizations]

# Knowledge base
* [Using pacman]
* [Udev rules]
* [Polkit]
* [Systemd-nspawn containers]
* [Using rsync]

## Maintaining the docs

Please see [Maintaining GitHub Pages](GitHubPages.md) for more info.

[Arch Linux]: https://www.archlinux.org/
[documentation regarding Arch Linux]: https://wiki.archlinux.org/
[`vi`]: https://www.archlinux.org/packages/core/x86_64/vi/
[`nano`]: https://www.archlinux.org/packages/core/x86_64/nano/
[`coreutils`]: https://www.archlinux.org/packages/core/x86_64/coreutils/
[`linux`]: https://www.archlinux.org/packages/core/x86_64/linux/
[`systemd`]: https://www.archlinux.org/packages/core/x86_64/systemd/

[Basic Installation]: installation/basic-installation.md
[Minimal Installation using systemd-boot]: installation/minimal_installation.md
[Essential configuration]: installation/essentials-installation.md
[Recommended supplements]: installation/recommended-installation.md
[Possible optimizations]: installation/optimizations.md
[Using pacman]: using-pacman.md
[Udev rules]: udev-rules.md
[Polkit]: polkit.md
[Systemd-nspawn containers]: systemd-nspawn-containers.md
[Using rsync]: using-rsync.md