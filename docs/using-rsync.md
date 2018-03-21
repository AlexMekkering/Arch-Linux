# Using rsync
This page describes how `rsync` can be used to synchronize folders between different locations. The [Arch Linux wiki](https://wiki.archlinux.org/index.php/Rsync) already contains some great info on that so please read that too.

## Installation
[Install](../using-pacman.md#install-a-package) package [`rsync`](https://www.archlinux.org/packages/extra/x86_64/rsync/)

> To be able to use the rsync protocol by running a server-side daemon, `rsync` should be installed on both client and server

### Server side rsync daemon
This part describes the server configuration for an rsync daemon which can be used as target to sync to.

#### Configuration
The rsync daemon uses configuration file `/etc/rsyncd.conf` which can be configured as follows to serve a target named `storage`:
```
uid = 0
gid = 0
use chroot = no
max connections = 4
syslog facility = local5
pid file = /run/rsyncd.pid

[storage]
        path = /mnt/Storage
        comment = Mass storage volume
        readonly = no
```

#### Enabling (and starting) the daemon
The server-side rsync daemon can be started (and enabled) by calling:

```bash
sudo systemctl enable --now rsyncd
```

## Usage
To synchronize some source folders with an rsync daemon's target, use the following command:
```bash
rsync -ahW --info=progress2,flist0,name,remove,stats --bwlimit=81920 --delete <folder1> [<folder2> [<folder3> [...]]] server::storage
```

In which the options are chosen as follows (please also see [rsync's man pages](https://linux.die.net/man/1/rsync):

| option | purpose |
| --- | --- |
| -a | archive mode (most sane options); equals -rlptgoD |
| -h | output numbers in a human-readable format | 
| -W | sync whole files to relieve cpu of checksumming blocks |
| --info=progress2 | show total transport progress |
| --info=flist0 | don't show file list |
| --info=name | show name of changed files |
| --info=remove | show name of removed files |
| --info=stats | show statistics |
| --bwlimit | limit transfer to *n* kB/s (81920 == 80MiB) |
| --delete | delete removed files from target |
