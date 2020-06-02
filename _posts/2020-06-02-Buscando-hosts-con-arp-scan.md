---
title: "Buscando hosts con arp-scan en nuestra red"
date: 2020-06-02
categories:
  - blog
tags:
  - tools
  - admin
---

ARP scan permite escanear una red LAN y descubrir los dispositivos IPv4 connectados a ella.
# Descripcion

La herramienta __arp-scan__ emplea paquetes ARP para descubrir los dispositivos activos en un rango IPv4 incluso cuando el firewall esta activo. Independientemente de que se este empleando Wifi o Ethernet, los dispositivos IPv4 de la red local deben responder a las peticiones ARP o no prodran comunicarse.

Los paquete ARP no son routables, esto implica que solo funciona en la red local.

Este comando se debe ejecutar con privilegios de root. Se puede especificar una IP o un rango de IPs de forma 192.168.1.3-192.168.1.27, 192.168.1.0/24, 192.168.1.0:255.255.255.0 o con la opción -l (--localnet). 

# Uso
El uso de la herramienta es claro, localizar la IP de un dispositivo que hemos conectado a nuestra red.
```shell
~]# arp-scan --interface enp0s25 10.9.0.44/24 | grep Gembird Europe BV
```

## Localizar la RPI y conectarnos a ella.
Vamos a localizar una tarjeta Raspberry PI 3 a la que le hemos grabado una imagen del sistema operativo Raspbian y activado el ssh. Esta RPI3 se encuentra conectada a la red local, pero es un sistema headless, es decir, no dispone de monitor y ni teclado.

```shell
~]# arp-scan --interface wlp2s0 --localnet

Interface: wlp2s0, type: EN10MB, MAC: e8:de:27:78:33:1b, IPv4: 192.168.0.19
Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.0.1	e0:37:17:25:8e:48	Technicolor CH USA Inc.
192.168.0.10	b8:27:eb:76:96:b9	Raspberry Pi Foundation
192.168.0.18	50:ec:50:0c:dd:3a	Beijing Xiaomi Mobile Software Co., Ltd
192.168.0.254	e0:37:17:25:8e:4a	Technicolor CH USA Inc
```			 

En este caso vemos como en nuestra red local, __arp-scan__ ha encontrado nuestra Raspberry Pi sin problemas. El usuario por defecto es **pi** y la contraseña **raspberry**. 

¡¡Recuerda cambiarlo!!

```shell
~]#  ssh pi@192.168.0.10
Linux raspberrypi 4.19.83-v7+ #1277 SMP Mon Nov 11 16:30:44 GMT 2019 armv7l

pi@raspberrypi:~ $
```

# Conclusiones
Las ventajas de este tipo de escaneo es que se realizar de forma muy rápida para entornos donde trabajamos con numerosos dispositivos y con diferentes segmentos de red.

Sin embargo, el protocolo ARP, no es enrutable hacia internet, es decir, este tipo de escaneo solo serán útiles en entornos de área local.

Algo parecido se puede obtener con el comando nmap con: ```nmap -sP -PR 192.168.0.0/24```.

# Referencias
[Nping and Nmap arp scan](https://linuxhint.com/nping_nmap_arp_scan/)
[Arp-Scan Command Tutorial With Examples](https://www.poftut.com/arp-scan-command-tutorial-examples/)

