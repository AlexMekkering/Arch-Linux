# Essential configuration for new installations

This page describes some essential configuration to get a [new installation] ready for basic use with support for networking.

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

## Networking

### Set the hostname

```bash
hostnamectl set-hostname Arch
```

### Enable basic networking

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

> This assumes that the names of network devices comply with pattern `e*` which supports [predictable network interface names] like i.e. `eno1`, `ens1` and `enp2s0` as well as unpredictable kernel-native ethX names like `eth0`.

### Enable Hostname Resolution

```bash
systemctl enable --now systemd-resolved
ln -sf /usr/lib/systemd/resolv.conf /etc/resolv.conf
```

#### mDNS

To add support for mDNS (for hosts in the .local domain), add `MulticastDNS=yes` to the `[network]` part of your .network file(s) (i.e. `/etc/systemd/network/physical.network`) and `systemctl restart systemd-networkd`.

#### single-lable lookups

To be able to resolve signle-lable names (without a dot in it), you can either use LLMNR or your own DNS server (like dnsmasq).
In the case of using LLNMR, no adaptations are necessary because systemd-resolved supports it out-of-the-box.
When you'd like to use your own DNS server, you need to do the following:

1. Disable LLMNR in systemd-resolved
1. Tweak `/etc/nsswitch.conf` to continue looking up single-lable lookups by using dns
1. Disable DNSSEC in systemd-resolved when you trust your own DNS server
1. Restart systemd-resolved

##### Disable LLMNR

To my taste, LLMNR nterferes too much which single-lable lookups by DNS. To disable LLMNR in systemd-resolved, do the following:

```bash
tee -a /etc/systemd/resolved.conf > /dev/null <<EOF
LLMNR=no
EOF
```

##### Tweak `/etc/nsswitch.conf`

According to <https://wiki.archlinux.org/index.php/systemd-networkd#systemd-resolve_not_searching_the_local_domain,> systemd-resolve may fail to resolve single-lable names by DNS in which case `[!UNAVAIL=return]` should be removed from `/etc/nsswitch.conf`'s `hosts:` line.

#### Disable DNSSEC

When you trust your own DNS server, you can disable DNSSEC as follows:

```bash
tee -a /etc/systemd/resolved.conf > /dev/null <<EOF
DNSSEC=no
EOF
```

Please check my [recommended supplements] to get a more functional system.

[new installation]: ../README.md
[predictable network interface names]: https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/
[recommended supplements]: recommended-installation.md
