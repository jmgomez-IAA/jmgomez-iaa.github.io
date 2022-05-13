---
title: "DevOps Configurar Redmine con podman en fedora"
date: 2021-10-20
categories:
  - blog
tags:
  - redmine
  - podman
  - devops
---

Redmine es una aplicacion web para manejo de proyectos.

# Descripcion


# Procedimiento

Creamos nuestra red y nuestro pod.
```shell
~]# podman network create redmine-net
~]# podman pod create -p 3180:3000 -p 3122:22 --net redmine-net  --name redmine-pod
```

Creamos las carpetas para los volumnes. En este caso solo la base de datos necesita almacenamiento en disco.
```shell
~]# mkdir -p /var/srv-data/redmine/{web-data,postgresql/data}
```

Postgresql
```shell
~]# podman run -d -e POSTGRES_PASSWORD=xxx -e POSTGRES_DB=redmine -e POSTGRES_USER=redmine -v /var/srv-data/redmine/postgresql/data:/var/lib/postgresql/data:z  --pod redmine-pod postgres:latest

Redmine
~]# podman run -d --pod redmine-pod -e REDMINE_DB_POSTGRES=localhost -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=db_pass -v /var/srv-data/redmine/web-data:/usr/src/redmine/files:z redmine
```

Redmine
```shell
~]# podman logs -l
```

Una vez instalado, la probamos que todo esta correcto:
http://udit76.iaa.es:3180
Redmine nos logeamos como administradores, por defecto trae preconfigurado el usuario __admin__ con password __admin__, y cuando entremos nos pedira que re-establezcamos la contraseña.

# Referencias
