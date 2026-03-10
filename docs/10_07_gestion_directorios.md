---
title: Gestión de Directorios
description:  Fundamentos de la Línea de Comandos en Linux
---

Los directorios (o carpetas) son la base de la organización de archivos en cualquier sistema operativo. En Linux, saber navegar y gestionar directorios desde la terminal es fundamental.

## Comandos Principales

### `pwd` - Mostrar directorio actual

**Print Working Directory** - Muestra la ruta completa del directorio en el que nos encontramos.

```bash
pwd
```

**Salida ejemplo:**
```bash
/home/sergio/Documentos/Proyectos
```

!!! tip "¿Dónde estoy?"
    Si te pierdes navegando por el sistema, `pwd` te dice exactamente dónde estás.

---

### `ls` - Listar contenido

Lista los archivos y directorios del directorio especificado (o del actual si no se indica ninguno).

Sintaxis básica: 

```bash
ls [opciones] [directorio]
```

Uso básico: 

```bash
ls                          # Lista el contenido del directorio actual
ls /home/juan              # Lista el contenido de /home/juan
ls ..                      # Lista el contenido del directorio padre
```

Opciones principales: 

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-l` | Lista con formato largo (detalles) | `ls -l` |
| `-h` | Tamaños en formato "humano" (KB, MB, GB) | `ls -lh` |
| `-a` | Muestra archivos ocultos | `ls -a` |
| `-A` | Muestra archivos ocultos (excepto `.` y `..`) | `ls -A` |
| `-R` | Recursivo (incluye subdirectorios) | `ls -R` |
| `-t` | Ordena por fecha de modificación | `ls -lt` |
| `-S` | Ordena por tamaño | `ls -lS` |
| `-r` | Invierte el orden | `ls -lr` |

Ejemplos prácticos: 

=== "Listado básico"
    ```bash
    ls
    ```
    ```
    Documentos  Descargas  Imágenes  Música  Vídeos
    ```

=== "Con detalles"
    ```bash
    ls -l
    ```
    ```
    drwxr-xr-x 5 sergio sergio 4096 feb  5 10:30 Documentos
    drwxr-xr-x 2 sergio sergio 4096 feb  4 15:20 Descargas
    drwxr-xr-x 3 sergio sergio 4096 feb  3 09:15 Imágenes
    ```

=== "Con detalles y tamaños legibles"
    ```bash
    ls -lh
    ```
    ```
    drwxr-xr-x 5 sergio sergio 4,0K feb  5 10:30 Documentos
    -rw-r--r-- 1 sergio sergio 2,5M feb  4 15:20 documento.pdf
    -rw-r--r-- 1 sergio sergio 156K feb  3 09:15 imagen.jpg
    ```

=== "Incluyendo ocultos"
    ```bash
    ls -la
    ```
    ```
    drwxr-xr-x 12 sergio sergio 4096 feb  5 10:30 .
    drwxr-xr-x  3 root   root   4096 ene 15 09:00 ..
    -rw-r--r--  1 sergio sergio  220 ene 15 09:00 .bash_logout
    -rw-r--r--  1 sergio sergio 3526 ene 15 09:00 .bashrc
    drwxr-xr-x  5 sergio sergio 4096 feb  5 10:30 Documentos
    ```

Combinando opciones: 

```bash
ls -lah                    # Lista con detalles, tamaños legibles y ocultos
ls -lhR                    # Lista con detalles, tamaños legibles, recursivo
ls -lt                     # Lista con detalles, ordenado por fecha
ls -lS                     # Lista con detalles, ordenado por tamaño
```

!!! tip "Alias común"
    Muchos sistemas tienen por defecto:
    ```bash
    alias ll='ls -lh'
    alias la='ls -A'
    alias l='ls -CF'
    ```

---

### `cd` - Cambiar de directorio

**Change Directory** - Cambia el directorio actual al que le indiquemos.

Sintaxis: 

```bash
cd [directorio]
```

Formas de uso: 

```bash
cd /home/juan/Desktop/pruebas    # Ruta absoluta
cd Documentos                     # Ruta relativa (entra en Documentos)
cd ..                             # Sube al directorio padre
cd ../..                          # Sube dos niveles
cd ~                              # Va al directorio personal del usuario
cd                                # Sin parámetros también va al home
cd -                              # Vuelve al directorio anterior
```

Ejemplos prácticos: 

=== "Navegación básica"
    ```bash
    sergio@ubuntu:~$ pwd
    /home/sergio
    
    sergio@ubuntu:~$ cd Documentos
    sergio@ubuntu:~/Documentos$ pwd
    /home/sergio/Documentos
    ```

=== "Subir niveles"
    ```bash
    sergio@ubuntu:~/Documentos/Proyectos/Web$ cd ..
    sergio@ubuntu:~/Documentos/Proyectos$ cd ../..
    sergio@ubuntu:~$
    ```

=== "Ir al home"
    ```bash
    sergio@ubuntu:/etc/network$ cd ~
    sergio@ubuntu:~$ pwd
    /home/sergio
    ```

=== "Volver atrás"
    ```bash
    sergio@ubuntu:~$ cd /etc
    sergio@ubuntu:/etc$ cd -
    /home/sergio
    sergio@ubuntu:~$
    ```

!!! info "El comando cd es un builtin"
    `cd` es un comando interno del shell (builtin), no un programa externo. Por eso no tiene página de manual (`man cd` no funciona), pero puedes usar `help cd`.

---

### `mkdir` - Crear directorios

**Make Directory** - Crea uno o más directorios nuevos.

Sintaxis: 

```bash
mkdir [opciones] nombre_directorio
```

Opciones principales: 

| Opción | Descripción |
|--------|-------------|
| `-p` | Crea directorios padre si no existen |
| `-v` | Modo verbose (muestra lo que hace) |
| `-m` | Establece permisos específicos |

Ejemplos básicos

```bash
mkdir clientes                          # Crea "clientes" en el directorio actual
mkdir /home/juan/clientes              # Crea "clientes" en /home/juan
mkdir ../clientes                      # Crea "clientes" en el directorio padre
```

!!!example "Crear múltiples directorios"

    ```bash
    mkdir dir1 dir2 dir3                   # Crea tres directorios
    mkdir Proyecto{1,2,3}                  # Crea Proyecto1, Proyecto2, Proyecto3
    ```

!!!example "Crear estructura de directorios"

    Sin la opción `-p`, si intentamos crear un directorio dentro de otro que no existe, dará error:

    ```bash
    mkdir proyectos/web/html               # ❌ Error si 'proyectos/web' no existe
    ```

    Con `-p` crea toda la estructura:

    ```bash
    mkdir -p proyectos/web/html            # ✅ Crea proyectos, web y html
    mkdir -p Documentos/2026/Enero/Facturas
    ```

!!!example "Crear con permisos específicos"

    ```bash
    mkdir -m 755 carpeta_publica           # Crea con permisos específicos
    mkdir -m 700 carpeta_privada           # Solo el propietario puede acceder
    ```

!!!example "Modo verbose"

    ```bash
    mkdir -pv proyecto/src/css proyecto/src/js
    ```
    **Salida:**
    ```
    mkdir: se ha creado el directorio 'proyecto'
    mkdir: se ha creado el directorio 'proyecto/src'
    mkdir: se ha creado el directorio 'proyecto/src/css'
    mkdir: se ha creado el directorio 'proyecto/src/js'
    ```

---

### `rmdir` - Eliminar directorios vacíos

**Remove Directory** - Elimina directorios **que estén vacíos**.

Sintaxis

```bash
rmdir [opciones] nombre_directorio
```

Ejemplos

```bash
rmdir clientes                         # Elimina "clientes" del directorio actual
rmdir /home/juan/clientes             # Elimina el directorio indicado
rmdir dir1 dir2 dir3                  # Elimina múltiples directorios
```

!!! warning "Solo directorios vacíos"
    `rmdir` solo funciona con directorios vacíos. Si el directorio tiene archivos o subdirectorios, dará un error:
    ```bash
    rmdir: no se puede borrar 'clientes': El directorio no está vacío
    ```

!!!example "Eliminar estructura vacía"

    ```bash
    rmdir -p proyecto/src/css              # Elimina css, luego src, luego proyecto
                                        # (solo si todos están vacíos)
    ```

---

!!!example "`rm -r` - Eliminar directorios con contenido"

    Para eliminar directorios que contienen archivos, usamos `rm` con la opción `-r` (recursivo).

    ```bash
    rm -r clientes                         # Elimina "clientes" y todo su contenido
    rm -rf clientes                        # Fuerza la eliminación sin preguntar
    rm -ri clientes                        # Pregunta antes de cada eliminación
    ```

!!! danger "¡Peligro!"
    El comando `rm -rf` es **extremadamente peligroso** si se usa incorrectamente. No pide confirmación y borra todo sin posibilidad de recuperación.
    
    **Nunca ejecutes:**
    ```bash
    rm -rf /                           # ¡NUNCA! Borra todo el sistema
    rm -rf /*                          # ¡NUNCA! Igual de peligroso
    ```

Opciones de `rm`:

| Opción | Descripción |
|--------|-------------|
| `-r` o `-R` | Recursivo (elimina directorios y su contenido) |
| `-f` | Force (fuerza, no pide confirmación) |
| `-i` | Interactive (pregunta antes de borrar) |
| `-v` | Verbose (muestra lo que hace) |

---

### `blkid` - Información de particiones

Muestra los identificadores únicos (UUID) de las particiones del sistema.

```bash
sudo blkid
```

**Salida ejemplo:**
```
/dev/sda1: UUID="1234-5678" TYPE="vfat" PARTUUID="abcd-01"
/dev/sda2: UUID="a1b2c3d4-e5f6-..." TYPE="ext4" PARTUUID="abcd-02"
/dev/sdb1: UUID="f8e7d6c5-..." TYPE="ext4" PARTUUID="efgh-01"
```

!!! info "¿Para qué sirve?"
    Los UUID son útiles para identificar particiones de forma única, especialmente al configurar montajes automáticos en `/etc/fstab`.

---

## Ejemplos Prácticos Completos

!!!example "Escenario 1: Organizar proyecto"

    ```bash
    # Ver dónde estamos
    pwd

    # Ir al directorio personal
    cd ~

    # Crear estructura de proyecto
    mkdir -p MiProyecto/{src,docs,tests}
    mkdir -p MiProyecto/src/{css,js,images}

    # Ver la estructura creada
    ls -R MiProyecto

    # Entrar al proyecto
    cd MiProyecto
    ```

!!!example "Escenario 2: Navegar por el sistema"

    ```bash
    # Estamos en home
    pwd                           # /home/sergio

    # Ver qué hay
    ls -lh

    # Ir a documentos
    cd Documentos

    # Subir un nivel y ver contenido
    cd ..
    ls

    # Ir a una ruta absoluta
    cd /var/log

    # Ver logs
    ls -lth | head -10           # Los 10 archivos más recientes

    # Volver a home
    cd
    ```

!!!example "Escenario 3: Limpieza de directorios"

    ```bash
    # Ver directorios vacíos
    ls -l

    # Eliminar directorio vacío
    rmdir directorio_vacio

    # Eliminar directorio con contenido (con precaución)
    rm -ri directorio_con_archivos    # Pregunta antes de borrar

    # Eliminar múltiples directorios vacíos
    rmdir temp1 temp2 temp3
    ```

---

## Atajos de Navegación

| Atajo | Descripción |
|-------|-------------|
| `cd` o `cd ~` | Va al directorio personal |
| `cd -` | Vuelve al directorio anterior |
| `cd ..` | Sube un nivel |
| `cd ../..` | Sube dos niveles |
| `cd /` | Va a la raíz del sistema |

---

## Resumen de Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `pwd` | Muestra el directorio actual | `pwd` |
| `ls` | Lista contenido | `ls -lah` |
| `cd` | Cambia de directorio | `cd Documentos` |
| `mkdir` | Crea directorio | `mkdir proyecto` |
| `mkdir -p` | Crea estructura completa | `mkdir -p a/b/c` |
| `rmdir` | Elimina directorio vacío | `rmdir temp` |
| `rm -r` | Elimina directorio con contenido | `rm -r carpeta` |
| `blkid` | Muestra UUIDs de particiones | `sudo blkid` |

---

## Ejercicios Prácticos

!!! example "Ejercicio 1: Navegación básica"
    1. Muestra tu directorio actual con `pwd`
    2. Ve a tu directorio personal con `cd`
    3. Lista su contenido con `ls -lh`
    4. Ve al directorio `/tmp` y lista su contenido
    5. Vuelve a tu home con `cd -`

!!! example "Ejercicio 2: Crear estructura"
    Crea la siguiente estructura de directorios en tu home:
    ```
    DAM/
    ├── Programacion/
    │   ├── Java/
    │   └── Python/
    ├── BBDD/
    └── SistemasInformaticos/
    ```
    Usa un solo comando con `mkdir -p`

!!! example "Ejercicio 3: Limpieza"
    1. Crea un directorio llamado `prueba`
    2. Entra en él y crea otros dos directorios: `a` y `b`
    3. Sal de `prueba` e intenta eliminarlo con `rmdir` (¿qué pasa?)
    4. Elimínalo correctamente con `rm -r`

---

## Consejos y Buenas Prácticas

!!! success "Recomendaciones"
    - ✅ Usa `ls` antes de `rm -r` para verificar qué vas a borrar
    - ✅ Usa `pwd` si no estás seguro de dónde estás
    - ✅ Usa TAB para autocompletar nombres de directorios
    - ✅ Usa `mkdir -p` para crear estructuras completas
    - ✅ Usa `ls -lah` como alias por defecto (`ll`)

!!! warning "Precauciones"
    - ⚠️ Ten cuidado con `rm -rf`, especialmente como root
    - ⚠️ Verifica la ruta antes de eliminar directorios
    - ⚠️ Los nombres con espacios necesitan comillas o escape: `mkdir "Mi Carpeta"`
    - ⚠️ Linux diferencia mayúsculas de minúsculas: `Docs ≠ docs`

