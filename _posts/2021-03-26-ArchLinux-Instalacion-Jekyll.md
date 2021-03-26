---
title: "Configurar Jekyll en Archlinux"
date: 2021-03-26
categories:
  - blog
tags:
  - tools
  - admin
---

Jekyll es un generador de sitios webs, estilo blog, estaticos escrito en Ruby.


# Descripcion

Proceso de instalacion y configuraion de jekyll en Archlinux.

# Procedimiento
El primer paso es instalar la herramienta __gem__. 
```shell
~]# pacman -S ruby rubygems
```
Es necesario incluir en el PATH los binarios de __ruby__ para poder ejecutar los gems que descarguemos.

```shell
~]$ nano .bash_profile 
```
y añadimos:

export GEM_HOME="$(ruby -e 'puts Gem.user_dir')"
export PATH="$PATH:$GEM_HOME/bin"

nuestro path ahora incluira a ruby

```shell
~]$echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/bin:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl:/home/jmgomez/.local/share/gem/ruby/2.7.0/bin
[jmgomez@udit
```

Una vez que tenemos gems y ruby gems instaldo y configurado pasamos a instalar jekyll.
```shell
~]$ gem env
~]$ gem update
~]$ gem install jekyll
```
Algunos paquetes complementarios necesarios son:
```shell
~]$ gem install rdiscount -s http://gemcutter.org
~]$ gem install alembic-jekyll-theme
~]$ gem install bundler rdoc
```

## Ejemplo basico de sitio jekyll
Un sitio web jekyll

Durante la instalacion pacman crea el usuario git. Pero lo crea sin directorio home. Tal vez seria mas limpio, primero crear el usuario con 
```shell
~]$ mkdir web-project0
~]$ cd web-project0/
~]$ jekyll new MyWebSyte
~]$ cd MyWebSyte/
~]$   bundle install
```
Ahora podemos previsualizar nuestra pagina web empleando
```shell
~]$ bundle exec jekyll serve
```
esto creará un web server en 127.0.0.1:4000

## Instalando themas
Existen muchos modulos con themas para Jekyll tal vez la practica mas comoda sea buscar en la base de datos de __ruby__ https://rubygems.org/. La mayoria de los temas inclyen las palabras jekyll-theme.

Una vez localizado nuestro modulo, o instalamos usando gems.
```shell
~]$ gem install minimal-mistakes-jekyll
```

Es buena practica añadir esta dependencia al fichero Gemfile
```shell
~]$ nano Gemfile
```
gem "minimal-mistakes-jekyll" 
gem  "jekyll-include-cache"

Finalmente, actulizamos las dependencias
```shell
~]$ bundle
```
Ahora en nuestro fichero _config.yml podemos seleccionar el tema:
gem install minimal-mistakes-jekyll

# Conclusiones




# Referencias
[ArchLinux Wiki](https://wiki.archlinux.org/index.php/jekyll)
[Building a website with Jekyll](http://www.amjacobs.net/notes/website/)


