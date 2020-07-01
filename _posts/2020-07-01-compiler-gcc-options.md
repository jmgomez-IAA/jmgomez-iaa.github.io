---
title: "Opciones de Compilacion habituales"
date: 2020-07-01
categories:
  - blog
tags:
  - gcc
  - sw
---
# GCC

GCC Compiler es un compilador de C/C++ muy potente y popular para varias distribuciones de Linux. Este artículo explica algunas de las opciones populares del compilador GCC.


# Ejemplo de código C

El siguiente código básico de C (main.c) se utilizará en este artículo:
```C
#include <stdio.h>

int main (nulo)
{
   printf ("\ n Hello world. \ n");
   return 0;
}
```

# Opciones del compilador GCC

## Especifique el nombre ejecutable de salida

En su forma más básica, ```gcc main.c``` ejecuta el proceso de compilación completo y genera un ejecutable con el nombre a.out.

La opción -o especifica el nombre del archivo de salida para el ejecutable.
```
gcc main.c -o main
```

## Habilite todas las advertencias 
La opción -Wall

Esta opción habilita todas las advertencias en GCC.

```C
#include <stdio.h>

int main (null)
{
   int i;
   printf ("\n Hello world [% d] \ n", i);
   return 0;
}

Si se compila el código anterior, se genera la siguiente advertencia relacionada con la variable no inicializada i:
```
$ gcc -Wall main.c -o main
main.c: En función 'main':
main.c: 6: 10: advertencia: "i" se utiliza sin inicializar en esta función [-Wuninitialized]
```

## Produzca solo la salida del preprocesador con la opción -E

La salida de la etapa de preprocesamiento se puede producir utilizando la opción -E.
```
$ gcc -E main.c> main.i
```
El comando gcc produce la salida en stdout para que pueda redirigir la salida en cualquier archivo. En nuestro caso (arriba), el archivo main.i contendría la salida preprocesada.
## Produzca solo el código de ensamblaje usando la opción -S

La salida de nivel de ensamblador se puede generar utilizando la opción -S.
```
gcc -S main.c> main.s
```

## Produzca solo el código compilado usando la opción -C

Para producir solo el código compilado (sin ningún enlace), use la opción -C.
```
gcc -C main.c
```

## Produzca todos los archivos intermedios usando la función -save-temps

La opción -save-temps puede hacer todo el trabajo realizado en los ejemplos 4,5 y 6 anteriores. A través de esta opción, la salida en todas las etapas de compilación se almacena en el directorio actual. Tenga en cuenta que esta opción también produce el ejecutable.

Por ejemplo :
```
$ gcc -save-temps main.c

$ ls
a.out main.c main.i main.o main.s
```

Entonces vemos que todos los archivos intermedios, así como el ejecutable final, se produjeron en la salida.

## Enlace con bibliotecas compartidas usando la opción -l

La opción -l se puede usar para vincular con bibliotecas compartidas. Por ejemplo:
```
gcc -Wall main.c -o main -lCPPfile
```

## Crear código independiente de posición usando la opción -fPIC

Al crear las bibliotecas compartidas, se debe generar un código independiente de la posición. Esto ayuda a que la biblioteca compartida se cargue como cualquier dirección en lugar de una dirección fija. Para esto se usa la opción -fPIC.

Por ejemplo, los siguientes comandos crean una biblioteca compartida libCfile.so a partir del archivo fuente Cfile.c:
```
$ gcc -c -Wall -Werror -fPIC Cfile.c
$ gcc -shared -o libCfile.so Cfile.o
```

## Imprima todos los comandos ejecutados usando la opción -v

La opción -v se puede usar para proporcionar información detallada sobre todos los pasos que realiza gcc al compilar un archivo fuente.

Por ejemplo :
```
$ gcc -Wall -v main.c -o main
Usando especificaciones incorporadas.
COLLECT_GCC = gcc
COLLECT_LTO_WRAPPER = / usr / lib / gcc / i686-linux-gnu / 4.6 / lto-wrapper
Objetivo: i686-linux-gnu
Configurado con: ../src/configure -v --with-pkgversion = 'Ubuntu / Linaro 4.6.3-1ubuntu5' --with-bugurl = file: ///usr/share/doc/gcc-4.6/README. Errores --enable-languages ​​= c, c ++, fortran, objc, obj-c ++ --prefix = / usr --program-suffix = -4.6 --enable-shared --enable-linker-build-id --with- system-zlib --libexecdir = / usr / lib --with-included-gettext --enable-threads = posix --with-gxx-include-dir = / usr / include / c ++ / 4.6 --libdir = / usr / lib --enable-nls --with-sysroot = / --enable-clocale = gnu --enable-libstdcxx-debug --enable-libstdcxx-time = yes --enable-gnu-unique-object --enable-plugin --enable-objc-gc --enable-objetivos = todos --disable-werror --with-arch-32 = i686 --with-tune = genérico --enable-Check = lanzamiento --build = i686-linux- gnu --host = i686-linux-gnu --target = i686-linux-gnu
Modelo de hebra: posix
gcc versión 4.6.3

```

## Macros de tiempo de compilación usando la opción -D

La opción del compilador D se puede usar para definir macros de tiempo de compilación en el código.

Aquí hay un ejemplo :
```C
#include <stdio.h>

int main (nulo)
{
#ifdef MY_MACRO
  printf ("\ n Macro definida \ n");
#terminara si
  char c = -10;
  // Imprime la cadena
   printf ("\ n Las cosas geek [% d] \ n", c);
   return 0;
}
```
La opción del compilador -D se puede utilizar para definir la macro MY_MACRO desde la línea de comandos.
```
$ gcc -Wall -DMY_MACRO main.c -o main
$ ./main
```
 Macro definida

 Las cosas geek [-10]


## Convierta las advertencias en errores con la opción -Werror

A través de esta opción, cualquier advertencia de que gcc podría informar se convierte en error.

Aquí hay un ejemplo :
```C
#include <stdio.h>

int main (nulo)
{
  char c;
  // Imprime la cadena
   printf ("\ n Las cosas geek [% d] \ n", c);
   devuelve 0;
}
```
La compilación del código anterior debería generar advertencias relacionadas con la variable indefinida c y esto debería convertirse en error mediante la opción -Werror.
```
$ gcc -Wall -Werror main.c -o main
main.c: En función 'main':
main.c: 7: 10: error: 'c' se usa sin inicializar en esta función [-Werror = sin inicializar]
cc1: todas las advertencias se tratan como errores
```

## Opciones de gcc a través de un archivo usando la opción @

Las opciones para gcc también se pueden proporcionar a través de un archivo. Esto se puede hacer usando la opción @ seguida del nombre del archivo que contiene las opciones. Más de una opción está separada por un espacio en blanco.

Aquí hay un ejemplo :
```
$ cat opt_file
-Wall -omain
```
Opt_file contiene las opciones.

Ahora compile el código proporcionando opt_file junto con la opción @.
```
$ gcc main.c @opt_file
main.c: En la función "main":
main.c: 6: 11: advertencia: "i" se utiliza sin inicializar en esta función [-Wuninitialized]

$ ls main
main
```

