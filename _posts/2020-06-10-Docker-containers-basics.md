---
title: "Docker Container en Archlinux Basics"
date: 2020-06-10
categories:
  - blog
tags:
  - tools
  - sysadmin
---

Docker es una plataforma de contenedores para encapsular ejecuciones.

# Descripcion

Docker es una plataforma de contenedores que permite solucionar el problema de fragmentación que existe en el Unix. Permite generar __facilmente__ un contenedor que garantice que un software se ejecute exactamente como esperamos independientemente de donde se despliege.

A diferencia dela virtualizacion solo es una capa quese ejcuta empleando el mismo kernel del sistema, por que su impacto en rendimiento es mucho mas bajo. ...

# Instalacion
Para la instalacion seguiremos [la guia oficial en arch wiki](https://wiki.archlinux.org/index.php/docker#Installation). 

```
# pacman -S docker
```

A continuación debemos activar el sevicio:

```
# systemctl start docker.service
# docker info
```

Comprobamos que el arranque es correcto y lo habilitamos para que se arranque al inicio:
```
# systemctl enable docker.service
```

Ahora, que ya tenemos docker instalado, vamos a probar que es capaz de ejecutar contenedores. Para ello, vamos a emplearla imagen de Arch Linux, en la que se ejecutara un comando Bash que dira "Hello World". 
```
# docker run -it --rm archlinux bash -c "echo Hello world"
```

Finalmente, añadimos nuestro usuario al grupo de docker para poder ejecutar contenedores docker sin necesidad de permisos root.

```
# usermod -a jmgomez -G docker
$ docker run -it --rm archlinux bash -c "echo Hello world"
```

> Precuacion:
> If you want to be able to run the docker CLI command as a non-root user, add your user to the docker user group and restart docker.service.
> Warning: Anyone added to the docker group is root equivalent because they can use the docker run --privileged command to start containers with root privileges. 

# Configuración
En Arch Linux la configuracion no se almacena en el fichero.json, sino que debemos pasarla por parametros en el servicio docker.service.

# Comandos rapidos
docker info
docker version
docker ps
docker search [image name]
docker pull [image name]
docker run [container id]
docker stop [container id]
docker rm [container id]
docker network ls
docker network inspect bridge
docker network create --driver bridge bridge_new
docker run --network= bridge_new -itd --name=[container ID] busybox
docker exec -it <mycontainer> bash
docker attach [container ID]


## DNS

Los contenedores docker, heredan del sistema los dns que estan almacenados en el fichero /etc/resolv.conf
```
$ cat /etc/resolv.conf 
nameserver 8.8.8.8
nameserver 8.8.4.4
```

En caso de no tener red, o mas bien si tener red, pero no poder alcanzar los host remotos, puede ser culpa de esto.

# Contenedores 
Existen muchos contenedores pre-creados disponibles en [dockerhub](https://hub.docker.com/) para ser descargados y usado directamente. Destacan:

- Alpine, Alpine is a lightweight linux distribution based on musl libc and busybox. 
```
docker run -it --rm alpine bash -c ping www.google.es
```
- [python:3.8-slim-buster](https://hub.docker.com/_/python), lightweight version of python on Ubuntu.
 
 - archlinux 

# Conclusiones
I fast and usefull.

# Referencias
[Docker en Archlinux Wiki](https://wiki.archlinux.org/index.php/docker)
[Arch Linux Docker Tutorial](https://linuxhint.com/arch-linux-docker-tutorial/)
[Docker Get-started](https://docs.docker.com/get-started/)
[Docker Container Tutorial](http://containertutorials.com/index.html)


