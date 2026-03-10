---
title: Rutas Absolutas y Relativas
description:  Fundamentos de la Línea de Comandos en Linux
---

## Concepto de Ruta

Cuando ejecutamos un comando, normalmente actúa sobre archivos o directorios. Para especificar **dónde** se encuentra un archivo o directorio, utilizamos su **ruta**.

!!! info "Importante"
    Si no especificamos una ruta, el sistema asume que el archivo o directorio está en el **directorio actual**.

**Tipos de Rutas**

Existen dos formas de especificar la ubicación de un archivo o directorio:

### Ruta Absoluta

Especifica la ubicación **completa** desde la raíz del sistema de archivos.

**Características:**

- Siempre comienza desde el principio del sistema
- En **Linux**: Empieza con `/`
- En **Windows**: Empieza con `\` o `C:\`
- Funciona desde cualquier ubicación actual
- Siempre es la misma, independientemente de dónde estemos

!!! example "Ejemplos de rutas absolutas"
    === "Linux"
        ```bash
        /home/juan/Documentos/informe.txt
        /etc/network/interfaces
        /var/log/syslog
        ```
    
    === "Windows"
        ```cmd
        C:\Users\juan\Documentos\informe.txt
        C:\Windows\System32\drivers
        D:\Backups\proyecto.zip
        ```

### Ruta Relativa

Especifica la ubicación **tomando como referencia** el directorio actual.

**Características:**

- No comienza con `/` (Linux) ni con `\` o letra de unidad (Windows)
- Depende de dónde estemos posicionados
- Más corta y rápida de escribir
- Puede cambiar según nuestra ubicación actual

!!! example "Ejemplos de rutas relativas"
    ```bash
    Documentos/informe.txt
    ../proyectos/web/
    ./script.sh
    ```

## Símbolos Especiales

Para facilitar el uso de rutas relativas, existen símbolos especiales:

| Símbolo | Significado | Ejemplo |
|---------|-------------|---------|
| `.` | Directorio actual | `./archivo.txt` |
| `..` | Directorio padre (anterior) | `../carpeta/` |
| `~` | Directorio personal del usuario (solo Linux) | `~/Documentos/` |

!!! tip "Virgulilla (~) en Linux"
    - En el terminal: ++"alt-gr"++++"Ñ"++
    - En un editor (Windows): ++alt++++"126"++

## Ejemplo Práctico

Dada la siguiente estructura de directorios en Windows (`C:\pruebas`):

```
C:\pruebas\
├── asix\
│   ├── alumnos\
│   └── ejercicios\
├── dam\
│   ├── alumnos\
│   ├── ejercicios\
│   └── herramientas\
└── smx\
    ├── alumnos\
    └── ejercicios\
```

### Caso 1: Copiar archivos de `asix\alumnos` a `dam\alumnos`

Si estamos en `C:\pruebas\asix\alumnos`:

=== "Ruta Absoluta"
    ```cmd
    copy C:\pruebas\asix\alumnos\*.* C:\pruebas\dam\alumnos
    ```
    ✅ Funciona desde cualquier ubicación

=== "Ruta Relativa"
    ```cmd
    copy *.* ..\..\dam\alumnos
    ```
    **Explicación del recorrido:**

    - Estamos en: `C:\pruebas\asix\alumnos`
    - Primer `..` → `C:\pruebas\asix`
    - Segundo `..` → `C:\pruebas`
    - Luego entramos en `dam\alumnos`

### Caso 2: Copiar archivos JPG de `dam\ejercicios` a `dam\herramientas`

**Ubicación inicial:** `C:\pruebas\dam\ejercicios`

=== "Ruta Absoluta"
    ```cmd
    copy C:\pruebas\dam\ejercicios\*.jpg C:\pruebas\dam\herramientas
    ```

=== "Ruta Relativa - Opción 1"
    ```cmd
    copy *.jpg ..\herramientas
    ```
    ✅ La más simple: subimos un nivel y entramos en herramientas

=== "Ruta Relativa - Opción 2"
    Si estamos en `C:\pruebas\dam\herramientas`:
    ```cmd
    copy ..\ejercicios\*.jpg .
    ```
    El punto `.` representa el directorio actual (herramientas)

=== "Ruta Relativa - Opción 3"
    Si estamos en `C:\pruebas\dam`:
    ```cmd
    copy ejercicios\*.jpg herramientas
    ```

=== "Ruta Relativa - Opción 4"
    Si estamos en `C:\pruebas\dam\alumnos`:
    ```cmd
    copy ..\ejercicios\*.jpg ..\herramientas
    ```

### Caso 3: Copiar GIF de `asix\herramientas` a `asix\ejercicios`

**Ubicación inicial:** `C:\temp\asix`

=== "Mixta (Relativa + Absoluta)"
    ```cmd
    copy herramientas\*.gif C:\pruebas\asix\ejercicios
    ```
    ✅ Puedes mezclar rutas relativas y absolutas

## Diagrama de Navegación

Para entender mejor las rutas relativas, visualiza la estructura como un árbol:

```
C:\pruebas          ← Para llegar aquí desde asix\alumnos: ..\..
    │
    ├── asix        ← Para llegar aquí desde asix\alumnos: ..
    │   ├── alumnos ← Estamos aquí
    │   └── ejercicios  ← Para llegar aquí: ..\ejercicios
    │
    ├── dam         ← Para llegar aquí desde asix\alumnos: ..\..\dam
    │   ├── alumnos ← Para llegar aquí: ..\..\dam\alumnos
    │   └── ejercicios
    │
    └── smx
```

## Ejercicio Mental

Imagina que estás en `C:\pruebas\smx\ejercicios` y quieres copiar algo a `dam\herramientas`:

<details>
<summary>🤔 Piensa la ruta relativa antes de ver la solución</summary>

```cmd
copy archivo.txt ..\..\dam\herramientas
```

**Recorrido:**

1. `..` → Subo a `smx`
2. `..` → Subo a `pruebas`
3. `dam\herramientas` → Bajo a dam y luego a herramientas

</details>

## Ventajas y Desventajas

| Tipo | Ventajas | Desventajas |
|------|----------|-------------|
| **Absoluta** | ✅ Funciona desde cualquier ubicación<br>✅ No hay ambigüedad<br>✅ Útil en scripts | ❌ Más larga de escribir<br>❌ Menos portable |
| **Relativa** | ✅ Más corta<br>✅ Más flexible<br>✅ Más portable | ❌ Depende de la ubicación actual<br>❌ Puede ser confusa al principio |

## Consejos Prácticos

!!! tip "¿Cuándo usar cada tipo?"
    - **Ruta absoluta**: Cuando escribes scripts o comandos que se ejecutarán desde ubicaciones variables
    - **Ruta relativa**: Para trabajo interactivo y cuando los archivos están cerca de tu ubicación actual

!!! warning "Error común"
    ```cmd
    # ❌ Olvidar dónde estás
    copy archivo.txt destino\
    
    # ✅ Verificar primero tu ubicación
    pwd                    # Linux
    cd                     # Windows (sin parámetros muestra la ruta)
    ```

## Resumen

| Concepto | Descripción |
|----------|-------------|
| **Ruta absoluta** | Desde la raíz del sistema (`/` o `C:\`) |
| **Ruta relativa** | Desde el directorio actual |
| `.` | Directorio actual |
| `..` | Directorio padre |
| `~` | Home del usuario (Linux) |

!!! success "Para recordar"
    - Las rutas absolutas son **completas e inequívocas**
    - Las rutas relativas son **más cortas pero dependen del contexto**
    - Usa `..` para subir niveles y nombres de carpeta para bajar
    - ¡Practica visualizando mentalmente el árbol de directorios!