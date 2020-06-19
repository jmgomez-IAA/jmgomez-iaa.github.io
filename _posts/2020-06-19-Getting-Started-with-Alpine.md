---
title: "Getting Started with Alpine"
date: 2020-06-19
categories:
  - blog
tags:
  - docker
  - tools
  - sysadmin
---
# Alpine Linux

Alpine es una distribución ligera de Linux basada en musl libc y busybox. Hay una imagen docker basada en Alpine de unos 5mb de tamaño.

# Getting Started
Obtener la imagen de Alpine de dockerhub.
```
$ docker pull alpine
```

Comprobamos la direccion ip de nuestro contenedor
```
$ docker run alpine ifconfig
```

Finalmente, ejecutamos una shell en el contanedor usando nuestro comando habitual
```
$ docker run -i -t alpine /bin/bash
```
Este comando falla, ya que bash no esta soportado en alpine.

> exec: "/bin/bash": stat /bin/bash: no such file or directory
> docker: Error response from daemon: Container command not found or does not exist..

Ahora si, ejecutamos una shell sh y entramos en nuestro contenedor
```
$ docker run -it alpine /bin/sh
/ #
```

Salir (deattach) del contenedor sin pararlo **Ctrl-P Ctrl-Q**

Finalmente, comprobamos el estado del contenedor y verficamos que se esta ejecutando en segundo plano.
```
$ docker ps -a
```

| CONTAINER ID | IMAGE  | COMMAND  | CREATED |
| ------------ | ------ | -------  | ------- |
| 8647ce2b84a5  | alpine  |"/bin/sh"  | About a minute  |

