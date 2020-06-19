---
title: "Eliminar ficheros duplicados en Archlinux"
date: 2020-06-20
categories:
  - blog
tags:
  - i3wm
  - fdupes
  - sysadmin
---
# fdupes

Resulta imposible seguir el rastro a nuestros ficheros que estan repartidos en mil sitios; cloud, backups, pendrives. La herramienta **fdupes** permite identificar o eliminar archivos duplicados que residen dentro de directorios específicos.

fdupes utiliza md5sums en la búsqueda de duplicados. En el caso de que el resultado sea igual, entonces realiza una comparación byte a byte.

# Instalacion

```shell
~]# pacman -S fdupes
```

# Uso 

Para encontrar duplicados, solo tiene que:
```shell
~]$ fdupes /path_to_/directory
```

Este comando solo buscará en el directorio especificado como argumento e imprimirá la lista de archivos duplicados (si los hay).
Si busca también en subdirectorios, debe agregar la opción "-r" que significa "recursivamente".

¿Y si quieres ver el tamaño de los archivos? Por supuesto que puede:
```shell
~]$ fdupes -S / path / to / some / directory
```
Puede especificar más de un directorio:
```shell
~]$ fdupes /ruta_a_primer_directorio /ruta_a_segundo_ / _directorio
```
y así.

Luego, si desea eliminar todos los duplicados, simplemente:
```shell
~]$ fdupes -d / ruta / al directorio /
``` 
Esto preservará la primera aparicion del fichero y eliminará todo lo demás.

Finalmente, si queremos borrar todos los archivos repetidos, sin que nos pregunte uno a uno cual borrar.
```shell
~]$ fdupes -rd /ruta_dir1 /ruta_dir2
```


# Conclusion

fpudes de una herramienta extremadamente sencilla de utilizar, y que de una forma muy intuitiva, te va a ayudar a terminar con tu basura digital, o por lo menos a reducir tu basura digital, y eliminar duplicados en Linux.

Por supuesto, esta herramienta entra dentro de nuestras preferidas para crear scripts.

Como siempre gracias al auto [Adrian Lopez Roche](https://github.com/adrianlopezroche/fdupes).

