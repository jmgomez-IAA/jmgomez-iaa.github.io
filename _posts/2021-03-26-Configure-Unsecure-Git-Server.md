---
title: "Configurar un servidor GIT"
date: 2021-03-26
categories:
  - blog
tags:
  - tools
  - admin
---

El paquete git nos permite configurar un servidor git en nuestro PC. Aunque existen alternativas, nuestro servidor sera inseguro, todos lo usuarios con permiso de acceso podras hacer push y clone del repositorio.

La seguridad, o mas bien, el control de accesos se realiza empleando la clave publica de los usuarios y el authorized_keys del usuario git en el servidor.

# Descripcion

Proceso de instalacion y configuraion de un servidor git en Archlinux.

# Procedimiento
El primer paso es instalar la herramienta __git__. 
```shell
~]# pacman -S git
```

Segun las instruccion, para poder hacer push al repositorio debemos modificar 
 nano  /usr/lib/systemd/system/git-daemon\@.service
Añadir el parametro **–enable=receive-pack** a la linea __ExecStart__

Una vez modificado el fichero de configuraion lanzamos el servior en systemd.
```shell
systemctl start git-daemon.socket
```

O bien, si queremos que se active automaticamente tras reinciar:
```shell
systemctl enable git-daemon.socket
```

## Control de accesos basado en SSH
Realmente este control de accesos permitira a todos los usuario hacer push. Todo ellos se logean en el servidor git, como el usuario git empleando sus rsa.pub.

Durante la instalacion pacman crea el usuario git. Pero lo crea sin directorio home. Tal vez seria mas limpio, primero crear el usuario con 
```shell
~]# useradd -r -m -U -d /srv/git -s /bin/bash git
```
Pero en caso, de que el usuario ya exista podemos crear su directorio home empleando el paquete **mkhomedir_helper**
```shell
~]# mkhomedir_helper git
```

La cuenta git, por defecto se crea expirada, si queremos solucionarlo: `~]# chage -E -1 git`.

Ahora que git, ya dispone de un directorio home, crearemos .ssh para almacenar las credenciales de acceso:
```shell
~]# passwd git
~]# mkdir -p ~/.ssh && chmod 0700 ~./ssh
~]# chown git:git .ssh
~]# touch .ssh/authorized_keys
~]# chown git:git .ssh/authorized_keys
~]# chmod 0600 .ssh/authorized_keys
```

Para poder logearnos empleando nuestra clave privada debemos actulizarel __home_dir__ del usuario git en __/etc/passwd__
Cambiar esto:
**git:x:975:975:git daemon user:/:/usr/bin/git-shell**
por esto
**git:x:975:975:git daemon user:/srv/git:/bin/bash**

Finalmente, para poder connectarnos al servidor, debemos copiar la clave publica de los usuario en el fichero authorized_keys. 
Las claves publicas se encuentran en ~/.ssh/id_rsa.pub, en caso de que no exista ninguna la podemos crear empleando:
```shell
~]$ ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
```

Este metodo requiere cambiar la shell en passwd a __/bin/bash__ dejando de lado la shell restrictiva original **/usr/bin/git-shell**
```shell
~]$ cat ~/.ssh/id_rsa.pub | ssh git@remote-server "sudo mkdir -p ~/.ssh && sudo cat >>  ~/.ssh/authorized_keys"
```
Una vez copiado, lo mas correcto seria devolver la shell de git a la original  **/usr/bin/git-shell** en /etc/passwd.
**git:x:975:975:git daemon user:/srv/git:/usr/bin/git-shell**

En caso, de no haber cambiado la shell  siempre podemos hacer un cortar pegar 
```shell
~]$ cat ~/.ssh/id_rsa.pub | xclip -target clipboard
```

# Crear un repositorio
Para crear un repositorio, desde el lado del servidor debemos crear una carpeta e inicializar el repositorio git en ella.

```shell
  ~]$ cd /srv/git
  ~]$ sudo -u git git --bare init test-project.git
```			 
Ahora en el PC de los clientes:
```shell
  ~]$ mkdir test-project
  ~]$ git init
  ~]$ touch README.md
  ~]$ git add .
  ~]$ git commit -am "Initial commit"
  ~]$ git remote add origin git@udit76.iaa.es:/srv/git/test-project.git
  ~]$ git push origin master
  ~]$ git status
```

Listo!!

# Conclusiones

Este repositorio, aunque no se puede identificar a los usuarios que tienen acceso, esta protegido gracias a ssh.
Todos acceden al repositorio como el usuario git, y por lo tanto, todos pueden hacer push en todos los repositorios.


# Referencias
[ArchLinux Wiki](https://wiki.archlinux.org/index.php/Git_server)
[Step-by-Step tutorial](https://miracoin.wordpress.com/2014/11/25/step-by-step-guide-on-setting-up-git-server-in-arch-linux-pushable/)
[Git-SCM book](https://git-scm.com/book/es/v2/Git-en-el-Servidor-Configurando-el-servidor)
[OSTechNix how-to articles](https://ostechnix.com/category/linux_distributions/arch-linux/)


