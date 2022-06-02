---
title: "Ejecutar programas i686 en fedora"
date: 2022-06-02
categories:
  - blog
tags:
  - fedora
  - sysadmin
  - grmon
---

GRMON v1 es una aplicacion 32 bits y por lo tanto para poder ejecutarla en los sistemas x64 necesitamos las librerias dinamicas que emplea.

# Descripcion

En los kernel


# Procedimiento

Empleamos el comando file para obtener información del tipo de archivo que tenemos.
```shell
~]#  file grmon_1.1.61
grmon_1.1.61: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.18, stripped
```

Esta inforamción tambien la podemos obtener mediante objdump. La opcion -f muestra la información disponible en la cabecera elf.
```shell
~]# objdump -f grmon_1.1.61

grmon_1.1.61:     formato del fichero elf32-i386
arquitectura: i386, opciones 0x00000112:
EXEC_P, HAS_SYMS, D_PAGED
dirección de inicio 0x0805fd00
```

Usamos ldd para mostrar las librerias dinamicas que necesita el ejecutable.
```shell
~]#  ldd grmon_1.1.61
        linux-gate.so.1 (0xf7ece000)
        libm.so.6 => /lib/libm.so.6 (0xf7dee000)
        libdl.so.2 => /lib/libdl.so.2 (0xf7de9000)
        libpthread.so.0 => /lib/libpthread.so.0 (0xf7de4000)
        librt.so.1 => /lib/librt.so.1 (0xf7ddf000)
        libz.so.1 => /lib/libz.so.1 (0xf7dc5000)
        libc.so.6 => /lib/libc.so.6 (0xf7bcf000)
        /lib/ld-linux.so.2 (0xf7ed0000)
```

## Instalamos las librerias
Finalmente solo tenemos que instalar las versiones i686 de la librerias que requiere este ejecutable. Para ellos empleamos dnf para que busque que paquetes las incluyen y elegimos el que mas nos interese.

```shell
~]# dnf provides libc.so.6 
Repositorio : fedora
Resultado de:
Proporciona : libc.so.6

glibc-2.34-34.fc35.i686 : The GNU libc libraries
Repositorio : @System
Resultado de:
Proporciona : libc.so.6

glibc-2.34-34.fc35.i686 : The GNU libc libraries
Repositorio : updates
Resultado de:
Proporciona : libc.so.6

~]# dnf info glibc-2.34-34.fc35.i686
Última comprobación de caducidad de metadatos hecha hace 0:09:28, el jue 02 jun 2022 11:53:43.
Paquetes instalados
Nombre       : glibc
Versión      : 2.34
Lanzamiento  : 34.fc35
Arquitectura : i686
Tamaño       : 5.3 M
Fuente       : glibc-2.34-34.fc35.src.rpm
Repositorio  : @System
Desde repo   : updates
Resumen      : The GNU libc libraries
URL          : http://www.gnu.org/software/glibc/
Licencia     : LGPLv2+ and LGPLv2+ with exceptions and GPLv2+ and GPLv2+ with exceptions and BSD and Inner-Net and ISC and Public Domain and GFDL
Descripción  : The glibc package contains standard libraries which are used by
             : multiple programs on the system. In order to save disk space and
             : memory, as well as to make upgrading easier, common system code is
             : kept in one place and shared between programs. This particular package
             : contains the most important sets of shared libraries: the standard C
             : library and the standard math library. Without these two libraries, a
             : Linux system will not function.

```


En este caso, podriamos instalar solo glibc y con el tendriamos suficiente para ejecutar aplicaciones i686, pero vamos a buscar algun paquete que englobe esta librarias y el resto de librarias de desarrollo para i386.

```shell
~]# dnf provides glibc*
Última comprobación de caducidad de metadatos hecha hace 2:12:49, el jue 02 jun 2022 09:44:01.
glibc-2.34-7.fc35.i686 : The GNU libc libraries
Repositorio : fedora
Resultado de:
Proporciona : glibc = 2.34-7.fc35
Proporciona : glibc(x86-32) = 2.34-7.fc35
....

glibc-devel-2.34-7.fc35.i686 : Object files for development using standard C libraries.
Repositorio : fedora
Resultado de:
Proporciona : glibc-headers(i686)
Proporciona : glibc-devel = 2.34-7.fc35
Proporciona : glibc-headers = 2.34-7.fc35
Proporciona : glibc-devel(x86-32) = 2.34-7.fc35
...

```


Instalamos libc.so.6 empleando el paquete dglibc-devel, 
```shell
~]# sudo dnf  install -y glibc-devel.i686
dnf info glibc-devel.i686
Última comprobación de caducidad de metadatos hecha hace 0:01:28, el jue 02 jun 2022 11:53:43.
Paquetes instalados
Nombre       : glibc-devel
Versión      : 2.34
Lanzamiento  : 34.fc35
Arquitectura : i686
Tamaño       : 100 k
Fuente       : glibc-2.34-34.fc35.src.rpm
Repositorio  : @System
Desde repo   : updates
Resumen      : Object files for development using standard C libraries.
URL          : http://www.gnu.org/software/glibc/
Licencia     : LGPLv2+ and LGPLv2+ with exceptions and GPLv2+ and GPLv2+ with exceptions and BSD and Inner-Net and ISC and Public Domain and GFDL
Descripción  : The glibc-devel package contains the object files necessary
             : for developing programs which use the standard C libraries (which are
             : used by nearly all programs).  If you are developing programs which
             : will use the standard C libraries, your system needs to have these
             : standard object files available in order to create the
             : executables.
             :
             : Install glibc-devel if you are going to develop programs which will
             : use the standard C libraries.
```

Ahora continuamos con el resto de las dependiencias vamos comprobando que paquete incluye las libdl, libm, libdl, libpthread, librt.
```shell
~]# dnf provides libdl.so.2
Última comprobación de caducidad de metadatos hecha hace 0:18:09, el jue 02 jun 2022 11:53:43.
glibc-2.34-7.fc35.i686 : The GNU libc libraries
Repositorio : fedora
Resultado de:
Proporciona : libdl.so.2

glibc-2.34-34.fc35.i686 : The GNU libc libraries
Repositorio : @System
Resultado de:
Proporciona : libdl.so.2

glibc-2.34-34.fc35.i686 : The GNU libc libraries
Repositorio : updates
Resultado de:
Proporciona : libdl.so.2
```

el resultado es que estan incluida dentro de glibc todas menos zlib. Asi que instalamos Zlib

```shell
~]# dnf provides zlib
zlib-1.2.11-30.fc35.i686 : Compression and decompression library
Repositorio : @System
Resultado de:
Proporciona : zlib = 1.2.11-30.fc35

~]# dnf install zlib.i686
Instalado:
  zlib-1.2.11-30.fc35.i686

```

Y para finalizar probamos nuestro ejecutable:
```shell
~]# ./grmon_1.1.61 -ftdi -u

 GRMON LEON debug monitor v1.1.61 professional version

 Copyright (C) 2004-2011 Aeroflex Gaisler - all rights reserved.
 For latest updates, go to http://www.gaisler.com/
 Comments or bug-reports to support@gaisler.com

 JTAG chain: 

 Device ID: : 
 GRLIB build version: 3696

 initialising .......................................
 detected frequency:  50 MHz

grlib>
```

# Conclusion

GRMON esta listo para usar en nuestro Fedora 35.

# Referencias

[Linux Kernel Versions: 32-Bit vs 64-Bit](https://www.baeldung.com/linux/kernel-versions-32-vs-64-bit)

[Check if a Library is 32-Bit or 64-Bit](https://www.baeldung.com/linux/check-library-32-or-64-bit)