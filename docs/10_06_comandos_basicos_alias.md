---
title: Comandos Básicos y Alias
description:  Fundamentos de la Línea de Comandos en Linux
---

Un **comando** puede ser una de estas **cuatro cosas**:

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Programa ejecutable** | Archivos binarios o scripts en `/usr/bin`, `/bin`, etc. | `ls`, `cp`, `firefox` |
| **Comando interno (builtin)** | Comandos integrados en el propio shell | `cd`, `echo`, `exit` |
| **Función de shell** | Scripts personalizados cargados en el entorno | Funciones definidas en `.bashrc` |
| **Alias** | Atajos personalizados para otros comandos | `ll`, `la` |

## Comandos de Información

### `type` - Identificar el tipo de comando

Muestra qué tipo de comando es el que indicamos.

```bash
type cd
```
**Salida:** `cd is a shell builtin`

```bash
type ls
```
**Salida:** `ls is aliased to 'ls --color=auto'`

```bash
type firefox
```
**Salida:** `firefox is /usr/bin/firefox`

!!! tip "Uso práctico"
    Útil para saber si un comando es interno del shell o un programa externo, y para descubrir si ya existe un alias.

### `which` - Localizar un ejecutable

Muestra la ruta completa de un programa ejecutable.

```bash
which python3
```
**Salida:** `/usr/bin/python3`

```bash
which firefox
```
**Salida:** `/usr/bin/firefox`

!!! info "Diferencia con type"
    - `type`: Funciona con cualquier tipo de comando (alias, builtins, funciones)
    - `which`: Solo localiza programas ejecutables

## Alias: Atajos Personalizados

Los **alias** son comandos personalizados que funcionan como atajos para ejecutar otros comandos (generalmente más complejos o largos).

Por qué usar alias: 

- ⚡ **Rapidez**: Escribe menos para hacer más
- 🎯 **Comodidad**: Comandos frecuentes en una sola palabra
- 🛡️ **Seguridad**: Añade confirmaciones a comandos peligrosos
- 🎨 **Personalización**: Adapta el sistema a tu forma de trabajar

Para ver los alias existentes

```bash
alias
```

**Salida típica:**
```bash
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
```

Para ver un alias específico

```bash
alias ll
```
**Salida:** `alias ll='ls -alF'`

### Crear Alias

Sintaxis: 

```bash
alias nombreAlias='comandos_a_ejecutar'
```

!!! warning "Importante"
    - No debe haber espacios alrededor del `=`
    - Si el comando tiene espacios, debe ir entre comillas simples

Ejemplos prácticos:

!!! example "Ejemplo 1: Listar con detalles"

    ```bash
    alias ll='ls -lh'
    ```

Ahora al escribir `ll` ejecutará `ls -lh` (lista con detalles y tamaños legibles)

!!! example "Ejemplo 2: Ir al directorio de proyectos"

    ```bash
    alias proyectos='cd /home/sergio/Documentos/Proyectos'
    ```

!!! example "Ejemplo 3: Actualizar el sistema"

    ```bash
    alias actualizar='sudo apt update && sudo apt upgrade -y'
    ```

!!! example "Ejemplo 4: Comando de seguridad"

    ```bash
    alias rm='rm -i'
    ```

Ahora `rm` siempre pedirá confirmación antes de borrar (opción `-i` = interactive)

!!! example "Ejemplo 5: Conectar a un servidor"

    ```bash
    alias servidor='ssh usuario@192.168.1.100'
    ```

!!! example "Ejemplo 6: Ver procesos ordenados por CPU"

    ```bash
    alias pscpu='ps aux --sort=-%cpu | head -n 10'
    ```

Para probar un alias, una vez creado, simplemente escribe su nombre:

```bash
sergio@ubuntu:~$ ll
total 48K
drwxr-xr-x 12 sergio sergio 4,0K feb  5 10:30 ./
drwxr-xr-x  3 root   root   4,0K ene 15 09:00 ../
drwxr-xr-x  2 sergio sergio 4,0K feb  3 14:20 Documentos/
drwxr-xr-x  2 sergio sergio 4,0K feb  4 16:45 Descargas/
```

### Alias Temporales vs Permanentes

Los alias creados con el comando `alias` son **temporales**: se pierden al cerrar la terminal o reiniciar el sistema.

```bash
alias temp='echo "Este alias es temporal"'
```

Para que un alias sea **permanente**, debemos añadirlo al archivo de configuración del shell: `~/.bashrc`

!!!tip "Pasos para crear un alias permanente"

    **1. Abrir el archivo de configuración:**

    ```bash
    nano ~/.bashrc
    ```

    **2. Ir al final del archivo y añadir el alias:**

    ```bash
    # Mis alias personalizados
    alias ll='ls -lh'
    alias actualizar='sudo apt update && sudo apt upgrade -y'
    alias proyectos='cd /home/sergio/Documentos/Proyectos'
    ```

    **3. Guardar el archivo:**
    - ++ctrl++"O"++ (guardar)
    - ++enter++ (confirmar)
    - ++ctrl++"X"++ (salir)

    **4. Recargar la configuración:**

    ```bash
    source ~/.bashrc
    ```

!!! note "¿Qué es .bashrc?"
    El archivo `~/.bashrc` se ejecuta automáticamente cada vez que abres una terminal. Todo lo que escribas ahí (alias, variables, funciones) estará disponible en todas tus sesiones.

### Eliminar un Alias

Temporalmente (sesión actual)

```bash
unalias nombreAlias
```

!!! example "Ejemplo eliminar alias `ll`"
    ```bash
    unalias ll
    ```

Para eliminar de forma permanente, edita el archivo `~/.bashrc` y elimina o comenta (con `#`) la línea del alias:

```bash
nano ~/.bashrc
```

```bash
# alias ll='ls -lh'    # Comentado con #
```

Luego recarga la configuración:
```bash
source ~/.bashrc
```

### Ejemplos de Alias Útiles

!!!example "Alias Para administración del sistema"

    ```bash
    alias puertos='sudo netstat -tulanp'
    alias espacio='df -h'
    alias memoria='free -h'
    alias procesos='ps aux | less'
    ```

!!!example "Alias Para desarrollo"

    ```bash
    alias gs='git status'
    alias ga='git add'
    alias gc='git commit -m'
    alias gp='git push'
    alias python='python3'
    ```

!!!example "Alias Para navegar rápidamente"

    ```bash
    alias ..='cd ..'
    alias ...='cd ../..'
    alias home='cd ~'
    alias desktop='cd ~/Desktop'
    ```

!!!example "Alias Para seguridad"

    ```bash
    alias rm='rm -i'        # Pide confirmación antes de borrar
    alias cp='cp -i'        # Pide confirmación antes de sobrescribir
    alias mv='mv -i'        # Pide confirmación antes de sobrescribir
    ```

### Alias con Parámetros

Los alias no aceptan parámetros directamente, pero puedes usar funciones para eso:

```bash
# En lugar de alias, define una función en ~/.bashrc
buscar() {
    find . -name "*$1*"
}
```

Uso:
```bash
buscar documento.txt
```

### Ver el Comando Real de un Alias

Si quieres ejecutar el comando original sin el alias:

```bash
\comando
```

Ejemplo:
```bash
\rm archivo.txt    # Ejecuta rm sin el alias (sin pedir confirmación)
```

## Resumen de Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `type` | Muestra el tipo de comando | `type ls` |
| `which` | Localiza la ruta de un ejecutable | `which python3` |
| `alias` | Muestra todos los alias | `alias` |
| `alias nombre` | Muestra un alias específico | `alias ll` |
| `alias nombre='comando'` | Crea un alias temporal | `alias ll='ls -lh'` |
| `unalias nombre` | Elimina un alias temporal | `unalias ll` |
| `source ~/.bashrc` | Recarga la configuración | `source ~/.bashrc` |

## Ejercicios Prácticos

!!! example "Ejercicio 1: Crear alias temporales"
    1. Crea un alias `docs` para ir a tu carpeta Documentos
    2. Crea un alias `limpiar` para borrar archivos temporales: `rm -rf /tmp/*`
    3. Prueba ambos alias
    4. Cierra la terminal y ábrela de nuevo. ¿Siguen funcionando?

!!! example "Ejercicio 2: Alias permanentes"
    1. Abre `~/.bashrc` con nano
    2. Añade estos alias al final:
       ```bash
       alias update='sudo apt update'
       alias upgrade='sudo apt upgrade'
       alias limpio='sudo apt autoremove && sudo apt clean'
       ```
    3. Recarga con `source ~/.bashrc`
    4. Prueba los nuevos alias

!!! example "Ejercicio 3: Investigar"
    1. Usa `type` para averiguar qué tipo de comando son: `cd`, `ls`, `echo`, `cat`
    2. Usa `which` para localizar: `bash`, `python3`, `firefox`
    3. Mira qué alias tienes definidos en tu sistema

## Buenas Prácticas

!!! success "Recomendaciones"
    - ✅ Usa nombres descriptivos para tus alias
    - ✅ Agrupa tus alias por categorías en `.bashrc` (con comentarios)
    - ✅ No sobrescribas comandos importantes sin buena razón
    - ✅ Documenta alias complejos con comentarios
    - ✅ Haz backup de tu `.bashrc` antes de cambios importantes

!!! warning "Evita"
    - ❌ Nombres de alias que coincidan con comandos existentes (salvo que sea intencional)
    - ❌ Alias excesivamente complejos (mejor usa scripts o funciones)
    - ❌ Depender de alias en scripts (no estarán disponibles)
