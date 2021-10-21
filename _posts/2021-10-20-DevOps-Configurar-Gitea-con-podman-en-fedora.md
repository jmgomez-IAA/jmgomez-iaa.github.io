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

Gitea es un servicio GIT que ejecuta un servicio web. Es muy similar a GitHub, Bitbucket, and GitLab. Gitea es un fork de Gogs.

# Descripcion

Gitea es la manera más sencilla, rápida y menos dolorosa de poner en marcha tu propio servicio de Git en tu infraestructura, tu propio Github, para entendernos. Gitea proporciona un entorno web que permite gestionar los respositorios Git desde el navegador, el acceso que tienen los usuarios, gestionar issues y pull requests e incluso crear un wiki para documentar el proyecto. 

Gitea documentation : https://docs.gitea.io/en-us/install-with-docker/

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

El acceso a Gitea se realiza a través de dos puertos expuestos en el contenedor: el puerto 22, para el acceso vía SSH y el 3000, donde se publica el servidor web. En nuestro pod vamos a mapear los puertos 22 al 222 y 3000 al 3000 que son puertos libres y disponibles en el host.

## Puertos y red
- 3000 : Puerto conexion HTTP a GITEA. 
- 222: Puerto conexion por SSH al servidor openSSH del contenedor GITEA
- 9187 Puerto de conexion para el postgresql exporter.

```shell
~]# podman  network create gitea-net
~]# podman pod create -p 3000:3000 -p 222:22 -p 9187:9187 --net gitea-net  --name gitea-pod
```
Vamos a comprobar que ha funcionado correctamente:

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
- 222  -->   22
- 3000 --> 3000
- 9187 --> 9187

## Volumenes
Necesitamos almacenar los datos de gitea en nuestro sistema host, por lo tanto vamos a crear varios volumenes:
- **/var/srv-data/gitea/postgresql/data** Postgres Database data
- **/var/srv-data/gitea/data** Gitea data
- **/etc/localtime** por que el fichero timezone no existe en Fedora, hay que usar localtime que son equivalentes.

Las opcion **z** indica a podman que añada etiquetas al fichero para que SeLinux perdita su uso compartido.

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
~]# podman ps -a
.0.0.0:9187->9187/tcp  f93c99b40592-infra
f4733809f9ae  docker.io/library/postgres:latest   postgres              23 hours ago  Up 22 hours ago         0.0.0.0:222->22/tcp, 0.0.0.0:3000->3000/tcp, 0.0.0.0:9187->9187/tcp  nice_hodgkin
9609bba81dc5  docker.io/gitea/gitea:latest        /bin/s6-svscan /e...  23 hours ago  Up 22 hours ago         0.0.0.0:222->22/tcp, 0.0.0.0:3000->3000/tcp, 0.0.0.0:9187->9187/tcp  determined_rhodes
```
Ya vemos que los contenedores postgres y el gitea estan funcionando, y los puertos estan mapeados correctamente.  

Si ha ocurrido algun error podemos revisar los logs de los contendores.

```shell
~]# podman logs -l
```

# Configuracion y Test
 
Si todo ha funcionado correctamente, y nuestro contenedor esta funcioando, ya podemos abrir nuestra aplicacion:

http://udit76.iaa.es:3000/

Debemos de configurar algunas opciones:

1.- URL BASE de GITEA: http://udit76.iaa.es:3000/

2.- Cuenta administador: Es buena practica crear el usuario administrador y su password. Hay que tener en cuenta que el email del administrador no puede ser reutilizado por otro usuario.

Si queremos cambiar algo de la configuración de gitea, se hace definiendo variables en el fichero de configuracion __ /var/srv-data/gitea/data/gitea/conf/app.ini __. El uso de estas variables viene documentado en [https://docs.gitea.io/en-us/config-cheat-sheet/]([https://docs.gitea.io/en-us/config-cheat-sheet/).

# Mejoras
Realmente cabe la posibilidad de lanzar los contenedores desde systemd. Esto nos permite automatizar el proceso:
[Podman - Setup Gitea](https://blog.while-true-do.io/podman-setup-gitea/)

Algunos de los repositorios, estarán ligados a webhooks como por ejemplo Jekyll Pages:
[Static websites automatic deployment with Gitea, an example with Jekyll](https://blog.samuel.domains/blog/tutorials/static-websites-automatic-deployment-with-gitea-an-example-with-jekyll)

# Referencias

[GITEA Installation with Docker](https://docs.gitea.io/en-us/install-with-docker/)

[Dockerless, part 3: Moving development environment to containers with Podman](https://mkdev.me/en/posts/dockerless-part-3-moving-development-environment-to-containers-with-podman)

[Podman - Volumes 1/2](https://blog.while-true-do.io/podman-volumes-1/)

[Podman - Networking 3/3](https://blog.while-true-do.io/podman-networking-3/)

[Spinning up and Managing Pods with multiple containers with Podman](https://mohitgoyal.co/2021/04/23/spinning-up-and-managing-pods-with-multiple-containers-with-podman/)

[User IDs and (rootless) containers with Podman](https://blog.christophersmart.com/2021/01/26/user-ids-and-rootless-containers-with-podman/)

