---
title: "Configurar IP estatica usando systemd en Archlinux"
date: 2021-08-05
categories:
  - blog
tags:
  - network
  - systemd
  - admin
---

Systemd es un gestor de sistemas y servicios encargado de iniciar el sistema. 

# Descripcion

Vamos a hacer que systemd que se encargue de configurar al arranque la direccion IP de nuestro sistema. Para ello, debemos configurar un servico para systemd, **systemd-networkd* que se encargue configurar nuestra red, en nuestro caso con IP estatica.

# Procedimiento
Para que systemd se encargue de configurar las redes, tenemos que deshabilitar netctl, network-mannager y dhcpcd. Crear un perfil de red para la interfaz que queremos conectar y activar el servicio **systemd-networkd** para que systemd lo ejecute al inicio del sistema y configure la red..

```shell
~]# ip address show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:18:fe:68:6c:8e brd ff:ff:ff:ff:ff:ff
    inet 10.9.0.44/24 brd 10.9.0.255 scope global enp0s25
       valid_lft forever preferred_lft forever
    inet6 fe80::218:feff:fe68:6c8e/64 scope link 
       valid_lft forever preferred_lft forever

```

El primer paso, es crear un perfil de red para la interfaz que queremos asignarle la ip.
```shell
~]# nano /etc/systemd/network/enp0s25.network

~]# cat /etc/systemd/network/enp0s25.network 
[Match]
Name=enp0s25

[Network]
Address=161.111.164.216/25
Gateway=161.111.164.131
DNS=161.111.164.130
DNS=161.111.10.3
```

Cerramos y guardamos el fichero.

A continuación deshabilitamos los servicios que estaban encargados de controlar la red:

## Netctl 
Comprobamos que servicos relacionados con netctl estan activos:

```shell
~]# systemctl list-unit-files
```

Once you identify all netctl related stuff, disable all of them. I had the following service enabled in my system, so I disabled it as shown below.
```shell
~]# systemctl disable netctl@enp0s25.service
```

Una vez desactivado borramos los paquetes del sistema empleando pacman.
```shell
~]# pacman -Rns netctl
```

## dhcp 
Otro servicio habitual que tenemos que desactivar es DHCP. DHCP se encarga de solicitar una IP al servidor cuando se arranca el sistema.
```shell
~]# systemctl stop dhcpcd
~]# systemctl disable dhcpcd
```

## Systemd-networkd
Ya que todo esta listo, iniciamnos y activamos el servicio systemd-networkd para que configure nuestra red cada vez que se arranque el sistema.

```shell
~]# systemctl enable systemd-networkd
~]# systemctl start systemd-networkd
```

Para comprobar que la ip ha sido asignada con exito:
```shell
~]#  ip addr
```

# Conclusiones
Listo!! systemd se encargará de configurar nuestra red.


# Referencias
[Systemd-nerworkd Wiki Archlinux](https://wiki.archlinux.org/title/Systemd-networkd)
