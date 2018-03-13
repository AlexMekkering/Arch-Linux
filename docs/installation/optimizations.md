# Optimizations for a new installation
This page describes some optimizations for a [new installation](basic-installation.md).
All commands are assumed to be executed by an [unprivileged user](recommended-installation.md#add-an-administrative-user).

# ZRam swap device at boot
This section adds a ZRam swap device to:
* extend the available amount of memory
* improve responsiveness because it's much quicker than swapping to a hard drive
* reduce disk read/write cycles on SSDs

The configuration for this is done as follows:
```bash
sudo tee -a /etc/modules-load.d/zram.conf > /dev/null <<EOF
zram
EOF

# Fill ATTR{disksize} with about 150% of physically available RAM for doubling RAM or 75% for i.e. file servers
sudo tee -a /etc/udev/rules.d/99-zram.rules > /dev/null <<EOF
KERNEL=="zram0", ATTR{initstate}=="0", ATTR{max_comp_streams}="4", ATTR{comp_algorithm}="lz4", ATTR{disksize}="1536M", RUN="/usr/bin/mkswap /dev/zram0", TAG+="systemd"
EOF

sudo tee -a /etc/fstab > /dev/null <<EOF
# ZRAM Swap device
/dev/zram0 none swap defaults 0 0
EOF
```

# deadline IO scheduler for non-rotational disks
```bash
sudo tee -a /etc/udev/rules.d/60-ssd-scheduler.rules > /dev/null <<EOF
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="deadline"
EOF
```

# Tune for more filesystem caching with faster expiration for file servers
```bash
sudo tee -a /etc/sysctl.d/99-sysctl.conf > /dev/null <<EOF
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
