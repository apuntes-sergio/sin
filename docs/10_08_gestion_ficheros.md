---
title: Gestión de Ficheros
description:  Fundamentos de la Línea de Comandos en Linux
---

Los ficheros (o archivos) son la unidad básica de almacenamiento de información. En este apartado aprenderemos a copiar, mover, eliminar, buscar y comprimir archivos desde la terminal.

## Comandos de Manipulación Básica

### `cp` - Copiar archivos

Copia archivos o directorios de una ubicación a otra.

Sintaxis:

```bash
cp [opciones] origen destino
```

Opciones principales:

| Opción | Descripción |
|--------|-------------|
| `-r` o `-R` | Copia directorios recursivamente |
| `-i` | Modo interactivo (pregunta antes de sobrescribir) |
| `-v` | Modo verbose (muestra lo que hace) |
| `-u` | Copia solo si el origen es más nuevo |
| `-p` | Preserva permisos, propietario y fechas |
| `-a` | Modo archivo (equivale a `-dpR`, mantiene todo) |

Ejemplos básicos: 

```bash
cp archivo.txt backup.txt              # Copia en el mismo directorio
cp leeme.txt ..                        # Copia al directorio padre
cp documento.pdf /home/juan/          # Copia a otro directorio
```

!!!example "Copiar múltiples archivos"

    ```bash
    cp archivo1.txt archivo2.txt /tmp/    # Copia varios archivos a /tmp
    cp *.txt /backup/                     # Copia todos los .txt
    cp *.{jpg,png} /imagenes/             # Copia jpg y png
    ```

!!!example "Copiar directorios"

    ```bash
    cp -r carpeta/ /backup/               # Copia carpeta y todo su contenido
    cp -r /home/juan/* /media/USB/        # Copia todo el contenido de juan a USB
    ```

!!!example "Ejemplos con opciones"

    ```bash
    cp -i archivo.txt destino.txt         # Pregunta antes de sobrescribir
    cp -v *.pdf /backup/                  # Muestra cada archivo copiado
    cp -a proyecto/ /backup/              # Copia preservando todo (permisos, fechas, etc.)
    ```

!!! tip "Diferencia importante"
    ```bash
    cp carpeta destino      # ❌ Error: necesita -r para directorios
    cp -r carpeta destino   # ✅ Correcto: copia el directorio
    ```

---

### `mv` - Mover o renombrar

Mueve archivos/directorios de una ubicación a otra, o los renombra.

Sintaxis

```bash
mv [opciones] origen destino
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-i` | Modo interactivo (pregunta antes de sobrescribir) |
| `-v` | Modo verbose (muestra lo que hace) |
| `-u` | Mueve solo si el origen es más nuevo |
| `-n` | No sobrescribe archivos existentes |

Mover archivos:

```bash
mv archivo.txt /tmp/                  # Mueve el archivo a /tmp
mv leeme.txt ..                       # Mueve al directorio padre
mv *.log /var/logs/                   # Mueve todos los .log
```

Renombrar archivos:

```bash
mv archivo_viejo.txt archivo_nuevo.txt    # Renombra el archivo
mv leeme.txt importante.txt               # Cambia el nombre
```

Mover directorios

```bash
mv carpeta/ /backup/                  # Mueve carpeta (no necesita -r)
mv /home/juan/* /media/USB/          # Mueve todo el contenido
```

!!!note "Diferencia entre `cp` y `mv`"

    === "cp (copiar)"
        ```bash
        cp archivo.txt /backup/
        # Resultado: archivo.txt existe en AMBOS lugares
        ```

    === "mv (mover)"
        ```bash
        mv archivo.txt /backup/
        # Resultado: archivo.txt solo existe en /backup/
        ```

!!! info "mv no necesita -r"
    A diferencia de `cp`, el comando `mv` mueve directorios sin necesidad de la opción `-r`.

---

### `rm` - Eliminar archivos

Elimina archivos y directorios (con precaución).

Sintaxis:

```bash
rm [opciones] archivo(s)
```

Opciones principales:

| Opción | Descripción |
|--------|-------------|
| `-r` o `-R` | Elimina directorios recursivamente |
| `-f` | Fuerza la eliminación sin preguntar |
| `-i` | Pregunta antes de eliminar cada archivo |
| `-v` | Modo verbose (muestra lo que hace) |
| `-d` | Elimina directorios vacíos |

Eliminar archivos

```bash
rm archivo.txt                        # Elimina un archivo
rm archivo1.txt archivo2.txt          # Elimina varios archivos
rm *.tmp                              # Elimina todos los .tmp
rm /home/juan/*.odt                   # Elimina todos los .odt del directorio
```

Eliminar directorios

```bash
rm -r carpeta/                        # Elimina carpeta y su contenido
rm -rf /tmp/temporal/                 # Fuerza eliminación sin preguntar
rm -ri proyecto/                      # Pregunta antes de eliminar cada elemento
```

Modo seguro

```bash
rm -i archivo.txt                     # Pregunta: ¿eliminar 'archivo.txt'?
rm -iv *.txt                          # Pregunta por cada archivo .txt
```

!!! danger "¡Extrema precaución!"
    El comando `rm` es **irreversible**. Una vez eliminado, el archivo no se puede recuperar.
    
    **Comandos peligrosos:**
    ```bash
    rm -rf /           # ¡NUNCA! Destruye el sistema completo
    rm -rf /*          # ¡NUNCA! Igual de destructivo
    rm -rf ~/*         # ¡CUIDADO! Borra todo tu directorio personal
    ```
    
    **Siempre verifica antes de ejecutar `rm -rf`**

---

### `touch` - Crear archivos vacíos

Crea archivos vacíos o actualiza la fecha de modificación de archivos existentes.

Sintaxis

```bash
touch archivo(s)
```

Ejemplos

```bash
touch nuevo.txt                       # Crea archivo vacío
touch archivo1.txt archivo2.txt       # Crea varios archivos
touch prueba{1..5}.txt                # Crea prueba1.txt, prueba2.txt, ..., prueba5.txt
```

!!!example "Actualizar fecha de modificación"

    ```bash
    touch archivo_existente.txt           # Actualiza su fecha a ahora
    ```

!!! tip "Uso práctico"
    Útil para crear archivos de prueba rápidamente o forzar que un archivo tenga fecha actual.

---

## Comandos de Información

### `file` - Identificar tipo de archivo

Muestra el tipo de archivo basándose en su contenido (no en su extensión).

```bash
file documento.pdf
```
**Salida:** `documento.pdf: PDF document, version 1.4`

```bash
file imagen.jpg
```
**Salida:** `imagen.jpg: JPEG image data, JFIF standard 1.01`

```bash
file script.sh
```
**Salida:** `script.sh: Bash shell script, ASCII text executable`

!!! info "No se fía de la extensión"
    `file` analiza el contenido real del archivo, no su nombre. Si renombras un PDF como .txt, seguirá detectando que es un PDF.

---

### `stat` - Información detallada

Muestra toda la información sobre un archivo: permisos, tamaño, fechas, inodo, etc.

```bash
stat archivo.txt
```

**Salida ejemplo:**
```
  Fichero: archivo.txt
  Tamaño: 1234      	Bloques: 8          Bloque E/S: 4096   fichero regular
Dispositivo: 801h/2049d	Nodo-i: 524312      Enlaces: 1
Acceso: (0644/-rw-r--r--)  Uid: ( 1000/  sergio)   Gid: ( 1000/  sergio)
Acceso: 2026-02-05 10:30:25.123456789 +0100
Modificación: 2026-02-05 09:15:30.987654321 +0100
      Cambio: 2026-02-05 09:15:30.987654321 +0100
   Creación: 2026-02-04 14:20:15.456789123 +0100
```

---

### `find` - Buscar archivos

Busca archivos en el sistema de archivos según diversos criterios.

Sintaxis

```bash
find [directorio] [opciones] [criterios]
```

Opciones principales

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-name` | Busca por nombre | `-name "*.txt"` |
| `-iname` | Busca por nombre (ignora mayúsculas) | `-iname "*.TXT"` |
| `-type` | Busca por tipo (f=archivo, d=directorio) | `-type f` |
| `-size` | Busca por tamaño | `-size +10M` |
| `-user` | Busca por propietario | `-user juan` |
| `-perm` | Busca por permisos | `-perm 644` |
| `-mtime` | Modificado hace N días | `-mtime -7` |
| `-exec` | Ejecuta comando sobre resultados | `-exec rm {} \;` |

Ejemplos prácticos

!!!example "Buscar por nombre:"
    ```bash
    find . -name "*.txt"                  # Busca .txt desde aquí
    find /home -name "informe.pdf"        # Busca informe.pdf en /home
    find . -iname "*.PDF"                 # Busca PDF (ignora mayúsculas)
    ```

!!!example "Buscar por tipo:"
    ```bash
    find . -type f                        # Solo archivos
    find . -type d                        # Solo directorios
    find /var/log -type f -name "*.log"   # Archivos .log en /var/log
    ```

!!!example "Buscar por tamaño:"
    ```bash
    find . -size +100M                    # Archivos mayores de 100 MB
    find . -size -1M                      # Archivos menores de 1 MB
    find /var -size +1G                   # Archivos mayores de 1 GB
    ```

!!!example "Buscar por propietario:"
    ```bash
    find / -user juan                     # Todos los archivos de juan
    find /home -user root                 # Archivos de root en /home
    ```

!!!example "Buscar por permisos:"
    ```bash
    find . -perm 777                      # Archivos con permisos 777
    find / -perm /4000                    # Archivos con SUID
    ```

!!!example "Buscar por fecha:"
    ```bash
    find . -mtime -7                      # Modificados en los últimos 7 días
    find . -mtime +30                     # Modificados hace más de 30 días
    find . -mtime 0                       # Modificados hoy
    ```

!!!example "Combinar criterios:"
    ```bash
    find . -name "*.log" -size +10M       # .log mayores de 10MB
    find /tmp -type f -mtime +7           # Archivos de más de 7 días en /tmp
    ```

!!!example "Ejecutar acciones:"
    ```bash
    find . -name "*.tmp" -delete          # Borra todos los .tmp
    find . -name "*.txt" -exec chmod 644 {} \;    # Cambia permisos
    find . -type f -exec ls -lh {} \;     # Lista detalles de cada archivo
    ```

!!! warning "Usar find con -delete"
    Ten mucho cuidado con `-delete`. Verifica primero la búsqueda sin `-delete` para asegurarte de que encuentra lo correcto.

---

## Archivos y Carpetas Ocultos

En Linux, los archivos y directorios que comienzan con un punto `.` están **ocultos**.

Para crear archivos/carpetas ocultos;

```bash
touch .archivo_oculto                 # Crea archivo oculto
mkdir .carpeta_oculta                 # Crea carpeta oculta
```

Para ver archivos ocultos

```bash
ls -a                                 # Muestra todo (incluidos . y ..)
ls -A                                 # Muestra todo (excepto . y ..)
```

Ejemplos comunes de archivos ocultos habituales en el sistema:

```bash
.bashrc                               # Configuración de bash
.ssh/                                 # Configuración de SSH
.gitignore                            # Configuración de Git
.config/                              # Carpeta de configuraciones
```

---

## Compresión y Archivado

### `gzip` - Comprimir archivos

Comprime archivos individuales con el algoritmo gzip.

```bash
gzip archivo.txt
```
**Resultado:** Se crea `archivo.txt.gz` y se elimina `archivo.txt`

Opciones

```bash
gzip -k archivo.txt                   # Mantiene el archivo original (-keep)
gzip -v archivo.txt                   # Modo verbose
gzip -9 archivo.txt                   # Máxima compresión (más lento)
gzip -1 archivo.txt                   # Mínima compresión (más rápido)
```

---

### `gunzip` - Descomprimir

Descomprime archivos `.gz`.

```bash
gunzip archivo.txt.gz
```
**Resultado:** Se crea `archivo.txt` y se elimina `archivo.txt.gz`

```bash
gunzip -k archivo.txt.gz              # Mantiene el .gz original
```

!!! tip "Alternativa"
    También puedes usar `gzip -d` para descomprimir:
    ```bash
    gzip -d archivo.txt.gz
    ```

---

### `tar` - Archivar y comprimir

`tar` es una herramienta muy potente para **agrupar** múltiples archivos y directorios en un solo archivo (tarball), opcionalmente comprimido.

Sintaxis

```bash
tar [opciones] archivo.tar [archivos/directorios]
```

Opciones principales

| Opción | Descripción |
|--------|-------------|
| `-c` | Crear un nuevo archivo tar |
| `-x` | Extraer archivos de un tar |
| `-t` | Listar contenido sin extraer |
| `-f` | Especifica el nombre del archivo tar |
| `-v` | Modo verbose (muestra progreso) |
| `-z` | Comprime/descomprime con gzip (.tar.gz) |
| `-j` | Comprime/descomprime con bzip2 (.tar.bz2) |
| `-C` | Extrae en directorio específico |

!!! info "tar permite omitir el guión"
    Puedes escribir `tar cvf` o `tar -cvf`, ambos funcionan.

Crear archivos `tar`:

!!!example "Sin comprimir:"
    ```bash
    tar cvf backup.tar /home/juan         # Crea backup.tar
    tar cf archivo.tar fichero1 fichero2  # Agrupa varios ficheros
    ```

!!!example "Con compresión gzip (.tar.gz o .tgz):"
    ```bash
    tar cvzf backup.tar.gz /home/juan     # Crea y comprime
    tar czf proyecto.tar.gz proyecto/     # Forma corta
    ```

!!!example "Con compresión bzip2 (.tar.bz2):"
    ```bash
    tar cvjf backup.tar.bz2 /home/juan    # Mayor compresión, más lento
    ```

Extraer archivos `tar`

!!!example "Sin descomprimir:"
    ```bash
    tar xvf archivo.tar                   # Extrae aquí
    ```

!!!example "Con descompresión gzip:"

    ```bash
    tar xvzf archivo.tar.gz               # Extrae y descomprime
    tar xzf backup.tar.gz                 # Forma corta
    ```

!!!example "Extraer en directorio específico:"

    ```bash
    tar xzf archivo.tar.gz -C /home/juan/Descargas    # Extrae en Descargas
    ```

!!!example "Ver contenido sin extraer"

    ```bash
    tar tvf archivo.tar                   # Lista contenido de .tar
    tar tzf archivo.tar.gz                # Lista contenido de .tar.gz
    ```

Ejemplos prácticos completos

!!!example "Hacer backup de /etc:"
    ```bash
    sudo tar cvzf /home/juan/etc-backup.tar.gz /etc
    ```

!!!example "Hacer backup de tu directorio personal:"
    ```bash
    tar cvzf backup-home.tar.gz ~
    ```

!!!example "Extraer solo un archivo específico:"
    ```bash
    tar xzf backup.tar.gz archivo.txt
    ```

!!!example "Añadir archivos a un tar existente:"
    ```bash
    tar rvf archivo.tar nuevo_archivo.txt    # Solo .tar, no funciona con .tar.gz
    ```

!!! success "Mnemotecnia para tar"
    - **c**reate, e**x**tract, **t**est, **f**ile, **v**erbose, **z**ip (gzip)
    - **Crear**: `tar czf nombre.tar.gz carpeta/`
    - **Extraer**: `tar xzf nombre.tar.gz`
    - **Listar**: `tar tzf nombre.tar.gz`

---

## Ejemplos Prácticos Completos

!!!example "Escenario 1: Organizar archivos"

    ```bash
    # Crear estructura
    mkdir -p Documentos/{2024,2025,2026}

    # Mover PDFs de 2024 a su carpeta
    mv *2024*.pdf Documentos/2024/

    # Copiar documentos importantes a backup
    cp -r Documentos/ /media/USB/backup/

    # Comprimir carpeta de 2024
    tar czf 2024-docs.tar.gz Documentos/2024/
    ```

!!!example "Escenario 2: Limpieza de archivos temporales"

    ```bash
    # Buscar archivos temporales grandes
    find /tmp -type f -size +100M

    # Buscar archivos antiguos
    find /tmp -type f -mtime +30

    # Eliminar archivos .tmp del sistema
    find ~ -name "*.tmp" -delete

    # Buscar y eliminar archivos vacíos
    find ~ -type f -empty -delete
    ```

!!!example "Escenario 3: Backup completo"

    ```bash
    # Crear backup de home con fecha actual
    tar cvzf backup-$(date +%Y%m%d).tar.gz /home/sergio

    # Copiar a disco externo
    cp backup-*.tar.gz /media/USB/backups/

    # Verificar contenido del backup
    tar tzf backup-20260205.tar.gz | less

    # Extraer solo un directorio específico
    tar xzf backup-20260205.tar.gz home/sergio/Documentos
    ```

---

## Resumen de Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `cp` | Copiar archivos/directorios | `cp -r origen destino` |
| `mv` | Mover o renombrar | `mv viejo.txt nuevo.txt` |
| `rm` | Eliminar archivos/directorios | `rm -r carpeta/` |
| `touch` | Crear archivo vacío | `touch nuevo.txt` |
| `file` | Identificar tipo de archivo | `file documento.pdf` |
| `stat` | Información detallada | `stat archivo.txt` |
| `find` | Buscar archivos | `find . -name "*.txt"` |
| `gzip` | Comprimir | `gzip archivo.txt` |
| `gunzip` | Descomprimir | `gunzip archivo.txt.gz` |
| `tar` | Archivar/comprimir | `tar czf backup.tar.gz carpeta/` |

---

## Ejercicios Prácticos

!!! example "Ejercicio 1: Copiar y mover"
    1. Crea un archivo `prueba.txt` con `touch`
    2. Cópialo a `prueba_backup.txt`
    3. Muévelo a tu directorio padre
    4. Renómbralo a `test.txt`

!!! example "Ejercicio 2: Buscar archivos"
    1. Busca todos los archivos `.txt` en tu home
    2. Busca archivos mayores de 10MB
    3. Busca archivos modificados hoy
    4. Busca directorios que contengan "Documentos" en su nombre

!!! example "Ejercicio 3: Backup con tar"
    1. Crea una carpeta `Proyecto` con varios archivos dentro
    2. Crea un backup comprimido: `proyecto-backup.tar.gz`
    3. Elimina la carpeta original
    4. Extrae el backup para recuperar los archivos

---

## Buenas Prácticas

!!! success "Recomendaciones"
    - ✅ Usa `cp -i` y `mv -i` para evitar sobrescribir accidentalmente
    - ✅ Verifica con `ls` antes de usar `rm`
    - ✅ Haz backups regulares con `tar`
    - ✅ Usa `find` antes de `find -delete`
    - ✅ Prueba comandos peligrosos en un entorno seguro primero

!!! danger "Precauciones"
    - ⚠️ **NUNCA** ejecutes `rm -rf /` o `rm -rf /*`
    - ⚠️ Verifica la ruta antes de `rm -rf`
    - ⚠️ Usa `rm -i` cuando borres cosas importantes
    - ⚠️ Ten cuidado con comodines: `rm * .txt` vs `rm *.txt`
    - ⚠️ Como root, cada comando es peligroso

