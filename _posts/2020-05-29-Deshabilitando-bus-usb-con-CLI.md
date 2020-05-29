---
title: "Deshabilitando bus USB con CLI"
date: 2020-05-29
categories:
  - blog
tags:
  - usb
  - device-tree
---

En linux podemos manejar los dispositivos HW de nuestro sistema desde la consola. 
# Descripcion

Vamos a localizar un dispositivo dentro de nuestro bus usb y vamos a desactivar.

El bus USB, no es un bus independiente, realente este bus esta ligado al bus PCI del que depende. El procesador no puede hablar directamente con el disposito USB, en realidad el procesor se comunica con el bus PCI en el que existe un controlador USB, que es el que se comunica con nuestro dispositivo.

critorio. Sin embargo, podemos emplear **-i** para indicarle una imagen (i3lock solo soporta formato png)que empleara como pantalla de bloqueo.

```shell
~]# lspci -tv
```

-+-[0000:ff]-+-0b.0  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 R3 QPI Link 0 & 1 Monitoring
 |           +-0b.1  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 R3 QPI Link 0 & 1 Monitoring
 |           +-0b.2  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 R3 QPI Link 0 & 1 Monitoring
 |           \-1f.2  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 VCU
 \-[0000:00]-+-00.0  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 DMI2
             +-01.0-[01]--
             +-01.1-[02]--
             +-02.0-[03]--+-00.0  Advanced Micro Devices, Inc. [AMD/ATI] Oland GL [FirePro W2100]
             |            \-00.1  Advanced Micro Devices, Inc. [AMD/ATI] Cape Verde/Pitcairn HDMI Audio [Radeon HD 7700/7800 Series]
             +-03.3-[07]--
             +-05.0  Intel Corporation Xeon E7 v3/Xeon E5 v3/Core i7 Address Map, VTd_Misc, System Management
             +-14.0  Intel Corporation C610/X99 series chipset USB xHCI Host Controller
             +-19.0  Intel Corporation Ethernet Connection I217-LM
             +-1a.0  Intel Corporation C610/X99 series chipset USB Enhanced Host Controller #2
             +-1b.0  Intel Corporation C610/X99 series chipset HD Audio Controller
             +-1c.0-[08]--
             +-1c.1-[09-0a]----00.0-[0a]--
             +-1d.0  Intel Corporation C610/X99 series chipset USB Enhanced Host Controller #1
             +-1f.0  Intel Corporation C610/X99 series chipset LPC Controller
             +-1f.2  Intel Corporation C610/X99 series chipset 6-Port SATA Controller [AHCI mode]
             \-1f.3  Intel Corporation C610/X99 series chipset SMBus Controller
			 
En la raiz de este árbol, tenemos [0000:00] que es el bus pci. Este PC solo tiene un bus pci del que cuelgan todos los dispositivos conectados e identificados con un numero. Si nos fijamos en la lista, nos dice que en el bus 14, tenemos el bus xHCI que se corresponde con el bus usb 3.0.

Vamos a mirar con mas detalle esos disposivitos USB:
```
~]# lsusb | sort
```

Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 8087:800a Intel Corp.
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 002: ID 8087:8002 Intel Corp.
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 003 Device 002: ID 413c:2107 Dell Computer Corp.
Bus 003 Device 003: ID 413c:301a Dell Computer Corp.
Bus 003 Device 004: ID 0bda:5411 Realtek Semiconductor Corp.
Bus 003 Device 005: ID 0bda:5411 Realtek Semiconductor Corp.
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 004 Device 002: ID 0bda:0411 Realtek Semiconductor Corp.
Bus 004 Device 003: ID 0bda:0411 Realtek Semiconductor Corp.
Bus 004 Device 004: ID 1825:1109
Bus 004 Device 005: ID 1825:1109
Bus 004 Device 006: ID 1825:1109

Y lo relacionamos con nuestro arbol de dispositivos pci:

```
~]# ls -l /sys/bus/usb/devices/
```
total 0
1-0:1.0 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-0:1.0
1-1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1
1-1:1.0 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1:1.0
2-0:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-0:1.0
2-1 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1
2-1:1.0 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1:1.0
3-0:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-0:1.0
3-6 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-6
3-6:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-6/3-6:1.0
3-7 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-7
3-7:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-7/3-7:1.0
3-8 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-8
3-8.1 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-8/3-8.1
3-8:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-8/3-8:1.0
3-8.1:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb3/3-8/3-8.1/3-8.1:1.0
4-0:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-0:1.0
4-2 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2
4-2.1 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.1
4-2:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2:1.0
4-2.1:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.1/4-2.1:1.0
4-2.2 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.2
4-2.2:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.2/4-2.2:1.0
4-2.3 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.3
4-2.4:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.4/4-2.4:1.0
4-2.3:1.0 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.3/4-2.3:1.0
4-2.4 -> ../../../devices/pci0000:00/0000:00:14.0/usb4/4-2/4-2.4
usb1 -> ../../../devices/pci0000:00/0000:00:1a.0/usb1
usb2 -> ../../../devices/pci0000:00/0000:00:1d.0/usb2
usb3 -> ../../../devices/pci0000:00/0000:00:14.0/usb3
usb4 -> ../../../devices/pci0000:00/0000:00:14.0/usb4

Tenemos 4 buses usb, en concretoe es el bus 4 el que corresponde con el dispositivo pci0000:00/0000:00:14.0 que es nuestro controlador C610/X99 series chipset USB xHCI Host Controller.

Finalmente, vamos a tratar de actuar sobre este bus, activando y desactivando dispositivos.


# Script
Finalmente, vamos a instalar este script dentro de nuestros __dotfiles__, en ~/.config/i3/scripts/lock_screen.sh y ha asignarle un short-cut de teclado en el fichero de configuracion de i3wm (~/.config/i3/config).


```shell
~]# echo -n "0000:14:00.0" | tee /sys/bus/pci/drivers/xhci_hcd/unbind
~]# echo -n "0000:14:00.0" | tee /sys/bus/pci/drivers/xhci_hcd/bind
```

# Resultado

Cuando se ejecuta el comando unbind se desactiva el controlador usb y por lo tanto se disconectan los dispositivos. Hasta no volver a activarlo no estaran disponbiles.

No obstante el dispositivo sigue alimentado?


# Referencias

[How USB bus number and device number been assigned?](https://unix.stackexchange.com/questions/297178/how-usb-bus-number-and-device-number-been-assigned)

