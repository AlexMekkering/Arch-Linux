# Configuration of installation essentials

## Networking
For simple DHCP networking, do:
```bash
tee -a /etc/systemd/network/physical.network > /dev/null <<EOF
[Match]
Name=e*
[Network]
DHCP=yes
EOF

systemctl enable --now systemd-networkd
systemctl enable --now systemd-resolved
ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

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
