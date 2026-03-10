---
title: Usuarios y Grupos en GNU/Linux
description:  Administración de usuarios y grupos en Linux. 
---

GNU/Linux es un **sistema operativo multiusuario**: múltiples usuarios pueden trabajar simultáneamente en el mismo sistema de forma segura y organizada.

Para garantizar la seguridad y el correcto funcionamiento del sistema, Linux implementa mecanismos de:

- **Autenticación**: Verificar la identidad de los usuarios
- **Autorización**: Controlar qué puede hacer cada usuario
- **Protección de datos**: Aislar los datos de cada usuario

---

## Cuentas de Usuario

Para utilizar un sistema GNU/Linux es necesario disponer de una **cuenta de usuario** que consta de:

| Componente | Descripción | Ejemplo |
|------------|-------------|---------|
| **Login** | Nombre de usuario (identificador único) | `sergio`, `jperez`, `alumno1` |
| **Password** | Contraseña (para autenticación) | `********` |

Proceso de inicio de sesión:

1. El usuario introduce su **login** (nombre de usuario)
2. El sistema solicita la **password** (contraseña)
3. Si las credenciales son correctas → Acceso concedido
4. Si son incorrectas → Acceso denegado

!!! warning "Seguridad"
    Si te equivocas al introducir tu nombre de usuario o contraseña, el sistema te denegará el acceso y no podrás entrar. Este mecanismo protege el sistema contra accesos no autorizados.



## El Usuario `root`

El **usuario root** es el **administrador del sistema** en GNU/Linux.

### Características de `root`

- ✅ **Control total**: Puede hacer cualquier operación en el sistema
- ✅ **Crea usuarios**: Gestiona todas las cuentas del sistema
- ✅ **Sin restricciones**: No tiene límites de permisos
- ⚠️ **Altamente peligroso**: Un error puede destruir el sistema

!!! danger "Precaución con root"
    El usuario root tiene poder absoluto sobre el sistema. Un comando mal escrito puede:
    - Borrar todo el sistema operativo
    - Eliminar datos de todos los usuarios
    - Dejar el sistema inoperativo
    
    **Regla de oro**: Solo usa root cuando sea estrictamente necesario.

### Usuarios administradores

Además de root, en sistemas como Ubuntu existen **usuarios administradores** que:

- Pertenecen al grupo **sudo** (o **wheel** en otras distribuciones)
- Pueden ejecutar comandos con privilegios de root usando `sudo`
- Tienen que introducir su contraseña para confirmar acciones administrativas

```bash
sudo apt update              # Ejecuta como root, pide tu contraseña
sudo systemctl restart nginx # Acción administrativa
```

### Identificadores de Usuario: UID

Cada usuario del sistema tiene un **UID** (User IDentifier) - un número único que lo identifica.

Rangos de UID:

| Rango UID | Tipo de Usuario | Ejemplo |
|-----------|-----------------|---------|
| **0** | Usuario root (siempre) | root = 0 |
| **1-999** | Usuarios del sistema (daemons, servicios) | www-data, mysql, nginx |
| **1000+** | Usuarios normales (creados por nosotros) | sergio = 1000, juan = 1001 |

!!! info "¿Por qué números?"
    Internamente, Linux trabaja con números (UID) en lugar de nombres. Es más eficiente y evita problemas con nombres duplicados.

Ejemplos de UID: 

```bash
root:x:0:0:root:/root:/bin/bash           # UID = 0
sergio:x:1000:1000:Sergio:/home/sergio:/bin/bash   # UID = 1000
juan:x:1001:1001:Juan Perez:/home/juan:/bin/bash   # UID = 1001
```


## Grupos de Usuarios

Los **grupos** permiten organizar usuarios y gestionar permisos de forma eficiente.

Para comprender el uso de los grupos, imagina un centro educativo:

- Grupo **profesores**: Acceso a carpetas de notas, exámenes, materiales
- Grupo **alumnos**: Acceso a carpetas de trabajos, apuntes

Cuando creas un nuevo profesor:

- Lo añades al grupo "profesores"
- Automáticamente tiene acceso a todo lo que necesita
- No tienes que configurar permisos individualmente

!!! tip "Administración eficiente"
    Los grupos permiten **administrar permisos colectivamente** en lugar de usuario por usuario.

Características de los grupos: 

- ✅ Un grupo puede contener **múltiples usuarios**
- ✅ Un usuario puede pertenecer a **múltiples grupos**
- ❌ Los grupos **NO pueden contener otros grupos**
- ✅ Todo usuario debe tener un **grupo principal** (obligatorio)
- ✅ Puede tener **grupos secundarios** (opcional)


### Identificadores de Grupo: GID

Cada grupo tiene un **GID** (Group IDentifier) - un número único que lo identifica.

Rangos de GID: 

| Rango GID | Tipo de Grupo | Ejemplo |
|-----------|---------------|---------|
| **0** | Grupo root (siempre) | root = 0 |
| **1-999** | Grupos del sistema | www-data, mysql, docker |
| **1000+** | Grupos normales (creados por nosotros) | profesores = 1000, alumnos = 1001 |

!!! info "GID = UID"
    Cuando creas un usuario, normalmente se crea automáticamente un grupo con el mismo nombre y mismo número (GID = UID).

Ejemplos de GID:

```bash
root:x:0:                              # GID = 0
sergio:x:1000:                         # GID = 1000, grupo principal de sergio
profesores:x:1003:juan,ana,pedro       # GID = 1003, miembros: juan, ana, pedro
```

### Grupo Principal vs Grupos Secundarios

Grupo Principal (Primario):

- **Obligatorio**: Todo usuario DEBE tener un grupo principal
- **Único**: Solo puede tener UN grupo principal
- **Por defecto**: Los archivos que crea el usuario pertenecen a este grupo
- **Creación automática**: Normalmente se crea un grupo con el nombre del usuario

Grupos Secundarios:

- **Opcionales**: Un usuario puede no tener grupos secundarios
- **Múltiples**: Un usuario puede pertenecer a muchos grupos secundarios
- **Permisos adicionales**: Dan acceso a recursos compartidos

Ejemplo visual:

```
Usuario: juan
├── Grupo Principal: juan (GID 1001)
└── Grupos Secundarios:
    ├── profesores (GID 1003)
    ├── informatica (GID 1005)
    └── claustro (GID 1010)
```

**¿Qué significa esto?**

- Juan crea archivos que pertenecen al grupo "juan"
- Juan puede acceder a recursos del grupo "profesores"
- Juan puede acceder a recursos del grupo "informatica"
- Juan puede acceder a recursos del grupo "claustro"


## Ejemplo Práctico: Centro Educativo

**Usuarios:**
```
sergio  (UID: 1000) - Profesor de informática
ana     (UID: 1001) - Profesora de matemáticas
juan    (UID: 1002) - Alumno de 2º DAM
maria   (UID: 1003) - Alumna de 2º DAM
```

**Grupos:**
```
profesores    (GID: 2000) - Miembros: sergio, ana
alumnos       (GID: 2001) - Miembros: juan, maria
dam           (GID: 2002) - Miembros: juan, maria
informatica   (GID: 2003) - Miembros: sergio, juan (alumno destacado)
```

**Permisos resultantes:**

| Usuario | Grupo Principal | Grupos Secundarios | Acceso |
|---------|----------------|-------------------|---------|
| sergio | sergio | profesores, informatica | Carpetas de profesores + informatica |
| ana | ana | profesores | Carpetas de profesores |
| juan | juan | alumnos, dam, informatica | Carpetas de alumnos + DAM + informatica |
| maria | maria | alumnos, dam | Carpetas de alumnos + DAM |

---

## Internamente: UID y GID

El sistema GNU/Linux trabaja **internamente con números** (UID y GID), no con nombres.

¿Por qué?

- ⚡ **Eficiencia**: Comparar números es más rápido que comparar texto
- 🔒 **Seguridad**: Evita problemas con caracteres especiales
- 🎯 **Unicidad**: Los números garantizan identificación única
- 💾 **Espacio**: Los números ocupan menos que los nombres

Los nombres son para humanos

```bash
# Lo que ves:
-rw-r--r-- 1 sergio profesores 1234 feb 10 10:30 documento.txt

# Lo que ve el sistema:
-rw-r--r-- 1 1000 2000 1234 feb 10 10:30 documento.txt
```

!!! info "Traducción automática"
    Cuando ejecutas `ls -l`, el sistema traduce automáticamente los UID/GID a nombres para que sean legibles. Consulta `/etc/passwd` y `/etc/group` para hacer la traducción.

---

## Arquitectura de Seguridad

El sistema de usuarios y grupos en Linux proporciona:

1. **Aislamiento**. Cada usuario tiene su propio espacio:

    - **Directorio personal**: `/home/usuario`
    - **Archivos privados**: Solo accesibles por él
    - **Procesos propios**: Ejecuta programas en su contexto

2. **Control de Acceso**

    - Solo el propietario (o root) puede modificar sus archivos
    - Los grupos permiten compartir selectivamente
    - Los permisos definen qué puede hacer cada uno

3. **Auditoría**

    - Cada archivo tiene un propietario conocido
    - Se puede rastrear quién hizo qué
    - Los logs registran acciones por usuario


## Comandos Básicos de Información

Aunque veremos comandos en profundidad más adelante, aquí tienes algunos básicos:

```bash
whoami              # ¿Quién soy yo?
id                  # Mi UID, GID y grupos
groups              # Mis grupos
who                 # ¿Quién está conectado?
```

!!!example "Ejemplo de salida:"
    ```bash
    $ whoami
    sergio

    $ id
    uid=1000(sergio) gid=1000(sergio) grupos=1000(sergio),27(sudo),1003(profesores)

    $ groups
    sergio sudo profesores

    $ who
    sergio   tty1         2026-02-10 09:30
    juan     pts/0        2026-02-10 10:15
    ```

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| **Cuenta de usuario** | Login + Password |
| **root** | Superusuario con control total |
| **UID** | Identificador numérico único del usuario |
| **Grupo** | Conjunto de usuarios para administración de permisos |
| **GID** | Identificador numérico único del grupo |
| **Grupo principal** | Grupo primario obligatorio de cada usuario |
| **Grupos secundarios** | Grupos adicionales opcionales |
| **Multiusuario** | Múltiples usuarios trabajando simultáneamente |

---

## Conceptos Clave

!!! success "Para recordar"
    - Linux es **multiusuario**: Varios usuarios pueden trabajar a la vez
    - Todo usuario necesita **login y password**
    - **root** es el administrador supremo (usar con precaución)
    - Los usuarios se identifican con **UID** (números únicos)
    - Los grupos se identifican con **GID** (números únicos)
    - Todo usuario **debe** tener un grupo principal
    - Un usuario puede pertenecer a **múltiples grupos secundarios**
    - Los grupos facilitan la **administración de permisos**

!!! tip "Buenas prácticas"
    - ✅ Trabaja con tu usuario normal habitualmente
    - ✅ Solo usa root/sudo cuando sea necesario
    - ✅ Organiza usuarios en grupos lógicos
    - ✅ Asigna permisos a grupos, no a usuarios individuales
    - ✅ Los UIDs/GIDs ≥ 1000 son para usuarios y grupos normales

