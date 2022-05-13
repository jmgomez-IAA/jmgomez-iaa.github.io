---
title: "DevOps Configurar Gitea con podman en fedora"
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

El acceso a Gitea se realiza a través de dos puertos expuestos en el contenedor: el puerto 22, para el acceso vía SSH y el 80, donde se publica el servidor web. En nuestro pod vamos a mapear los puertos 22 al 3022 y 3000 al 3080 que son puertos libres y disponibles en el host.

## Puertos y red
- 3180 : Puerto conexion HTTP a GITEA. 
- 3122: Puerto conexion por SSH al servidor openSSH del contenedor GITEA
- 3187 Puerto de conexion para el postgresql exporter.

```shell
~]# podman  network create gitea-net
~]# podman pod create -p 3080:3000 -p 3022:22 -p 3087:9187 --net gitea-net  --name gitea-pod
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
                        "HostPort": "3022"
                    }
                ],
                "3000/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "3080"
                    }
                ],
                "9187/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "3087"
                    }
                ]
            },

Como vemos en la informacion de nuestro pod, podman ha consigurado en el contenedor infra el mapeo de puertos:
- 3022 -->   22
- 3080 --> 3000
- 9187 --> 3087

## Volumenes
Necesitamos almacenar los datos de gitea en nuestro sistema host, por lo tanto vamos a crear varios volumenes:
- **/var/srv-data/gitea/postgresql/data** Postgres Database data
- **/var/srv-data/gitea/web-data** Gitea data
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
~]# podman run -d -v /var/srv-data/gitea/web-data:/data:z -v /etc/localtime:/etc/localtime:ro --env GITEA__database__DB_TYPE=postgres --env GITEA__database__HOST=localhost:5432 --env GITEA__database__NAME=gitea --env GITEA__database__USER=gitea --env GITEA__database__PASSWD=XXXXX --pod gitea-pod docker.io/gitea/gitea
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
Generating /data/ssh/ssh_host_ed25519_key...
Generating /data/ssh/ssh_host_rsa_key...
Generating /data/ssh/ssh_host_dsa_key...
2021/11/08 09:27:57 cmd/web.go:102:runWeb() [I] Starting Gitea on PID: 12
2021/11/08 09:27:57 ...s/install/setting.go:21:PreloadSettings() [I] AppPath: /app/gitea/gitea
2021/11/08 09:27:57 ...s/install/setting.go:22:PreloadSettings() [I] AppWorkPath: /app/gitea
2021/11/08 09:27:57 ...s/install/setting.go:23:PreloadSettings() [I] Custom path: /data/gitea
2021/11/08 09:27:57 ...s/install/setting.go:24:PreloadSettings() [I] Log path: /data/gitea/log
2021/11/08 09:27:57 ...s/install/setting.go:25:PreloadSettings() [I] Preparing to run install page
Generating /data/ssh/ssh_host_ecdsa_key...
Server listening on :: port 22.
Server listening on 0.0.0.0 port 22.
2021/11/08 09:27:57 ...s/install/setting.go:28:PreloadSettings() [I] SQLite3 Supported
2021/11/08 09:27:57 cmd/web.go:196:listen() [I] Listen: http://0.0.0.0:3000
2021/11/08 09:27:57 ...s/graceful/server.go:62:NewServer() [I] Starting new Web server: tcp:0.0.0.0:3000 on PID: 12
```

La instalación ha sido un exito, vamos a crear un punto de recuperación generando un kube de forma que podamos levantar el servidor:
```shell
~]# podman ps -a
```

# Configuracion y Test
 
Si todo ha funcionado correctamente, y nuestro contenedor esta funcioando, ya podemos abrir nuestra aplicacion:

http://udit76.iaa.es:3080/

Debemos de configurar algunas opciones:

1.- URL BASE de GITEA: http://udit76.iaa.es:3080/

2.- Cuenta administador: Es buena practica crear el usuario administrador y su password. Hay que tener en cuenta que el email del administrador no puede ser reutilizado por otro usuario.
        - admin: jmgomez
        - pass: xx
        - jmgomez@iaa.es

3.- Servidor de correo smtp:
    Servidor smtp: smtp.iaa.csic.es:465
    Enviar Correos como: "andromeda" <jmgomez@iaa.es>

    - Configuracion del servidor y de servicios de terceros:
        - Habilitar autentificacion Local
        - Deshabilitar auto-registro.
        - Requerir inicio de sesion para ver paginas.

Si queremos cambiar algo de la configuración de gitea, se hace definiendo variables en el fichero de configuracion __ /var/srv-data/gitea/data/gitea/conf/app.ini __. El uso de estas variables viene documentado en [https://docs.gitea.io/en-us/config-cheat-sheet/]([https://docs.gitea.io/en-us/config-cheat-sheet/).

a) Para poder permitir, que los repositorios se puedan copiar usando ssh tenemos que editar el archivo de configuracion app.ini.
    - DOMAIN   = udit76.iaa.es
    - SSH_DOMAIN = udit76.iaa.es
    - SSH_PORT = 3022

b) En nuestro repositorio debemos copiar la clave publica en Configuracion/Claves de Implementación.

# Mejoras
Realmente cabe la posibilidad de lanzar los contenedores desde systemd. Esto nos permite automatizar el proceso:
[Podman - Setup Gitea](https://blog.while-true-do.io/podman-setup-gitea/)

Algunos de los repositorios, estarán ligados a webhooks como por ejemplo Jekyll Pages:
[Static websites automatic deployment with Gitea, an example with Jekyll](https://blog.samuel.domains/blog/tutorials/static-websites-automatic-deployment-with-gitea-an-example-with-jekyll)

# Referencias

[GITEA Installation with Docker](https://docs.gitea.io/en-us/install-with-docker/)
[Install a self-hosted Git server with Gitea](https://golb.hplar.ch/2018/06/self-hosted-git-server.html)
[Dockerless, part 3: Moving development environment to containers with Podman](https://mkdev.me/en/posts/dockerless-part-3-moving-development-environment-to-containers-with-podman)

[Podman - Volumes 1/2](https://blog.while-true-do.io/podman-volumes-1/)

[Podman - Networking 3/3](https://blog.while-true-do.io/podman-networking-3/)

[Spinning up and Managing Pods with multiple containers with Podman](https://mohitgoyal.co/2021/04/23/spinning-up-and-managing-pods-with-multiple-containers-with-podman/)

[User IDs and (rootless) containers with Podman](https://blog.christophersmart.com/2021/01/26/user-ids-and-rootless-containers-with-podman/)

