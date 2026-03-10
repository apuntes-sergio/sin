---
title: Organización del Almacenamiento
description:  Fundamentos de la Línea de Comandos en Linux
---

## Diferencias entre Windows y Linux

Cada sistema operativo organiza sus unidades de almacenamiento de forma diferente. Comprender estas diferencias es fundamental para trabajar correctamente en cada sistema.

### Windows: Sistema de Letras

Windows utiliza **letras** para identificar cada unidad de almacenamiento accesible en el sistema.

**Letras comunes**

| Letra | Uso típico |
|-------|------------|
| `C:\` | Partición del sistema operativo |
| `D:\` | Partición de datos de usuario o unidad de CD/DVD |
| `E:\`, `F:\`, `G:\`... | Otras particiones, dispositivos USB, unidades de red |

!!! example "Ejemplo en Windows"
    ```
    C:\Users\Sergio\Documentos\informe.txt
    D:\Backups\proyecto.zip
    E:\USB\fotos\
    ```

Características de Windows:

- **Múltiples árboles**: Cada letra representa un árbol de directorios independiente
- **Separador**: Utiliza la barra invertida `\` (++alt-gr++"tecla de superíndices"++)
- **Raíz**: Cada unidad tiene su propia raíz (`C:\`, `D:\`, etc.)

### Linux: Sistema de Árbol Único

GNU/Linux utiliza un **único árbol de directorios** donde todas las unidades se integran en la misma estructura.

Características de Linux:

- **Raíz única**: Todo el sistema cuelga de `/` (directorio raíz)
- **Sin letras**: No necesita letras identificativas
- **Separador**: Utiliza la barra normal `/` (++shift++++"7"++)
- **Montaje**: Las unidades se "montan" como carpetas dentro del árbol

!!! example "Ejemplo en GNU/Linux"
    ```
    /home/sergio/Documentos/informe.txt
    /media/usb/fotos/
    /mnt/disco_externo/backups/
    ```

Estructura básica del árbol de Linux:

```
/                           (raíz del sistema)
├── home/                   (directorios personales de usuarios)
│   ├── sergio/
│   └── juan/
├── etc/                    (archivos de configuración)
├── var/                    (archivos variables: logs, bases de datos)
├── usr/                    (programas y aplicaciones)
├── tmp/                    (archivos temporales)
├── media/                  (punto de montaje para dispositivos extraíbles)
└── mnt/                    (punto de montaje manual)
```

### Comparación Visual

=== "Windows"
    ```
    C:\                         D:\
    ├── Users\                  ├── Datos\
    │   └── Sergio\             └── Backups\
    ├── Program Files\
    └── Windows\
    
    (Dos árboles separados)
    ```

=== "Linux"
    ```
    /
    ├── home/
    │   └── sergio/
    ├── media/
    │   └── usb/              (unidad USB montada aquí)
    └── mnt/
        └── disco_d/          (otra partición montada aquí)
    
    (Un único árbol)
    ```

### Tabla Comparativa

| Aspecto | Windows | Linux |
|---------|---------|-------|
| **Identificación** | Letras (`C:\`, `D:\`) | Árbol único (`/`) |
| **Separador de rutas** | `\` (barra invertida) | `/` (barra normal) |
| **Raíz** | Múltiples raíces | Una única raíz `/` |
| **Dispositivos externos** | Letra automática | Montaje en `/media` o `/mnt` |
| **Ejemplo de ruta** | `C:\Users\profesor\imagen.png` | `/home/profesor/imágenes/imagen.png` |

## Ejemplo Práctico

Supongamos que tenemos el archivo `Carta.txt` en el directorio personal del usuario `luis`:

=== "Windows"
    ```
    C:\Users\luis\Carta.txt
    ```

=== "Linux"
    ```
    /home/luis/Carta.txt
    ```

## Puntos de Montaje en Linux

En Linux, las unidades de almacenamiento se **montan** como directorios dentro del árbol principal:

```bash
# Disco duro principal
/                           → Partición raíz (/)

# Partición de datos
/home                       → Puede ser otra partición montada aquí

# Dispositivos extraíbles
/media/sergio/USB           → Pendrive USB
/media/sergio/DATOS         → Disco externo

# Montajes manuales
/mnt/windows_c              → Partición de Windows
/mnt/servidor               → Unidad de red
```

!!! info "Ventaja del sistema Linux"
    Al tener un único árbol, no importa dónde esté físicamente almacenado un archivo (en qué disco o partición). El usuario ve una estructura unificada y coherente.

!!! warning "CUIDADO: Mayúsculas y minúsculas"

    Mientras que *Windows* no es ***case-sensitive***, quiere decir que pondemos poner el nombre de una carpeta o fichero indistintamente en mayúsculas o minúsculas, *Linux* si es ***case-sensitive*** debemos indicar los nombres exactamente con las mayúsculas o minúsculas que tiene:

    - Si archivo se llama `fichero.txt` (todo en minúsculas):

        - En Windows es válido `Fichero.txt`, `fichero.txt`, `FICHERO.TXT`, `FiChErO.TxT`...
        - En Linux solo es válido `fichero.txt` y por ejemplo `Fichero.txt` será otro fichero

## Resumen

| Concepto | Descripción |
|----------|-------------|
| **Windows usa letras** | Cada unidad tiene su árbol independiente (`C:\`, `D:\`) |
| **Linux usa un árbol único** | Todo cuelga de la raíz `/` |
| **Separadores diferentes** | Windows `\` vs Linux `/` |
| **Montaje en Linux** | Las unidades se integran como carpetas en el árbol |

!!! tip "Para recordar"
    - En **Windows** piensa en "múltiples árboles con letras"
    - En **Linux** piensa en "un solo árbol donde todo se integra"
    - El separador te indica el sistema: `\` = Windows, `/` = Linux