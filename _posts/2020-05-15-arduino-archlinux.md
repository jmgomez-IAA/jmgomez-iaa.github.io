---
title: "Instalar Arduino en Archlinux"
date: 2020-05-15
categories:
  - blog
tags:
  - i3wm
  - arduino
  - sysadmin
---

Arduino esta incluido en los repositorios de Archlinux, asi que la instalación es sencilla ya que pacman se encargará de hacerla.

# Instalacion

Lo primero será instalar el paquete arduino y su dependencia arduino-avr-core, disponibles en los repositorios oficiales (Y de paso actualizamos nuestro sistema):

```shell
~]# pacman -Syu arduino arduino-avr-core
```

En ArchLinux, el grupo propietario de /dev/ttyUSB0 es uucp, esto es porque nuestra placa se comunica con nuestro PC a través de una conexion serial (o serie a USB):

```shell
~]$ ls -l /dev/ttyUSB0
crw-rw—- 1 root uucp 188, 0 may 15 15:01 /dev/ttyUSB0
```

Por lo tanto para poder usar arduino con nuestro usuario tenemos que agragar a nuestro usuario al grupo uucp y asi poder enviar los sketches a nuestra placa:

```shell
~]# sudo usermod -a -G uucp
```


# Q&A
¿Que java runtime debemos emplear?
En nuestro caso, hemos seleccionado **jre8-openjdk** por compatibilidad con thingsboard.
 > There are 4 providers available for java-runtime>=8:
 > :: Repository extra
 > 
 >  jre-openjdk 2) jre10-openjdk 3) jre11-openjdk 4) jre8-openjdk

 > Enter a number (default=1): 4

¿Es i3wm un non-reparenting window manager? No, no es necesario crear la variable _JAVA_AWT_WM_NONREPARENTING.
 > when you use a non-reparenting window manager, set _JAVA_AWT_WM_NONREPARENTING=1 in /etc/profile.d/jre.sh