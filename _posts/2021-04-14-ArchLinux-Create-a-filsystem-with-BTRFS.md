---
title: "Crear un sistema de archivos BTRFS en Archlinux"
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

Un sistema de archivos btrfs crearse encima de muchos dispositivos. Por defecto, los metadatos seran replicados en dos dispositivos y los datos seran distribuidos en todos los dispositivos presentes. Esto es equivalente a mkfs.btrfs -m raid1 -d raid0.

En caso de solo disponer de un solo dispositivo, los metadatos sera duplicados en esa unidad. Para crear una sistema de archivos BTRFS la recomendacion para HDD mkfs.btrfs -m dup -d single, y para SSD (or non-rotational device) mkfs.btrfs -m single -d single

Existe la posibilidad de construir el sistema BTRFS sobre toda la unidad (crear un sistema de archivos sin particiones) o bien realizarlo sobre una particion de la unidad. Nosotros vamos a decir a BTRFS que ocupe todo el espacio disponible en la unidad, partitionless. 

# Procedimiento
Creamos un sistema BTRFS con un dispositivo solamente, en un HDD y sin particiones. 

```shell
~]# mkfs.btrfs -L galaxias -m dup -d single /dev/sdc -f
btrfs-progs v5.12 
See http://btrfs.wiki.kernel.org for more information.

Label:              galaxias
UUID:               73666001-b9d7-40f3-9449-54ae55ebc8d9
Node size:          16384
Sector size:        4096
Filesystem size:    931.51GiB
Block group profiles:
  Data:             single            8.00MiB
  Metadata:         DUP               1.00GiB
  System:           DUP               8.00MiB
SSD detected:       no
Zoned device:       no
Incompat features:  extref, skinny-metadata
Runtime features:   
Checksum:           crc32c
Number of devices:  1
Devices:
   ID        SIZE  PATH

```

Ahora, vamos  a crear unos cuantos subvolumenes. Los subvolumenes no son como las particiones, es mas parecido a archivos POSIX que montamos.
```shell
~]# monut /sdc /mnt

~]# btrfs subvolume create /mnt/backward
Create subvolume /mnt/backward

~]# btrfs subvolume list /mnt
ID 257 gen 7 top level 5 path backward
ID 258 gen 8 top level 5 path butterfly
```

Montamos uno de estos volumenes como usuario root
```shell
~]# mount -t btrfs -o subvol=backward,defaults /dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9  /var/cloud-data
~]# mount -t btrfs -o subvol=butterfly,defaults /dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9  /mnt
~]# df -hP
Filesystem      Size  Used Avail Use% Mounted on
dev              16G     0   16G   0% /dev
run              16G  1.1M   16G   1% /run
/dev/sda2        82G   57G   22G  73% /
tmpfs            16G   26M   16G   1% /dev/shm
tmpfs            16G  4.0M   16G   1% /tmp
/dev/sda1      1022M  144K 1022M   1% /boot/efi
tmpfs           3.1G   16K  3.1G   1% /run/user/1000
/dev/sdc        932G  3.9M  930G   1% /var/cloud-data
/dev/sdc        932G  3.9M  930G   1% /mnt
 ```

  y para incluirlo en nuestro fstab:

> UUID=73666001-b9d7-40f3-9449-54ae55ebc8d9 	/var/cloud-data btrfs		rw,noauto,x-systemd.automount,relatime,subvol=backward 0 2


Mediante el parametro **noauto,x-systemd.automount** le indicamos a fstab que lo monte pero no lo chequee hasta que sea accedido por primera vez.

## Systemd.mounts (para ampliar)
Existe la posibilidad de emplear [systemd.mounts](https://man.archlinux.org/man/systemd.mount.5) para montar nuestro sistema de archivos. 

Create a unit file for the mount point.
```shell
~]# touch /etc/systemd/system/var-lib-cloud.mount
```

> [Unit]
> Description=Cloud Data Storage
> 
> [Mount]
> What=/dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9
> Where=/var/cloud-data
> Type=btrfs
> Options=subvol=backward,defaults

```shell
~]# systemctl daemon-reload 
~]# systemctl enable var-lib-cloud.mount 
~]# systemctl start var-lib-cloud.mount
~]# systemctl status var-lib-cloud.mount  
```

# Conclusiones

Sistema de archivos BTRFS creado.

# Referencias
[BTRFS ArchLinux Wiki](https://wiki.archlinux.org/title/Btrfs)

[BTRFS Wiki](https://btrfs.wiki.kernel.org/index.php/Using_Btrfs_with_Multiple_Devices#Removing_devices)

[Filesystem Hierarchy Standard v3.0](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)

[BTRFS Subvolumenes short guide](https://puerto53.com/linux/filesystems-btrfs/?cn-reloaded=1)

[BTRFS Subvolumenes](https://cloudnull.io/2017/12/btrfs-subvolume-mounts-yes-you-can/)
