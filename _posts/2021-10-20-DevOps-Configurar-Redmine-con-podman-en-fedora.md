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
pms Project Management Software
```shell
~]# podman network create redmine-net
~]# podman pod create -p 3180:3000 -p 3122:22 --net redmine-net  --name pms
```

Creamos las carpetas para los volumnes. En este caso solo la base de datos necesita almacenamiento en disco.
```shell
~]# mkdir -p /var/srv-data/redmine/{web-data,postgresql/data}
```

Postgresql
```shell
~]# podman run -d -e POSTGRES_PASSWORD=sirio -e POSTGRES_DB=redmine -e POSTGRES_USER=redmine -v /var/srv-data/redmine/postgresql/data:/var/lib/postgresql/data:z  --pod pms postgres
```shell
```


Redmine
```shell
~]# podman run -d --pod redmine-pod -e REDMINE_DB_POSTGRES=localhost -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=sirio -v /var/srv-data/redmine/web-data:/usr/src/redmine/files:z redmine
```
En nuestro caso, vamos a recuperar los datos de una instalación anterior. Lanzamos nuestro contenedor, pero queremos que redmine no haga el migrate al arranca, de forma que no rellene con datos la base de datos. Para ellos debemos enviar un comando distinto de vacio (**/bin/bash**) cuando lanzamos nuestro contenedor.

```shell
~]# podman run -it --pod pms -e REDMINE_DB_POSTGRES=localhost -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=sirio -v /var/srv-data/redmine/web-data:/usr/src/redmine/files:z redmine /bin/bash
exit
```

Copiamos nuestro dump de la base de datos al contenedor:
```shell
~]# podman cp redmine_carmencita_postgre.dump bdcfd7879148:/tmp/
```

Ejecutamos un Bash en modo interactivo en nuestro contenedor, y comprobamos que la base de datos existe. Despues restaramos la copia de seguridad que teniamos y salimos.
```shell
~]# podman exec -it bdcfd7879148 /bin/bash
root@pms:/#  psql -U redmine
psql (14.1 (Debian 14.1-1.pgdg110+1))
Type "help" for help.

redmine=# \l
                               List of databases
   Name    |  Owner  | Encoding |  Collate   |   Ctype    |  Access privileges
-----------+---------+----------+------------+------------+---------------------
 postgres  | redmine | UTF8     | en_US.utf8 | en_US.utf8 |
 redmine   | redmine | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | redmine | UTF8     | en_US.utf8 | en_US.utf8 | =c/redmine         +
           |         |          |            |            | redmine=CTc/redmine
 template1 | redmine | UTF8     | en_US.utf8 | en_US.utf8 | =c/redmine         +
           |         |          |            |            | redmine=CTc/redmine
(4 rows)

redmine=# exit
~]# root@pms:/# psql -U redmine redmine < /tmp/redmine_carmencita_postgre.dump
```
Borramos el contenedor de Redmine que empleamos para crear la base de datos y volvemos a lanzar un nuevo redmine.

```shell
~]# podman container rm 329d17500fec
~]# podman run -d --pod pms -e REDMINE_DB_POSTGRES=localhost -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=sirio -v /var/srv-data/redmine/web-data:/usr/src/redmine/files:z redmine 
~]# podman logs -l
The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x64-mingw32, x86-mswin32. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x64-mingw32 x86-mswin32`.
The dependency ffi (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x64-mingw32, x86-mswin32. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x64-mingw32 x86-mswin32`.
The Gemfile's dependencies are satisfied
[2022-01-13 09:09:21] INFO  WEBrick 1.6.1
[2022-01-13 09:09:21] INFO  ruby 2.7.5 (2021-11-24) [x86_64-linux]
[2022-01-13 09:09:21] INFO  WEBrick::HTTPServer#start: pid=1 port=3000

```

Listo, lo probamos que todo esta correcto:
http://udit76.iaa.es:3180
Redmine nos logeamos como administradores, por defecto trae preconfigurado el usuario __admin__ con password __admin__, y cuando entremos nos pedira que re-establezcamos la contraseña.

# Conclusiones
Un poco chapuzas pero funciona. Tal vez sería mejor crear la base de datos y el usuario, inicializar Redmine del tiron.


# Referencias
https://github.com/sameersbn/docker-redmine#installation
