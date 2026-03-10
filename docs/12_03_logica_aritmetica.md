---
title: Lógica matemática
description: Scripts en Bash para la administración de linux
---


## Valores devueltos por programas

En shell script de Linux, los programas cuando terminan de ejecutarse, devolverán un valor que indicará si se han ejecutado de forma correcta, o por el contrario, ha ocurrido algún tipo de error durante su ejecución. Cuando todo ha ido **correctamente**, devolverán el valor `0`, mientras que si algo ha ido **mal**, devolverán un valor diferente de `0`, normalmente `1` o `-1`.

Para terminar un programa devolviendo uno de estos valores hay que ejecutar la orden `exit` dentro del programa pasándole como primer y único parámetro el valor de salida. Si no se le pasa ningún parámetro se interpreta como valor 0, es decir, el programa ha ido correctamente.

!!! example "Ejemplo"
    ```bash
    #!/bin/bash
    # Escribimos el programa y comprobamos si ha ido todo bien
    # Si todo ha ido bien
    exit 0
    # Si algo ha fallado
    exit 1
    ```

!!!note "Nota"

    Con esto se puede deducir que el valor verdadero (todo correcto), se representa con el número 0, y otro valor diferente, por ejemplo el 1, se interpretará como falso***. 

Esto puede liar un poco porque funciona justo al revés que en muchos lenguajes de programación ya que normalmente sevsuele asociar al 0 como valor falso.



## Expresiones matemáticas

Básicamente existen 3 comandos que evalúan expresiones matemáticas y lógicas. 


### `expr`

El primero de ellos es el comando `expr` que evalúa una expresión matemática, lógica, e incluso con cadenas de texto, y
devuelve el resultado de la evaluación por la salida estándar.

```bash 
expr 3 + 4
# obtenemos como resultado un 7
```

Este comando acepta los operadores matemáticos `+`, `-`, `*`, `/` y `%` (resto de una división). 

También acepta los operadores lógicos `>`, `<` , `>=`, `<=`, `=` y `!=` (distinto de). También acepta operadores de cadenas de texto como `length “cadena de texto”` que te muestra la longitud que tiene una cadena de texto, o `substr “cadena de texto” inicio longitud`, que saca un trozo del texto con una longitud indicada empezando desde la posición indicada también.

!!!example "Ejemplos"
    ```bash
    expr length “esto es un texto”
    # Retorna : 16

    # Desde el carácter número 6 -> 'q', devuelve 3 caracteres.
    expr substr “hola que tal” 6 3
    # Retorna : que

    # Desde el carácter número 4 -> 'a', devuelve 7 caracteres.
    expr substr “hola que tal” 4 7
    # Retorna : a que t
    ```

Además tenemos que tener en cuenta algunos aspectos esenciales a la hora de utilizar la funcion `expr`:

- Los caracteres especiales, como por ejemplo `*`, `>`, `<`, `(`, `)`, han de '***escaparse***', es decir ponerles la barra invertida '`\`' delante para que se interpreten como caracteres normales.
- Todos los operadores y operandos deben de estar separados por un espacio entre si.
- Se pueden agrupar expresiones entre paréntesis
- Los operadores lógicos devuelven 1 si es verdadero y 0 si es falso.


!!!example "Mas Ejemplos"

    ```Bash
    expr 2 \* 3
    # Retorna : 6

    expr 3 \< 2
    # Retorna : 0

    expr \( 2 + 2 \) \> 3
    # Retorna : 1
    ```

###`let`

El otro comando que evalúa expresiones matemáticas y lógicas es `let`. Está limitado a este tipo de expresiones, pero es bastante más flexible que el anterior. Con este comando asignamos la evaluación de una expresión a una variable (es aconsejable casi siempre encerrar la expresión entre comillas ya que nos ahorrará problemas con caracteres especiales. El comando expr no soporta las comillas, pero let si).

!!!example "Ejemplos"

    ```bash
    let "var = 4 + 3"
    echo $var
    # Retorna : 7

    let "var = 4 > ( 6 – 4 )"
    echo $var
    # Retorna : 0

    let "var = 4"
    let "var = $var * 3"
    echo $var
    # Retorna : 12
    ```

No hace falta con este comando separar los operadores y los operandos por espacios, además, al estar encerrada la expresión entre comillas no hace falta '***escapar***' los caracteres especiales.

Ahora un par de equivalencias útiles:

- `let "var = $var + 1"` → `let var++`
- `let "var = $var - 1"` → `let var--`

O sea que `++` y `--` se pueden utilizar con `let` como en otros lenguajes de programación, para incrementar o decrementear un valor entero.

Por último, vamos a ver la diferencia entre asignar un valor a una variable utilizando el comando `expr` y el comando `let`.

!!!example "Ejemplos"
    ```bash
    # Forma clásica con expr, asignar a una variable la salida de un comando
    var=`expr 3 + 4`

    # Con let puede parecer que es más intuitivo, aunque es menos utilizada
    let "var = 3 + 4”
    ```

!!!warning "Importante"

    Cuando se utiliza `let`, a diferencia de `expr`, utilizar '`=`' no significa comparar si dos números son iguales, sino asignar un valor a una variable. Para comparar si dos números son iguales se utiliza el doble igual '`==`' (dos símbolos de igual pegados).


### `bc`

Por último, tenemos el comando `bc`, que nos permite hacer operaciones con decimales. Como en realidad este comando es muy potente (se puede programar con él) suele leer los datos desde un archivo, pero en nuestro caso se los podemos pasarse con una tubería de la siguiente forma:

!!!example "Ejemplos"
    ```bash
    variable=`echo “5.5 * 2.5” | bc`
    echo $variable
    # Retona : 13.7

    y=10
    y=$(echo $y/3 | bc)
    echo $y
    Retorna : 2
    ```

!!!note "Nota"
    se debe incluir toda la operación entre `` acentos abiertos o $()

En todo caso, `bc` es una herramienta potente para la realización de cálculos matemáticos que tal vez escape al propósito de nuestro curso. Mas información en [freeshell: Cálculo numérico con bc](http://marcmmw.freeshell.org/esp/programacion/bc.html)


## Condiciones en shell script

Podemos representar un condición de 2 maneras cuando realizamos un script. La primera es ejecutando un comando y evaluando su salida (0 -> correcto, y otro valor -> falso) y la segunda es mediante la orden test que sirve para evaluar expresiones.

### Condiciones al ejecutar comandos

Cada vez que ejecutemos un comando o programa y este termine nos devuelve un valor que indica si todo ha ido correcto o no, lo equivalente a `verdadero` y `falso` en este caso. Por ejemplo el comando:

`cd dir1`

Nos devolverá `verdadero` (**0**) si el directorio 'dir1' existe y ha podido entrar en el, y falso en caso contrario (algo ha ido mal, es decir, no ha podido entrar en el directorio, bien sea porque no existe, o porque no tiene permiso el usuario).

En este tipo de condiciones podemos utilizar los operadores '`y`' y '`o`' que vimos anteriormente.

En este caso, el operador `y` se representa '`&&`' y el operador '`o`' se representa '`||`'. Podemos usar paréntesis para agrupar comandos también (pero no se comportarán igual, ya que comandos como `cd dir` se evaluarán pero no tendrán ningún efecto), y hay que tener en cuenta que estos **se evalúan de izquierda a derecha**, es decir, si tenemos algo del tipo: 'comando1 y comando2', el comando 2 sólo se ejecutará si el comando1 es verdadero (ha ido bien), ya que si unimos 2 condiciones con '`y`', y alguna es falsa, el resultado siempre será falso, no nos hará falta evaluar la otra condición. 

Si las unimos con '`o`', en cuanto encuentre un comando que vaya correctamente se parará y no ejecutará el resto.

Imaginemos que queremos crear un archivo dentro de un directorio, pero no sabemos si el directorio existe:

```bash
cd dir1 && touch arch1
```

De esta forma, el comando '`touch arch1`' sólo se ejecutará si el comando '`cd dir1`' se ha ejecutado correctamente.


Otro ejemplo:

```bash
cd dir1 || echo “No he podido entrar en dir1”
```

De esta forma, `echo “No he podido entrar en dir1”` sólo se ejecutará si '`cd dir1`' ha fallado, es decir, si no podemos entrar en `dir1` por alguna razón, mostrará un mensaje de error.

```bash
cd dir1 || cd dir2 && touch arch1
```

Primero evalúa '`cd dir1`', si todo es correcto, no evaluará '`cd dir2`'. Si '`cd dir1`' o '`cd dir2`' funcionan bien, creará un archivo '`arch1`' dentro de ellos.

Digamos que va evaluando primero los 2 primeros comandos y el resultado lo evalúa junto con el siguiente de la derecha y así sucesivamente.

Si ponemos un símbolo de admiración '`!`' antes de un comando (separado por un espacio), estaremos haciendo una negación, es decir, si el resultado del comando es V, pasará a ser F y viceversa.

```bash
! cd dir || echo “enhorabuena, estamos dentro de dir”
cd dir && echo “enhorabuena, estamos dentro de dir”
```

### Evaluación de expresiones. La orden `test`.

En shell script, tenemos una orden o comando llamado `test`, que evalúa una serie de condiciones y devuelve el valor final, es decir, verdadero, o falso. Hay dos formas de llamar a este comando.

- `test` condición
- [ condición ]

Cuando evaluamos una expresión con los corchetes `[]`, hay que tener muy en cuenta que se debe dejar un espacio entre el primer corchete y la condición y también al poner el último corchete.


1. **Operadores para ficheros**: Trabajan con ficheros y algunos de los que existen son:

    | operador | resultado |
    | --- | --- |
    | `-e` fichero | Comprueba si el fichero existe |
    | `-f` fichero | Comprueba si el fichero existe y además es un fichero normal y corriente |
    | `-d` fichero | Comprueba si el fichero existe y además es un directorio | 
    | `-r` fichero | Comprueba si el proceso (script) tiene permiso de lectura sobre el fichero |
    | `-w` fichero | Comprueba si el proceso (script) tiene permiso de escritura sobre el fichero |
    | `-x` fichero | Comprueba si el proceso (script) tiene permiso de ejecución sobre el fichero |
    | `-s` fichero | Comprueba si el fichero no es vacío (tiene más de 0 bytes) |
    | fich1 `-nt` fich2 | Comprueba si fich1 se ha modificado más recientemente que fich2 |
    | fich1 `-ot` fich2 | Comprueba si fich1 se ha modificado antes (es más viejo) que fich2 |


2. **Operadores para cadenas de texto**: Trabajan con texto y algunos de los que existen son:

    | operador | resultado |
    | --- | --- |
    | texto1 `=` texto2 | Comprueba si las dos cadenas de texto son idénticas (no pueden ser cadenas de texto vacías) (estandar POSIX) |
    | texto1 `==` texto2 | Comprueba si las dos cadenas de texto son idénticas (no pueden ser cadenas de texto vacías) (estandar Bash) |
    | texto1 `!=` texto2 | Comprueba si las dos cadenas de texto son diferentes (no vacías) |
    | `-z` texto | Comprueba si la cadena de texto está vacía (longitud 0) |
    | `-n` texto | Comprueba que la cadena de texto no esté vacía (longitud > 0) |
    
3. **Operadores para manejar números**: Trabajan comparando valores numéricos:

    | operador | resultado |
    | --- | --- |
    | num1 `-eq` num2 | Comprueba si los números son iguales |
    | num1 `-ne` num2 | Comprueba si los números son distintos |
    | num1 `-gt` num2 | Comprueba si num1 es mayor que num2 |
    | num1 `-ge` num2 | Comprueba si num1 es mayor o igual que num2 |
    | num1 `-lt` num2 | Comprueba si num1 es menor que num2 |
    | num1 `-le` num2 | Comprueba si num1 es menor o igual que num2 |

4. **Operadores `y`, `o`, `no` y `paréntesis`**: Se pueden unir condiciones o negarlas y agruparlas de la siguiente manera:

    - El operador 'y' se representa con '`-a`'.
    - El operador 'o' se representa con '`-o`'.
    - El operador 'no' se representa con '`!`'.
    - Para utilizar los paréntesis hay que escaparlos, es decir, poner el símbolo '`\`' antes de cada paréntesis, ya que estos son símbolos especiales para el intérprete y queremos evitar que los interprete como tales.

!!!example "Ejemplos"
    ```bash
    # Comprueba si num1 es igual a num2 y además mayor que num3. 
    num1 -`eq` num2 `-a` num1 `-gt` num3 

    # Comprueba si arch1 tiene permisos de lectura o de ejecución. 
    `-r` arch1 `-o` `-x` arch1 

    # Comprueba que el fichero arch1 si es vacío 
    `!` `-s` arch1 

    # Comprueba si el valor de la variable $var es distinto de 0 y además, comprueba si arch1 es un fichero normal o un directorio. 
    $var `-ne` 0 `-a` `\(` `-f` arch1 `-o` `-d` arch1 `\)` 
    ```

### Diferencias `test`, `[]` y `[[]]`

Para comparar en Bash se utiliza `test` o `[`. Ambos son totalmente equivalentes como hemos visto anteriormente, y ambos están implementados, en el propio Bash. 

Por otro lado, también puedes utilizar dobles corchetes `[[`, para realizar tus comparaciones. Los dobles corchetes resultan ser ***una mejora respecto a los simples***. Así, las diferencias entre uno y otro son las siguientes,

1. No tienes que utilizar las comillas con las variables, los dobles corchetes trabajan perfectamente con los espacios. Así `[ -f "$file" ]` es equivalente a `[[ -f $file ]]`.
2. Con `[[` puedes utilizar los operadores `||` y `&&` , así como para las comparaciones de cadena.
3. Puedes utilizar el operador `=~` para expresiones regulares, compo por ejemplo `[[ $respuesta =~ ^s(i)?$ ]]`
4. También puedes utilizar comodines como por ejemplo en la expresión `[[ abc = a\* ]]`

Es posible que te preguntes por la razón para seguir utilizando `[` *simple corchete* en lugar de *doble*. La cuestión es por compatibilidad. Si utilizas Bash en diferentes equipos es posible que te encuentres alguna incompatibilidad. Así que depende  de ti y de donde lo vayas a utilizar. 

Por ejemplo, el uso de varias condiciones será diferente según utilices cada uno de ellos


Operadores booleanos que son diferentes según el tipo de corchetes elegido

- `[[ ]]` → `&&` , `||`
- `[ ]`   → `-a`, `-o`

Por último, reseñar que debes también advertir que `[[ 001 = 1 ]]` es falso mientras que `[[ 001 -eq 1 ]]` es cierto. Lo mejor probar, probar y volver a probar hasta asegurarte de que lo entiendes y tiene el resultado esperado.

### Evaluación de condiciones booleanas: verdadero (0) y falso (1)

Una condición es algo que cuando se comprueba sólo puede dar 2 valores como resultado, el valor `verdadero` (**se representa como 0** en `bash` script) que indica que la condición es válida, y el valor `falso` (**se representa como 1**) que indica lo contrario. 

Una condición puede ser por ejemplo “x es mayor que y”, siendo 'x'  e 'y' números.

| condición | resultado | 
| ---       | :---:     |
| verdadero | **0**     |
| falso     | **1**     |

Cuando queremos unir 2 o más condiciones para evaluar si son ciertas o no, es decir, podemos crear un programa que te pregunte la altura y el peso, y en función de eso mostrar mensajes diferentes. En este caso podemos evaluar 2 condiciones (altura y peso) de la siguiente forma:

- Si altura es igual que x y peso mayor que y
- Si altura es igual que x y peso menor que z
- Si altura es mayor que f o peso menor que g
- etcétera.


En este caso tendremos 2 operadores especiales, llamados operadores lógicos, estos son el operador `y` y el operador `o`.

-**Operador `y`**: El operador “y” se evalúa como verdadero si todas las condiciones unidas con este operador son verdaderas, y falso en el caso contrario.

Imaginemos la evaluación de: “condición1 **y** condición2 **y** condición3”

| condición1 | condicion2 | condicion3 | ***resultado*** |
| --- | --- | --- | --- |
| Verdadero | Verdadero | Verdadero | ***Verdadero*** | 
| Verdadero | Falso | Verdadero | ***Falso*** | 
| Falso | Verdadero | Falso | ***Falso*** | 
| Falso | Falso | Falso | ***Falso*** | 

En definitiva si cualquier valor evaluado con “y” es falso, el resultado será falso.

- **Operador `o`**: El operador “o” se evalúa como falso si todas las condiciones unidas con este operador son falsas, y verdadero en el caso contrario. Es decir, es suficiente con que se cumpla una de las condiciones.

Imaginemos la evaluación de: “condición1 **o** condición2 **o** condición3”

| condición1 | condicion2 | condicion3 | ***resultado*** |
| --- | --- | --- | --- |
| Verdadero | Verdadero | Verdadero | ***Verdadero*** | 
| Verdadero | Falso | Verdadero | ***Verdadero*** | 
| Falso | Verdadero | Falso | ***Verdadero*** | 
| Falso | Falso | Falso | ***Falso*** | 

También existe otro **operador lógico** que es la **negación**, en este caso lo vamos a representar como **“`!`”**, cualquier condición que tenga delante esta negación da como resultado el valor contrario al de la condición, es decir, se niega el resultado de la condición dando como resultado lo contrario.

| **condición** | **!condicion** | 
| ---       | :---:     |
| verdadero | falso |
| falso     | verdadero |

Las condiciones se pueden agrupar entre paréntesis igual que si de expresiones matemáticas se tratara, simplemente hay que evaluar primero las condiciones de los paréntesis y sustituir el paréntesis por el resultado:

Siendo: 

    - condición1=verdadero
    - condición2=verdadero
    - condición3=falso
    - condición4=verdadero

1. condición1 y condición2 y (condición3 o condición4)
2. verdadero y verdadero y (falso o verdadero) -> falso o verdadero = verdadero
3. verdadero y verdadero y verdadero
4. verdadero


Otro ejemplo:

1. !(verdadero y (falso o falso o (verdadero y falso)))
2. !(verdadero y (falso o falso o falso))
3. !(verdadero y falso)
4. !(falso)
5. verdadero
