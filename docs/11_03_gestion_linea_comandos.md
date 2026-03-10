---
title: Gestión desde la Línea de Comandos
description:  Administración de usuarios y grupos en Linux. 
---


La gestión de usuarios y grupos desde la **terminal** es la forma más potente, flexible y eficiente de administrar un sistema GNU/Linux. Además, es la **única opción** en servidores sin entorno gráfico.

!!! success "Ventajas de la terminal"
    - ⚡ **Rapidez**: Operaciones masivas en segundos
    - 🔄 **Automatización**: Scripts para tareas repetitivas
    - 🎯 **Precisión**: Control total sobre cada parámetro
    - 🌐 **Universal**: Funciona en cualquier sistema Linux
    - 📡 **Remoto**: Gestión desde cualquier lugar vía SSH

---

## Archivos de Configuración

En GNU/Linux, **todo son archivos**. La información de usuarios, grupos y contraseñas se almacena en archivos de texto dentro de `/etc`.

Ubicación de los archivos:

| Archivo | Contenido |
|---------|-----------|
| `/etc/passwd` | Información de usuarios |
| `/etc/shadow` | Contraseñas cifradas de usuarios |
| `/etc/group` | Información de grupos |
| `/etc/gshadow` | Contraseñas cifradas de grupos |
| `/etc/sudoers` | Configuración de privilegios sudo |

!!! info "Permisos de lectura"
    Cualquier usuario puede **leer** `/etc/passwd` y `/etc/group`, pero solo **root** puede modificarlos o leer `/etc/shadow`.

---

### `/etc/passwd` - Información de Usuarios

Contiene la información básica de todos los usuarios del sistema.

Cada línea representa un usuario, con **7 campos** separados por dos puntos (`:`):

```
nombre:x:UID:GID:comentario:directorio_home:shell
```

Descripción de los campos:

| Campo | Posición | Descripción | Ejemplo |
|-------|----------|-------------|---------|
| **Nombre** | 1 | Login del usuario | `jperez` |
| **Contraseña** | 2 | `x` (la contraseña está en `/etc/shadow`) | `x` |
| **UID** | 3 | Identificador único del usuario | `1130` |
| **GID** | 4 | Identificador del grupo principal | `1003` |
| **Comentario** | 5 | Nombre completo, teléfono, etc. | `Juan Pérez` |
| **Directorio home** | 6 | Carpeta personal del usuario | `/home/jperez` |
| **Shell** | 7 | Intérprete de comandos | `/bin/bash` |

!!!example "Ejemplos reales"

    ```bash
    root:x:0:0:root:/root:/bin/bash
    sergio:x:1000:1000:Sergio Rey:/home/sergio:/bin/bash
    jperez:x:1130:1003:Juan Pérez:/home/jperez:/bin/bash
    vmarti:x:1131:1003::/home/vmarti:/bin/bash
    www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
    ```

    **Análisis:**

    - `root`: UID=0 (siempre), home en `/root`, shell bash
    - `sergio`: Usuario normal (UID=1000), grupo principal 1000
    - `jperez`: UID=1130, grupo principal 1003 (compartido con vmarti)
    - `www-data`: Usuario del sistema (UID=33), sin shell de login

Para ver el archivo:

```bash
cat /etc/passwd                # Ver todo el archivo
grep sergio /etc/passwd        # Buscar un usuario específico
tail -5 /etc/passwd           # Ver los últimos 5 usuarios
```

!!! tip "Campo de contraseña"
    Antiguamente, las contraseñas se guardaban aquí (campo 2). Por seguridad, ahora solo contiene una `x` y las contraseñas cifradas están en `/etc/shadow`.

---

### `/etc/shadow` - Contraseñas Cifradas

Contiene las contraseñas cifradas y la información de caducidad de las mismas.

Estructura: 

```
nombre:contraseña_cifrada:último_cambio:mín:máx:aviso:inactivo:expiración:reservado
```

Campos principales:

| Campo | Descripción |
|-------|-------------|
| **Nombre** | Login del usuario |
| **Contraseña cifrada** | Hash de la contraseña (algoritmo SHA-512) |
| **Último cambio** | Días desde 01/01/1970 del último cambio de contraseña |
| **Días mínimos** | Días antes de poder cambiar la contraseña |
| **Días máximos** | Días de validez de la contraseña |
| **Días de aviso** | Días antes de expirar que se avisa al usuario |
| **Días inactivos** | Días tras expirar antes de deshabilitar la cuenta |
| **Fecha de expiración** | Días desde 01/01/1970 cuando expira la cuenta |

!!!example "Ejemplo"

    ```bash
    sergio:$6$random$hash...:19740:0:99999:7:::
    jperez:$6$random$hash...:19740:5:90:7:14::
    ```

!!! warning "Solo lectura de root"
    Por seguridad, **solo root** puede leer `/etc/shadow`:
    ```bash
    sudo cat /etc/shadow
    ```


### `/etc/group` - Información de Grupos

Contiene la información de todos los grupos del sistema.

Cada línea representa un grupo, con **4 campos** separados por dos puntos (`:`):

```
nombre_grupo:x:GID:lista_de_miembros
```

Descripción de los campos:

| Campo | Posición | Descripción | Ejemplo |
|-------|----------|-------------|---------|
| **Nombre del grupo** | 1 | Identificador del grupo | `profesores` |
| **Contraseña** | 2 | `x` (en `/etc/gshadow` si existe) | `x` |
| **GID** | 3 | Identificador único del grupo | `1003` |
| **Miembros** | 4 | Usuarios separados por comas | `jperez,vmarti,gllopis` |

!!!example "Ejemplos reales"

    ```bash
    root:x:0:
    sergio:x:1000:
    sudo:x:27:sergio,juan
    alumnos:x:1003:
    smr:x:1004:jperez,vmarti,gllopis,ralos,fmonllor
    profesores:x:1005:sergio,ana,pedro
    ```

    **Análisis:**

    - `root`: GID=0, sin miembros adicionales
    - `sergio`: Grupo personal del usuario sergio
    - `sudo`: Grupo de administradores, contiene a sergio y juan
    - `smr`: Grupo con 5 miembros

!!! info "Miembros del grupo"
    La lista de miembros solo incluye usuarios para los que este es un **grupo secundario**. Los usuarios que lo tienen como grupo principal no aparecen aquí.

Para ver el archivo:

```bash
cat /etc/group                 # Ver todos los grupos
grep profesores /etc/group     # Buscar un grupo específico
grep sergio /etc/group         # Ver en qué grupos está sergio
```


### `/etc/gshadow` - Contraseñas de Grupos

Contiene las contraseñas cifradas de los grupos (raramente usado).

Estructura:

```
nombre_grupo:contraseña_cifrada:administradores:miembros
```

!!! info "Poco usado"
    Las contraseñas de grupos se usan raramente en la práctica. La mayoría de sistemas no las utilizan.

---

### `/etc/sudoers` - Privilegios de sudo

Configura qué usuarios pueden ejecutar `sudo` y con qué privilegios.

!!! danger "No editar directamente"
    **NUNCA edites `/etc/sudoers` directamente** con un editor de texto. Usa el comando `visudo`:
    ```bash
    sudo visudo
    ```
    `visudo` comprueba la sintaxis antes de guardar, evitando errores que podrían dejarte sin acceso sudo.

!!!example  "Ejemplos de configuración"

    ```bash
    # Usuario root tiene todos los privilegios
    root    ALL=(ALL:ALL) ALL

    # Grupo sudo puede ejecutar cualquier comando
    %sudo   ALL=(ALL:ALL) ALL

    # Usuario sergio puede reiniciar sin contraseña
    sergio  ALL=NOPASSWD: /sbin/reboot, /sbin/poweroff
    ```


## Comandos de Información

Antes de gestionar usuarios y grupos, veamos comandos para consultar información.

### `whoami` - Quién soy

Muestra el nombre del usuario actual.

```bash
whoami
```

**Salida:**
```
sergio
```

---

### `id` - Información completa del usuario

Muestra UID, GID y todos los grupos del usuario.

```bash
id                  # Usuario actual
id jperez          # Usuario específico
```

**Salida ejemplo:**
```bash
uid=1000(sergio) gid=1000(sergio) grupos=1000(sergio),27(sudo),1003(profesores),1005(informatica)
```

**Interpretación:**
- UID: 1000 (nombre: sergio)
- GID: 1000 (grupo principal: sergio)
- Grupos: sergio, sudo, profesores, informatica

---

### `groups` - Grupos del usuario

Muestra los grupos a los que pertenece un usuario.

```bash
groups              # Grupos del usuario actual
groups jperez       # Grupos de jperez
```

**Salida ejemplo:**
```
sergio sudo profesores informatica
```


### `who` - Usuarios conectados

Muestra qué usuarios están actualmente conectados al sistema.

```bash
who
```

**Salida ejemplo:**
```
sergio   tty1         2026-02-10 09:30
juan     pts/0        2026-02-10 10:15 (192.168.1.50)
root     pts/1        2026-02-10 11:00
```

**Interpretación:**
- `sergio` está en la consola física (tty1)
- `juan` está conectado por SSH (pts/0) desde 192.168.1.50
- `root` está en una pseudoterminal (pts/1)

Siguiendo exactamente el formato y la estructura de tu documentación para `useradd`, aquí tienes el bloque correspondiente al comando `su`:


### `su` - Cambiar de usuario

Permite cambiar la identidad del usuario actual a otra cuenta durante la sesión de terminal. Por defecto, si no se especifica un nombre, intenta cambiar al superusuario (`root`).

Sintaxis:

```bash
su [opciones] [nombre_usuario]

```

Opciones principales:

| Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `-` (o `-l`) | Inicia sesión de **login** (carga entorno y variables) | `su -` |
| `-c "comando"` | Ejecuta un comando único y finaliza | `-c "ls /root"` |
| `-s /bin/shell` | Utiliza una shell específica para la sesión | `-s /bin/zsh` |

!!!example "Ejemplo: Cambio a root (sesión limpia)"

    ```bash
    su
    ```

    - Solicita la contraseña de **root**.
    - Carga el `PATH` y las variables de entorno de root.
    - Cambia el directorio actual a `/root`.


!!!example "Ejemplo: Cambio a otro usuario estándar"

    ```bash
    su amiro 
    ```

    Cambia la sesión al usuario `amiro`:

    - Solicita la contraseña de **amiro** (no la de root).
    - Carga el entorno completo del usuario destino.
    - El directorio de trabajo pasa a ser `/home/alumnos/amiro`.


!!! danger "Diferencia entre `su` y `su -`"

    Es altamente recomendable usar siempre el guion (`-`).
    
    - **`su`**: Mantiene las variables de entorno del usuario original (puede causar errores de permisos en rutas).
    - **`su -`**: Simula un inicio de sesión real, refrescando todo el entorno de trabajo.

!!! info "Finalizar sesión"
    Para volver al usuario original tras usar `su`, escribe `exit` o pulsa `Ctrl+D`.



## Gestión de Usuarios

### `passwd` - Cambiar contraseña

Permite establecer o cambiar la contraseña de un usuario.

Para cambiar tu propia contraseña

```bash
passwd
```

Te pedirá:
1. Contraseña actual
2. Nueva contraseña (2 veces)

Para cambiar contraseña de otro usuario (root)

```bash
sudo passwd jperez
```

Solo pide la nueva contraseña (2 veces).

Opciones adicionales:

```bash
passwd -l jperez        # Bloquear cuenta (lock)
passwd -u jperez        # Desbloquear cuenta (unlock)
passwd -d jperez        # Eliminar contraseña (sin contraseña)
passwd -e jperez        # Expirar contraseña (fuerza cambio en próximo login)
```

!!! tip "Forzar cambio de contraseña"
    `passwd -e usuario` es útil cuando creas un usuario con contraseña temporal y quieres que la cambie en su primer inicio de sesión.


### `useradd` - Crear usuario

Crea un usuario con opciones específicas.

Sintaxis: 

```bash
sudo useradd [opciones] nombre_usuario
```

Opciones principales:

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-m` | Crea el directorio home | `-m` |
| `-d /ruta` | Especifica directorio home | `-d /home/alumnos/juan` |
| `-s /bin/bash` | Define la shell | `-s /bin/bash` |
| `-g grupo` | Grupo principal | `-g empleados` |
| `-G grupos` | Grupos secundarios | `-G ventas,admin` |
| `-u UID` | Especifica el UID | `-u 1500` |
| `-c "comentario"` | Comentario (nombre completo) | `-c "Juan Pérez"` |

!!!example "Ejemplo: Crear usuario básico"

    ```bash
    sudo useradd -m jperez
    ```
    - Crea usuario `jperez`
    - Crea `/home/jperez`
    - NO tiene contraseña aún (añádela con `passwd`)

!!!example "Ejemplo: Crear usuario completo"
    ```bash
    sudo useradd -m -d /home/alumnos/amiro -g empleados -G isca,ventas -s /bin/bash -c "Antonio Miró" amiro
    ```

    Crea usuario `amiro`:

    - Home: `/home/alumnos/amiro` (debe existir `/home/alumnos`)
    - Grupo principal: `empleados`
    - Grupos secundarios: `isca`, `ventas`
    - Shell: `/bin/bash`
    - Comentario: "Antonio Miró"

!!! warning "useradd NO activa el usuario"
    Después de `useradd`, **debes establecer contraseña**:
    ```bash
    sudo passwd amiro
    ```


### `adduser` - Crear usuario (método interactivo)

Crea usuario de forma **interactiva y completa**.

Sintaxis

```bash
sudo adduser nombre_usuario
```

Cometido: 

1. Crea el usuario
2. Crea el grupo con su mismo nombre (grupo principal)
3. Crea `/home/usuario`
4. **Pide la contraseña**
5. Pide información adicional (nombre completo, teléfono, etc.)
6. Copia archivos de configuración de `/etc/skel/`

!!!example "Ejemplo"

    ```bash
    sudo adduser jperez
    ```

    Salida interactiva:
    ```
    Añadiendo el usuario `jperez' ...
    Añadiendo el nuevo grupo `jperez' (1001) ...
    Añadiendo el nuevo usuario `jperez' (1001) con grupo `jperez' ...
    Creando el directorio personal `/home/jperez' ...
    Copiando los ficheros desde `/etc/skel' ...
    Nueva contraseña: 
    Vuelva a escribir la nueva contraseña: 
    passwd: contraseña actualizada correctamente
    Cambiando la información de usuario para jperez
    Introduzca el nuevo valor, o presione INTRO para usar el valor predeterminado
        Nombre completo []: Juan Pérez
        Número de habitación []: 
        Teléfono de trabajo []: 123456789
        Teléfono de casa []: 
        Otro []: 
    ¿Es correcta la información? [S/n] S
    ```

!!! danger "Recomendado para principiantes, NO PARA NOSOTROS"
    `adduser` es más fácil de usar que `useradd` porque es interactivo y crea todo automáticamente, pero para administradores de sistema no es muy útil si vamos a crear scripts, puesto que no tiene sentido lanzar un script para generar 5 usuarios y que nos pregunte dato a dato de cada uno de ellos. 

#### Añadir usuario a un grupo

`adduser` también sirve para añadir usuarios a grupos:

```bash
sudo adduser amiro produccion
```

Añade el usuario `amiro` al grupo `produccion` (ambos deben existir).


### `usermod` - Modificar usuario

Modifica las propiedades de un usuario existente.

Sintaxis:

```bash
sudo usermod [opciones] nombre_usuario
```

Opciones principales

Mismas que `useradd`:

| Opción | Descripción |
|--------|-------------|
| `-d /nueva/ruta` | Cambiar directorio home |
| `-g nuevo_grupo` | Cambiar grupo principal |
| `-G grupos` | Establecer grupos secundarios |
| `-aG grupos` | Añadir a grupos secundarios (sin quitar los actuales) |
| `-s /bin/shell` | Cambiar shell |
| `-l nuevo_nombre` | Cambiar nombre de usuario (login) |
| `-L` | Bloquear cuenta |
| `-U` | Desbloquear cuenta |

!!!example "Ejemplos"

    Cambiar grupo principal:
    ```bash
    sudo usermod -g profesores jperez
    ```

    Añadir a un grupo sin quitar los demás:
    ```bash
    sudo usermod -aG sudo jperez       # Añade a sudo, mantiene los grupos actuales
    ```

    Reemplazar todos los grupos secundarios:
    ```bash
    sudo usermod -G jefes,ventas,admin jperez    # Ahora solo está en estos 3 grupos
    ```

    !!! danger "Cuidado con -G"
        ```bash
        usermod -G jefes amiro          # ❌ Quita de isca y ventas
        usermod -aG jefes amiro         # ✅ Añade a jefes sin quitar los demás
        ```

    Cambiar shell:
    ```bash
    sudo usermod -s /bin/zsh jperez
    ```

    Cambiar nombre de usuario:
    ```bash
    sudo usermod -l juanp jperez       # jperez ahora se llama juanp
    ```

---

### `userdel` o `deluser` - Eliminar usuario

Elimina una cuenta de usuario.

Sintaxis

```bash
sudo userdel [opciones] nombre_usuario
sudo deluser [opciones] nombre_usuario
```

Opciones:

| Opción | Descripción |
|--------|-------------|
| `-r` | Elimina también el directorio home y correo |
| `--remove-home` | (deluser) Elimina el directorio home |

!!!example "Ejemplos"

    Eliminar usuario sin borrar archivos:
    ```bash
    sudo userdel jperez
    ```
    - Elimina la cuenta
    - `/home/jperez` se conserva

    Eliminar usuario y sus archivos:
    ```bash
    sudo userdel -r jperez
    ```
    - Elimina la cuenta
    - Elimina `/home/jperez` y todo su contenido

    !!! warning "Irreversible"
        Con `-r`, **todos los archivos del usuario se pierden** permanentemente. Haz backup si contienen datos importantes.


## Gestión de Grupos

### `groupadd` o `addgroup` - Crear grupo

Crea un nuevo grupo.

Sintaxis:

```bash
sudo groupadd [opciones] nombre_grupo
sudo addgroup nombre_grupo
```

Opciones: 

| Opción | Descripción |
|--------|-------------|
| `-g GID` | Especifica el GID |

!!!example "Ejemplos"

    ```bash
    sudo groupadd profesores              # Crea grupo con GID automático
    sudo groupadd -g 2000 alumnos        # Crea grupo con GID 2000
    sudo addgroup desarrollo             # Método alternativo
    ```


### `groupdel` o `delgroup` - Eliminar grupo

Elimina un grupo del sistema.

Sintaxis:

```bash
sudo groupdel nombre_grupo
sudo delgroup nombre_grupo
```

!!!example "Ejemplo"

    ```bash
    sudo groupdel temporal
    ```

!!! warning "No eliminar grupo principal"
    No puedes eliminar un grupo si es el **grupo principal** de algún usuario. Primero cambia el grupo principal del usuario.

---

## Ejemplos Prácticos Completos

!!!example "Escenario 1: Nuevo empleado"

    ```bash
    # 1. Crear usuario con adduser (interactivo)
    sudo adduser antonio

    # 2. Añadirlo a grupos necesarios
    sudo usermod -aG sudo,ventas antonio

    # 3. Verificar
    id antonio
    groups antonio
    ```

!!!example "Escenario 2: Proyecto temporal"

    ```bash
    # 1. Crear grupo del proyecto
    sudo groupadd -g 2500 proyecto_x

    # 2. Añadir miembros al proyecto
    sudo usermod -aG proyecto_x juan
    sudo usermod -aG proyecto_x ana
    sudo usermod -aG proyecto_x pedro

    # 3. Verificar miembros
    grep proyecto_x /etc/group
    ```

!!!example "Escenario 3: Usuario que se va de la empresa"

    ```bash
    # 1. Bloquear la cuenta (no puede entrar)
    sudo passwd -l empleado_saliente

    # 2. Hacer backup de sus archivos
    sudo tar czf /backup/empleado_saliente.tar.gz /home/empleado_saliente

    # 3. Eliminar usuario y sus archivos
    sudo userdel -r empleado_saliente
    ```

!!!example "Escenario 4: Cambio de departamento"

    ```bash
    # 1. Quitar de grupo antiguo y añadir a nuevo
    sudo deluser juan ventas
    sudo adduser juan marketing

    # O con usermod (reemplaza todos los grupos secundarios):
    sudo usermod -G marketing,usuarios juan

    # O con usermod (mantiene grupos actuales):
    sudo usermod -aG marketing juan
    ```

---

## Resumen 

Comandos de información: 

| Comando | Función |
|---------|---------|
| `whoami` | Usuario actual |
| `id` | UID, GID y grupos |
| `groups` | Grupos del usuario |
| `who` | Usuarios conectados |

Usuarios:

| Comando | Función |
|---------|---------|
| `passwd` | Cambiar/establecer contraseña |
| `useradd` | Crear usuario (avanzado) |
| `adduser` | Crear usuario (interactivo) |
| `usermod` | Modificar usuario |
| `userdel` | Eliminar usuario |

Grupos: 

| Comando | Función |
|---------|---------|
| `groupadd` | Crear grupo |
| `groupdel` | Eliminar grupo |
| `adduser usuario grupo` | Añadir usuario a grupo |


Archivos de Configuración:

| Archivo | Contenido | Permisos lectura |
|---------|-----------|------------------|
| `/etc/passwd` | Info de usuarios | Todos |
| `/etc/shadow` | Contraseñas cifradas | Solo root |
| `/etc/group` | Info de grupos | Todos |
| `/etc/gshadow` | Contraseñas de grupos | Solo root |
| `/etc/sudoers` | Privilegios sudo | Solo root |


## Buenas Prácticas

!!! success "Recomendaciones"
    - ✅ Usa `adduser` en lugar de `useradd` (más fácil)
    - ✅ Siempre usa `usermod -aG` para añadir grupos (no `-G`)
    - ✅ Haz backup antes de eliminar usuarios con `-r`
    - ✅ Bloquea cuentas en lugar de eliminarlas si hay dudas
    - ✅ Usa `visudo` para editar `/etc/sudoers`
    - ✅ Verifica con `id` y `groups` después de cambios

!!! warning "Precauciones"
    - ⚠️ No edites `/etc/passwd` o `/etc/shadow` manualmente
    - ⚠️ Ten cuidado con `usermod -G` (sin `-a` quita grupos)
    - ⚠️ `userdel -r` es irreversible
    - ⚠️ No elimines grupos que son principales de usuarios
    - ⚠️ Verifica antes de dar privilegios sudo

---

## Ejercicios Prácticos

!!! example "Ejercicio 1: Crear usuarios"
    1. Crea un usuario `alumno1` con `adduser`
    2. Crea un usuario `alumno2` con `useradd -m`
    3. Establece contraseña a alumno2
    4. Verifica ambos usuarios con `id`

!!! example "Ejercicio 2: Gestión de grupos"
    1. Crea un grupo `dam`
    2. Añade alumno1 y alumno2 al grupo dam
    3. Verifica con `groups alumno1` y `groups alumno2`
    4. Consulta `/etc/group` para ver el grupo

!!! example "Ejercicio 3: Modificaciones"
    1. Cambia la shell de alumno1 a `/bin/sh`
    2. Añade alumno1 al grupo sudo
    3. Cambia el nombre de alumno2 a `estudiante2`
    4. Verifica los cambios

!!! example "Ejercicio 4: Limpieza"
    1. Bloquea la cuenta de estudiante2
    2. Intenta iniciar sesión (debe fallar)
    3. Elimina ambos usuarios conservando sus archivos
    4. Elimina el grupo dam

