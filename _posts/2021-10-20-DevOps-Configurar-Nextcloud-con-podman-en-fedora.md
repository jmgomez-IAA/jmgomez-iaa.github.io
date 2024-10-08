---
title: "DevOps Configurar Nextcloud con podman en fedora"
date: 2021-10-20
categories:
  - blog
tags:
  - nextcloud
  - podman
  - devops
---

Nextcloud es una serie de programas cliente-servidor con el objetivo de crear servicio de alojamiento de archivos. Nextcloud ha evolucionado a un sistema software de colaboración ofreciendo ficheros, calendarios, contactos e incluso Kanban Boards.

# Descripcion

La instalación de Nexcloud requiere de un conjunto de servicios para un correcto funcionamiento. La siguiente figura podría resumir nuestro objetivo:

<img src="/assets/2021-10-20-DevOps-podman-nextcloud/nextcloud-podman-architecture.png">

Como vemos, nuestra aplicación requiere de un servidor de bases de datos, un conjunto de volumenes y de exponer los puertos de los servicios.

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
~]# podman pod rm nextcloud-pod -f
```

El acceso a Nextcoud se realiza a través de dos puertos expuestos en el contenedor: el puerto 22, para el acceso vía SSH y el 80, donde se publica el servidor web. En nuestro pod vamos a mapear los puertos 22 al 222 y 3000 al 3000 que son puertos libres y disponibles en el host.

## Puertos y red
- 3380 : Puerto conexion HTTP a Nextcloud. 

```shell
~]# podman network create nextcloud-net
~]# podman pod create -p 3380:3000 -p 332:22 -p 9187:9187 --net nextcloud-net  --name nextcloud-pod
```
## Volumenes
Necesitamos almacenar los datos de gitea en nuestro sistema host, por lo tanto vamos a crear varios volumenes:
- **/var/srv-data/nextcloud/nextcloud-app** 
- **/var/srv-data/nextcloud/nextcloud-data** 
- **/var/srv-data/nextcloud/nextcloud-db**

Las opcion **z** indica a podman que añada etiquetas al fichero para que SeLinux perdita su uso compartido.

```shell
~]# mkdir -p /var/srv-data/nextcloud/{nextcloud-app,nextcloud-data,nextcloud-db}
```

```shell
~]# podman pod create --name nextcloud-pod -p 4080:80 --network nextcloud-net

~]# podman container run -d --pod nextcloud-pod --name nextcloud_db -e POSTGRES_USER="nextcloud" --volume /var/srv-data/nextcloud/nextcloud-db:/var/lib/postgresql/data:z -e POSTGRES_PASSWORD="db_pass" postgres

~]# podman container run -d --pod nextcloud-pod --name nextcloud_redis redis

~]# podman container run -d --pod nextcloud-pod --name nextcloud_app -e POSTGRES_HOST="localhost" -e POSTGRES_DB="nextcloud" -e POSTGRES_USER="nextcloud" -e POSTGRES_PASSWORD="db_pass" -e NEXTCLOUD_ADMIN_USER="nextcloud" -e NEXTCLOUD_ADMIN_PASSWORD="nc_pass" -e NEXTCLOUD_TRUSTED_DOMAINS="udit76.iaa.es" -e REDIS_HOST="localhost" --volume /var/srv-data/nextcloud/nextcloud-app:/var/www/html:z --volume /var/srv-data/nextcloud/nextcloud-data:/var/www/html/data:z nextcloud

```

Notas: podman exec --user www-data Nextcloud php occ config:system:set trusted_domains 2 --value=cloud

# Referencias

[Installing Nextcloud 20 on Fedora Linux with Podman](https://fedoramagazine.org/nextcloud-20-on-fedora-linux-with-podman/)
[Déployer un POD sur podman](https://ios.dz/deployer-un-pod-sur-podman/)