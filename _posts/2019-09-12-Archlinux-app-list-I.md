---
title: "Lista aplicaciones para Archlinux I"
date: 2019-09-12
categories:
  - blog
tags:
  - i3wm
  - arch
  - sysadmin
---

# Introduccion
Algunas de las aplicaciones seleccionadas de  las lista:
[https://wiki.archlinux.org/index.php/list_of_applications](https://wiki.archlinux.org/index.php/list_of_applications)

## File Manager
- Ranger, Console File Manager 
``` 
$]# pacman -S ranger atool lynx w3m highlight mediainfo poppler
```
Optional dependencies for ranger:
- atool: for previews of archives
- elinks: for previews of html pages
- ffmpegthumbnailer: for video previews
- highlight: for syntax highlighting of code
- libcaca: for ASCII-art image previews
- lynx: for previews of html pages
- mediainfo: for viewing information about media files
- odt2txt: for OpenDocument texts
- perl-image-exiftool: for viewing information about media files
- poppler: for pdf previews
- python-chardet: in case of encoding detection problems [installed]
- sudo: to use the "run as root"-feature [installed]
- transmission-cli: for viewing bittorrent information
- w3m: for previews of images and html pages

## Gestor de correo electronico en consola:

 - mutt
 - [neomutt](https://neomutt.org/distro/arch)
          [install y extras](https://www.guckes.net/neomutt/)

## Visor PDF
- mupdf, es lo minimo.
- [Zathura](https://wiki.archlinux.org/index.php/Zathura), es el front-end vi keybinding.
**Enable the copy to clipboard feature**
```
~]# pacman -S zathura zathura-cb zathura-djvu zathura-pdf-mupdf zathura-ps
~]$ cat << EOF | tee  ~/.config/zathura/zathurarc
> set selection-clipboard clipboard
> EOF
```

## Navegador Web
- links

## RSS feeds / atom
- newsboat

## Conversor de documentos
    [Pandoc](https://pandoc.org/) — Swiss-army knife for converting markup and document formats.

## Bienestar

[redshift](http://jonls.dk/redshift/) Ajusta el brillo del monitor a la hora y posicion del sol.

 > Altenativamente se puede hacer manualmente empleando xrandr.
 > [jmgomez@greencloud scripts]$ xrandr --listmonitors
 > Monitors: 2
 > 0: +*DVI-D-1 1920/527x1080/296+0+0  DVI-D-1
 > 1: +HDMI-1 1920/527x1080/296+1920+0  HDMI-1
 > ]$ xrandr --output DVI-D-1 --gamma 1:1:1 --brightness 1.0
 > ]$ xrandr --output DVI-D-1 --gamma 1:1:0.5 --brightness 0.7


## Q&A

### PACMAN
1.- Actualizar el keyring y el sistema.
```pacman -Sy archlinux-keyring && pacman -Syyu```

2 .-Sobreescribir un paquete que esta molestando
``` pacman -Syu --overwrite /usr/lib/libstfl.so.0```

Configuracion
1.- Resolucion pantalla
xrandr
https://unix.stackexchange.com/questions/81746/how-can-i-get-better-looking-fonts-in-my-terminal-urxvt
http://wiki.afterstep.org/index.php?title=Rxvt-Unicode_Configuration_Tutorial
