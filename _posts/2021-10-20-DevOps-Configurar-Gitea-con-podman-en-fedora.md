---
title: "-DevOps Configurar Gitea con podman en fedora"
date: 2021-10-20
categories:
  - blog
tags:
  - gitea
  - podman
  - devops
---

PODMAN es un gestor de contenedores. 

# Descripcion


Creamos un sistema BTRFS con un dispositivo solamente, en un HDD y sin particiones. 

```shell
~]# podman

```


Gitea

Gitea documentation : https://docs.gitea.io/en-us/install-with-docker/

Developer to be able to run this application locally he or she needs:

Gitea
PostgreSQL server;
Redis server;
Mattermost instance (for our chat solution);
Mattermost test instance (to be used during automated tests);

# Procedimiento

En primer lugar, vamos a ver con que estamos trabajando:
```shell
~]$ cat /etc/fedora-release
Fedora release 34 (Thirty Four)
~]$ podman --version
podman version 3.3.1
```

Para empezar vamos a borrar los rastros de antiguas instalacion de gitea.

```shell
~]# podman pod rm gitea-pod -f
```

Ahora vamos a crear un pod, en el que incluiremos todos los servicios necesario para lanzar gitea. Nuestro pod, debe permitir acceso desde el exterior, exponiendo los puertos:

## Puertos y red
- 3000 : Puerto conexion HTTP a GITEA. 
- 222: Puerto conexion por SSH al servidor openSSH del contenedor GITEA
- 9187 Puerto de conexion para el postgresql exporter.

```shell
~]# podman  network create gitea-net
~]# podman pod create -p 3000:3000 -p 222:22 -p 9187:9187 --net gitea-net  --name gitea-pod
```
Vamos a comprobar los que hemos creado, debe

```shell
~]# podman inspect gitea-pod
```
   "InfraConfig": {
            "PortBindings": {
                "22/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "222"
                    }
                ],
                "3000/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "3000"
                    }
                ],
                "9187/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "9187"
                    }
                ]
            },

Como vemos en la informacion de nuestro pod, podman ha consigurado en el contenedor infra el mapeo de puertos:
- 222 --> 22
- 3000 -->3000
- 9187 -->9187

## Volumenes
Necesitamos almacenar los datos de gitea en nuestro sistema host, por lo tanto vamos a crear varios volumenes:
- **/var/srv-data/gitea/postgresql/data** Postgres Database data
- **/var/srv-data/gitea/data** Gitea data
- **/etc/localtime** por que el fichero timezone no existe en Fedora, hay que usar localtime que son equivalentes.

Las opciones z y U
- **z** indica a podman que añada etiquetas al fichero para que SeLinux perdita su uso compartido.
- **U** indica a podman que cambie los UID y GUID para que coincidan con los UID y GUID del contenedor.

```shell
~]# mkdir -p /var/srv-data/gitea/{postgresql/data,data}
```

## Contenedores

Procedemos a crear nuestros contenedores con los servicios necesario para lanzar gitea.

Postgresql
```shell
~]# podman run -d -e POSTGRES_PASSWORD=XXXXX -e POSTGRES_DB=gitea -e POSTGRES_USER=gitea -v /var/srv-data/gitea/postgresql/data:/var/lib/postgresql/data:z  --pod gitea-pod postgres:latest
```

Gitea
```shell
~]# podman run -d -v /var/srv-data/gitea/data:/data:z -v /etc/localtime:/etc/localtime:ro --env GITEA__database__DB_TYPE=postgres --env GITEA__database__HOST=localhost:5432 --env GITEA__database__NAME=gitea --env GITEA__database__USER=gitea --env GITEA__database__PASSWD=XXXXX --pod gitea-pod docker.io/gitea/gitea
```

Verificamos que los contenedores esten funcionando.

```shell
~]# podman logs -l
```

# Configuracion y Test
 
http://udit76.iaa.es:3000/

URL BASE de GITEA: http://udit76.iaa.es:3000/

Es buena practica crear el usuario administrador y su password.

This is a cheat sheet for the Gitea configuration file. It contains most of the settings that can be configured as well as their default values.  https://docs.gitea.io/en-us/config-cheat-sheet/


# Referencias

[GITEA Installation with Docker](https://docs.gitea.io/en-us/install-with-docker/)

Buena description, incluye script sobre como hacer un deplyment de los containers. mola xq explica bastante ameno de leer.
https://mkdev.me/en/posts/dockerless-part-3-moving-development-environment-to-containers-with-podman

Using Podman in real Ruby on Rails application
At mkdev we completely automated our development environment with Podman. New developers (assuming they have a Linux machine running) can run a single script ./script/bootstrap.sh to get the application running. The script itself looks like this



Este seria el siguiente paso, hacer un deploy de los pods y contenedores desde el systemd.
https://blog.while-true-do.io/podman-setup-gitea/
De este mismo tio:
https://blog.while-true-do.io/podman-volumes-1/
https://blog.while-true-do.io/podman-networking-3/

muy bueno para extraer una lista de comandos utiles y frecuentes. vienen con sus explicaciones.
Spinning up and Managing Pods with multiple containers with Podman
https://mohitgoyal.co/2021/04/23/spinning-up-and-managing-pods-with-multiple-containers-with-podman/

https://blog.christophersmart.com/2021/01/26/user-ids-and-rootless-containers-with-podman/