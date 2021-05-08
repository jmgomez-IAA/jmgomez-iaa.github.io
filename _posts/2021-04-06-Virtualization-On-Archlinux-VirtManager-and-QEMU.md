---
title: "Virtualización con Virt-Manager y QEMU en Archlinux"
date: 2021-05-06
categories:
  - blog
tags:
  - qemu
  - tools
  - admin
---

QEMU permite emular una maquian X86_64 de modo completo y por lo tanto lanzar un sistema operativo distinto a Linux. El entorno de virtualizacion requiere del soporte hardware en el procesador INTEL-VTX o AMD-V, en Intel y AMD respectivamente.

```shell
~]$ LC_ALL=C lscpu | grep Virtualization

LC_ALL=C lscpu | grep Virtualization
Virtualization:                  AMD-V
```
Comprobamos que el modulo este instalado en nuestro kernel

```shell
~]$ zgrep CONFIG_KVM /proc/config.gz
CONFIG_KVM_GUEST=y
CONFIG_KVM_MMIO=y
CONFIG_KVM_ASYNC_PF=y
CONFIG_KVM_VFIO=y
CONFIG_KVM_GENERIC_DIRTYLOG_READ_PROTECT=y
CONFIG_KVM_COMPAT=y
CONFIG_KVM_XFER_TO_GUEST_WORK=y
CONFIG_KVM=m
CONFIG_KVM_INTEL=m
CONFIG_KVM_AMD=m
CONFIG_KVM_AMD_SEV=y
CONFIG_KVM_MMU_AUDIT=y
```
y que el modulo esta activo y cargado

```shell
$ lsmod | grep kvm
kvm_amd               131072  0
ccp                   114688  1 kvm_amd
kvm                   970752  1 kvm_amd
irqbypass              16384  1 kvm
```

Aparte de emular el procesador necesitamos de soporte para algunos perifericos, memoria, redes, etc..
```shell
zgrep VIRTIO /proc/config.gz 
CONFIG_BLK_MQ_VIRTIO=y
CONFIG_VIRTIO_VSOCKETS=m
CONFIG_VIRTIO_VSOCKETS_COMMON=m
CONFIG_NET_9P_VIRTIO=m
CONFIG_VIRTIO_BLK=m
CONFIG_SCSI_VIRTIO=m
CONFIG_VIRTIO_NET=m
CONFIG_VIRTIO_CONSOLE=m
CONFIG_HW_RANDOM_VIRTIO=m
CONFIG_DRM_VIRTIO_GPU=m
CONFIG_VIRTIO=y
CONFIG_VIRTIO_MENU=y
CONFIG_VIRTIO_PCI=m
CONFIG_VIRTIO_PCI_LEGACY=y
CONFIG_VIRTIO_VDPA=m
CONFIG_VIRTIO_PMEM=m
CONFIG_VIRTIO_BALLOON=m
CONFIG_VIRTIO_MEM=m
CONFIG_VIRTIO_INPUT=m
CONFIG_VIRTIO_MMIO=m
CONFIG_VIRTIO_MMIO_CMDLINE_DEVICES=y
CONFIG_VIRTIO_DMA_SHARED_BUFFER=m
CONFIG_RPMSG_VIRTIO=m
CONFIG_VIRTIO_FS=m
CONFIG_CRYPTO_DEV_VIRTIO=m
```

Ahora que sabemos que todo esta soportado, vamos a por los paquetes necesario.

# Descripcion

La ejecución de una maquina virtual, requiere de un servidor y un cliente (ambos pueden estar en la misma maquina). se hace empleando QEMU y un gestor y configuraion de un servidor git en Archlinux.

# Procedimiento
El primer paso es instalar la herramienta __virt-manager__ y  __qemu__/__libvirt__. Además, vamos a instalar las herramientas necesarias para montar una red.

```shell
~]# pacman -S virt-manager qemu libvirt vde2 ebtables dnsmasq bridge-utils openbsd-netcat dmidecode
```
De estos tres se prodian escribir, y los habrá, libros!! Asi, que enlace a la Wiki y a leer ahi!!
__qemu__
__libvirt__
__virt-manager__

__vde__ son un conjunto de herramientas para administrar redes virtuales.

Durante la instalación, pacman nos indica donde debemos configurar estos serivicos:
(40/47) installing vde2                                                                       [#######################################################] 100%
vde config files should be placed in /etc/vde, sample files are provided.
iptables and dhcpd sample files have been installed to '/usr/share/vde2'.
Merge those examples, if needed to the according config files.
(41/47) installing spice                                                                      
__dnsmasq__ y __ebtables__ se necesitan para hacer NAT/DHCP para las maquinas huesped.
__openbsd-netcat__ nos permite administrar nuestras maquinas virtuales remotas mediante ssh.
__dmidecode__ obtiene informacion de la bios del sistema.

Con estos paquetes listos, pasamos a activar el servicio libvirt. 
```shell
~]# systemctl start libvirtd.service
~]# systemctl start virtlogd.service
~]# systemctl status libvirtd.service
~]# systemctl status virtlogd.service
```
Si no hay problemas lo habilitamos de forma permanente. Cuando hacemos el enable de libvirtd.service, se habilitan automaticamente las unidades virtlogd.socket y virtlockd.socket, por lo que no es necesario habilitar virtlogd.service.
```shell
~]# systemctl stop libvirtd.service
~]# systemctl stop virtlogd.service
~]# systemctl enable libvirtd.service
```

## Test de instalacion
Antes de meternos en configuración probamos que libvirt funciona correctamente tanto a nivel de systema como de usuario.

1.- Sistema, por defecto el metodo de autentificacion es polkit, es decir el parametro unix_sock_auth es polkit por defecto, y por lo tanto libvirt considera todos lo usuario del grupo wheel autorizados (administrador).
 ```shell
~]$ virsh -c qemu:///system
==== AUTHENTICATING FOR org.libvirt.unix.manage ====
System policy prevents management of local virtualized systems
Authenticating as: jmgomez
Password: 
==== AUTHENTICATION COMPLETE ====
Welcome to virsh, the virtualization interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # 
```
2.- Usuario
virsh -c qemu:///session
Welcome to virsh, the virtualization interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # 



Listo!!

# Conclusiones

KVM instalado, en el proximo HOW-TO maquinas virtuales.


# Referencias
[Libvirt ArchLinux Wiki](https://wiki.archlinux.org/title/libvirt)
[QEMU ArchLinux Wiki](https://wiki.archlinux.org/title/QEMU)
[Install and Configure KVM in ArchLinux](https://linuxhint.com/install_configure_kvm_archlinux/)
[OSTechNix how-to articles](https://ostechnix.com/category/linux_distributions/arch-linux/)


