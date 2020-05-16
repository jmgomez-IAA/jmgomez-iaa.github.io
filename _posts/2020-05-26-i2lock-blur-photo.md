---
title: "Bloquear pantalla en i3w"
date: 2020-05-26
categories:
  - blog
tags:
  - i3wm
  - misc
---

En i3wm podemos bloquear la pantalla del ordenador empleando i3lock. Este bloqueo es aburrido y monotono, asi que lo vamos a mejorar.

# Descripcion

Por defecto i3lock, bloque la pantalla y coloca un fondo blanco que cubre el escritorio. Sin embargo, podemos emplear **-i** para indicarle una imagen (i3lock solo soporta formato png)que empleara como pantalla de bloqueo.

```shell
~]$ i3lock -i /tmp/screen.png
```

Para nuestro proposito vamos a crear un script que empleando [scrot](https://en.wikipedia.org/wiki/Scrot) e [imagemagick](https://en.wikipedia.org/wiki/ImageMagick) haga una captura del escritorio y aplicarle un efecto borroso.

Podemos ampliar el script o modificarlo a nuestro gusto. Por ejemplo, yo para darle un caracter diferenciador voy a superponer una imagen a la captura de pantalla.

# Script

```
#!/bin/bash
revert() {
  rm /tmp/*screen*.png
  xset dpms 0 0 0
}
trap revert HUP INT TERM
xset +dpms dpms 0 0 5
scrot -d 1 /tmp/locking_screen.png
convert -blur 0x8 /tmp/locking_screen.png /tmp/screen_blur.png
convert -composite /tmp/screen_blur.png /home/jmgomez/Workspace/web/resources/28093206.jpg -gravity SouthWest -geometry -20x1200 /tmp/screen.png
i3lock -i /tmp/screen.png
revert
```
# Instalacion

Finalmente, vamos a instalar este script dentro de nuestros __dotfiles__, en ~/.config/i3/scripts/lock_screen.sh y ha asignarle un short-cut de teclado en el fichero de configuracion de i3wm (~/.config/i3/config).

```shell
# i3lock to custom script with blur+photo from gallery.
bindsym $mod+Pause exec "~/.config/i3/scripts/lock_screen.sh"
```

# Resultado
Ahora cuando pulsemos la combinacion de teclas $mod+Pause, nuestro ordenador se bloqueara y el resultado sera algo similar a esto:

<img src="/assets/2020-05-26-i2lock-blur-photo/i3lock_result.png">

# Subscribe

<!-- Begin Mailchimp Signup Form 
<link href="//cdn-images.mailchimp.com/embedcode/horizontal-slim-10_7.css" rel="stylesheet" type="text/css">
<style type="text/css">
	#mc_embed_signup{background:#fff; clear:left; font:14px Helvetica,Arial,sans-serif; width:100%;}
	/* Add your own Mailchimp form style overrides in your site stylesheet or in this style block.
	   We recommend moving this block and the preceding CSS link to the HEAD of your HTML file. */
</style>
<div id="mc_embed_signup">
<form action="https://gmail.us3.list-manage.com/subscribe/post?u=92fe86c389878585bc87837e8&amp;id=50543deff9" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank" novalidate>
    <div id="mc_embed_signup_scroll">
	<label for="mce-EMAIL">Liked this article and want to hear more?</label>
	<input type="email" value="" name="EMAIL" class="email" id="mce-EMAIL" placeholder="email address" required>
    <!-- real people should not fill this in and expect good things - do not remove this or risk form bot signups-->
<!--
    <div style="position: absolute; left: -5000px;" aria-hidden="true"><input type="text" name="b_92fe86c389878585bc87837e8_50543deff9" tabindex="-1" value=""></div>
    <div class="clear"><input type="submit" value="Subscribe" name="subscribe" id="mc-embedded-subscribe" class="button"></div>
    </div>
</form>
</div>

End mc_embed_signup-->
