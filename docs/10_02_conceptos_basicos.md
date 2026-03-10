---
title: Conceptos Básicos
description:  Fundamentos de la Línea de Comandos en Linux
---

## CLI - Command Line Interface

La **interfaz de línea de comandos** (CLI) es una pantalla donde el usuario se comunica con el ordenador escribiendo comandos. Aunque requiere memorizar comandos y su sintaxis, proporciona un control total sobre el sistema.

Por sistemas operativos tenemos: 

- **Windows**: Usa el programa `cmd.exe` (Símbolo del sistema) para interpretar comandos de MS-DOS o el programa `powershell.exe` para interpretar comandos PowerShell más avanzados y modernos.
- **GNU/Linux**: Utiliza diferentes shells, siendo `bash` el más común

## El Shell

El **shell** o **intérprete de comandos** es el programa que:

1. Lee el comando que escribes
2. Comprueba si la sintaxis es correcta
3. Ejecuta la acción solicitada
4. Muestra el resultado
5. Vuelve a mostrar el prompt esperando el siguiente comando

## El Prompt

El **prompt** es el símbolo que indica que el sistema está listo para recibir comandos. Además, muestra información útil sobre el contexto actual.

### Prompt en Windows

```cmd
C:\Users\Sergio>
```

- `C:\Users\Sergio` → Directorio actual
- `>` → Símbolo del prompt

### Prompt en GNU/Linux (usuario normal)

```bash
sergio@sergio-virtualbox:~$
```

- `sergio` → Nombre del usuario
- `sergio-virtualbox` → Nombre del equipo
- `~` → Directorio actual (~ representa el directorio personal `/home/sergio`)
- `$` → Símbolo del prompt de usuario normal

### Prompt en GNU/Linux (root)

```bash
root@sergio-virtualbox:/home/sergio#
```

- `root` → Usuario administrador
- `#` → Símbolo del prompt de root
- `/home/sergio` → Directorio actual (no es su home, por eso no aparece ~)

!!! warning "Directorio actual"
    El **directorio actual** es donde nos encontramos en este momento y donde se ejecutarán los comandos por defecto. Si creamos un archivo o carpeta, se creará aquí a menos que especifiquemos otra ruta.

## Sintaxis de un Comando

La estructura básica de cualquier comando es:

```bash
comando [opciones] [parámetros]
```

**Componentes:**

- **comando**: Palabra que expresa la operación a realizar
- **opciones**: Modifican el comportamiento del comando (opcionales)
- **parámetros**: Información necesaria para la ejecución (pueden ser obligatorios u opcionales)

!!! example "Ejemplo"
    ```bash
    mkdir prueba
    ```
    - `mkdir` → comando para crear directorios
    - `prueba` → parámetro (nombre del directorio a crear)

Los elementos de un comando se separan con **espacios en blanco**. El sistema interpreta cada espacio como separador entre comando, opciones y parámetros.

## Caracteres Comodines

Los **comodines** o **wildcards** permiten hacer referencia a múltiples archivos mediante patrones:

| Comodín | Significado | Ejemplo | Coincide con |
|---------|-------------|---------|--------------|
| `*` | Cero o más caracteres | `inf*.txt` | `inf.txt`, `informe.txt`, `informacion_junio.txt` |
| `?` | Un único carácter | `ma?o.jpg` | `mano.jpg`, `mazo.jpg` (NO `marzo.jpg` ni `mao.jpg`) |

Ejemplos con el comando `copy` (Windows): 

!!! example "Copia todos los archivos que empiezan por "inf" con extensión .txt"
    ```cmd
    copy inf*.txt C:\Users\sergi\prueba
    ```


!!! example "Copia todos los archivos .doc que terminan en '_junio'"
    ```cmd
    copy *_junio.doc C:\Users\sergi\prueba
    ```

!!! example "Copia todos los archivos (cualquier nombre y extensión)"
    ```cmd
    copy *.* C:\Users\sergi\prueba
    ```

!!! example "Copia archivos .jpg con patrón 'ma + 1 carácter + o'"
    ```cmd
    copy ma?o.jpg C:\Users\sergi
    ```

## Scripts

Un **script** es un archivo de texto que contiene una lista de comandos, uno por línea. Ejecutar el script equivale a escribir todos esos comandos uno tras otro.

Ventajas de los scripts: 

- ✅ **Automatización**: Ejecuta tareas repetitivas con un solo comando
- ✅ **Consistencia**: Siempre se ejecutan los mismos pasos
- ✅ **Ahorro de tiempo**: No hay que escribir cada comando manualmente
- ✅ **Programación**: Se pueden ejecutar automáticamente (tareas programadas)

!!! example "Uso típico de scripts"
    - Copias de seguridad periódicas
    - Limpieza de archivos temporales
    - Instalación y configuración automatizada
    - Procesamiento batch de archivos

Extensión de scripts:

- **Windows**: `.bat` o `.cmd` para archivos por lotes MS-DOS
- **Windows**: `.ps1` para scripts en PowerShell
- **GNU/Linux**: `.sh` (shell script)

```bash
# Ejemplo de script simple
#!/bin/bash
echo "Iniciando copia de seguridad..."
cp -r /home/usuario/documentos /backup/
echo "Copia completada"
```


!!! tip "Consejos iniciales"
    - El **directorio actual** es importante: determina dónde se ejecutan los comandos
    - Los **espacios separan** los elementos del comando
    - Los **comodines** ahorran tiempo al trabajar con múltiples archivos
    - Los **scripts** son fundamentales para automatización