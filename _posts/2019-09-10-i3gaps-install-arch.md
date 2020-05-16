---
title: "Instalacion i3-gaps en Archlinux"
date: 2019-09-10
categories:
  - blog
tags:
  - i3wm
  - arch
  - sysadmin
---

# Descripcion
i3 es un gestor de ventanas y como tal requiere de un servidor de ventanas. 

# Proceso

En primer lugar, debemos instalar el servidor de ventanas xorg y los drivers graficos.
`$ sudo pacman -S xorg-server xorg-init `

OK, now we’re ready to set up our graphical environment.  The first thing we need is xorg-server, which provides us with our X11 base:

`$ sudo pacman -S xorg-server`

Para el caso, de una instalacion en maquina virtual es recomendable emplear los drivers vesa:
` $ sudo pacman -S xf86-video-vesa`

Para poder realizar el startx, y acceder al entorno grafico debemos instalar:

```shell
$ sudo pacman -S xorg-xinit
$ sudo pacman -S i3-gaps 
```

Esta es la version minima, adicionalmente esta bien instalar:
`$ sudo pacman -S i3status dmenu i3lock rxvt-unicode`

Despues modifcamos el fichero de inicio de xinit para que en lugar de twm arranque i3.
`$ sudo nano /etc/X11/xinit/xinitrc`
 > #twm &
 > exec i3

Y ya podemos arrancar emplear startx


