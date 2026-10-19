---
title: Verificación, documentación y exportación
description: UD04 - Simulación de redes con Packet Tracer
---

El proyecto ISCA_DAM está montado y funcionando. Pero una red que funciona sin documentación es una trampa: si mañana hay una avería, si entra un técnico nuevo o si hay que ampliar la red, nadie sabrá qué hay montado ni por qué se tomaron las decisiones que se tomaron. **Documentar es parte del trabajo, no un extra.**

En este último punto aprenderemos a verificar la red de forma sistemática, a documentarla correctamente y a exportar el esquema de Packet Tracer para entregarlo o compartirlo.

## Verificación sistemática de la red

Antes de dar por terminado cualquier proyecto de red hay que verificarlo de forma **completa y ordenada**, no solo hacer un ping al azar y asumir que todo funciona. Una verificación sistemática sigue siempre el mismo proceso: de lo más simple a lo más complejo, y documentando los resultados.

### Nivel 1 — Verificación física (capa 1)

Antes de probar nada con comandos, revisamos visualmente:

- ✅ Todos los indicadores de enlace están en **verde**
- ✅ No hay ningún enlace en rojo ni en naranja permanente
- ✅ Todos los dispositivos están correctamente nombrados

Si algún enlace está en rojo → cable desconectado o interfaz de router sin `no shutdown`.

### Nivel 2 — Verificación de configuración IP (capa 3)

En cada PC ejecutamos `ipconfig` y verificamos:

```
C:\> ipconfig

FastEthernet0 Connection:
   IP Address............: 192.168.0.11
   Subnet Mask...........: 255.255.255.0
   Default Gateway.......: 192.168.0.1
```

Comprobamos que:
- ✅ La IP es la correcta según la tabla de asignación
- ✅ La máscara es correcta
- ✅ El gateway es la IP del router en esa subred
- ✅ El DNS apunta al servidor (192.168.100.2)

### Nivel 3 — Verificación de conectividad local (misma subred)

Desde cada subred verificamos que todos los equipos de esa subred se comunican entre sí:

```
! Desde PCAula01 — verificar toda la red del aula
ping 192.168.0.10    → PCAulaProfesor
ping 192.168.0.20    → PCAula10

! Desde PCDev01 — verificar toda la red de empresa
ping 192.168.100.2   → Servidor
ping 192.168.100.44  → PCCom04
```

### Nivel 4 — Verificación del router (entre subredes)

Verificamos que el router enruta correctamente entre las dos redes:

```
! Desde PCAula01 hacia la red de empresa
ping 192.168.100.1   → Router (interfaz empresa)
ping 192.168.100.2   → Servidor
ping 192.168.100.11  → PCDev01

! Tracert para confirmar que pasa por el router
tracert 192.168.100.11
  1  192.168.0.1    ← router (primer salto)
  2  192.168.100.11 ← destino
```

### Tabla de verificación completa

Usa esta tabla para documentar los resultados. Marca ✅ o ❌ en cada prueba:

| Origen | Destino | IP destino | Resultado |
|--------|---------|-----------|-----------|
| PCAula01 | PCAula10 | 192.168.0.20 | |
| PCAula01 | Router (GW) | 192.168.0.1 | |
| PCAula01 | Servidor | 192.168.100.2 | |
| PCAula01 | PCDev01 | 192.168.100.11 | |
| PCDev01 | PCCom04 | 192.168.100.44 | |
| PCDev01 | Servidor | 192.168.100.2 | |
| PCDev01 | PCAulaProfesor | 192.168.0.10 | |
| PCAdmon01 | PCDev01 | 192.168.100.11 | |
| PCAdmon01 | PCAula01 | 192.168.0.11 | |

---

## El modo Simulación para entender el tráfico

Antes de documentar, dedica unos minutos a observar cómo fluye el tráfico en **modo Simulación**. Es la mejor forma de afianzar los conceptos:

1. Activa el modo Simulación (botón reloj amarillo).
2. Filtra solo el protocolo **ICMP** (Edit Filters → marca solo ICMP).
3. Desde PCAula01, haz `ping 192.168.100.11` (PCDev01).
4. Avanza paso a paso con **Capture/Forward** y observa:
    - El paquete sale de PCAula01 hacia Switch-Aula
    - Switch-Aula lo envía al Router
    - El Router lo reenvía hacia Switch-Empresa
    - Switch-Empresa lo entrega a PCDev01
    - La respuesta vuelve por el mismo camino

<!-- IMG: captura del modo simulación mostrando un paquete ICMP entre las dos subredes pasando por el router -->
<figure markdown="span" align="center">
  ![Simulación tráfico entre subredes](./imgs/packet_tracer/pt_simulacion_entre_subredes.png){ width="85%" }
  <figcaption>Modo Simulación: un paquete ICMP viajando de la red del aula a la red de empresa pasando por el router</figcaption>
</figure>

!!! tip "Observa también el tráfico ARP"
    Activa ARP además de ICMP en los filtros. Verás cómo antes del primer ping el equipo envía un paquete ARP preguntando "¿quién tiene la IP 192.168.0.1?" para obtener la MAC del router. Esto ilustra perfectamente la diferencia entre capa 2 (MAC) y capa 3 (IP).

---

## Documentación de la red

Una buena documentación de red incluye siempre estos elementos:

### 1. Esquema lógico de red

El diagrama que hemos construido en Packet Tracer es el esquema lógico: muestra cómo están conectados los dispositivos, sus IPs y a qué red pertenecen. Es el documento más importante para el administrador de red.

### 2. Tabla de dispositivos

| Dispositivo | Tipo | IP | Máscara | Gateway | DNS | Ubicación |
|------------|------|----|---------|---------|-----|-----------|
| Router | Router | 192.168.0.1 / 192.168.100.1 | /24 | — | — | Rack central |
| Servidor | Server | 192.168.100.2 | /24 | 192.168.100.1 | 127.0.0.1 | Sala servidores |
| Switch-Aula | Switch | — | — | — | — | Aula |
| Switch-Empresa | Switch | — | — | — | — | Rack central |
| PCAula01–10 | PC | 192.168.0.11–20 | /24 | 192.168.0.1 | 192.168.100.2 | Aula |
| PCAulaProfesor | PC | 192.168.0.10 | /24 | 192.168.0.1 | 192.168.100.2 | Aula |
| PCDev01–05 | PC | 192.168.100.11–15 | /24 | 192.168.100.1 | 192.168.100.2 | Dpto. Desarrollo |
| PCTaller01–03 | PC | 192.168.100.21–23 | /24 | 192.168.100.1 | 192.168.100.2 | Taller |
| ImpTaller01 | Printer | 192.168.100.24 | /24 | — | — | Taller |
| PCAdmon01–03 | PC | 192.168.100.31–33 | /24 | 192.168.100.1 | 192.168.100.2 | Dpto. Administración |
| ImpAdmon01–02 | Printer | 192.168.100.34–35 | /24 | — | — | Dpto. Administración |
| PCCom01–04 | PC | 192.168.100.41–44 | /24 | 192.168.100.1 | 192.168.100.2 | Dpto. Comercial |

### 3. Tabla de subredes

| Red | Dirección | Máscara | Gateway | Hosts utilizables | Uso |
|-----|-----------|---------|---------|------------------|-----|
| Red Aula | 192.168.0.0 | /24 | 192.168.0.1 | 254 | Aula de formación |
| Red Empresa | 192.168.100.0 | /24 | 192.168.100.1 | 254 | Departamentos + servidor |

### 4. Configuración del router

Documenta siempre la configuración activa del router. En PT puedes verla con:

```
Router# show running-config
```

Copia y pega el resultado en el documento de configuración. Así, si el router se resetea o hay que reconfigurar uno nuevo, tenemos los comandos listos.

---

## Exportar el esquema de Packet Tracer

Packet Tracer permite exportar el esquema como imagen para incluirlo en documentos:

**Opción 1 — Captura de pantalla:**
La más sencilla. Usa `Ctrl+Shift+S` en Windows o la herramienta de recorte. Asegúrate de que todos los dispositivos sean visibles antes de capturar.

**Opción 2 — Exportar desde PT:**
`File → Export → Image (PNG/JPG)` — genera una imagen de alta calidad del área de trabajo completa, incluyendo los dispositivos fuera de la pantalla visible.

**Opción 3 — Guardar el fichero .pkt:**
El fichero `.pkt` es el proyecto completo. Es el formato que entregarás en Aules junto con el documento PDF.

---

## Reflexión final de la UD04

Al terminar esta unidad hemos pasado de la teoría a la práctica. Partimos de conceptos como dirección IP, máscara, subnetting y gateway, y los hemos aplicado para diseñar e implementar una red empresarial real.

Lo más importante que debes llevarte de esta unidad:

- **Las IPs no son arbitrarias**: cada una tiene una razón de ser, define a qué red pertenece el equipo y determina cómo se comunica con el resto.
- **El gateway es imprescindible para salir de la propia red**: sin él, los equipos solo pueden hablar con los de su subred.
- **Documentar es parte del trabajo**: una red sin documentación es una red que nadie puede mantener.
- **Esta red la implementaremos en VirtualBox**: en la 2ª evaluación, con Windows Server y Windows 11, montaremos la misma arquitectura pero con máquinas virtuales reales.

!!! info "Próximo paso: UD05 — Virtualización"
    Antes de implementar la red con máquinas virtuales, necesitamos aprender a crear y gestionar VMs. En la UD05 instalaremos VirtualBox, crearemos VMs con Windows Server y Ubuntu, y configuraremos las interfaces de red virtuales. Será el puente entre este diseño en PT y la implementación real de la 2ª evaluación.

---

## Actividad en clase

### 🖥️ Puesta en común del proyecto ISCA_DAM

**Duración:** 30 minutos
**Dinámica:** revisión entre compañeros + corrección del profesor

1. Por parejas, intercambiad los ficheros `.pkt` y revisad el trabajo del compañero:
    - ¿Están todos los dispositivos renombrados correctamente?
    - ¿Las IPs coinciden con la tabla de asignación?
    - ¿Funcionan los pings entre las dos redes?
    - ¿El tracert muestra el router como primer salto?

2. Cada pareja reporta al grupo: ¿qué errores habéis encontrado en el proyecto del compañero?

3. El profesor muestra en el proyector la solución de referencia y resuelve las dudas más frecuentes.

4. **Pregunta de reflexión para el grupo:** Si mañana la empresa crece y añade un nuevo departamento con 8 equipos, ¿qué cambios habría que hacer en la red? ¿Sería necesario un nuevo switch? ¿Cambiaría el subnetting?
