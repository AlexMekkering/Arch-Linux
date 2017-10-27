# Essential configuration for new installations

## Time
```bash
timedatectl set-timezone Europe/Amsterdam
timedatectl set-ntp yes
```

## Locale
```bash
tee -a /etc/locale.gen > /dev/null <<EOF
en_US.UTF-8 UTF-8
en_US ISO-8859-1
nl_NL.UTF-8 UTF-8
nl_NL ISO-8859-1
nl_NL@euro ISO-8859-15
EOF

locale-gen
localectl set-locale LANG=en_US.UTF-8
```

## Hostname
```bash
hostnamectl set-hostname Arch
```

## Networking
### Enabling basic networking
For simple DHCP networking with `systemd-networkd`, do:
```bash
tee -a /etc/systemd/network/physical.network > /dev/null <<EOF
[Match]
Name=e*
[Network]
DHCP=yes
EOF

systemctl enable --now systemd-networkd
```
> This assumes that the names of network devices comply with pattern `e*` which supports [predictable network interface names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/) like i.e. `eno1`, `ens1` and `enp2s0` as well as   unpredictable kernel-native ethX names like `eth0`.

### Enabling Name Resolution
```bash
systemctl enable --now systemd-resolved
ln -sf /usr/lib/systemd/resolv.conf /etc/resolv.conf
```
