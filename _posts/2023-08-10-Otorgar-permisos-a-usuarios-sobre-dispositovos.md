---
title: "Otorgar permisos a usuarios sobre dispositovos"
date: 2023-08-10
categories:
  - blog
tags:
  - fedora
  - sysadmin
  - udev
---

# Descripcion

Generalmente cuando conectamos un disposito (usb) a nuestro sistema, este crea un fichero en __/dev__ para permitirnos trabajar con él. 

En el momento en que el driver kernel inicializa un dispositivo, su estado por decto es que pertenece al root:root y sus permisos de accesso seran 600, sin embargo el 90% de las ocasiones nosotros quermeos acceder al mismo empleando nuestro usuario sin privilegios.

## Nota

Ubuntu's approach is to create a plugdev group that devices are added to, but this practice is not only discouraged by the systemd developers.

# Procedimiento

El primer paso es descubir que ocurre cuando el dispositivo se conecta en el sistema, para ellos vamos a desconectarlo y conectarlo, de forma que el kernel lo reconozca y nos muestre que ocurre:
```shell
~]# dmesg
[ 1561.825732] usb 2-5.4: new high-speed USB device number 8 using xhci_hcd
[ 1561.916363] usb 2-5.4: New USB device found, idVendor=0aad, idProduct=0197, bcdDevice= 4.01
[ 1561.916371] usb 2-5.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 1561.916375] usb 2-5.4: Product: NGP814
[ 1561.916378] usb 2-5.4: Manufacturer: Rohde & Schwarz GmbH & Co. KG
[ 1561.916381] usb 2-5.4: SerialNumber: 5601.4007k04-100662
~]# ls -l /dev/usbtmc0
crw-------. 1 root root 180, 176 Aug 10 11:49 /dev/usbtmc0
```

Tal y como esperabamos, ha creado el fichero __/dev/usbtmc0__, pero con permisos __root:root__. Tenemos que intervenir!!!



# Resolviendo el problema de permisos

Vamos a crear una regla UDEV para que cuando se conecte este dispositivo, el sistema le otorgue los permisos que nosotros deseemos.

```shell
~]# udevadm info -a /dev/usbtmc0

Udevadm info starts with the device specified by the devpath and then
walks up the chain of parent devices. It prints for every device
found, all possible attributes in the udev rules key format.
A rule to match, can be composed by the attributes of the device
and the attributes from one single parent device.

  looking at device '/devices/pci0000:00/0000:00:14.0/usb2/2-5/2-5.4/2-5.4:1.0/usbmisc/usbtmc0':
    KERNEL=="usbtmc0"
    SUBSYSTEM=="usbmisc"
    DRIVER==""
    ATTR{power/control}=="auto"
    ATTR{power/runtime_active_time}=="0"
    ATTR{power/runtime_status}=="unsupported"
    ATTR{power/runtime_suspended_time}=="0"

```

Ahora, podemos identificar nuestro dispositivo USB, este usbtmc0 emplea el KERNEL=="usbtmc0" y el SUBSYSTEM=="usbmisc". Seguimos revisando los niveles superiores de nuestro arbol y un par de padres mas arriba encontramos los ATTRS{idProduct}=="0197" y ATTRS{idVendor}=="0aad".

```shell
  looking at parent device '/devices/pci0000:00/0000:00:14.0/usb2/2-5/2-5.4':
    KERNELS=="2-5.4"
    SUBSYSTEMS=="usb"
    DRIVERS=="usb"
    ATTRS{authorized}=="1"
    ATTRS{avoid_reset_quirk}=="0"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bDeviceClass}=="00"
    ATTRS{bDeviceProtocol}=="00"
    ATTRS{bDeviceSubClass}=="00"
    ATTRS{bMaxPacketSize0}=="64"
    ATTRS{bMaxPower}=="2mA"
    ATTRS{bNumConfigurations}=="1"
    ATTRS{bNumInterfaces}==" 1"
    ATTRS{bcdDevice}=="0401"
    ATTRS{bmAttributes}=="c0"
    ATTRS{busnum}=="2"
    ATTRS{configuration}==""
    ATTRS{devnum}=="8"
    ATTRS{devpath}=="5.4"
    ATTRS{idProduct}=="0197"
    ATTRS{idVendor}=="0aad"
    ATTRS{ltm_capable}=="no"
    ATTRS{manufacturer}=="Rohde & Schwarz GmbH & Co. KG"
    ATTRS{maxchild}=="0"
```


Necesitamos producir una regla que afecte a este nodo, si echamos un vistazo a la regla /lib/udev/rules.d/70-uaccess.

 Next, look at /lib/udev/rules.d/70-uaccess. You will see a bunch of rules that all end with TAG+="uaccess". This tag is handled by 73-seat-access to automatically assign an ACL to the device that grants access to the user who is currently logged in (on seat0 by default). You should use this tag as well. Your rules file should probably be numbered around 70- as well, but as I said before it must have a different name than anything in /lib/udev/rules.d.

The ACLs are also automatically updated by logind as required, since a user may log in after the device is enumerated, or the active user on a seat can change. 

```
ACTION=="add", KERNEL=="usbtmc[0-9]*", SUBSYSTEM=="usbmisc", SUBSYSTEMS=="usb", SYMLINK+="psu%n", ATTRS{idVendor}=="0aad", ATTRS{idProduct}=="0197", MODE="0660", TAG+="uaccess"

ACTION=="add", KERNEL=="usbtmc[0-9]*", SUBSYSTEM=="usbmisc", SUBSYSTEMS=="usb", SYMLINK+="psu%n", ATTRS{idVendor}=="0aad", ATTRS{idProduct}=="0197", GROUP="dev", MODE="0666"
```

Probamos nuestra nueva regla, empleando la opcion test y tenemos que decirle la accion para la que queremos probarlo:
```shell
~]# sudo udevadm test /devices/pci0000:00/0000:00:14.0/usb2/2-5/2-5.3/2-5.3:1.0/usbmisc/usbtmc0 --action=add
This program is for debugging only, it does not run any program
specified by a RUN key. It may show incorrect results, because
some values may be different, or not available at a simulation run.

Trying to open "/etc/systemd/hwdb/hwdb.bin"...
Trying to open "/etc/udev/hwdb.bin"...
=== trie on-disk ===
tool version:          251
file size:        12396860 bytes
header size             80 bytes
strings            2510300 bytes
nodes              9886480 bytes
Load module index
Found cgroup2 on /sys/fs/cgroup/, full unified hierarchy
Found container virtualization none.
Using default interface naming scheme 'v251'.
Parsed configuration file "/usr/lib/systemd/network/99-default.link"
Created link configuration context.
Loaded timestamp for '/etc/udev/rules.d'.
Reading rules file: /usr/lib/udev/rules.d/01-md-raid-creating.rules
Reading rules file: /usr/lib/udev/rules.d/10-dm.rules
...
...

usbtmc0: /etc/udev/rules.d/70-rohdewchwarz_usb.rules:4 MODE 0660
usbtmc0: /etc/udev/rules.d/70-rohdewchwarz_usb.rules:4 LINK 'psu0'
usbtmc0: /usr/lib/udev/rules.d/71-seat.rules:74 Importing properties from results of builtin command 'path_id'
usbtmc0: /usr/lib/udev/rules.d/73-seat-late.rules:16 RUN 'uaccess'
usbtmc0: Preserve permissions of /dev/usbtmc0, uid=0, gid=0, mode=0660
usbtmc0: Handling device node '/dev/usbtmc0', devnum=c180:176
usbtmc0: sd-device: Created db file '/run/udev/data/c180:176' for '/devices/pci0000:00/0000:00:14.0/usb2/2-5/2-5.3/2-5.3:1.0/usbmisc/usbtmc0'
DEVPATH=/devices/pci0000:00/0000:00:14.0/usb2/2-5/2-5.3/2-5.3:1.0/usbmisc/usbtmc0
DEVNAME=/dev/usbtmc0
MAJOR=180
MINOR=176
ACTION=add
SUBSYSTEM=usbmisc
TAGS=:seat:uaccess:
DEVLINKS=/dev/psu0
CURRENT_TAGS=:seat:uaccess:
ID_PATH=pci-0000:00:14.0-usb-0:5.3:1.0
ID_PATH_TAG=pci-0000_00_14_0-usb-0_5_3_1_0
ID_FOR_SEAT=usbmisc-pci-0000_00_14_0-usb-0_5_3_1_0
USEC_INITIALIZED=6675642669
run: 'uaccess'
Unload module index
Unloaded link configuration context.

```

Recargamos las reglas

```shell
~]# sudo udevadm control --reload-rules && sudo udevadm trigger
```


# Conclusion

Ahora podemos acceder a nuestros dispositivos sin problemas.

# Referencias


