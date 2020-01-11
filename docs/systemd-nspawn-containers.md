# Systemd-nspawn containers

This page describes the setup and use of [systemd-nspawn] containers.
> Please also see [the info concerning this topic for Arch Linux].

## Prerequisites

Please [install] package [`arch-install-scripts`].

## Use

### Container setup

To create a container named 'container', do the following as root:

```bash
export CONTAINER=container
export FOLDER=/var/lib/machines/$CONTAINER
mkdir -p $FOLDER
pacstrap -icMGd $FOLDER systemd
```

#### Add port mapping from host to container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn <<EOF
[Network]
Port=<host port>:<container port>
EOF
```

#### Bind host folders in container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn <<EOF
[Files]
Bind=<folder on host>:/mnt
EOF
```

#### Enable host device in Container

e.g. for making available `/dev/ttyUSB0` in the container:

##### Bind the device in container

```bash
tee -a /etc/systemd/nspawn/$CONTAINER.nspawn <<EOF
[Files]
Bind=/dev/ttyUSB0
EOF
```

##### Allow (cgroup) use of the device

First check the device's attributes with `file`:

```bash
file /dev/ttyUSB0
# /dev/ttyUSB0: character special (188/0)
```

Then determine the alias of its primary device number (in this case `188`) from `/proc/devices`:

```bash
cat /proc/devices | grep 188
# 188 ttyUSB

tee /etc/systemd/system/systemd-nspawn@$CONTAINER.service.d/override.conf <<EOF
[Service]
DeviceAllow=char-ttyUSB rwm
EOF
```

### Container startup

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

### Start a shell

```bash
machinectl shell $CONTAINER
```

### Enable at boot time

```bash
systemctl enable machines.target # Only the first time
systemctl enable systemd-nspawn@$CONTAINER
```

### Initialize container's host0 network settings

From within the container:

```bash
systemctl enable --now systemd-networkd
```

From host system:

```bash
ln -s /usr/lib/systemd/system/systemd-networkd.service\
 $FOLDER/etc/systemd/system/multi-user.target.wants/systemd-networkd.service
```

### To enable host resolution too

From within the container:

```bash
systemctl enable --now systemd-resolved
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf
```

From host system:

```bash
ln -s /usr/lib/systemd/system/systemd-resolved.service\
 $FOLDER/etc/systemd/system/multi-user.target.wants/systemd-resolved.service
ln -sf /run/systemd/resolve/stub-resolv.conf $FOLDER/etc/resolv.conf
```

## Privileged & Unprivileged containers

Since kernel 4.14, systemd-nspawn containers will be unprivileged by default (`PrivateUsers=pick`) which means that those may not have the right permissions to fulfill the functions for which they're created. In addition, the owner of all files/directories within the root folder of the container (in `/var/lib/machines/`) is recursively shifted to the automatically created unprivileged user namespace.

### Configuration of a privileged container

To create a privileged container, add `PrivateUsers=false` to the `[Exec]` section of the respective `.nspawn` file like:

```bash
[Exec]
PrivateUsers=false
```

### Reverting a shift of ownership for unprivileged containers

To revert a shift to the automatically created unprivileged user namespace back to the user namespace of the host (starting at `0` for root):

1. stop the container
2. make sure that the `.nspawn` doesn't contain `PrivateUsers=false`
3. execute the following:

   ```bash
   systemd-nspawn --quiet --boot --link-journal=try-guest --network-veth -U --settings=override --private-users=0 --private-users-chown --machine=$CONTAINER
   ```

To make the container privileged again, enable the `PrivateUsers=false` again in the `.nspawn` file and start the container.

## Updating a container's packages

A container's packages can be updated by calling the following from the host:

``` bash
pacman -Syur /var/lib/machines/$CONTAINER
```

### Updating a container's keyring

> Running the command above will sometimes fail with the following in which case you'll need to update the keyring locally:
  `==> ERROR: DDB867B92AA789C165EEFA799B729B06A680C281 could not be locally signed.`

A container's keyring can be updated by:

```bash
sudo machinectl shell $CONTAINER /usr/bin/pacman-key --populate archlinux
```

[install]: using-pacman.md#install-a-package
[systemd-nspawn]: http://www.freedesktop.org/software/systemd/man/systemd-nspawn.html
[the info concerning this topic for Arch Linux]: https://wiki.archlinux.org/index.php/Systemd-nspawn
[`arch-install-scripts`]: https://www.archlinux.org/packages/extra/any/arch-install-scripts/
