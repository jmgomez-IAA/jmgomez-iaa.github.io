---
title: "Resolviendo dependiencias de librerias en ejecutable"
date: 2023-08-04
categories:
  - blog
tags:
  - fedora
  - sysadmin
  - ELF32
---

# Descripcion

En muchas ocasiones cuando ejecutamos un ELF, este se queja de que no encuentra una libreria. 

Este es por ejemplo, el caso de cuando recibimos el ERROR: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory”, ld-linux.so es una libreria dinamica encargada de cargar o linkar archivos ELF y es parte del sistema operativo y por lo tanto debe estar instalada.


# Procedimiento

El primer paso es descubir las dependencias de nuestro archivo ejecutable empleando cualquiera de estas tecnicas: 
```shell
~]#  file grmon_1.1.61
grmon_1.1.61: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.18, stripped

~]# readelf -a grmon_1.1.61 | grep ELF
ELF Header:
    Class:                           ELF32

~]# objdump -f grmon_1.1.61
grmon_1.1.61:     formato del fichero elf32-i386
arquitectura: i386, opciones 0x00000112:
EXEC_P, HAS_SYMS, D_PAGED
dirección de inicio 0x0805fd00
```

Aqui ya observamos que se trata de un archivo ejecutable de 32bits (soporte eliminado en kernel modernos) y que necesita /lib/ld-linux.so.2.


Para obtener una lista de todas las dependencias del ejecutable empleamos readelf:

```shell
~]# readelf -a grmon | grep NEEDED
 0x00000001 (NEEDED)                     Shared library: [libm.so.6]
 0x00000001 (NEEDED)                     Shared library: [libdl.so.2]
 0x00000001 (NEEDED)                     Shared library: [libpthread.so.0]
 0x00000001 (NEEDED)                     Shared library: [librt.so.1]
 0x00000001 (NEEDED)                     Shared library: [libz.so.1]
 0x00000001 (NEEDED)                     Shared library: [libc.so.6]
```

o bien empleando ldd como ya vimos en Ejecutar programas i686 en fedora:
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

# Resolviendo la dependencia

Ahora debemos localizar paquetes que inluyan las liberias que necesitamos, por ejemplo libz.so.1:
```shell
~]# yum provides libz.so.1
Last metadata expiration check: 0:50:48 ago on Thu 03 Aug 2023 11:42:27 AM CEST.
zlib-1.2.12-5.fc37.i686 : Compression and decompression library
Repo        : fedora
Matched from:
Provide    : libz.so.1
```

El paquete sera *zlib-1.2-12-5.fc37.i686* y procedemos a instalarlo con nuestro gestor de paquetes.


# Conclusion

GRMON esta listo para usar en nuestro Fedora 35.

# Referencias

[Linux Kernel Versions: 32-Bit vs 64-Bit](https://www.baeldung.com/linux/kernel-versions-32-vs-64-bit)

[Check if a Library is 32-Bit or 64-Bit](https://www.baeldung.com/linux/check-library-32-or-64-bit)

[Ejecutar programas i686 en fedora](https://jmgomez-iaa.github.io/blog/Ejecutar-programas_i686-en-fedora/)
