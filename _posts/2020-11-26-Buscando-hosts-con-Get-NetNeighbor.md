---
title: "Buscando hosts con Get_NetNeighbor en nuestra red"
date: 2020-11-26
categories:
  - blog
tags:
  - tools
  - admin
---

EL modulo [Get_NetNeighbor](https://docs.microsoft.com/en-us/powershell/module/nettcpip/get-netneighbor?view=win10-ps) de plowershell permite escanear una red LAN y descubrir los dispositivos connectados a ella.
# Descripcion

The Get-NetNeighbor cmdlet gets neighbor cache entries. The cmdlet returns information about IP addresses and link-layer addresses for the neighbor cache entries

# Uso
El uso de la herramienta es claro, localizar la IP de un dispositivo que hemos conectado a nuestra red.
```shell
PS > Get_NetNeighbor 

ifIndex IPAddress                                          LinkLayerAddress      State       PolicyStore
------- ---------                                          ----------------      -----       -----------
4       ff02::16                                           33-33-00-00-00-16     Permanent   ActiveStore
10      ff02::1:2                                          33-33-00-01-00-02     Permanent   ActiveStore
10      ff02::16                                           33-33-00-00-00-16     Permanent   ActiveStore
16      ff02::1:ffb8:cd3e                                  33-33-FF-B8-CD-3E     Permanent   ActiveStore
16      ff02::1:3                                          33-33-00-01-00-03     Permanent   ActiveStore
16      ff02::1:2                                          33-33-00-01-00-02     Permanent   ActiveStore
16      ff02::fb                                           33-33-00-00-00-FB     Permanent   ActiveStore
16      ff02::16                                           33-33-00-00-00-16     Permanent   ActiveStore
16      ff02::2                                            33-33-00-00-00-02     Permanent   ActiveStore
16      ff02::1                                            33-33-00-00-00-01     Permanent   ActiveStore
16      fe80::86aa:9cff:fe41:18f9                          84-AA-9C-41-18-F9     Stale       ActiveStore
1       ff02::1:2                                                                Permanent   ActiveStore
1       ff02::16                                                                 Permanent   ActiveStore
4       224.0.0.2                                          01-00-5E-00-00-02     Permanent   ActiveStore
10      224.0.0.22                                         01-00-5E-00-00-16     Permanent   ActiveStore
10      224.0.0.2                                          01-00-5E-00-00-02     Permanent   ActiveStore
16      255.255.255.255                                    FF-FF-FF-FF-FF-FF     Permanent   ActiveStore
16      239.255.255.250                                    01-00-5E-7F-FF-FA     Permanent   ActiveStore
16      224.0.0.252                                        01-00-5E-00-00-FC     Permanent   ActiveStore
16      224.0.0.251                                        01-00-5E-00-00-FB     Permanent   ActiveStore
16      224.0.0.22                                         01-00-5E-00-00-16     Permanent   ActiveStore
16      224.0.0.2                                          01-00-5E-00-00-02     Permanent   ActiveStore
16      192.168.1.255                                      FF-FF-FF-FF-FF-FF     Permanent   ActiveStore
16      192.168.1.1                                        84-AA-9C-41-18-F9     Reachable   ActiveStore
1       239.255.255.250                                                          Permanent   ActiveStore
1       224.0.0.22                                                               Permanent   ActiveStore
1       224.0.0.2                                                                Permanent   ActiveStore
```

## Localizar la RPI y conectarnos a ella.
Vamos a localizar una tarjeta Raspberry PI 3 a la que le hemos grabado una imagen del sistema operativo Raspbian y activado el ssh. Esta RPI3 se encuentra conectada a la red local, pero es un sistema headless, es decir, no dispone de monitor y ni teclado.

```shell
PS > Get-NetNeighbor -State Reachable

ifIndex IPAddress                                          LinkLayerAddress      State       PolicyStore
------- ---------                                          ----------------      -----       -----------
16      192.168.1.1                                        84-AA-9C-41-18-F9     Reachable   ActiveStore
```			 


¡¡Recuerda cambiarlo!!

```shell
~]#  ssh pi@192.168.0.10
Linux raspberrypi 4.19.83-v7+ #1277 SMP Mon Nov 11 16:30:44 GMT 2019 armv7l

pi@raspberrypi:~ $
```

# Conclusiones

Este tipo de escaneo solo serán útiles en entornos de área local.

Algo parecido se puede obtener con el comando nmap con: ```nmap -sP -PR 192.168.0.0/24```.

# Referencias
[Get_NetNieghbor Microsoft Documenation](https://docs.microsoft.com/en-us/powershell/module/nettcpip/get-netneighbor?view=win10-ps)
[Seguridad informatica en PowerShell](https://www.jesusninoc.com/08/23/buscar-direcciones-ip-en-la-red-local-y-realizar-una-consulta-wmi/)

