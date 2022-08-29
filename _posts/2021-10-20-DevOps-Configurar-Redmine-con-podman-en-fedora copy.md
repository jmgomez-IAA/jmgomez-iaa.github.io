---
title: "DevOps Configurar Redmine con podman en fedora"
date: 2022-09-01
categories:
  - blog
tags:
  - nodered
  - mosquitto
  - iot
  - podman
  - devops
---

Nodered es una aplicacion web para manejo de proyectos.

# Descripcion


# Procedimiento

Creamos nuestra red y nuestro pod.
```shell
~]# podman network create labiote-net
~]# podman pod create -p 3780:1880,3783:1883 --net labiote-net --name labiote-pod
```

Creamos las carpetas para los volumnes. En este caso solo la base de datos necesita almacenamiento en disco.
```shell
~]# mkdir -p /var/srv-data/labiote/nodered/
~]# chown 1000:1000 /var/srv-data/labiote/nodered
~]# mkdir -p /var/srv-data/labiote/mosquitto/{config,data,log}
```

Node-Red
```shell
~]# podman run -it --pod labiote-pod  -v /var/srv-data/labiote/nodered:/data:z --name mynodered nodered/node-red

Mosquitto
~]# podman run -it --pod labiote-pod --name mosquitto -v /var/srv-data/labiote/mosquitto/:/mosquitto:z eclipse-mosquitto
```

Redmine
```shell
~]# podman logs -l
```

Una vez instalado, la probamos que todo esta correcto:
http://udit76.iaa.es:3780




# Referencias
