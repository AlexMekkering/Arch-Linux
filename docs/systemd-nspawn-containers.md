# Systemd-nspawn containers
This page describes the setup and use of [systemd-nspawn](http://www.freedesktop.org/software/systemd/man/systemd-nspawn.html) containers.
> Please also see [the info concerning this topic for Arch Linux](https://wiki.archlinux.org/index.php/Systemd-nspawn).

# Prerequisites
Please [install](using-pacman.md#install-a-package) package [`arch-install-scripts`](https://www.archlinux.org/packages/extra/any/arch-install-scripts/).

# Use

## Container setup

To create a container named 'container', do the following as root:
```bash
export CONTAINER=container
export FOLDER=/var/lib/machines/$CONTAINER
mkdir -p $FOLDER
pacstrap -icMGd $FOLDER systemd
```

### Add port mapping from host to container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn > /dev/null <<EOF
[Network]
Port=<host port>:<container port>
EOF
```

### Bind host folders in container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn > /dev/null <<EOF
[Files]
Bind=<folder on host>:/mnt
EOF
```

### Enable host device in Container
e.g. for making available `/dev/ttyUSB0` in the container:

#### Bind the device in container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn > /dev/null <<EOF
[Files]
Bind=/dev/ttyUSB0
EOF
```

#### Allow (cgroup) use of the device
First check the device's attributes with `file`:
```bash
file /dev/ttyUSB0
# /dev/ttyUSB0: character special (188/0)
```

Then determine the alias of its primary device number (in this case `188`) from `/proc/devices`:
```bash
cat /proc/devices | grep 188
# 188 ttyUSB

tee -a /etc/systemd/system/systemd-nspawn@$CONTAINER.service.d/override.conf\
 > /dev/null <<EOF
[Service]
DeviceAllow=char-ttyUSB rwm
EOF
```

## Container startup

```bash
machinectl start $CONTAINER
```

or

```bash
systemctl start systemd-nspawn@$CONTAINER
```

or

```bash
systemd-nspawn -bnD $FOLDER
```

## Start a shell

```bash
machinectl shell $CONTAINER
```

## Enable at boot time

```bash
systemctl enable machines.target # Only the first time
systemctl enable --now systemd-nspawn@$CONTAINER
```

## Initialize container's host0 network settings

### from within the container

```bash
systemctl enable --now systemd-networkd
```

### from host system

```bash
ln -s /usr/lib/systemd/system/systemd-networkd.service\
 $FOLDER/etc/systemd/system/multi-user.target.wants/systemd-networkd.service
```

### To enable host resolution too

#### from within the container

```bash
systemctl enable --now systemd-resolved
ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

#### from host system

```bash
ln -s /usr/lib/systemd/system/systemd-resolved.service\
 $FOLDER/etc/systemd/system/multi-user.target.wants/systemd-resolved.service
ln -sf /run/systemd/resolve/resolv.conf $FOLDER/etc/resolv.conf
```
