---
title: Anexos
description: Scripts en Bash para la administración de linux
---


Para finalizar esta documentación sobre scripting vamos a revisar una serie de elementos que nos permiten crear scripts más profesionales con mejores interacciones con el usuario, comando avanzados como `awk` y aclaraciones sobre algunos aspectos de la sintaxis

## El doble paréntesis `(( .. ))`

El doble paréntesis, cuyo uso en un caso muy particular ya hemos visto con el bucle `for`, forma un estructura con funcionamiento muy similar al comando `let`. De hecho podemos hacer lo mismo que hacíamos con `let` (El dólar para leer las variables sigue siendo opcional en este caso solamente, sólo para leer, al darles valor siempre van sin dólar):

!!!example "Ejemplo"

    ```bash
    #!/bin/bash
    (( n = 3 + 5 ))
    echo $n
    # La variable n valdrá 8
    ```

Si en lugar de usarlo como el `let` (asignar un valor a una variable), queremos que funcione más bien como usar `expr operación` basta con ponerle el dólar '`$`' delante y se sustituirá por el resultado de la operación.

!!!example "Ejemplo"

    ```bash
    #!/bin/bash
    n=5
    echo “El doble de $n es $((n * 2))” # El doble de 5 es 10
    ```

Lo mejor de usar el doble paréntesis, es que cuando ponemos una condición lógica dentro devuelve un valor verdadero o falso, con lo cual se hace posible utilizarlo como condición, para un if o para un bucle while por ejemplo:

!!!example "Ejemplo"

    ```bash
    #!/bin/bash
    i=0

    # Contador desde i=0 a i=4
    while (( i < 5 ))
    do
        if (( (i % 2) == 0 )) #Numero par
        then
            echo "Numero par: $i"
        else #Numero impar
            echo "Numero impar: $i"
        fi
        (( i++ ))
    done
    ```

Otra posibilidad del doble paréntesis es la de poder hacer operaciones con variables, para mostrarlas por pantalla, por ejemplo, si tenemos una posición de un array, y queremos mostrar una posición más (para empezar desde la 1 y no desde la 0), en ese caso se pone el símbolo `$`, seguido del doble paréntesis y la operación dentro. Ejemplo:

!!!example "Ejemplo"

    ```bash
    for (( i = 0; i < ${#vector[*]}; i++ ))
    do
        echo "Elemento $((i+1)): ${vector[$i]} "
    done
    ```

## Expresiones regulares

Dominar las expresiones regulares es fundamental para realizar todo tipo de operaciones en texto.

A continuación tenemos un compendio extraido de la siguiente fuente: [UPV. COMAV. Expresiones regulares](https://bioinf.comav.upv.es/courses/unix/expresiones_regulares.html)

- **Contenedores**

| POSIX |	ASCII | Significado |
| --- | --- | --- |
| [:alnum:] |	[A-Za-z0-9] |	Caracteres alfanuméricos (letras y números) |
| [:word:] |	[A-Za-z0-9_] |	Caracteres alfanuméricos y “_” |
| [:alpha:] |	[A-Za-z] |	Caracteres alfabéticos |
| [:blank:] |	[ \t] |	Espacio y tabulador |
| [:space:] |	[ \t\r\n\v\f] |	Espacios |
| [:digit:] |	[0-9] |	Dígitos |
| [:lower:] |	[a-z] |	Letras minúsculas |
| [:upper:] |	[A-Z] |	Letras mayúsculas |
| [:punct:] |	[][!”#$%&’()*+,./:;<=>?@\^_`{\|}~-] |	Caracteres de puntuación |

- **Cuantificadores**

| Expr | Significado |
| --- | --- | 
| `?` | el carácter aparece ninguna o una vez. “usher1?” coincidiría con “usher” o “usher1”. |
| `*` | cero, una o varias veces. |
|  `+ ` | al menos una vez |
|  `{4} ` | cuatro veces. |
|  `{4,10} ` | entre 4 y 10 veces |


- **Puntos de anclaje**

| Expr | Significado |
| --- | --- | 
|  `^ ` | inicio de línea |
|  `$ ` | fin de línea |
|  `< ` | principio de palabra |
|  `> ` | fin de palabra |
|  `\b ` | límite de palabra |

Veamos algunos ejemplos de uso: 

!!!example "Ejemplo 1. Búsquedas de carpetas"

    Buscamos solo directorios

    ```bash
    ls /etc | grep ^d
    ```
!!!example "Ejemplo 2. Búsquedas de archivos concretos"

    Buscamos archivos de configuración, que sean archivos y que tengan cosas intermedias y que acaben en `.conf`. La barra invertida es para que el punto se tome como punto (`\.`)

    ```bash
    ls -l /etc | grep ^-.*\.conf$
    ```

!!!example "Ejemplo 3. Más filtros para archivos"

    Buscamos ficheros que comiencen y terminen por una vocal
    ```bash
    ls /etc | grep ^[aeiouAEIOU].*[aeiouAEIOU]$ 
```

!!!example "Ejemplo 4. Búsquedas de caracteres especiales"

    Para los caracteres comodines, si los queremos buscar utilizamos escapes, por ejemplo para buscar  3 o 4 puntos 

    ```bash
    echo fdf....fdfsaf...a | grep "\.\.\."
    echo fdf....fdfsaf...a | grep "\.\.\.\."

    ls -1 /etc | grep ".conf"   //aquí el punto se interpreta como cualquier caracter
    ls -1 /etc | grep "\.conf"  //aqui el punto se toma como un punto
    ```

!!!example "Ejemplo 5. Búsquedas usando máscaras de permisos"

    podemos crear máscaras para mostrar los que tengan ciertos permisos
    ```bash
    ls -l /etc | grep '^......x.w.'
    ```
    en este caso, los que los del grupo puedan ejecutar y los otros puedan escribir.

!!!example "Ejemplo 6. Búsquedas para contar"

    Todos los directorios: contarlos
    ```bash
    ls -l /etc | grep ^d | wc -l
    ```

!!!example "Ejemplo 7. Transformaciones tras búsquedas"

    listado de todos los directorios en mayúsculas
    ```bash
    ls -l /etc | grep ^d | tr -s " " | cut -f9 -d" " |tr a-z A-Z
    ```

!!!example "Ejemplo 8. Filtros sobre entrada teclado"

    Con el siguiente comando, te coge la entrada estándar, que es el teclado, y te repite las lineas que cumplen las condiciones indicadas, comienzan por letra, sigue por letra o número o _, y acaba por letra o numero
    ```bash
    grep '^[a-zA-Z][a-zA-Z0-9_]*[a-zA-Z0-9]$'
    ```

!!!example "Ejemplo 9. Enumerando búsquedas"

    Numerara a la hora de buscar / filtra
    ```bash
    ls  /etc | grep -n ^d

    # resultado 
    39:dbus-1
    40:dconf
    41:debconf.conf
    42:debian_version
    43:default
    44:deluser.conf
    45:depmod.d
    46:dhcp
    47:dictionaries-common
    48:dpkg
    ```

## Número aleatorios

A veces, estamos programando algún script en `Bash` y necesitamos (por algún motivo) generar algún **número aleatorio**. `RANDOM` es una variable de shell que se utiliza para generar enteros aleatorios en Linux. Es un comando bash interno que devuelve un entero de 16 bits en el rango 0 - 32767.Devuelve un entero diferente en cada invocación.

- Generando un entero aleatorio cuando se da el límite superior.

Si se desea generar un número entero aleatorio dentro de Y (donde Y no está incluido). El formato para lograr esto es:

!!!example ""

    ```bash
    NUM=$(( $RANDOM % Y))
    ```

Para incluir el valor del limite superior (Y) la operación será Y+1. El formato es:

!!!example ""

    ```bash
    R=$(($RANDOM % Y+1))
    ```

- Generando un número entero aleatorio cuando se da tanto el límite superior como el inferior

Si se desea generar un número entero aleatorio entre X e Y donde X es el límite inferior (X ≠ 0) e Y es el límite superior. El formato para lograr esto es:

!!!example ""

    ```bash
    RANGO = $ ((Y-X + 1))
    NUM = $ (($ (($ RANDOM% $ RANGO)) + X))
    ```

Otra forma de conseguir un valor aleatorio es mediante el comando `shuf` (*shuffle* = barajar). Como argumentos más representativos tenemos: 

- `-i`: indica el intervalo del que debe extraer el número aletorio
- `-n`: indica la cantidad de números aleatorios a obtener, si no ponemos nada realiza un orden aleatorio de todos el intervalo indicado
- `-r`: se pueden repetir los números obtenidos. Si no se pone, no se repiten, se van descartando.
- `-z`: el delimitador de línes es NUL. Obtiene todos los números en una misma línea.

!!!example "Ejemplo" 

    ```bash
    shuf -i1-10 -n3
    # Obtiene 3 números aleatorios desde el 1 al 10 ambos incluidos

    shuf -i 0-9 -n 5 -r -z
    # obtiene 5 dígitos aleatorios entre 0 y 9, que se pueden repetir y se obtienen en una misma línea

    echo "El número primeado ha sido el `shuf -i0-9 -n5 -rz`"
    # ídem anterior, pero lo muestra en una frase.
    ```


## Comando `awk`

El comando `awk` de linux es una herramienta que te permite procesar y modificar archivos de texto según tus necesidades. Con `awk` puedes buscar palabras o patrones en los archivos y realizar acciones sobre ellos, como imprimir, reemplazar o hacer operaciones matemáticas1. `awk` también ***tiene su propio lenguaje de programación y scripting***, que te permite escribir programas más complejos y flexibles.

Aquí tienes algunos ejemplos de cómo usar el comando awk:

!!!example "Ejemplo 1"

    ```bash
    # Para mostrar solo la primera columna de un archivo separado por espacios
    awk '{print $1}' archivo.txt
    ```

!!!example "Ejemplo 2"

    ```bash
    # Para mostrar solo las líneas que contienen la palabra “Linux”:
    awk '/Linux/ {print}' archivo.txt
    ```

!!!example "Ejemplo 3"

    ```bash
    # Para sumar los valores de la segunda columna de un archivo separado por comas:
    awk -F ',' '{sum += $2} END {print sum}' archivo.txt
    ```

!!!example "Ejemplo 4"

    ```bash
    # Para contar el número de líneas que contienen una vocal:
    awk '/[aeiou]/ {count++} END {print count}' archivo.txt
    ```

!!!example "Ejemplo 5"

    ```bash
    # Para mostrar las líneas que tienen una longitud mayor que 10 caracteres:
    awk 'length($0) > 10 {print}' archivo.txt
    ```

!!!example "Ejemplo 6"

    ```bash
    # Para convertir las letras minúsculas en mayúsculas:
    awk '{print toupper($0)}' archivo.txt
    ```

!!!example "Ejemplo 7"

    ```bash
    # Para generar un código QR con el texto del archivo:
    awk 'BEGIN {srand()} {a[NR] = $0} END {for (i=1; i<=NR; i++) {n = int(rand() * NR) + 1; print "\\qr{" a[n] "}\\\\"}}' archivo.txt
    ```

!!!example "Ejemplo 8"

    ```bash
    # Para generar un poema con las palabras del archivo:
    awk 'BEGIN {srand()} {a[NR] = $0} END {for (i=1; i<=4; i++) {n = int(rand() * NR) + 1; print a[n]}}' archivo.txt
    ```    

## Definir color y posición del texto en la consola

Para hacer que el texto aparezca de un determinado color o en una determinada posición de la consola (definida por número de columna y de fila), se utilizan una serie de códigos especiales, que luego serán interpretados por el comando echo con la opción -n (igual que cuando queremos que funcionen los saltos de línea o tabuladores `\n\t)`.

Estos son los códigos para colores y estilos de letra (*subrayado*, *negrita*), lo más útil es guardarlos en variables por ejemplo, al principio de nuestro script o en otro al cual llamaremos desde el nuestro (lo ejecutamos).

!!!note "Codificación"

    ```bash
    # Resetear el formato de texto (volver al original)
    Color_Off='\e[0m'
    # Text Reset

    # Colores normales
    Black='\e[0;30m'    # Black
    Red='\e[0;31m'      # Red
    Green='\e[0;32m'    # Green
    Yellow='\e[0;33m'   # Yellow
    Blue='\e[0;34m'     # Blue
    Purple='\e[0;35m'   # Purple
    Cyan='\e[0;36m'     # Cyan
    White='\e[0;37m'    # White

    # Colores + negrita
    BBlack='\e[1;30m'   # Black
    BRed='\e[1;31m'     # Red
    BGreen='\e[1;32m'   # Green
    BYellow='\e[1;33m'  # Yellow
    BBlue='\e[1;34m'    # Blue
    BPurple='\e[1;35m'  # Purple
    BCyan='\e[1;36m'    # Cyan
    BWhite='\e[1;37m'   # White

    # Colores + subrayado
    UBlack='\e[4;30m'   # Black
    URed='\e[4;31m'     # Red
    UGreen='\e[4;32m'   # Green
    UYellow='\e[4;33m'  # Yellow
    UBlue='\e[4;34m'    # Blue
    UPurple='\e[4;35m'  # Purple
    UCyan='\e[4;36m'    # Cyan
    UWhite='\e[4;37m'   # White

    # Color de fondo (anteriores era para el texto)
    On_Black='\e[40m'   # Black
    On_Red='\e[41m'     # Red
    On_Green='\e[42m'   # Green
    On_Yellow='\e[43m'  # Yellow
    On_Blue='\e[44m'    # Blue
    On_Purple='\e[45m'  # Purple
    On_Cyan='\e[46m'    # Cyan
    On_White='\e[47m'   # White

    # Colores de alta intensidad
    IBlack='\e[0;90m'   # Black
    IRed='\e[0;91m'     # Red
    IGreen='\e[0;92m'   # Green
    IYellow='\e[0;93m'  # Yellow
    IBlue='\e[0;94m'    # Blue
    IPurple='\e[0;95m'  # Purple
    ICyan='\e[0;96m'    # Cyan
    IWhite='\e[0;97m'   # White

    # Colores de alta intensidad + negrita
    BIBlack='\e[1;90m'  # Black
    BIRed='\e[1;91m'    # Red
    BIGreen='\e[1;92m'  # Green
    BIYellow='\e[1;93m' # Yellow
    BIBlue='\e[1;94m'   # Blue
    BIPurple='\e[1;95m' # Purple
    BICyan='\e[1;96m'   # Cyan
    BIWhite='\e[1;97m'  # White

    # Colores de fondo de alta intensidad
    On_IBlack='\e[0;100m'   # Black
    On_IRed='\e[0;101m'     # Red
    On_IGreen='\e[0;102m'   # Green
    On_IYellow='\e[0;103m'  # Yellow
    On_IBlue='\e[0;104m'    # Blue
    On_IPurple='\e[10;95m'  # Purple
    On_ICyan='\e[0;106m'    # Cyan
    On_IWhite='\e[0;107m'   # White
    ```

Para probar estos colores, simplemente debemos meter la variable entre el texto (los cambios serán permanentes hasta que los volvamos a resetear o cambiar de nuevo):

!!!example "Ejemplo con códigos de colores"

    ```bash
    echo -e "${Blue}Texto azul ${UGreen}Texto verde subrayado${Color_Off} Reset"
    ```

## Definir la posición

Se hace de una forma parecida a usar colores, es decir, mediante un código. Los 3 tipos de códigos que nos interesan son:

Tabla: Control del Cursor y Pantalla (Secuencias ANSI)

| Código ANSI | Acción / Descripción |
| --- | --- |
| `\033[s` | **Guarda** la posición actual del cursor (útil para volver a ella más tarde). |
| `\033[<F>;<C>f` | **Posiciona** el cursor en la fila `<F>` y columna `<C>` indicadas (ambas empiezan en 1). |
| `\033[u` | **Restaura** el cursor a la última posición guardada con `\033[s`. |
| `\033[<N>A` | Mueve el cursor **hacia arriba** `<N>` líneas. |
| `\033[<N>B` | Mueve el cursor **hacia abajo** `<N>` líneas. |
| `\033[<N>C` | Mueve el cursor **hacia adelante** (derecha) `<N>` columnas. |
| `\033[<N>D` | Mueve el cursor **hacia atrás** (izquierda) `<N>` columnas. |
| `\033[2J` | **Limpia** completamente la pantalla de la terminal. |
| `\033[K` | **Limpia** la línea desde la posición actual del cursor hasta el final de esta. |

Ejemplos de uso

!!!example "Ejemplos"

    ```bash
    echo -e '\033[2J\033[1;1fBienvenido a: \033[4;7f La consola\033[4C...Del futuro'
    ```

!!!example "Ejemplos"

    ```bash
    #!/bin/bash
    # Limpiar pantalla y situar el cursor en la fila 5, columna 10
    echo -e "\033[2J\033[5;10fHola Mundo en una posición específica"

    # Mover el cursor 2 líneas hacia abajo para la siguiente frase
    echo -e "\033[2BPróximo texto dos líneas más abajo."
    ```

!!!note "Nota técnica:" 
    
    El código `\033` representa el carácter **ESC** (Escape). En algunos manuales también lo verás escrito como `\e` o `\x1b`, pero `\033` es la representación octal más estándar en Bash.



## `Zenity`

`Zenity` es una herramienta que te permite crear cuadros de diálogo gráficos desde la línea de comandos o desde scripts de shell1. Con `Zenity` puedes interactuar con el usuario y recibir información de forma sencilla y visual. `Zenity` usa las librerías GTK y se integra bien con el entorno de escritorio GNOME, pero también funciona en otros entornos2.

Para usar `Zenity`, solo tienes que escribir el nombre del comando seguido del tipo de cuadro de diálogo y las opciones que quieras. Por ejemplo, para mostrar un cuadro de diálogo de entrada con el texto *“Escribe tu nombre”*, puedes escribir:

!!!example "Ejemplo uso Zenity"

    ```bash
    zenity --entry --text="Escribe tu nombre"
    ```

`Zenity` tiene varios tipos de cuadros de diálogo que puedes usar según tus necesidades, como por ejemplo:

| Tipo de Diálogo | Opción de Zenity | Descripción |
| --- | --- | --- |
| **Calendario** | `--calendar` | Muestra un calendario para seleccionar una fecha. |
| **Entrada** | `--entry` | Solicita al usuario que introduzca un texto. |
| **Error / Aviso** | `--error` / `--warning` | Muestra un mensaje de error o una advertencia. |
| **Información** | `--text-info` | Muestra un área de texto con información. |
| **Lista** | `--list` | Muestra una lista de opciones para elegir una o varias. |
| **Notificación** | `--notification` | Muestra un icono de notificación en el área de estado. |
| **Progreso** | `--progress` | Muestra una barra para indicar el progreso de una tarea. |
| **Pregunta** | `--question` | Realiza una pregunta con botones de respuesta Sí/No. |
| **Selector de archivos** | `--file-selection` | Abre un diálogo para seleccionar archivos o carpetas. |
| **Selector de color** | `--color-selection` | Abre una paleta para seleccionar un color. |

!!!example "Un ejemplo de uso sería:"

    ```bash
    nombre=$(zenity --entry \
                    --title="Datos del usuario" \
                    --width=250 \
                    --ok-label="Aceptar" \
                    --cancel-label="No te lo quiero decir" \
                    --text="¿Cual es tu nombre?")
    ans=$?
    if [ $ans -eq 0 ]
    then
        echo "Su nombre es: ${nombre}"
    else
        echo "No me quiere decir su nombre"
    fi
    ```

Para más informción:

- [Manual de Zenity](https://help.gnome.org/users/zenity/stable/index.html.es)
- [atareao con Linux. Zenity, díálogos para GNOME, Cinnamon, MATE...](https://atareao.es/tutorial/dialogos-para-scripts/zenity-dialogos-para-gnome-cinnamon-mate/)
- [DesdeLinux: Usos prácticos de la caja de dialog Zenity](https://blog.desdelinux.net/usos-practicos-de-la-caja-de-dialog-zenity/)


## Comandos para mejorar la salida por consola.

Existe una serie de comandos curiosos/divertido/inutiles que permiten mejorar nuestra interaccón con la consola. 

Uno que seguramente habrás visto en clase es la utilidad/fuente `figlet`, pero hay muchos otros similares como `cowsay`, `banner` o `espeak`.

Existen suficiente literatura sobre el tema, a continuación un par de enlaces que te servirán de introducción, a partir de ahí, tu sabrás en qué quieres invertir tu tiempo.

- [FUNNY COMMANDS IN LINUX](https://www.linkedin.com/pulse/funny-commands-linux-anudeepthi-kolagani)
- [20 curiosidades geeks para terminales linux](https://www.emezeta.com/articulos/20-curiosidades-geeks-para-terminales-linux)
- [Diez herramientas para divertirse con el arte ASCII en la terminal Linux](https://es.linux-console.net/?p=17876)


## Reflexión final

Finalmente cabe resaltar que todavía no hemos aprendido todas las posibilidades de programar con `bash script`. Aunque lo aprendido nos servirá para salir airosos de la mayoría de las situaciones, hay ocasiones en las que se requieren conocimientos más avanzados, y por ello, quien esté interesado puede buscar y consultar en los cientos de tutoriales y ejemplos que existen en internet.

Allí podrá encontrar nuevas posibilidades para programar con bash script. ¡Simplemente usa tu buscador favorito para ello!.