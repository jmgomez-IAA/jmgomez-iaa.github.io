---
title: "Opciones de Compilacion: Parametros por defecto"
date: 2020-07-02
categories:
  - blog
tags:
  - leon3
  - sw
  - grlib
---
# BCC

BCC es el compilador para arquitecturas __leon3__ de la __grlib__. 

Aunque pensemos que nuestra llamada al compilador es simple, realmente el compilador empleará un conjunto de parametros mucho mas extenso de lo que pensamos. Es mas tenemos que considerar nuestro compilador **bcc** como un conjunto de herramientas necesarias para realizar la compilacion.

# Codigo de ejemplo
Para nuestras pruebas vamos a emplear un Hello world!!.

```C
#include <stdio.h>

main()
{
	printf("Hello World\n");
}

```

# Mostrar pciones por defecto

Las opciones -Q -v nos permiten observar todos los parametros por defecto que emplea nuestro compilador para el objetivo que le hemos indicado:

 ```
 $ sparc-gaisler-elf-gcc -Q -v src/hello.c -o build/hello.elf 
 ```
Entre las opciones que vienen activas por defecto nos encontramos:  
 '-mcpu=v7'   
 Esta es la CPU por defecto,leon3 v7.  

 '-B' '/opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3'   
Esta es la direccion donde se encuentran las aplicaciones para poder compilar.  

#include <...> search starts here:    
 /opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/include  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/include  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/include-fixed  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/../../../../sparc-gaisler-elf/include  

Ahora algunos de los parametros de linkado para  
 /opt/bcc/bcc-2.1.1-gcc/bin/../libexec/gcc/sparc-gaisler-elf/7.2.0/collect2  
 -plugin /opt/bcc/bcc-2.1.1-gcc/bin/../libexec/gcc/sparc-gaisler-elf/7.2.0/liblto_plugin.so  
 -plugin-opt=/opt/bcc/bcc-2.1.1-gcc/bin/../libexec/gcc/sparc-gaisler-elf/7.2.0/lto-wrapper  
 -plugin-opt=-fresolution=/tmp/ccsU98JI.res -plugin-opt=-pass-through=-lgcc -plugin-opt=-pass-through=-lbcc   -plugin-opt=-pass-through=-latomic-plugin-opt=-pass-through=-lc   
 -plugin-opt=-pass-through=-lgcc   
 --sysroot=/opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf   
 -o build/hello.elf   
 /opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/trap_table_mvt.S.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/crt0.S.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crti.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtbegin.o   
 -L/opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3   
 -L/opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0   
 -L/opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc -L/opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/../../../../sparc-gaisler-elf/lib   
 -L/opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/lib   
 /tmp/ccxGMEKK.o   
 -T /opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/linkcmds   
 -lgcc   
 --start-group   
 -lbcc   
 -latomic   
 -lc   
 --end-group   
 -lgcc   
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtend.o   
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtn.o  

Entre los parametros, tenemos los mas habituales y conocidos por todos, como son -L y -l para añadir nuestras librerias. Mediante estas dos directivas, añadimos las librerias estandar que hemos empleando en nuestro programa: libc, libbcc, libatomic, libgcc....

El parametro -T se emplea para especificar nuestro linker script, donde se especifica los parametros necesario para el linkado, tamaño de la memoria, posiciones de la diferencites secciones, etc...

Observamos que el linker nos incluye por defecto un conjunto de ficheros objecto, que son además especificaos para nuestra arquitectura:  
/opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/trap_table_mvt.S.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../sparc-gaisler-elf/bsp/leon3/crt0.S.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crti.o  
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtbegin.o   
y adicionalmente añade   
/opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtend.o   
 /opt/bcc/bcc-2.1.1-gcc/bin/../lib/gcc/sparc-gaisler-elf/7.2.0/crtn.o  

Estos, ficheros incluyen por ejemplo, el proceso de puesta en marcha del procesador antes de la llamada a nuestra funciona main(), el vector de interrupciones, etc...

Finalmente, __--start-group__ -lbcc -latomic -lc __--end-group__, esta orden ayuda a resolver las dependencias circulares entre varias librerias. De esta forma, el orden en que incluyamos las librarias es indifierente. En otras palabras, en los archivos especificados entre  __--start-group__  y __--end-group__ se busca repetidamente hasta que no quede ningu referencia indefinida.  

Citing 
> Why does the order in which libraries are linked sometimes cause errors in GCC? or man ld http://linux.die.net/man/1/ld
>
>    -( archives -) or --start-group archives --end-group
>
>    The archives should be a list of archive files. They may be either explicit file names, or -l options.
>
>    The specified archives are searched repeatedly until no new undefined references are created. Normally, an archive is searched only once in the order that it is specified on the command line. If a symbol in that archive is needed to resolve an undefined symbol referred to by an object in an archive that appears later on the command line, the linker would not be able to resolve that reference. By grouping the archives, they all be searched repeatedly until all possible references are resolved.
>
>    Using this option has a significant performance cost. It is best to use it only when there are unavoidable circular references between two or more archives.

# Conclusion
BCC, o mas bien GCC, nos asiste en nuestras compilaciones con un conjunto de parametros y ficheros precompilados. Estos ficheros, enmascaran parte de la complejidad de la programación de la arquitectura, facilitando la viva a los llamados programadores de bajo nivel...

En conclusion podemos decir, que no existen programas simples, son simples gracias al trabajo de otros...

