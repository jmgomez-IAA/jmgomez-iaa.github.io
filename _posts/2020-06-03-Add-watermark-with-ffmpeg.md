---
title: "Añadir marca de agua a un video con ffmpeg"
date: 2020-06-03
categories:
  - blog
tags:
  - tools
  - ffmpeg
---

FFMPEG es una herramienta open source que permite grabar, convertir y transmitir audio y video. se the VLC player instead

# Descripcion

La herramienta [ __ffmpeg__ ](https://ffmpeg.org/) se compone de un conjunto de aplicaciones que permite manipular videos. FFMPEG no incluye ningun GUI, se trata de una aplicación en linea de comandos. Aunque esto puede parecer una desventaja, cuando se le coje el truco, se convierte en una gran cualidad, ya que permite crear scripts y trabajar de modo rapido con cualquier video.

# Añadir la marca de agua
Para añadir la marca de agua vamos a emplear la opcion ```-filter_complex``` y en concreto el filtro __[overlay](https://ffmpeg.org/ffmpeg-filters.html#overlay-1)__. Overlay tal y como su nombre indica superpone un video a otro. Este filtro recibe como entrada dos ficheros de video y produce uno en la salida. 
~]$  ffmpeg -i input_1_main -i input_2_volaid -filter_complex "overlay=x:y" output_video.mp4

El primer video es el video main y el segundo sera el que se sobreescriba. 

De los parametros que acepta el filtro overlay son imprescindibles **x** e **y**, que son las coordenadas en el video __main__ donde se sobrepondra el video __overlaid__. Existen dos expresiones definidas para cada uno de los videos que nos indican sus tamaños, y que resultan muy utiles para calcular estos parametros.
main:
  - main_w, W : The main input width.
  - main_h, H : The main input height.    
overlay:
  - overlay_w, w : The overlay input width.
  - overlay_h, h : The overlay input height.

Asi por ejemplo, vamos a colocar nuestra marca de agua en la esquina inferior derecha. Por lo tanto, las coordenadas **x** e **y** donde iría __overlay__ son
**x = main_w-overlay_w-5**  e **y=main_h-overlay_h-5**.  Le hemos dejado un margen de 5 px para que no quedara muy al borde.

Si generamos el comando ffmpeg:
    __ffmpeg -i input -i logo -filter_complex 'overlay=main_w-overlay_w-5:main_h-overlay_h-5' output__

# Script
En este caso no vamos a crear ningun script, dado que es una unica sentiencia. En nuestra linea de comandos:
```shell
~]$  ffmpeg -i 2MinMontaje.mp4 -i ../../resources/logos/logo_2020_320x320.png -filter_complex "overlay=main_w-overlay_w-5:main_h-overlay_h-5" -codec:a copy montaje_timelapse.mp4
```

Cabe la posibilidad de mejorar nuestro comando, por que no siempre querremos que el logo este visible durante todo el video. Con esta modificación podemos controlar cuando sera aplicado el filtro, es decir, crea una copia del vidoe __main__ con marca de agua (el logo), pero solo la coloca desde el segundo 1 hasta el 135.

```shell
~]$ ffmpeg -i 2MinMontaje.mp4 -i ../../resources/logos/logo_2020_320x320.png -filter_complex "[1]lut=a=val*0.3[a];[0][a]overlay=main_w-overlay_w-5:main_h-overlay_h-5:enable='between(t,1,135)':format=rgb" -codec:a copy timelapse_v0.4.mp4
```			 

# Conclusiones
I love ffmpeg.

# Referencias
[Welcome to the FFmpeg Bug Tracker and Wiki](https://trac.ffmpeg.org/#CommunityContributedDocumentation)
[FFMPEG Overlay filter Documentation](https://ffmpeg.org/ffmpeg-filters.html#overlay-1)
[How to add transparent watermark in center of a video with ffmpeg?](https://stackoverflow.com/questions/10918907/how-to-add-transparent-watermark-in-center-of-a-video-with-ffmpeg)
[How to add watermark to video with ffmpeg after 1 minute, lasting 30 seconds?](https://superuser.com/questions/1091226/how-to-add-watermark-to-video-with-ffmpeg-after-1-minute-lasting-30-seconds)
[ffmpeg add semi transparent watermark(png) with different size](https://stackoverflow.com/questions/39591675/ffmpeg-add-semi-transparent-watermarkpng-with-different-size)

