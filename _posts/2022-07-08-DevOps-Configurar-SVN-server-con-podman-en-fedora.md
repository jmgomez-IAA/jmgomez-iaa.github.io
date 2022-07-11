---
title: "DevOps Configurar SVN server con podman en fedora"
date: 2022-07-08
categories:
  - blog
tags:
  - svn
  - podman
  - devops
---

SVN es un control de versiones para ficheros binarios.

# Descripcion

En este caso vamos a emplear una imagen de docker.io elleflorio/svn-server sin esforzarnos mucho.

# Procedimiento

Creamos las carpetas para los volumnes. En este caso solo la base de datos necesita almacenamiento en disco. 
```shell
~]# mkdir -p /var/srv-data/svn-server/svn-root
```

Descargamos la imagen de docker.io y ejectuamos nuestro contenedor.
```shell
~]# podman run -dit --name svn-server -v /var/srv-data/svn-server/svn-root:/home/svn:z -p 3680:80 -p 3690:3690 -w /home/svn elleflorio/svn-server
# podman ps -a

957e3324ec83  docker.io/elleflorio/svn-server:latest                        About an hour ago  Up About an hour ago  0.0.0.0:3680->80/tcp, 0.0.0.0:3690->3690/tcp                          svn-server
```

Para poder probar nuestro servicio necesitamos un usuario y un password. 
```shell
~]# docker exec -t svn-server htpasswd -b /etc/subversion/passwd svnadmin password
```
Ya podemos comprobar que funciona correctamente accediendo al la direccion http://localhost/svn.

# Manejo de Repositorios
Ahora nuestro contenedor esta creado y funcionando, necesitamos usuarios, contraseña, repositorios, etc...

## Repositorios
Crear un repositorio
```shell
~]# podman exec -it svn-server svnadmin create Test
```

Vamos a crear unos usuarios que puedan acceder usando el protocolo custom (svn) y tengan permisos de escritura para poder hacer modificaciones en el repositorio.

En el repositorio, existe una carpeta /home/svn/Test/conf, donde se almacenan la configuracion de acceso. El fichero **passwd** y el fichero svnserve.conf almacenan la contraseña y los permisos de acceso respectivamente.

## Usuarios
Comenzamos añadiendo una nueva entrada en passwd con nuestro usuario y password, siguiendo el formato <username>=<password>.

```shell
~]# podman exec -it svn-server sh -c "cat /home/svn/Test/conf/passwd"
### This file is an example password file for svnserve.
### Its format is similar to that of svnserve.conf. As shown in the
### example below it contains one section labelled [users].
### The name and password for each user follow, one account per line.

[users]
# harry = harryssecret
# sally = sallyssecret

~]# podman exec -it svn-server sh -c "cat >> /home/svn/Test/conf/passwd << EOF 
jmgomez = testpass
EOF
"

~]# podman exec -it svn-server sh -c "cat /home/svn/Test/conf/passwd"
### This file is an example password file for svnserve.
### Its format is similar to that of svnserve.conf. As shown in the
### example below it contains one section labelled [users].
### The name and password for each user follow, one account per line.

[users]
# harry = harryssecret
# sally = sallyssecret
jmgomez = testpass
```

Ya tenemos un usuario para este repositorio, ahora debemos indicar al servirdor SVN que fichero usar para almancenar las contraseñas de los usuarios e indicarle que permisos queremos otorgar a los usuarios autorizados. Para ello modificamos el fichero svnserve.conf, quitandole los comentarios (y los espacios que pueda haber al principio) a las lineas:

### some configuration lines...###
auth-access=write
### ... ###
password-db=passwd
### the rest of the file... ###

```shell
~]# podman exec -it svn-server sh -c "sed -i '/# auth-access = write/s/^#*\s*//g' /home/svn/Test/conf/svnserve.conf"
~]# podman exec -it svn-server sh -c "sed -i '/# password-db = passwd/s/^#*\s*//g' /home/svn/Test/conf/svnserve.conf"
```

 
## Importar un dump de un repositorio
```shell
~]# podman exec -t svn-server svnadmin load /home/svn/Test < /home/svn/repo-saved.dump
```
# Conclusiones
Ahora debemos añadir el servidor svn a nuestra politica de backups, pero estamos listos para trabajar con el.

# Referencias

[The SVN Dockerization](https://medium.com/@elle.florio/the-svn-dockerization-84032e11d88d)