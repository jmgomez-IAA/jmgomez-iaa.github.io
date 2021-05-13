---
title: "Eliminar un disco de una particion BTRFS RAID1 en Archlinux"
date: 2021-05-06
categories:
  - blog
tags:
  - btrfs
  - tools
  - admin
---

BTRFS es un sistema de archivos copy-on-write (CoW) para Linux que incluye caracteristicas enfocadas a la tolerancia a fallos, reparación y facil administracion.

# Descripcion

Ahora mismo disponemos de un sistema de archivos BTRFS con dos discos duros configurados en RAID1.

```shell
~]#tail -1 /etc/fstab 
UUID=47fccbb6-a712-4971-acb1-e3ecb44033e7	/var/cloud-data btrfs		rw,x-systemd.automount,relatime	0 

~]# btrfs filesystem show /var/cloud-data
Label: 'cloud-data'  uuid: 47fccbb6-a712-4971-acb1-e3ecb44033e7
	Total devices 2 FS bytes used 279.69GiB
	devid    1 size 681.51GiB used 281.03GiB path /dev/sdb1
	devid    2 size 931.51GiB used 281.03GiB path /dev/sdc

~]# btrfs filesystem df /var/cloud-data
Data, single: total=8.00MiB, used=64.00KiB
System, RAID1: total=8.00MiB, used=16.00KiB
Metadata, RAID1: total=102.38MiB, used=112.00KiB
GlobalReserve, single: total=3.25MiB, used=0.00B
```

Nuestro objetivo es eliminar uno de los discos duros del sistema y dejar el sistema de archivos funcionado solo con uno de ellos. Para ello debemos balancear los discos y convertir el btrfs en single.

# Procedimiento
Para poder eliminar se emplea el comando delete, pero como nuestro disco duro esta en RAID1 no podemos eliminar el disco manteniendo al estructura RAID. 

```shell
~]# btrfs device delete /dev/sdc /var/cloud-data
ERROR: error removing device '/dev/sdc': unable to go below two devices on raid1
```

Por lo tanto en primer lugar pasamos nuestro sistema RAID1 a single, de modo que toda la información del nuestro BTRFS este contenida en un solo volumen. 

```shell
~]# btrfs balance start -f -sconvert=single -mconvert=single -dconvert=single /var/cloud-data
Done, had to relocate 282 out of 282 chunks
~]# btrfs filesystem df /var/cloud-data/
Data, single: total=280.00GiB, used=279.27GiB
System, single: total=64.00MiB, used=48.00KiB
Metadata, single: total=1.00GiB, used=418.30MiB
GlobalReserve, single: total=313.91MiB, used=0.00B
~]# btrfs filesystem show /var/cloud-data
Label: 'cloud-data'  uuid: 47fccbb6-a712-4971-acb1-e3ecb44033e7
	Total devices 2 FS bytes used 279.68GiB
	devid    1 size 681.51GiB used 16.00GiB path /dev/sdb1
	devid    2 size 931.51GiB used 265.06GiB path /dev/sdc
```
Borramos nuestra unidad del volumen BTRFS, para ello usamos el comando __delete__. Antes de eliminar la unidad, debe mover cualquier dato que este en ese disco a otra posicion dentro del volumen BTRFS.

```shell
~]# btrfs device delete /dev/sdc /var/cloud-data
~]# btrfs filesystem df /var/cloud-data/
Data, single: total=280.00GiB, used=279.27GiB
System, single: total=32.00MiB, used=48.00KiB
Metadata, single: total=1.00GiB, used=418.56MiB
GlobalReserve, single: total=314.08MiB, used=0.00B
~]# btrfs filesystem show .
Label: 'cloud-data'  uuid: 47fccbb6-a712-4971-acb1-e3ecb44033e7
	Total devices 1 FS bytes used 279.68GiB
	devid    1 size 681.51GiB used 281.03GiB path /dev/sdb1
```

# Conclusiones

Disco eliminado del RAID1.


# Referencias
[BTRFS ArchLinux Wiki](https://wiki.archlinux.org/title/Btrfs)
[BTRFS Wiki](https://btrfs.wiki.kernel.org/index.php/Using_Btrfs_with_Multiple_Devices#Removing_devices)
