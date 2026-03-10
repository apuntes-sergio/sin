---
title: Fundamentos del scripting en linux
description: Scripts en Bash para la administración de linux
---


## ¿Qué es un script?

!!!note "nota"
    Es imprescindible un conocimiento alto de “shell scripting” para aquellos que quieran dominar la administración de sistemas

Un script no es más que fichero de texto que contiene una serie de instrucciones y órdenes que se ejecutarán en la línea de comandos de forma seguida. Estas órdenes funcionarían de la misma manera si las fuésemos introduciendo en la línea de comandos nosotros.

En el arranque de una máquina Linux, se ejecuta el shell script `/etc/init.d` (dependiendo del sistema de inicio utilizado) para restaurar la configuración del sistema y activar los servicios. Es muy importante conocer el funcionamiento de dichos scripts para analizar el comportamiento de la máquina y realizar posibles cambios.

El procedimiento para **crear un programa o comando** necesita de los siguientes pasos:

1. Escribir el código de nuestra herramienta. Si ya sabes qué necesitas o qué quieres, escribe el código fuente de tu herramienta sea la que sea y sea cual sea el lenguaje que hayas elegido. Por ejemplo, puedes hacerlo en *C*, *Python*, *Perl*, o como un script para *Bash*.
2. Compilar nuestro código fuente para generar el ejecutable. Por ejemplo, si es en *C* o *C++,* etc., puedes hacerlo con ayuda del compilador ***gcc*** de una forma fácil. Si se trata de un lenguaje interpretado, como *Python*, *Perl*, *Ruby*, etc., tendremos que tener instalado el intérprete de éste y hacer ejecutable el fichero con el código fuente. Ese también es el caso de un script para Bash, en este caso el intérprete es el propio *Bash* y para hacerlo ejecutable podemos usar: `chmod +x nombre_script.sh`
3. Una vez compilado o tenemos el fichero ejecutable, lo copiamos o movemos a una ruta incluida en la variable de entorno `$PATH`, como por ejemplo `/usr/bin`. Puedes ver las rutas con `echo $PATH`. Con esto podemos ejecutarlo simplemente introduciendo su nombre y no tendremos que poner la ruta absoluta.

Para ejecutar un script tenemos 2 alternativas. La primera es llamar al intérprete pasándole el fichero de texto que contiene el script como primer parámetro, para esto sólo requiere que el archivo tenga permiso de lectura. 

!!!example "Ejemplos de forma de ejecutar un script"
    ```bash
    sh script.sh    # se ejecuta utilizando la shell `sh`
    bash script.sh  # se ejecuta utilizando la shell `bash`
    /bin/dash script_ejemplo.sh  # también
    ```

La tercera es indicando en el archivo la ruta del intérprete de comandos en la primera línea de la siguiente forma: `#!/bin/bash` (en este caso `/bin/bash` es la ruta al intérprete bash, que será el que utilicemos, pero aquí se puede poner la ruta a nuestro intérprete favorito). En este último caso basta con darle permiso de ejecución y llamar directamente al script

```
./script.sh
```

Un script simple puede tener una serie de comandos seguidos y ya está:

!!!example "Ejemplo script básico"
    ```bash 
    #!/bin/bash
    mkdir dir1
    cd dir1
    touch arch1
    cd ..
    ln -s dir1/arch1 enl1
    ```

!!!example "Ejemplo: Script que borra los ficheros `.log` en `/var/log`"

    ```bash
    #!/bin/bash

    LOG_DIR=/var/log

    cd $LOG_DIR
    cat /dev/null > messages
    cat /dev/null > wtmp
    echo "Logs cleaned up."
    exit
    ```

## Empezando el script con el sha-­bang (`#!`) 

Este símbolo (`#!`) en la cabecera del script, le indica a tu sistema este fichero (script) es un conjunto de comandos a interpretar por el intérprete de comandos que se indica. Inmediatamente a continuación del ***sha-­bang*** está el *pathname*. Éste es el path al programa que interpreta los comandos en el script, sea un shell, un lenguaje de programación o una utilidad. Este intérprete de comandos, entonces, ejecuta los comandos comentarios.

```bash 
#!/bin/sh
#!/bin/bash
#!/usr/bin/perl
#!/usr/bin/python3
#!/usr/bin/tcl
#!/bin/sed -f
#!/bin/awk -f
```


## Comentarios

Un comentario en un script es una línea que será ignorada por el intérprete de comandos. En estas líneas podemos poner cualquier nota o apunte que nos resulte útil, por ejemplo, para recordar en el futuro que es lo que realizaba una parte del script que hicimos hace tiempo.

Un comentario siempre empieza por '`#`', lo que haya detrás será completamente ignorado hasta que pasemos a la siguiente línea:


!!! example "Ejemplo scripts con comentarios"
    ```bash
    #!/bin/bash
    # Este es un comentario cualquiera

    # Creamos el directorio “dir1” y dentro creamos el archivo “arch1”
    mkdir dir1
    cd dir1
    touch arch1
    cd ..

    # Creamos un enlace a dir1/arch1
    ln -s dir1/arch1 enl1
    ```

## Variables

Una variable se puede decir que es un nombre que se le da a un dato cualquiera para luego poder acceder a él sin problemas. Por ejemplo, si queremos preguntarle a alguien por su nombre en un script, habrá que almacenar ese nombre en una variable, ya que no podemos saber el nombre que introducirá. De esa forma podremos acceder a su nombre sin conocerlo, simplemente conociendo el nombre de la variable es suficiente:

!!!example "Ejemplo uso variables"
    ```bash
    #!/bin/bash
    echo “Escribe tu nombre”

    # Almacenamos el nombre que escriba el usuario en una variable llamada nombre
    read nombre

    # Ahora no sabemos su nombre, pero cuando lo escriba lo tendremos guardado en nombre 
    # correcto
    echo “Hola $nombre”
    #incorrecto
    echo “Hola nombre”

    # También podemos dar el valor a una variable de esta forma
    var=”Hola”
    echo “$var $nombre”
    ```

Para acceder al valor de una variable hay que ponerle el símbolo **`$`** delante y para darle un nuevo valor no hace falta. Si queremos acceder a una variable, pero tiene texto pegado, podemos encerrar el nombre entre llaves '`{}`' detrás del símbolo `$`.

!!!example "Ejemplo uso de variables"
    ```bash
    #!/bin/bash
    var=”arch”
    #Queremos crear un archivo llamado archnombre
    touch ${var}nombre
    #Así no busca una variable que se llame 'varnombre', sino 'var'
    ```

Si queremos que un carácter especial como `$` se interprete como una carácter normal y no como un identificador de variables, hay que “*escaparlo*”, es decir, poner el símbolo '`\`' delante:

!!!example "Ejemplo uso de variables. Usando el carácter `$`"
    ```bash
    #!/bin/bash
    var=20
    echo “Esto me ha costado $var \$.”
    #Esto mostraría la frase: Esto me ha costado 20 $.
    ```

Debemos tener en cuenta que hay varios tipos de variables en tu script de `Bash`:

- Variables de usuario
- Variables de entorno
- Variables especiales

> **Nota**: Tradicionalmente se sigue la regla de que las variables de entorno definidas por el sistema irán en mayúsculas (pueden usar números y _ ), mientras que las variables definidas por las aplicaciones, usarán únicamente minúsculas (y por supuesto número y símbolos permitidos)

### Variables de usuario/script

Las variables de usuario son las que hemos visto anteriormente; un usuario las define en su terminal o en un script, y mientras no cierre el terminal o no finalice el script esta variable perdurará en el tiempo.

### Variables de entorno

Estas variables  vienen definidas por defecto en Bash, y que se refieren al entorno en el que trabajas. Para conocer estas variables puedes utilizar el comando `env` (de environtment)

Si lo ejecutas puedes encontrar algunas variables tan interesantes como:

- `$SHELL`: que indica el shell que estás ejecutando
- `$EDITOR`: donde está indicado el editor por defecto. En mi caso vim.
- `$HOME`: la ruta del usuario. En mi caso `/home/sergio`
- `$USERNAME`: el nombre del usuario. En mi caso `sergio`
- `$PATH`: la ruta por defecto donde encontrar binarios, etc.
- `$PS1`: prompt primario. El siguiente ejemplo modifica el prompt, utilizando colores, el nombre del usuario aparece en color verde, y el resto en negro: `PS1=’[\033[0;32;48m\u\033[0;30;48m@\h \W ] \ $’` 
- `$PS2`: prompt secundario. 
- `$LOGNAME`: nombre del usuario. 
- `$PWD`: directorio activo. 
- `$TERM`: el tipo de la terminal actual.
- `$IFS`: el Separador Interno de Campo que se emplea para la división de palabras tras la expansión y para dividir líneas en palabras con la orden interna read. El valor por defecto es un espacio en blanco ("` `”). Se puede utilizar uno diferente. El siguiente ejemplo lo cambia a dos puntos ( `:` ) 

    !!!example ""
        ```bash
        IFS=: 
        ```

!!!note "nota"
    `$*` o `$@` usa el primer carácter almacenado en IFS. 

    Esto lo veremos más adelante en el apartado de parámetros.


Para **crear una nueva variable de entorno** en Linux seguiremos la siguiente sintaxis básica :

!!!example ""
    ```bash
    export VAR="value"
    ```

Vamos a desglosarlo:

- `export`: el comando utilizado para crear la variable.
- `VAR`: el nombre de la variable.
- `=`: indica que la siguiente sección es el valor.
- `«value»`: el valor real

Por ejemplo:

!!!example "Por ejemplo"
    ```bash
    export MAIN_USER="Sergio Rey"
    ```

Veamos ahora cómo podrías **cambiar el valor de una variable de entorno**, por ejemplo la variable TZ (zona horaria):

Primero, chequea la hora:

!!!example ""
    ```bash
    date
    ```

El comando debería mostrar la hora actual.

Luego de esto, puedes usar el comando de exportación para alterar la zona horaria:

!!!example ""
    ```bash
    export TZ="US/Pacific"
    ```

Ahora que modificaste el valor de la variable, puedes verificar la hora nuevamente utilizando el comando `date`, que generará una hora diferente, acorde a los cambios realizados en la variable de entorno Linux.

Para deshacer un cambio realizado sobre una variable de entorno, y volver a su valor por defecto, lo podemos hacer con el comando `unset`. La sintaxis del comando se ve de la siguiente manera:

!!!example ""
    ```bash
    unset VAR
    ```

Las partes del comando son:

    - `unset`: el comando en sí
    - `VAR`: la variable cuyo valor queremos revertir

Como ejemplo, veamos cómo revertir la variable de zona horaria:

```bash
unset TZ
```

Esto aplicará a la hora su valor predeterminado, el cual puedes verificar utilizando el comando `date` una vez más.

Configurar y revertir una variable de entorno de Linux desde la línea de comandos **afecta solo tus sesiones actuales**. Si deseas que la configuración se mantenga en todos los inicios de sesión, debes definir las variables de entorno en tu archivo de inicialización personal, es decir `.bashrc`. No obstante las variables de entorno no se encuentran definidas únicamente en este fichero.


## Variables especiales de bash

En Bash, hay algunas variables especiales y que están definidas por defecto, y que se refieren al script, al que ha ejecutado el script, o a la máquina en la que se ha ejecutado el script. Así, algunas de ellas son las siguientes:

- `$0`, `$1`...: parámetros que veremos más adelante
- `$?`: la salida del último proceso que se ha ejecutado
- `$USER`: el nombre del usuario que ha ejecutado el script
- `$HOSTNAME`: se refiere al hostname ejecutando el script
- `$SECONDS`: se refiere al tiempo transcurrido desde que se inició el script, contabilizado en segundos.
- `$RANDOM`: devuelve un número aleatorio cada vez que se lee esta variable.
- `$LINENO`: indica el número de líneas que tiene nuestro script.
