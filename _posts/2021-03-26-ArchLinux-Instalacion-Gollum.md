---
title: "Configurar Gollum en Archlinux"
date: 2021-03-26
categories:
  - blog
tags:
  - tools
  - admin
---

Gollum es un generador de sitios webs estaticos, estilo wiki, escrito en Ruby. Los datos almacenados por los wikis Gollum son solo text en ficheros formateados empleando algunos de los markdown habituales en un repositorio Git.

# Descripcion

Proceso de instalacion y configuracion de gollum en Archlinux.

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

Una vez que tenemos gems y ruby gems instaldo y configurado pasamos a instalar gollum.
```shell
~]$ gem env
~]$ gem update
~]$ gem install gollum
```
Algunos paquetes complementarios necesarios son:
```shell
~]$ gem install bundler rdoc
```

## Ejemplo basico de sitio gollum




# Conclusiones




# Referencias
[Gollum Wiki](https://github.com/gollum/gollum/wiki)
[Running Gollum as a User Service in Arch Linux](https://exocortex.anothernode.com/2016/08/05/running-gollum-as-a-user-service-in-arch-linux.html/)


