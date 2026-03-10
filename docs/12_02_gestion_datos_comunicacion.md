---
title: Comando entrada salida
description: Scripts en Bash para la administración de linux
---


## `echo`, `read` y `printf`

En este apartado comentaremos detalladamente los comandos `echo`, `printf` y `read`, los cuales
nos permiten realizar las operaciones de entrada/salida que requiere un script.

### `echo`

Ya hemos visto que la orden `echo` es para mostrar mensajes por pantalla. 

El comando `echo` simplemente escribe en la salida estándar los argumentos que recibe. La siguiente tabla muestra las opciones que podemos pasar a echo.

| Opción | Descripción |
| :---: | --- |
| `-e` | Activa la interpretación de caracteres precedidos por el carácter de escape. |
| `-E` | Desactiva la interpretación de caracteres precedidos por el carácter de escape. Es la opción por defecto. |
| `-n` | Omite el carácter `\n` al final de la línea (es equivalente a la secuencia de escape `\c`).

Por ejemplo, la opción `-e` activa la interpretación de las secuencias de escape, que por defecto echo las ignora, es decir:

!!!example "Ejemplo de uso de "escapes" con `echo`"
    ```bash
    $ echo "Hola\nAdios"
    # Obtenemos una línea: Hola\nAdios

    $ echo -e "Hola\nAdios"
    # Obtenemos el texto en dos líneas: 
    # Hola
    # Adios
    ```

La siguiente tabla nos muestra las secuencias de escape que acepta `echo` (cuando se usa la opción `-e`). Para que estas tengan éxito deben encerrarse entre comillas simples o dobles, ya que sino el carácter de escape `\` es interpretado por el shell y no por `echo`.

| Escape | Descripción |
| :---: | --- |
| `\a` | Produce un sonido "poom" (alert) |
| `\b` | Borrar hacía atrás (backspace) |
| `\c` | Omite el `\n` al final (es equivalente a la opción `-n`). Debe colocarse al final de la línea |
| `\n` | Cambio de línea |
| `\r` | Retorno de carro |
| `\t` | Tabulador horizontal |
| `\v` | Tabulador vertical |


### `printf`

El comando `printf` en Linux, se usa para mostrar cadenas formateadas, ya sea por número o por cualquier otro formato en la ventana de nuestra terminal. Funciona de la misma manera, que `printf` en el lenguaje de programación `C`, que es su base. El comando `printf` no estaba disponible en las primeras shell, por lo que si estas en un sistema muy antiguo tal vez no tengas acceso. Deberíamos usar este comando antes que `echo` para generar las salidas formateadas.

La sintaxis de `printf` no es difícil, tan solo debes acostumbrarte a ella.

```bash
printf formato_de_cadena argumento1 argumento2 ...  argumenton
```

***Formato de cadena*** representa la cadena que se mostrará por pantalla. Puede contener formatos que serán substituidos por el valor de las expresiones citadas a su continuación. Tiene que haber tantos formatos como argumentos.


Existen varios indicadores de formato, algunos de los más utilizados son:

- `%s` : indicador de cadena para la salida.
- `%b` : Nos permite interpretar secuencias de escape con un argumento.
- `%d` : Permite mostrar valores numérico enteros.
- `%x` : Imprime valores hexadecimales en minúsculas con relleno de salida.
- `%f` : Permite mostrar valores con coma flotante.

También se permiten *secuencias de escape* e incluso, caracteres ordinarios. Una de las más conocidas y casi imprescindible es «`\n`», que indica salto de línea.

!!!example "Ejemplos: "

    ```bash
    # Una cadena con un salto de línea al final 
    printf "%s\n" "Hola, Linux!"
    # Obtenemos: Hola, Linux!

    # Una cadena sin mas, sin salto de línea. El prompt aparecerá en la misma línea
    printf "%s" "Hola, Linux!"
    # Hola, Linux!

    # Dos cadena separadas por un salto de línea
    printf "%b%b" "Hola, Linux! \n" "Gracias por vuestra labor\n"
    # Hola, Linux!
    # Gracias por vuestra labor

    # Valores numéricos, separados por un salto de línea
    printf "%d\n %d\n" "2020" "2021"
    # 2020
    # 2021

    # Valores hexadecimales
    printf "%08x\n" "2021"
    # 000007e5

    # Valores en coma flotante, con decimales 
    printf "%f\n" "1,82" "2,16"
    # 1,820000
    # 2,160000
    ```

Debes tener presente, que los valores decimales deben estar separados por una coma. Si usas como separador un punto, recibirás un error.

Ejemplos de formatos comúnmente utilizados:

| Formato | Descripción |
| ---: | --- |
| `%20s` | Visualización de una cadena (string) de 20 posiciones (alineada a la derecha por defecto). |
| `%-20s` | Visualización de una cadena (string) de 20 posiciones con alineación a la izquierda. |
| `%3d` | Visualización de un entero decimal de 3 posiciones (alineación a la derecha |
| `%03d` | Visualización de un entero (decimal) de 3 posiciones (alineación a la derecha) completado con 0 a la izquierda. |
| `%-3d` | Visualización de un entero (decimal) de 3 posiciones (alineación a la izq.) |
| `%+3d` | Visualización de un entero (decimal) de 3 posiciones (alineación a la derecha) con escritura sistemática del signo (un número negativo siempre mostrará su signo). |
| `%10.2f` | Visualización de un número en coma flotante de 10 posiciones, 2 de las cuales decimales. |
| `%+010.2f` | Visualización de un número en coma flotante de 10 posiciones, 2 de las cuales decimales, alineación a la derecha, con escritura sistemática del signo, completado con ceros a la izquierda. |


### `read`

La orden `read` se puede usar para darle valor a una variable como ya se ha mostrado en los ejemplos. También se puede dar valores a más de una variable de una vez:

!!!example "Ejemplo `read`"
    ```bash
    $ read var
    hola que tal
    # var contendrá el texto “hola que tal”

    $ read var1 var2 var3
    hola que tal bien
    # var1 contendrá “hola”, var2 contendrá “que” y var3 contendrá “tal bien”
    ```

A cada variable se le asigna una palabra, y a la última, el resto del texto.

Principales opciones del comando `read`, que se resumen en la tabla:

| Opción | Descripción |
| :---: | --- |
| `-a` | Permite leer las palabras como elementos de un ***array*** | 
| `-n` max | Permite leer como máximo max caracteres. |  
| `-p` | Permite indicar un texto de prompt | 
| `-s` | Ocultar los caracteres introducidos (para contraseñas). | 
| `-t` | Permite indicar un timeout para la operación de lectura | 

Reseñar la opción `-p`, que permite anteponer un texto a la entrada por teclado

!!!example "Ejemplo"

    ```bash
    read -p "Introduce un valor para la variable "valor": " hola
    # Imprimirá el mensaje y al final del mensaje esperará la entrada de la variable.
    ```


## Metacaracteres y algunos caracteres especiales

Los metacaracteres son caracteres especiales que tienen un significado determinado cuando los utilizamos:

Estos caracteres tienen un significado especial para el intérprete de comandos al buscar patrones de archivos.

| Carácter | Significado |
| --- | --- |
| `*` | Significa "cualquier cosa": representa 0 o más caracteres, sin importar cuáles sean. |
| `?` | Significa "cualquier carácter": representa exactamente 1 carácter cualquiera. |
| `[]` | Representa cualquiera de los caracteres encerrados individualmente entre los corchetes. |


Los corchetes permiten definir grupos específicos o rangos utilizando el guion (`-`).

| Patrón | Descripción y Ejemplos Válidos | Casos No Válidos |
| --- | --- | --- |
| **`dir*`** | Palabra *dir* seguida de cualquier cadena (ej: `dir`, `direct`, `dir1`, `directorio`). | (Ninguno, siempre que empiece por *dir*) |
| **`dir?`** | Palabra *dir* seguida de un único carácter (ej: `dir1`, `dira`, `dirz`, `dir7`). | `dir`, `dir10`, `directorio` |
| **`dir[1234]`** | Palabra *dir* seguida de uno de los números indicados (ej: `dir1`, `dir2`, `dir3`, `dir4`). | `dir9` |
| **`dir[a-j]`** | Palabra *dir* seguida de una letra entre la 'a' y la 'j' (ej: `dira`, `dirf`, `dird`). | `dirt` |

Bash permite unir varios metacaracteres en una sola palabra y utilizar los símbolos `!` o `^` para negar el patrón.

| Comando / Patrón | Explicación del Filtro | Ejemplos Coincidentes |
| --- | --- | --- |
| **`ls a*t?[1-9]`** | Empieza por 'a', seguido de cualquier cosa, una 't', un carácter cualquiera y finaliza con una cifra del 1 al 9. | `ate1`, `aperiti2`, `alto8`, `alt91` |
| **`ls [!a]*`** | Lista archivos que **no** empiecen por la letra "a". | `bin`, `config`, `data` |
| **`ls [!0-9]*`** | Lista archivos que **no** empiecen por un número. | `script.sh`, `notas.txt` |
| **`mv ?m[a-z].txt`** | Archivos con: 1º carácter cualquiera, 2º una 'm', 3º una letra entre 'a' y 'z', y extensión '.txt'. | `amz.txt`, `1ma.txt`, `_mb.txt` |

!!! nota "Caracteres para negaciones" 

    Recuerda que puedes usar indistintamente `!` o `^` dentro de los corchetes para realizar una negación.

Existen caracteres especiales representar para cadenas de texto, que no simbolizan letras ni números, sino que simbolizan espacios, tabulaciones, saltos de línea, etc... Estos caracteres se pueden consultar por ejemplo en el manual del comando echo (man echo). Algunos son:

- `\n` → Salto de línea
- `\t` → Tabulación

Para activar estos caracteres utilizando `echo`, hay que utilizar la opción `-e`. Ejemplos:

!!!example "Ejemplo"

    ```bash
    echo -e “Esto es una línea.\nEsta es otra”
    # Tiene como resultado el texto en dos líneas separadas:
    # Esto es una línea
    # Esto es otra
    ```

Esto se suele utilizar mucho a la hora de realizar ***menus***:

!!!example "Ejemplo"

    ```bash
    echo -n -e “1)Opción 1 \n2)Opción 2\n\tElige una opción: ”
    1)Opción 1
    2)Opción 2
    Elige una opción:
    ```

## Parámetros

Los parámetros son opciones o valores que se le pasan al programa cuando se ejecuta por línea de comandos, la estructura es la siguiente:

```
nombre_programa param1 param2 param3 param4 ...
```

Por ejemplo, al crear un directorio con `mkdir` podemos ver las equivalencias de la siguiente manera:

!!!example "Ejemplo"
    ```bash
    mkdir         -p     dir1/dir1-1 dir2   dir3
    # nombre_prog param1 param2      param3 param4
    ```

Para acceder a los valores de los parámetros dentro del programa, se accede con las variables especiales `$1`, `$2`, `$3,` `$4,` ... Estarán numeradas según el orden del parámetro. Para acceder a los parámetros a partir del 9, deberán encerrarse los números entre llaves '`{}`' (`${10}`, `${11}`, ...).


!!!example "Ejemplo de uso de parámetros"

    Ejemplo, con el siguiente script 

    ```bash
    #!/bin/bash
    echo “Me llamo $1 y tengo $2 años”
    ```

    Al ejecutarlo con con los siguientes parámetros
        `./script.sh Sergio 25`

    Obtendremos la siguiente salida:

    > `Me llamo Sergio y tengo 25 años`


También tenemos las variables especiales:

- `$0` : Nombre del programa
- `$#` : Número de parámetros enviados
- `$*` : Lista con todos los parámetros enviados
- `$$` : PID del script
- `$!` : PID del último comando lanzado en background dentro del script
- `$?` : Valor devuelto por el ultimo comando ejecutado (si es 0 es que es se ha ejecutado correctamente)
  

## Comillas

!!!warning "Advertencia"

    Al copiar y pegar trozos de código de internet en tus scripts que contengan comillas, puede que estas no se copien de forma correcta (caso de las comillas dobles casi siempre) y el programa no funcione bien. Por ello se recomienda, una vez pegado el trozo de código, verificar o volver a reescribir todas las comillas que aparezcan.

En la programación en el shell `Bash` existen 3 tipos de comillas:

- `Comillas simples (') ` : Las comillas simples encierran una cadena de texto que se representa después tal como se ha escrito, es decir, no se sustituyen ni variables ni caracteres especiales que estén dentro de este tipo de comillas.
- `Comillas dobles (“)`: Las comillas dobles tienen una función similar a las comillas simples. La diferencia es que primero sustituyen las variables y los caracteres especiales por su valor y a continuación el resultado de esa sustitución es como si estuviera encerrado en comillas simples.
- `Comillas invertidas o acento grave (``) ` *( A la derecha de la tecla p)* : Las comillas invertidas se utilizan para encerrar un comando y después son sustituidas por la salida que produce la ejecución de ese comando

!!!example "Ejemplo de uso de comillas"

    ```bash
    #!/bin/bash
    var=”Mensaje”

    echo 'os mando un $var'
    # Esto escribirá: os mando un $var

    echo “os mando un $var”
    # Esto escribirá: os mando un Mensaje

    echo 'A veces queremos "entrecomillar algo" y usamos los dos tipos de comillas'
    # Esto escribirá: A veces queremos "entrecomillar algo" y usamos los dos tipos de comillas

    echo `ls -l`
    #Mostrará la salida de ejecutar ls -l

    #También se pueden poner cadenas de texto seguidas encerradas en diferentes tipos #de comillas
    echo 'La variable $var tiene el valor:' “$var” `date`
    #Esto mostrara: La variable $var tiene el valor: Mensaje lun feb 26 12:23:36 CET 2023
    ```
