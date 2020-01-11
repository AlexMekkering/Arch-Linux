# Optimizations for a new installation

This page describes some optimizations for a [new installation](basic-installation.md).
All commands are assumed to be executed by an [unprivileged user](recommended-installation.md#add-an-administrative-user).

## ZRam swap device at boot

This section adds a ZRam swap device to:

* extend the available amount of memory
* improve responsiveness because it's much quicker than swapping to a hard drive
* reduce disk read/write cycles on SSDs

The configuration for this is done as follows:

```bash
sudo tee /etc/modules-load.d/zram.conf <<EOF
zram
EOF

# Fill ATTR{disksize} with about 150% of physically available RAM for doubling RAM or 75% for i.e. file servers
sudo tee /etc/udev/rules.d/99-zram.rules <<'EOF'
KERNEL=="zram0", ATTR{initstate}=="0", ATTR{comp_algorithm}="lz4", ATTR{disksize}="3G", RUN="/sbin/mkswap $env{DEVNAME}", TAG+="systemd"
EOF

sudo tee -a /etc/fstab <<EOF
# ZRAM Swap device
/dev/zram0 none swap defaults 0 0
EOF
```

## Tune for more filesystem caching with faster expiration for file servers

```bash
sudo tee -a /etc/sysctl.d/99-sysctl.conf <<EOF
# 128 MB of data before starting asynchronous writes
vm.dirty_background_bytes = 134217728
# use a maximum of 50% of RAM for caching before starting synchronous writes
vm.dirty_ratio = 50
# expire after 15 seconds
vm.dirty_expire_centisecs = 1500
# check expiration every 15 seconds
vm.dirty_writeback_centisecs = 1500
# disable nmi wactchdog (not really fs related)
kernel.nmi_watchdog = 0
EOF
```
