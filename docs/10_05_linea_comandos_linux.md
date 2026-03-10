---
title: La Línea de Comandos de GNU/Linux
description:  Fundamentos de la Línea de Comandos en Linux
---

La línea de comandos de Linux es una herramienta **extremadamente potente** que permite realizar cualquier acción en el sistema. A diferencia de Windows, en Linux el entorno gráfico es opcional: podemos instalar y administrar completamente el sistema sin interfaz gráfica.

!!! info "Linux sin entorno gráfico"
    Los servidores Linux suelen instalarse sin interfaz gráfica para optimizar recursos y mejorar la seguridad.

## Permisos y Usuarios. El Superusuario: `root`

No todos los usuarios pueden ejecutar todos los comandos. Muchas operaciones requieren **permisos de administrador**.

| Usuario | Prompt | Permisos |
|---------|--------|----------|
| **Usuario normal** | `$` | Limitados a sus archivos y algunas operaciones |
| **root (superusuario)** | `#` | Control total del sistema |

!!! warning "Precaución con root"
    Cuando ejecutamos comandos como root, el sistema **no pide confirmaciones**. Asume que sabemos lo que hacemos. Un comando mal escrito puede dañar el sistema.

!!!tip "Recomendación de uso"

    - ✅ Trabaja siempre como **usuario normal**
    - ⚠️ Solo conviértete en root cuando sea **estrictamente necesario**
    - 🔒 Vuelve a tu usuario normal inmediatamente después

## Cambiar de Usuario

### Comando `su`

Permite cambiar al usuario indicado (o a root si no se especifica ninguno).

```bash
su jmonllor          # Cambia al usuario jmonllor (pide su contraseña)
su                   # Cambia a root (pide la contraseña de root)
```

Para volver a tu usuario:
```bash
exit                 # Cierra la sesión del usuario actual
```

### Comando `sudo`

Ejecuta **un único comando** con permisos de root, sin cambiar de usuario.

```bash
sudo apt update              # Ejecuta este comando como root
sudo systemctl restart nginx # Ejecuta este comando como root
```

!!! tip "sudo vs su"
    - `su`: Cambias completamente al otro usuario (todas las órdenes serán como ese usuario)
    - `sudo`: Solo ejecutas un comando con privilegios, luego sigues siendo tú

### Grupo sudoers

Para usar `sudo`, tu usuario debe pertenecer al grupo de **sudoers** (usuarios autorizados).

**Diferencias entre distribuciones:**

=== "Ubuntu"
    - El usuario creado durante la instalación **pertenece a sudoers**
    - **No se pide** contraseña de root durante la instalación
    - Para ser root: `sudo su`

=== "Debian"
    - El usuario creado es **normal** (no puede hacer sudo)
    - **Sí se pide** contraseña de root durante la instalación
    - Para ser root: `su` (y escribir la contraseña de root)

## Las Terminales en GNU/Linux

### Terminales de Texto (TTY)

Linux arranca por defecto **7 terminales**:

- **6 terminales de texto**: `tty1` a `tty6`
- **1 terminal gráfica**: `tty7` (o `tty1` en algunas distribuciones)

Para cambiar entre terminales usa la combinación: ++ctrl+alt++++"F1"++ hasta ++ctrl+alt++++"F7"++

```
Ctrl + Alt + F1     → Primera terminal de gráfica (tty1)
Ctrl + Alt + F2     → Segunda terminal de gráfica (tty2)
Ctrl + Alt + F3     → Primera terminal de texto (tty3)
...
Ctrl + Alt + F7     → Terminal texto (tty7)
```

!!! example "Nombres de las terminales"
    - Terminal 1: `/dev/tty1`
    - Terminal 2: `/dev/tty2`
    - Terminal gráfica: `/dev/tty1` (o `/dev/tty7` según distribución puede cambiar)

### Pseudoterminales (PTS)

Dentro del entorno gráfico, puedes abrir múltiples **ventanas de terminal**. Cada una se denomina **pseudoterminal** y se identifica como `pts/N`. Esto no es así en todos las distribuciones linux.id

```bash
pts/0    # Primera terminal abierta en el entorno gráfico
pts/1    # Segunda terminal abierta
pts/2    # Tercera terminal abierta
```

### Comando `who`

Muestra los usuarios conectados actualmente al sistema:

```bash
who
```

!!! example "Salida del comando who"
    ```
    sergio   tty1         2026-02-05 09:30
    sergio   pts/0        2026-02-05 09:35 (:0)
    sergio   pts/1        2026-02-05 10:15 (:0)
    root     tty2         2026-02-05 10:20
    ```
    
    **Interpretación:**
    - Usuario `sergio` está en tty1 (terminal de texto)
    - Usuario `sergio` tiene 2 pseudoterminales abiertas (pts/0 y pts/1)
    - Usuario `root` está en tty2

## El Shell: Intérprete de Comandos

El **shell** es el programa que interpreta y ejecuta los comandos que escribimos en la terminal.

Shells disponibles:

| Shell | Ubicación | Descripción |
|-------|-----------|-------------|
| **bash** | `/bin/bash` | El más utilizado (Bourne Again Shell) |
| **sh** | `/bin/sh` | Shell clásico de Unix |
| **zsh** | `/bin/zsh` | Shell moderno con muchas características |
| **fish** | `/bin/fish` | Shell amigable e interactivo |

!!! info "Shell por defecto"
    Cada usuario tiene asignado un shell por defecto, que se especifica en el archivo `/etc/passwd`.

Para ver el shell de un usuario

```bash
cat /etc/passwd | grep sergio
```

Salida ejemplo:
```
sergio:x:1000:1000:Sergio,,,:/home/sergio:/bin/bash
```

El último campo (`/bin/bash`) indica el shell asignado.

## Ayuda y Utilidades

### 1. Manual: `man`

La forma más completa de obtener ayuda sobre un comando.

```bash
man cp              # Muestra el manual del comando cp
man ls              # Muestra el manual del comando ls
man man             # ¡Hasta puedes ver el manual de man!
```

**Navegación en el manual:**
- ++arrow-down++ / ++arrow-up++: Navegar línea a línea
- ++space++: Avanzar página
- ++q++: Salir del manual

### 2. Help interno: `help`

Para algunos comandos internos del shell:

```bash
help cd             # Ayuda del comando cd
help echo           # Ayuda del comando echo
```

### 3. Opción `--help`

Muchos comandos aceptan esta opción para mostrar una ayuda rápida:

```bash
cp --help           # Ayuda rápida del comando cp
ls --help           # Ayuda rápida del comando ls
```

## Autocompletado con TAB

Una de las funciones más útiles: **no es necesario escribir comandos completos**.

¿Cómo funciona?

1. Escribe las primeras letras del comando
2. Pulsa ++tab++
3. El sistema completa automáticamente

!!! example "Ejemplos de autocompletado"
    === "Un solo resultado"
        ```bash
        reb[TAB]        → reboot
        ```
    
    === "Múltiples resultados"
        ```bash
        re[TAB][TAB]    → Muestra: reboot, rename, resize, reset...
        ```

También funciona con **nombres de archivos y directorios**:

```bash
cd /home/ser[TAB]              → cd /home/sergio/
cp /etc/netw[TAB]              → cp /etc/network/
```

!!! tip "Beneficios del autocompletado"
    - ⚡ **Rapidez**: Escribes menos
    - ✅ **Precisión**: Evitas errores de escritura
    - 🎯 **Descubrimiento**: Ves qué comandos están disponibles

## Historial de Comandos

Linux guarda un historial de todos los comandos que has ejecutado.

Navegar por el historial:

- ++arrow-up++: Comando anterior
- ++arrow-down++: Comando siguiente

Ver todo el historial:

```bash
history             # Muestra todos los comandos guardados
```

!!! example "Salida de history"
    ```
    1  ls -la
    2  cd /home/sergio
    3  sudo apt update
    4  mkdir proyecto
    5  history
    ```

Ejecutar un comando del historial: 

```bash
!3                  # Ejecuta el comando número 3 del historial
!!                  # Ejecuta el último comando
!sudo               # Ejecuta el último comando que empezaba por "sudo"
```

## Sintaxis General de Comandos

```bash
comando [-o | --opción] [argumentos]
```

Componentes:

| Elemento | Descripción | Ejemplo |
|----------|-------------|---------|
| **comando** | Nombre del comando | `ls`, `cp`, `mkdir` |
| **opción corta** | Una letra precedida de `-` | `-a`, `-l`, `-r` |
| **opción larga** | Palabra precedida de `--` | `--all`, `--help` |
| **argumentos** | Parámetros del comando | nombres de archivos, rutas |

Formas de especificar opciones: 

=== "Opciones separadas"
    ```bash
    ls --all --reverse
    ls -a -r
    ```

=== "Opciones juntas (forma corta)"
    ```bash
    ls -ar              # Equivalente a -a -r
    ls -lah             # Equivalente a -l -a -h
    ```

Algunas opciones aceptan valores:

```bash
ls -l --sort=time           # Con =
ls -l --sort time           # Con espacio
```

## Interrumpir un Comando

Si un comando tarda demasiado o queremos cancelarlo:

```bash
Ctrl + C            # Interrumpe el comando actual
```

## Comandos Útiles para Empezar

| Comando | Descripción |
|---------|-------------|
| `whoami` | Muestra tu nombre de usuario actual |
| `pwd` | Muestra el directorio actual (Print Working Directory) |
| `clear` | Limpia la pantalla |
| `exit` | Cierra la sesión o terminal actual |

## Resumen

| Concepto | Descripción |
|----------|-------------|
| **root** | Superusuario con control total (prompt `#`) |
| **su** | Cambiar de usuario |
| **sudo** | Ejecutar un comando como root |
| **TTY** | Terminales de texto (Ctrl+Alt+F1...F6) |
| **PTS** | Terminales dentro del entorno gráfico |
| **bash** | Shell más utilizado en Linux |
| **man** | Manual completo de comandos |
| **TAB** | Autocompletar comandos y rutas |
| **history** | Historial de comandos ejecutados |

!!! success "Consejos finales"
    - 📖 Usa `man` para aprender sobre cualquier comando
    - ⌨️ Abusa del ++tab++ para autocompletar
    - 📜 Usa las flechas para recuperar comandos anteriores
    - ⚠️ Ten cuidado cuando seas root
    - 🔄 Practica, practica y practica