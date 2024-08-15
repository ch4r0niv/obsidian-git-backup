---
sticker: emoji//1f3a9
---
# Basic Bash Concepts


### Enviroment Variables

#### Lista corta de algunas variables de entorno disponibles por defecto.

![![Black Hat Bash/#^Table1]]

Full list of enviroment variables:
<https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html>


### The Shebang Line

Cada script debería comenzar con la línea shebang.

```
#!/bin/bash
```

El intérprete de bash suele estar ubicado en **/bin/bash**. Si en su lugar escribieras scripts en Python o Ruby, tu línea shebang incluiría la ruta completa al intérprete de Python o Ruby.

Algunas veces encontraras scripts rn bash que usan un shebang como el siguiente:

```
#!/usr/bin/env bash
```

Este shebang es mas portable que el primero. Algunas distribuciones de linux colocan el interprete de bash en diferentes locaciones del sistema, y este shebang intentara encontrar esa locación. Esto puede ser muy util en un **Pentesting**.

La linea del shebang tambien puede tomar de forma opcional argumentos para cambiar como el script se ejecuta. Por ejemplo: le puedes pasar el argumento especial **-x** a la linea del shebang:

```
#!/bin/bash -x
```

Esta opcion mostrara todos los comandos y sus argumentos al momento de ser ejecutados en la terminal. Es muy util para hacer debugging.

Otro ejemplo de un argumento opcional es **-r**

```
#!/bin/bash -r
```

Este argumento opcional crea una **restricted bash shell** , por lo que restringe ciertos comandos potencialmente peligrosos que podrian, por ejemplo, navegar a ciertos directorios, cambiar variables de entorno sensibles, o intentar desactivar la restricted bash shell del script.

Especificar un argumento en la linea del shebang requiere modificar el script, pero tambien puedes pasarle argumentos al interprete de bash:

```
bash -r myscript.sh
```

### Debugging

Este parametro lee los comandos en el script pero no los ejecuta, si hay un error de sintaxis, se mostrara.

```
bash -n script.sh
```

Tambien puedes usar **x** para activar el modo verbose.

```
bash -x script.sh
```

Si quieres empezar a debugear en cierto punto en el script, puedes hacer esto incluyendo el comando **set** dentro del script.

```
#!/usr/bin/env bash
set -x

-- snip --

set +x
```

--------------------------------------------------------------------------
### Nota

> Cuando un comando es ejecutado, bash espera hasta que termine antes de avanzar a la siguiente linea.

--------------------------------------------------------------------------

### Variables

Cada lenguaje de scripting tienes variables. **Variables** son nombres que asignamos a un espacio en memoria para guardar algún valor. Podemos directamente asignar valores a variables o ejecutar comandos en bash y guardar su salida como variable para usar para distintos propositos.

--------------------------------------------------------------------------

### Asignar y Acceder a Variables

```
$ book="Black Hat Bash"
$ echo "This book's name is ${book}"
This book's name is Black Hat Bash
```

Tambien puedes asignar la salida de un comando a una variable usando command substitution syntax $().

```
$ root_directory=$(ls -ld /)
$ echo "root_directory"

drwxr-xr-x 1 user user 0 Feb 13 20:12 /
```

### Unassigning Variables

Tambien puedes desasignar variables usando el comando **unset**:

```
$ book="Black Hat Bash"
$ unset book
$ echo "${book}"
```

si ejecutas estos comandos en la terminal, no se mostrara ninguna salida despues de ejecutarse el comando echo.

--------------------------------------------------------------------------
### Scoping Variables

Variables **Globales** son esas que estan disponibles en el programa entero. Pero la variables en bash tambien pueden estar **scoped** asi pueden solamente ser accesibles desde un ciero bloque de codigo. Esas variables son llamadas **local** variables y se declaran usando la palabra **local**.

```
#!/usr/bin/env bash

PUBLISHER="No Scratch Press"

print_name(){
	local name
	name="Black Hat Bash"
	echo "${name} by ${PUBLISHER}"
}

print_name()

echo "The variable ${name} will not be printed because it is a local variable"
```

--------------------------------------------------------------------------

### Streams

**Streams** son archivos que actuan como canal de comunicación entre un programa y su entorno.
![![Black Hat Bash/#^Table2]]
-------------------------------------------------------------------------------------------------------------

### Control Operators
![![Black Hat Bash/#^Table3]]-------------------------------------------------------------------------------------------------------------

### Redirection Operators
![![Black Hat Bash/#^Table4]]-------------------------------------------------------------------------------------------------------------

# Advanced Bash Concepts

### Test Operators

Hay multiples tipos de test operators. **File test operators** nos permiten iniciar una prueba hacia un archivo on the filesystem. podemos checar si un archivo es ejecutable o si un directorio existe.

**File Test Operators**
![![Black Hat Bash/#^Table5]]Full list: https://ss64.com/bash/test.html

**String Comparison Operators**
![![Black Hat Bash/#^Table6]]
**Integer Comparison Operators**
![![Black Hat Bash/#^Table7]]
































