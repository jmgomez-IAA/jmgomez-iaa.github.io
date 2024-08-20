---
title: "Revisar un sistema de archivos BTRFS"
date: 2024-08-20
categories:
  - blog
tags:
  - btrfs
  - tools
  - admin
---

Vamos a recuperar la información de nuestro, olvidado, sistema BTRFS y asi aprovechamos para hacer un pequeño repaso  a los comandos mas frecuentes. 

# Descripcion

Un sistema de archivos btrfs esta creado encima de uno o varios dispositivos. Por defecto, los metadatos seran replicados en dos dispositivos y los datos seran distribuidos en todos los dispositivos presentes. 

# Dispositivos

Vamos a ver que discos hay instalados en el sistema y cuales de ellos estan en uso.

```shell
~]# btrfs filesystem show
Label: 'galaxias'  uuid: 73666001-b9d7-40f3-9449-54ae55ebc8d9
        Total devices 2 FS bytes used 288.02GiB
        devid    1 size 931.51GiB used 289.03GiB path /dev/sdc
        devid    2 size 931.51GiB used 289.03GiB path /dev/sdb
```

Nuestro sistema BTRFS con ```uuid: 73666001-b9d7-40f3-9449-54ae55ebc8d9``` cuenta con dos unidades de disco duro. Ambas tienen un tamaño de 931GB de los cuales estan ocupados 289GB.

# Punto de montaje

Nuestro sistema BTRFS debe estar montado en algun punto de montaje, podemos localizarlo empleando lo comandos habituales.

```shell
~]# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 111.8G  0 disk
├─sda1   8:1    0     1G  0 part /boot/efi
└─sda2   8:2    0  82.8G  0 part /
sdb      8:16   0 931.5G  0 disk
sdc      8:32   0 931.5G  0 disk /var/cloud-data
sdd      8:48   1     0B  0 disk
sr0     11:0    1  1024M  0 rom
```


En caso de que no este montado, lo montamos empleando ```mount -t btrfs -o defaults /dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9  /mnt```.

Ahora ya podemos acceder a la información del sistema:

```shell
~]# btrfs filesystem usage /var/cloud-data/
Overall:
    Device size:                   1.82TiB
    Device allocated:            578.06GiB
    Device unallocated:            1.25TiB
    Device missing:                  0.00B
    Device slack:                    0.00B
    Used:                        576.04GiB
    Free (estimated):            642.88GiB      (min: 642.88GiB)
    Free (statfs, df):           642.88GiB
    Data ratio:                       2.00
    Metadata ratio:                   2.00
    Global reserve:              313.38MiB      (used: 0.00B)
    Multiple profiles:                  no

Data,RAID1: Size:288.00GiB, Used:287.60GiB (99.86%)
   /dev/sdc      288.00GiB
   /dev/sdb      288.00GiB

Metadata,RAID1: Size:1.00GiB, Used:428.48MiB (41.84%)
   /dev/sdc        1.00GiB
   /dev/sdb        1.00GiB

System,RAID1: Size:32.00MiB, Used:80.00KiB (0.24%)
   /dev/sdc       32.00MiB
   /dev/sdb       32.00MiB

Unallocated:
   /dev/sdc      642.48GiB
   /dev/sdb      642.48GiB
```

Como podemos ver los datos, metadata y systema estan duplicados en RAID1 en los dos discos duros. Esta información tambien la podiamos haber optenido de forma mas reducida con

```shell
~]# btrfs fi df /var/cloud-data/
Data, RAID1: total=288.00GiB, used=287.60GiB
System, RAID1: total=32.00MiB, used=80.00KiB
Metadata, RAID1: total=1.00GiB, used=428.48MiB
GlobalReserve, single: total=313.38MiB, used=0.00B
```

# Subvolumes

Lo normal es estructurar el espacio disponible empleando subvolumenes, para poder hacer snapshoots.

```shell
~]# btrfs subvolume list /var/cloud-data/
ID 257 gen 2775 top level 5 path backward
ID 258 gen 2776 top level 5 path butterfly
```

Nosotros tenemos dos subvolumenes. Si quisieramos montar solo uno de los subvolumenes o bien montarlos en puntos de montaje distintos:

```shell
~]# mount -t btrfs -o subvol=backward,defaults /dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9  /var/cloud-data
~]# mount -t btrfs -o subvol=butterfly,defaults /dev/disk/by-uuid/73666001-b9d7-40f3-9449-54ae55ebc8d9  /mnt
```

# Verificacion

Obtenemos las estadisticas de las unidades:

```shell
~]# btrfs device stats /dev/sdc
[/dev/sdc].write_io_errs    0
[/dev/sdc].read_io_errs     0
[/dev/sdc].flush_io_errs    0
[/dev/sdc].corruption_errs  0
[/dev/sdc].generation_errs  0
```

Y la otra unidad:

```shell
~]# btrfs device stats /dev/sdb
[/dev/sdb].write_io_errs    0
[/dev/sdb].read_io_errs     0
[/dev/sdb].flush_io_errs    0
[/dev/sdb].corruption_errs  0
[/dev/sdb].generation_errs  0
```
# Conclusiones

Sistema de archivos BTRFS verificado.

# Referencias
[BTRFS ArchLinux Wiki](https://wiki.archlinux.org/title/Btrfs)

[BTRFS Wiki](https://btrfs.wiki.kernel.org/index.php/Using_Btrfs_with_Multiple_Devices#Removing_devices)

[Filesystem Hierarchy Standard v3.0](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)

[BTRFS Subvolumenes short guide](https://puerto53.com/linux/filesystems-btrfs/?cn-reloaded=1)

[BTRFS Subvolumenes](https://cloudnull.io/2017/12/btrfs-subvolume-mounts-yes-you-can/)

[SMART Test](https://www.thomas-krenn.com/en/wiki/SMART_tests_with_smartctl)