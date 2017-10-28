# Udev rules
This page describes some info about defining udev rules, i.e. to automatically create a symbolic link to a plugged-in USB device.

## Create a symbolic link to a device
This section describes how to define an udev rule that creates a symbolic link to a device, i.e. to a plugged-in USB device.
Here, two devices are taken as example:
* A USB smartcard reader
* A USB to TTL converter

These devices both use the same ftdi driver and are both detected as either `/dev/ttyUSB0` or `/dev/ttyUSB1`.
The problem with this is that the sequence in which they're detected may differ, so the number they'll actually get may also differ from boot to boot which makes it practically impossible to use them in application configurations.

Following is a description of how to define specific udev rules to deterministically create specific symbolic links for them so applications can use those in their configuration.
The secuence is to:
* Determine the unique udev attributes of a device
* Use those attributes in a udev rule

### Determine the unique udev attributes of a device
The udev attributes of any device in the `/dev/` folder can be determined by `udevadmin info -a /dev/<device>`, so for i.e. `/dev/ttyUSB0` this will be:
```bash
udevadmin info -a /dev/ttyUSB0
```
This will give something like:
```
Udevadm info starts with the device specified by the devpath and then
walks up the chain of parent devices. It prints for every device
found, all possible attributes in the udev rules key format.
A rule to match, can be composed by the attributes of the device
and the attributes from one single parent device.

  looking at device '/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.6/2-1.6:1.0/ttyUSB0/tty/ttyUSB0':
    KERNEL=="ttyUSB0"
    SUBSYSTEM=="tty"
    DRIVER==""

  looking at parent device '/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.6/2-1.6:1.0/ttyUSB0':
    KERNELS=="ttyUSB0"
    SUBSYSTEMS=="usb-serial"
    DRIVERS=="ftdi_sio"

  looking at parent device '/devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.6/2-1.6:1.0':
    KERNELS=="2-1.6:1.0"
    SUBSYSTEMS=="usb"
    DRIVERS=="ftdi_sio"
    ATTRS{interface}=="USB smartcard server"
```

In my case, only `ATTRS{interface}` differs between the concerning devices, so that's the unique attribute we're looking for.
In addition to this unique attribute, we can take other attributes which are specific for these devices to make the match even more deterministic.
The attributes which I've chose in this case are `KERNEL`, `SUBSYSTEM` and `DRIVERS`.

### Use the attributes in a udev rule
To use the found udev attributes in a udev rule, we create a file with extension `.rules` in the `/etc/udev/rules.d` folder.
In this case, we'll create `/etc/udev/rules.d/99-usb-special.rules` with the following content:
```
KERNEL=="ttyUSB*", SUBSYSTEM=="tty", DRIVERS=="ftdi_sio", ATTRS{interface}=="USB smartcard server", SYMLINK+="smartreader"
KERNEL=="ttyUSB*", SUBSYSTEM=="tty", DRIVERS=="ftdi_sio", ATTRS{interface}=="FT232R USB UART", SYMLINK+="smartmeter"
```
In this file an attribute is match by a value by using `==` and the attributes (including their values) are delimited by `, `.
The symlink to create is defined by adding `, SYMLINK+="<symbolic link in /dev>"`.
