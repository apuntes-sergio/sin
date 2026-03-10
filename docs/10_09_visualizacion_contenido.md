---
title: Visualización de Contenido
description:  Fundamentos de la Línea de Comandos en Linux
---

Uno de los trabajos más frecuentes en la terminal es **visualizar el contenido de archivos de texto**. Linux proporciona varios comandos para ver archivos completos, parcialmente, o de forma interactiva.

En este apartado veremos los comandos básicos para visualizar contenido. En el siguiente apartado profundizaremos en el procesamiento avanzado de texto.

---

## `cat` - Concatenar y mostrar

El comando más básico para mostrar el contenido de archivos.

Sintaxis

```bash
cat [opciones] archivo(s)
```

Uso básico

```bash
cat archivo.txt                        # Muestra el contenido completo
cat archivo1.txt archivo2.txt          # Muestra ambos archivos secuencialmente
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-n` | Numera todas las líneas |
| `-b` | Numera solo líneas no vacías |
| `-s` | Suprime líneas vacías repetidas |
| `-A` | Muestra caracteres no imprimibles |

!!! example "Ejemplos básicos"

    ```bash
    cat -n /etc/passwd                     # Muestra con números de línea
    cat -b documento.txt                   # Numera solo líneas con contenido
    cat -s archivo.txt                     # Elimina líneas vacías extra
    ```

!!! example "Concatenar archivos"

    ```bash
    cat file1.txt file2.txt file3.txt > archivo_total.txt
    # Une los tres archivos en uno solo
    ```

!!!example "Crear archivos rápidamente"

    ```bash
    cat > nuevo.txt
    Escribe el contenido aquí
    Puedes escribir varias líneas
    Ctrl+D para terminar
    ```

!!! warning "Limitación de cat"
    Si el archivo es muy largo, `cat` muestra todo de golpe y no podrás ver el principio. Para archivos largos, usa `less` o `more`.

---

## `more` - Ver página a página

Muestra el contenido de un archivo **pantalla a pantalla**, esperando a que presiones una tecla para continuar.

Sintaxis

```bash
more archivo
```

Navegación

| Tecla | Acción |
|-------|--------|
| ++space++ | Avanza una página |
| ++enter++ | Avanza una línea |
| ++q++ | Salir |
| `b` | Retrocede una página (solo en algunos sistemas) |
| `/texto` | Busca "texto" hacia adelante |
| `n` | Repite última búsqueda |

!!!example "Ejemplos"

    ```bash
    more /etc/services                     # Ver archivo grande
    ls -la /usr/bin | more                # Ver listado largo página a página
    ```

!!! info "Porcentaje de avance"
    `more` muestra en la parte inferior el porcentaje del archivo que has visto.

---

## `less` - Visor mejorado

Similar a `more` pero con **más funcionalidades**. Permite desplazarse hacia atrás y tiene búsqueda más potente.

Sintaxis

```bash
less archivo
```

Navegación

| Tecla | Acción |
|-------|--------|
| ++space++ o ++page-down++ | Avanza una página |
| ++page-up++ o `b` | Retrocede una página |
| ++arrow-down++ o ++enter++ | Avanza una línea |
| ++arrow-up++ | Retrocede una línea |
| `g` | Ir al inicio del archivo |
| `G` | Ir al final del archivo |
| `/texto` | Buscar "texto" hacia adelante |
| `?texto` | Buscar "texto" hacia atrás |
| `n` | Siguiente coincidencia |
| `N` | Coincidencia anterior |
| ++q++ | Salir |

!!!example "Ejemplos"

    ```bash
    less /var/log/syslog                   # Ver log del sistema
    ps aux | less                          # Ver procesos con navegación
    man bash | less                        # Los manuales usan less por defecto
    ```

Ventajas de `less` sobre `more`

  - ✅ Permite retroceder libremente
  - ✅ No carga todo el archivo en memoria (más rápido con archivos grandes)
  - ✅ Búsqueda bidireccional
  - ✅ Más opciones de navegación

!!! tip "less is more"
    El nombre `less` es un juego de palabras: "less is more" (menos es más). Aunque se llama "less", hace más cosas que "more" 😄

---

## `head` - Primeras líneas

Muestra las **primeras líneas** de un archivo (por defecto 10).

Sintaxis:
```bash
head [opciones] archivo
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-n N` | Muestra las primeras N líneas |
| `-n -N` | Muestra todo excepto las últimas N líneas |
| `-c N` | Muestra los primeros N bytes |

!!!example "Ejemplos básicos"

```bash
head archivo.txt                       # Primeras 10 líneas (por defecto)
head -n 5 archivo.txt                  # Primeras 5 líneas
head -5 archivo.txt                    # Forma corta (igual que -n 5)
```

!!!example "Ver el principio de múltiples archivos"

    ```bash
    head archivo1.txt archivo2.txt
    # Muestra las primeras líneas de cada archivo con su nombre
    ```

!!!example "Excluir últimas líneas"

    ```bash
    head -n -5 archivo.txt                 # Todo excepto las últimas 5 líneas
    head -n -1 archivo.txt                 # Todo excepto la última línea
    ```

!!!example "Casos de uso prácticos"

    ```bash
    # Ver los primeros usuarios del sistema
    head /etc/passwd

    # Ver el inicio de un log
    head -20 /var/log/syslog

    # Primeros 100 bytes de un archivo
    head -c 100 documento.txt
    ```


## `tail` - Últimas líneas

Muestra las **últimas líneas** de un archivo (por defecto 10).

Sintaxis:

```bash
tail [opciones] archivo
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-n N` | Muestra las últimas N líneas |
| `-n +N` | Muestra desde la línea N hasta el final |
| `-f` | Modo "follow" (sigue mostrando nuevas líneas) |
| `-F` | Como `-f` pero reintenta si el archivo se recrea |
| `-c N` | Muestra los últimos N bytes |

!!!example "Ejemplos básicos"

    ```bash
    tail archivo.txt                       # Últimas 10 líneas (por defecto)
    tail -n 20 archivo.txt                 # Últimas 20 líneas
    tail -20 archivo.txt                   # Forma corta
    ```

!!!example "Desde una línea específica"

    ```bash
    tail -n +5 archivo.txt                 # Desde la línea 5 hasta el final
    tail -n +1 archivo.txt                 # Todo el archivo (desde línea 1)
    tail -n +10 archivo.txt                # Desde la línea 10 hasta el final
    ```

!!!example "Modo seguimiento: `-f`"

    La opción más útil de `tail`: **seguir mostrando** las nuevas líneas que se añaden al archivo en tiempo real.

    ```bash
    tail -f /var/log/syslog                # Muestra nuevas líneas según se escriben
    tail -f /var/log/apache2/access.log    # Monitorizar accesos web en tiempo real
    ```

    **Para salir:** ++ctrl+c++

!!! tip "Monitorización en tiempo real"
    `tail -f` es **esencial** para administradores de sistemas. Permite ver logs en tiempo real y detectar problemas al instante.

!!!example "Ejemplos prácticos"

    ```bash
    # Ver las últimas entradas del log
    tail -50 /var/log/syslog

    # Monitorizar un log filtrando errores
    tail -f /var/log/syslog | grep "error"

    # Ver últimas 100 líneas de varios logs
    tail -n 100 /var/log/syslog /var/log/auth.log

    # Ver últimos accesos web
    tail -f /var/log/apache2/access.log
    ```


## Combinaciones Potentes

`head` + `tail`: Extraer líneas específicas

!!!example "Para extraer un rango de líneas específico:"

    ```bash
    # Líneas de la 10 a la 20
    head -20 archivo.txt | tail -11

    # Líneas de la 5 a la 10
    head -10 archivo.txt | tail -6
    ```

    **Explicación:**
    - `head -20`: Obtiene las primeras 20 líneas
    - `tail -11`: De esas 20, toma las últimas 11 (líneas 10-20)

`cat` + `grep`: Buscar contenido

!!!example "`cat` + `grep`: Buscar contenido (lo veremos en detenimiento más adelante)"

    ```bash
    # Ver líneas que contienen "error"
    cat /var/log/syslog | grep "error"

    # O directamente
    grep "error" /var/log/syslog
    ```

!!!example "less con búsqueda"

    ```bash
    # Abrir archivo y buscar
    less /etc/services
    # Dentro de less, presiona /http y Enter para buscar "http"
    ```

---

## Ejemplos Prácticos Completos

En estos ejemplos, vamos a ver el uso de `|` *(pipe)*, que veremos más detenidamente en el siguiente apartado. Por ello, para comprender estos usos es mejor revisar la siguiente sección.

!!!example "Escenario 1: Análisis de logs"

    ```bash
    # Ver últimas 50 líneas del log de sistema
    tail -50 /var/log/syslog

    # Buscar errores en las últimas 100 líneas
    tail -100 /var/log/syslog | grep -i error

    # Monitorizar log en tiempo real filtrando por "failed"
    tail -f /var/log/auth.log | grep --line-buffered "Failed"

    # Ver primeras líneas del log (cuando empezó)
    head -20 /var/log/syslog
    ```

!!!example "Escenario 2: Revisar archivos de configuración"

    ```bash
    # Ver archivo de configuración completo
    cat /etc/ssh/sshd_config

    # Ver solo líneas activas (sin comentarios)
    cat /etc/ssh/sshd_config | grep -v "^#" | grep -v "^$"

    # Buscar configuración específica
    grep "Port" /etc/ssh/sshd_config

    # Ver con números de línea
    cat -n /etc/hosts
    ```

!!!example "Escenario 3: Revisar archivos grandes"

    ```bash
    # Contar líneas totales
    wc -l archivo_grande.txt

    # Ver primeras 100 líneas
    head -100 archivo_grande.txt

    # Ver últimas 100 líneas
    tail -100 archivo_grande.txt

    # Ver desde línea 1000 hasta el final
    tail -n +1000 archivo_grande.txt | less
    ```

!!!example "Escenario 4: Monitorización en tiempo real"

    ```bash
    # Ver log de Apache en tiempo real
    tail -f /var/log/apache2/access.log

    # Monitorizar múltiples logs simultáneamente
    tail -f /var/log/syslog /var/log/auth.log

    # Monitorizar y filtrar IPs específicas
    tail -f /var/log/apache2/access.log | grep "192.168.1"
    ```



## Tabla Comparativa

| Comando | Uso principal | ¿Navega? | ¿Archivos grandes? |
|---------|---------------|----------|-------------------|
| `cat` | Mostrar archivos pequeños | ❌ | ❌ |
| `more` | Ver página a página | ⚠️ (solo adelante) | ✅ |
| `less` | Visor completo | ✅ (ambas direcciones) | ✅ |
| `head` | Primeras líneas | ❌ | ✅ |
| `tail` | Últimas líneas | ❌ | ✅ |
| `tail -f` | Monitorización en tiempo real | ❌ | ✅ |

---

## Resumen de Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `cat` | Muestra archivo completo | `cat archivo.txt` |
| `cat -n` | Muestra con números de línea | `cat -n /etc/passwd` |
| `more` | Página a página (básico) | `more archivo.txt` |
| `less` | Visor avanzado | `less /var/log/syslog` |
| `head` | Primeras líneas | `head -20 archivo.txt` |
| `head -n -N` | Todo menos últimas N líneas | `head -n -5 archivo.txt` |
| `tail` | Últimas líneas | `tail -30 archivo.txt` |
| `tail -n +N` | Desde línea N hasta el final | `tail -n +10 archivo.txt` |
| `tail -f` | Seguimiento en tiempo real | `tail -f /var/log/syslog` |


!!! quote "Filosofía Unix"
    "Haz una cosa y hazla bien". Estos comandos hacen una cosa simple (mostrar texto) pero la combinación de ellos con pipes crea posibilidades infinitas.

---

## Ejercicios Prácticos

!!! example "Ejercicio 1: Visualización básica"
    1. Muestra el contenido de `/etc/hostname`
    2. Muestra `/etc/passwd` con números de línea
    3. Ve `/etc/services` página a página con `less`
    4. Busca "http" dentro de `/etc/services` usando less

!!! example "Ejercicio 2: `head` y `tail`"
    1. Muestra los primeros 5 usuarios de `/etc/passwd`
    2. Muestra los últimos 5 usuarios de `/etc/passwd`
    3. Muestra las líneas 10 a 20 de `/etc/passwd`
    4. Muestra todo `/etc/passwd` excepto la última línea

!!! example "Ejercicio 3: Monitorización"
    1. Monitoriza `/var/log/syslog` en tiempo real
    2. Monitoriza y filtra solo las líneas que contengan "error"
    3. Guarda las últimas 50 líneas del log en un archivo
    4. Cuenta cuántas líneas tiene el archivo `/etc/services`

!!! example "Ejercicio 4: Combinaciones"
    1. Crea un archivo con 100 líneas numeradas
    2. Extrae solo las líneas 40 a 60
    3. Concatena dos archivos y muestra el resultado
    4. Ver los 10 procesos que más CPU consumen