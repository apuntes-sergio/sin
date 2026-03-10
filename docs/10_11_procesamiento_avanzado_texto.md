---
title: Procesamiento Avanzado de Texto
description:  Fundamentos de la Línea de Comandos en Linux
---

Linux proporciona herramientas extremadamente potentes para **procesar, filtrar y transformar archivos de texto**. Combinando estos comandos con pipes, podemos realizar análisis complejos de datos, logs y archivos de configuración.

Estos comandos se denominan **filtros** porque reciben una entrada, la procesan/filtran/modifican y devuelven una salida transformada.

!!! tip "Filosofía Unix"
    Cada comando hace una cosa simple muy bien. La magia está en **combinarlos** mediante pipes para resolver problemas complejos.


## `grep` - Buscar y filtrar texto

El comando más utilizado para **buscar patrones** en archivos de texto.

Sintaxis: 

```bash
grep [opciones] patron [archivo(s)]
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-i` | Ignora mayúsculas/minúsculas |
| `-v` | Invierte la búsqueda (líneas que NO contienen el patrón) |
| `-c` | Cuenta líneas que coinciden |
| `-n` | Muestra número de línea |
| `-r` o `-R` | Búsqueda recursiva en directorios |
| `-h` | No muestra el nombre del archivo |
| `-w` | Busca palabras completas |
| `-A N` | Muestra N líneas después de la coincidencia |
| `-B N` | Muestra N líneas antes de la coincidencia |
| `-C N` | Muestra N líneas antes y después |

!!!example "Ejemplos básicos"

    ```bash
    grep "juan" alumnos.txt                    # Busca "juan" en el archivo
    grep "error" /var/log/syslog               # Busca "error" en el log
    grep -i "warning" log.txt                  # Ignora mayúsculas
    grep -v "comentario" script.sh             # Líneas que NO contienen "comentario"
    ```

!!!example "Contar coincidencias"

    ```bash
    grep -c "error" /var/log/syslog            # Cuenta líneas con "error"
    grep -i -c "valencia" alumnos.txt          # Cuenta alumnos de Valencia
    ```

!!!example "Con números de línea"

    ```bash
    grep -n "TODO" codigo.py                   # Muestra en qué líneas está "TODO"
    ```

!!!example "Búsqueda recursiva"

    ```bash
    grep -r "192.168.1.1" /etc/                # Busca IP en toda la carpeta /etc
    grep -r "password" ~/Documentos/           # Busca en todos los archivos
    grep -rh "error" /var/log/                 # Sin nombres de archivo
    ```

!!!example "Contexto de búsqueda"

    ```bash
    grep -A 3 "error" log.txt                  # Muestra 3 líneas después del error
    grep -B 2 "warning" log.txt                # Muestra 2 líneas antes del warning
    grep -C 2 "critical" log.txt               # Muestra 2 líneas antes y después
    ```

!!!example "Patrones especiales"

    ```bash
    grep "^inicio" archivo.txt                 # Líneas que EMPIEZAN con "inicio"
    grep "final$" archivo.txt                  # Líneas que TERMINAN con "final"
    grep "^$" archivo.txt                      # Líneas vacías
    grep -v "^$" archivo.txt                   # Líneas NO vacías
    grep "^#" archivo.conf                     # Líneas comentadas
    grep -v "^#" archivo.conf                  # Líneas NO comentadas
    ```

!!!example "Múltiples patrones"

    ```bash
    grep -e "error" -e "warning" log.txt       # Busca "error" O "warning"
    grep "error\|warning" log.txt              # Equivalente con expresión regular
    ```

!!!example "Palabras completas"

    ```bash
    grep -w "boo" file                         # Busca la palabra "boo" completa
                                               # No encontrará "boom" ni "bamboo"
    ```


## `sort` - Ordenar líneas

Ordena las líneas de un archivo según diferentes criterios.

Sintaxis:

```bash
sort [opciones] [archivo]
```

Opciones principales:

| Opción | Descripción |
|--------|-------------|
| `-r` | Orden inverso (descendente) |
| `-n` | Ordenación numérica |
| `-h` | Ordenación por tamaño humano (K, M, G) |
| `-k N` | Ordena por el campo N |
| `-t "X"` | Define "X" como delimitador de campos |
| `-u` | Elimina duplicados (unique) |
| `-f` | Ignora mayúsculas/minúsculas |

!!!example "Ejemplos básicos"

    ```bash
    sort archivo.txt                           # Ordena alfabéticamente
    sort -r archivo.txt                        # Orden inverso (Z-A)
    sort -n numeros.txt                        # Orden numérico
    sort -u archivo.txt                        # Ordena y elimina duplicados
    ```

!!!example "Ordenar por campos"

    Dado un archivo `alumnos.txt`:
    ```
    Juan:Lopez:25
    Ana:Garcia:22
    Pedro:Martinez:28
    ```

    ```bash
    sort -t ":" -k 2 alumnos.txt               # Ordena por apellido (campo 2)
    sort -t ":" -k 3 -n alumnos.txt            # Ordena por edad (campo 3, numérico)
    sort -t ":" -k 2,2 -k 1,1 alumnos.txt      # Por apellido, luego por nombre
    ```

!!!example "Ejemplos prácticos"

    ```bash
    # Ordenar usuarios por UID
    sort -t ":" -k 3 -n /etc/passwd

    # Ver archivos ordenados por tamaño
    ls -lh | sort -k 5 -h

    # Ordenar IPs en un log
    cat access.log | cut -d' ' -f1 | sort -u

    # Procesos ordenados por uso de CPU
    ps aux | sort -k 3 -n -r | head -10
    ```

---

## `uniq` - Eliminar líneas duplicadas

Detecta y elimina **líneas consecutivas** duplicadas.

!!! warning "Importante"
    `uniq` solo detecta duplicados **consecutivos**. Por eso se suele usar después de `sort`.

Sintaxis

```bash
uniq [opciones] [archivo]
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-c` | Cuenta ocurrencias de cada línea |
| `-d` | Muestra solo líneas duplicadas |
| `-u` | Muestra solo líneas únicas |
| `-i` | Ignora mayúsculas/minúsculas |

!!!example "Ejemplos básicos"

    ```bash
    uniq archivo.txt                           # Elimina duplicados consecutivos
    sort archivo.txt | uniq                    # Ordena y elimina duplicados
    sort archivo.txt | uniq -c                 # Cuenta ocurrencias
    ```

!!!example "Encontrar duplicados"

    ```bash
    sort lista.txt | uniq -d                   # Solo duplicados
    sort lista.txt | uniq -u                   # Solo únicos
    sort lista.txt | uniq -c | sort -nr        # Más repetidos primero
    ```

!!!example "Casos de uso"

    ```bash
    # Contar IPs únicas en log
    cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr

    # Palabras más frecuentes en un texto
    cat texto.txt | tr ' ' '\n' | sort | uniq -c | sort -nr | head -20

    # Usuarios únicos conectados
    who | cut -d' ' -f1 | sort | uniq
    ```


## `cut` - Extraer campos

Extrae **columnas** o **campos** específicos de cada línea.

Sintaxis:

```bash
cut [opciones] [archivo]
```

Opciones principales:

| Opción | Descripción |
|--------|-------------|
| `-c N` | Extrae caracteres en posición N |
| `-c N-M` | Extrae caracteres de posición N a M |
| `-f N` | Extrae campo N |
| `-d "X"` | Define "X" como delimitador de campo |
| `--output-delimiter="X"` | Define delimitador de salida |

Ejemplos: 

!!!example "Extraer por caracteres"

    ```bash
    cut -c 4-12 archivo.txt                    # Caracteres 4 al 12 de cada línea
    cut -c 1-5 alumnos.txt                     # Primeros 5 caracteres
    cut -c 10- archivo.txt                     # Desde carácter 10 hasta el final
    ```

!!!example "Extraer por campos"

    ```bash
    # Archivo /etc/passwd tiene campos separados por ":"
    cut -d ":" -f 1 /etc/passwd                # Nombres de usuario
    cut -d ":" -f 1,6 /etc/passwd              # Usuario y directorio home
    cut -d ":" -f 1,3,6 /etc/passwd            # Usuario, UID y home
    cut -d ":" -f 1-3 /etc/passwd              # Campos 1 al 3
    ```

!!!example "Cambiar delimitador de salida"

    ```bash
    cut -d ":" -f 1,6 /etc/passwd --output-delimiter=" -> "
    # Resultado: sergio -> /home/sergio
    ```

!!!example "Ejemplos prácticos"

    Archivo `datos.txt`:
    ```
    Nombre:Apellido:Edad:Ciudad
    Juan:Lopez:25:Valencia
    Ana:Garcia:22:Madrid
    Pedro:Martinez:28:Barcelona
    ```

    ```bash
    cut -d ":" -f 1 datos.txt                  # Solo nombres
    cut -d ":" -f 4 datos.txt                  # Solo ciudades
    cut -d ":" -f 1,3 datos.txt                # Nombres y edades
    ```


## `wc` - Contar palabras, líneas y caracteres

Cuenta líneas, palabras y caracteres de un archivo.

Sintaxis

```bash
wc [opciones] [archivo]
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-l` | Cuenta solo líneas |
| `-w` | Cuenta solo palabras |
| `-c` | Cuenta solo bytes |
| `-m` | Cuenta solo caracteres |

!!!example "Ejemplos"

    ```bash
    wc archivo.txt                             # Muestra líneas, palabras, caracteres
    wc -l archivo.txt                          # Solo líneas
    wc -w archivo.txt                          # Solo palabras
    wc -c archivo.txt                          # Solo bytes
    ```

!!!example "Casos de uso"

    ```bash
    # Contar usuarios del sistema
    wc -l /etc/passwd

    # Contar archivos en un directorio
    ls | wc -l

    # Contar líneas con "error" en log
    grep "error" /var/log/syslog | wc -l

    # Contar palabras únicas en un texto
    cat texto.txt | tr ' ' '\n' | sort -u | wc -l
    ```

## `nl` - Numerar líneas

Numera las líneas de un archivo (similar a `cat -n`).

Sintaxis

```bash
nl [opciones] archivo
```

!!!example "Ejemplos"

    ```bash
    nl archivo.txt                             # Numera líneas
    nl -ba archivo.txt                         # Numera todas (incluso vacías)
    ```


## `tr` - Transformar caracteres

Reemplaza o elimina caracteres.

Sintaxis

```bash
tr [opciones] conjunto1 [conjunto2]
```

!!! warning "tr no acepta archivos"
    `tr` lee solo de stdin, así que siempre se usa con redirección `<` o pipe `|`.

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-d` | Elimina caracteres del conjunto1 |
| `-s` | Elimina repeticiones consecutivas |

!!!example "Ejemplos básicos"

    ```bash
    # Minúsculas a mayúsculas
    cat archivo.txt | tr "[a-z]" "[A-Z]"
    tr "[a-z]" "[A-Z]" < archivo.txt

    # Mayúsculas a minúsculas
    echo "HOLA MUNDO" | tr "[A-Z]" "[a-z]"

    # Usando conjuntos predefinidos
    cat archivo.txt | tr "[:lower:]" "[:upper:]"
    ```

!!!example "Reemplazar caracteres"

    ```bash
    # Cambiar espacios por guiones
    echo "hola mundo" | tr " " "-"              # hola-mundo

    # Cambiar : por tabulador
    cat /etc/passwd | tr ":" "\t"

    # Cambiar múltiples caracteres
    echo "hola123" | tr "0-9" "X"               # holaXXX
    ```

!!!example "Eliminar caracteres"

    ```bash
    # Eliminar números
    echo "abc123def456" | tr -d "0-9"           # abcdef

    # Eliminar espacios
    echo "hola mundo" | tr -d " "               # holamundo

    # Eliminar saltos de línea
    cat archivo.txt | tr -d "\n"
    ```

!!!example "Eliminar repeticiones"

    ```bash
    # Eliminar espacios múltiples
    echo "hola    mundo" | tr -s " "            # hola mundo

    # Eliminar líneas vacías repetidas
    cat archivo.txt | tr -s "\n"

    # Eliminar letras repetidas
    echo "hoooola" | tr -s "o"                  # hola
    ```

!!!note "Conjuntos especiales"

    ```bash
    [:alnum:]    # Letras y dígitos
    [:alpha:]    # Letras
    [:digit:]    # Dígitos
    [:lower:]    # Minúsculas
    [:upper:]    # Mayúsculas
    [:space:]    # Espacios (espacios, tabs, saltos de línea)
    [:punct:]    # Signos de puntuación
    ```

!!!example "Ejemplos prácticos"

    ```bash
    # Eliminar puntuación
    cat texto.txt | tr -d "[:punct:]"

    # Convertir espacios en saltos de línea (una palabra por línea)
    cat texto.txt | tr " " "\n"

    # Eliminar todo excepto números
    echo "Tel: 123-456-789" | tr -cd "0-9"      # 123456789

    # Limpiar espacios extra
    cat archivo.txt | tr -s " "
    ```

---

## `sed` - Editor de flujo

Editor de texto en línea de comandos para transformaciones complejas.

Las funcionalidades más habituales son:

- Sustitución de texto (El uso más popular y la que vamos a trabajar nosotros). Permite buscar una palabra y cambiarla por otra.
- Eliminar líneas. Puedes filtrar contenido que no necesites basándote en el número de línea o en un patrón.
- Mostrar líneas específicas. Aunque cat o grep suelen usarse para esto, sed es muy preciso.
- Edición "in-place" (Modificar el archivo real). Normalmente, sed solo muestra el resultado en la terminal sin alterar el archivo original. Para guardar los cambios directamente, se usa la opción -i

Centrándonos en la **sustitución de texto**, la sintaxis básica es: 

```bash
sed 's/patron/reemplazo/' archivo
```

El comando de sustitución: `s`

```bash
# Sintaxis completa
sed 's/texto_buscar/texto_reemplazar/flags' archivo
```

Flags de sustitución

| Flag | Descripción |
|------|-------------|
| `g` | Global (todas las ocurrencias en la línea) |
| `i` | Ignora mayúsculas/minúsculas |
| `N` | Solo la ocurrencia N en cada línea |

!!!example "Ejemplos básicos"

    ```bash
    # Reemplazar primera ocurrencia de cada línea
    sed 's/viejo/nuevo/' archivo.txt

    # Reemplazar todas las ocurrencias
    sed 's/viejo/nuevo/g' archivo.txt

    # Reemplazar solo la segunda ocurrencia
    sed 's/viejo/nuevo/2' archivo.txt
    ```

!!!example "Casos de uso comunes"

    ```bash
    # Eliminar espacios al inicio de línea
    sed 's/^ *//' archivo.txt

    # Eliminar espacios al final de línea
    sed 's/ *$//' archivo.txt

    # Eliminar líneas vacías
    sed '/^$/d' archivo.txt

    # Eliminar comentarios (líneas que empiezan por #)
    sed '/^#/d' archivo.txt

    # Reemplazar múltiples espacios por uno
    sed 's/  */ /g' archivo.txt
    ```

!!!example "Ejemplos prácticos"

    ```bash
    # Cambiar formato de fecha
    echo "2024-02-05" | sed 's/-/\//g'          # 2024/02/05

    # Eliminar extensión de archivos
    echo "archivo.txt" | sed 's/\.txt$//'       # archivo

    # Añadir texto al inicio de cada línea
    sed 's/^/> /' archivo.txt                   # Añade "> " al inicio

    # Ignorar mayúsculas/minúsculas
    sed 's/error/ERROR/gi' log.txt
    ```


**Edición "in-place" (Modificar el archivo real)**:  Normalmente, sed solo muestra el resultado en la terminal sin alterar el archivo original. Para guardar los cambios directamente, se usa la opción `-i`.


!!! Example "Editar archivo in-place"
    ```bash
    # CUIDADO: Modifica el archivo original
    sed -i 's/viejo/nuevo/g' archivo.txt

    # Con backup
    sed -i.bak 's/viejo/nuevo/g' archivo.txt    # Crea archivo.txt.bak
    ```

Hay muchas más opciones en el comando `sed`, pero nosotros lo dejaremos aquí.



## `paste` - Unir líneas de archivos

Junta las líneas de dos o más archivos lado a lado.

Sintaxis

```bash
paste [opciones] archivo1 archivo2
```

Opciones

| Opción | Descripción |
|--------|-------------|
| `-d "X"` | Usa "X" como delimitador (por defecto es tabulador) |
| `-s` | Une todas las líneas del archivo en una sola |

!!!example "Ejemplos"

    Archivo `nombres.txt`:
    ```
    Juan
    Ana
    Pedro
    ```

    Archivo `apellidos.txt`:
    ```
    Lopez
    Garcia
    Martinez
    ```

    ```bash
    paste nombres.txt apellidos.txt
    # Resultado:
    # Juan    Lopez
    # Ana     Garcia
    # Pedro   Martinez

    paste -d ":" nombres.txt apellidos.txt
    # Resultado:
    # Juan:Lopez
    # Ana:Garcia
    # Pedro:Martinez
    ```


## `diff` - Comparar archivos

Compara dos archivos línea por línea y muestra las diferencias.

Sintaxis

```bash
diff [opciones] archivo1 archivo2
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-y` | Muestra comparación lado a lado |
| `-i` | Ignora mayúsculas/minúsculas |
| `-w` | Ignora espacios en blanco |
| `-u` | Formato unificado (usado en patches) |

!!!example "Ejemplos"

    ```bash
    diff archivo1.txt archivo2.txt             # Diferencias básicas
    diff -y archivo1.txt archivo2.txt          # Lado a lado
    diff -u archivo1.txt archivo2.txt          # Formato unificado
    ```

---

## Combinaciones Potentes

!!!example "Análisis de logs"

    ```bash
    # Top 10 IPs más frecuentes
    cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr | head -10

    # Errores únicos del día
    grep "$(date +%Y-%m-%d)" /var/log/syslog | grep "error" | cut -d':' -f4- | sort -u

    # Contar tipos de errores
    grep "error" /var/log/syslog | cut -d':' -f4 | sort | uniq -c | sort -nr
    ```

!!!example "Procesamiento de archivos CSV"

    ```bash
    # Ver columna específica
    cut -d',' -f2 datos.csv

    # Ordenar por columna 3
    sort -t',' -k3 datos.csv

    # Eliminar duplicados por campo 1
    sort -t',' -k1 -u datos.csv
    ```

!!!example "Limpieza de texto"

    ```bash
    # Eliminar líneas vacías y comentarios
    cat script.sh | grep -v "^$" | grep -v "^#"

    # Convertir a minúsculas y eliminar puntuación
    cat texto.txt | tr "[:upper:]" "[:lower:]" | tr -d "[:punct:]"

    # Contar palabras únicas
    cat texto.txt | tr " " "\n" | tr "[:upper:]" "[:lower:]" | sort | uniq | wc -l
    ```

!!!example "Análisis de sistema"

    ```bash
    # Top 10 comandos más usados
    history | cut -c 8- | cut -d' ' -f1 | sort | uniq -c | sort -nr | head -10

    # Puertos abiertos únicos
    netstat -tuln | grep LISTEN | awk '{print $4}' | cut -d':' -f2 | sort -u

    # Procesos por usuario
    ps aux | tail -n +2 | cut -d' ' -f1 | sort | uniq -c | sort -nr
    ```

---

## Ejemplo Completo: Análisis de Archivo de Alumnos

Supongamos un archivo `alumnos.txt`:
```
Juan:Lopez:25:Valencia
Ana:Garcia:22:Madrid
Pedro:Martinez:28:Valencia
Maria:Lopez:23:Barcelona
Carlos:Sanchez:26:Valencia
```

!!!example "Operaciones comunes"

    ```bash
    # Listar solo nombres
    cut -d':' -f1 alumnos.txt

    # Alumnos de Valencia
    grep "Valencia" alumnos.txt

    # Alumnos de Valencia ordenados por nombre
    grep "Valencia" alumnos.txt | sort

    # Contar alumnos de Valencia
    grep "Valencia" alumnos.txt | wc -l

    # Ciudades únicas
    cut -d':' -f4 alumnos.txt | sort -u

    # Alumnos por ciudad (contando)
    cut -d':' -f4 alumnos.txt | sort | uniq -c

    # Apellido más común
    cut -d':' -f2 alumnos.txt | sort | uniq -c | sort -nr | head -1

    # Edad promedio (necesita awk o bc)
    cut -d':' -f3 alumnos.txt | paste -sd+ | bc

    # Alumnos ordenados por edad
    sort -t':' -k3 -n alumnos.txt

    # Nombres y apellidos en mayúsculas
    cut -d':' -f1,2 alumnos.txt | tr "[:lower:]" "[:upper:]"
    ```


## Resumen de Comandos

| Comando | Función principal | Ejemplo común |
|---------|-------------------|---------------|
| `grep` | Buscar/filtrar patrones | `grep "error" log.txt` |
| `sort` | Ordenar líneas | `sort -n numeros.txt` |
| `uniq` | Eliminar duplicados consecutivos | `sort file \| uniq` |
| `cut` | Extraer campos/columnas | `cut -d':' -f1 /etc/passwd` |
| `wc` | Contar líneas/palabras | `wc -l archivo.txt` |
| `tr` | Transformar caracteres | `tr "[a-z]" "[A-Z]"` |
| `sed` | Editar/reemplazar texto | `sed 's/old/new/g'` |
| `paste` | Unir archivos lado a lado | `paste f1 f2` |
| `diff` | Comparar archivos | `diff f1.txt f2.txt` |
| `nl` | Numerar líneas | `nl archivo.txt` |

---

## Ejercicios Prácticos

!!! example "Ejercicio 1: grep y conteo"
    1. Busca tu nombre de usuario en `/etc/passwd`
    2. Cuenta cuántos usuarios hay en el sistema
    3. Busca todas las líneas que contengan "bash" en `/etc/passwd`
    4. Cuenta cuántos procesos de tu usuario están corriendo

!!! example "Ejercicio 2: Procesamiento con cut y sort"
    1. Extrae solo los nombres de usuario de `/etc/passwd`
    2. Extrae nombres y directorios home
    3. Ordena los usuarios alfabéticamente
    4. Ordena los usuarios por UID numéricamente

!!! example "Ejercicio 3: Análisis de texto"
    1. Crea un archivo de texto con varias líneas
    2. Cuenta cuántas palabras tiene
    3. Convierte todo a mayúsculas
    4. Elimina todas las vocales del texto
    5. Cuenta cuántas palabras únicas hay

!!! example "Ejercicio 4: Combinación avanzada"
    Usando `/var/log/syslog` (o cualquier log):

    1. Encuentra las 5 palabras más repetidas
    2. Cuenta cuántas líneas contienen "error" o "warning"
    3. Extrae solo las horas de los eventos (usando cut)
    4. Determina en qué hora hay más actividad

!!! example "Ejercicio 5: Procesamiento de datos"
    Crea un archivo CSV con datos de productos (nombre, precio, categoría):
    
    1. Ordena por precio
    2. Extrae solo los nombres de productos
    3. Cuenta productos por categoría
    4. Encuentra el producto más caro


## Recursos Adicionales

**Expresiones regulares**

Para usar `grep` y `sed` de forma avanzada, aprende expresiones regulares básicas:

| Expresión | Uso |
| --- | --- |
| `^`   | Inicio de línea |
| `$`   | Final de línea |
| `.`   | Cualquier carácter |
| `*`   | 0 o más del anterior |
| `+`   | 1 o más del anterior |
| `[]`  | Conjunto de caracteres |
| `[^]` | Conjunto negado |
| `\`   | Escape |

**Comandos relacionados (para aprender más)**

| Comando | Descripción |
| --- | --- |  
| `awk` | Procesamiento de texto más potente |
| `perl` | Scripting con expresiones regulares avanzadas |
| `join` | Unir archivos por campo común |
| `comm` | Comparar archivos ordenados línea por línea |
| `column` | Formatear salida en columnas |

