---
title: "Instalar Chrome en Archlinux"
date: 2020-06-20
categories:
  - blog
tags:
  - i3wm
  - chrome
  - sysadmin
---

Chrome no esta incluido en los repositorios de Arch Linux, asi que buscamos chrome en AUR y obtenemos el paquete [google-chrome](https://aur.archlinux.org/packages/google-chrome/) (AUR).

# Instalacion
Para instalar este paquete desde aur, como siempre tenemos dos opciones el metodo automatico empleando un gestor como **yay** o el metodo manual mediante **git** y **makepkg**.

## Automatica 
Desde yay la instalacion resulta transparente: 

```shell
~]# yay -S google-chrome
```

## Manual
```
$ mkdir build
$ cd build/
$ git clone https://aur.archlinux.org/google-chrome.git
 Cloning into 'google-chrome'...
 remote: Enumerating objects: 802, done.
 remote: Counting objects: 100% (802/802), done.
 remote: Compressing objects: 100% (457/457), done.
 remote: Total 802 (delta 387), reused 758 (delta 345), pack-reused 0
 Receiving objects: 100% (802/802), 188.51 KiB | 1.43 MiB/s, done.
 Resolving deltas: 100% (387/387), done.
$ cd google-chrome/
$ makepkg --help
 makepkg (pacman) 5.2.1
 Make packages compatible for use with pacman
 Usage: /usr/bin/makepkg [options]
 Options:
   -s, --syncdeps   Install missing dependencies with pacman
 If -p is not specified, makepkg will look for 'PKGBUILD'
$ makepkg -s  
```
Una vez que se completa el proceso de compilación del paquete, debería haberse creado un google-chrome-63.0.3239.108-1-x86_64.pkg.tar.xz con nuestro paquete.

Finalmente instalamos nuestro paquete con pacman:
```
# pacman -U google-chrome-63.0.3239.108-1-x86_64.pkg.tar.xz
```

# Ejecucion
Ctrl+D y ejecutamos Chrome

# Resultado

La instalacion es facil, rapida y funciona a la primera. 

Como siempre gracias AUR.

