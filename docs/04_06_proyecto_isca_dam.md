---
title: Proyecto — Red de ISCA_DAM en Packet Tracer
description: UD04 - Simulación de redes con Packet Tracer
---

Ha llegado el momento de aplicar todo lo aprendido en un proyecto real. Vamos a diseñar e implementar en Packet Tracer la red completa de **ISCA_DAM**, una empresa dedicada a servicios informáticos, mantenimiento y formación. Esta red será el punto de partida del proyecto que desarrollaremos durante toda la 2ª evaluación, donde la implementaremos con máquinas virtuales en VirtualBox.

!!! info "Conexión con el resto del curso"
    El diseño que hagáis en este punto es el plano de la red que vais a construir en la 2ª evaluación con Windows Server. Lo que aquí hacéis en simulación, lo montaréis después con máquinas virtuales reales. Es importante que el diseño sea correcto desde el principio.

## La empresa: ISCA_DAM

ISCA_DAM es una empresa dedicada a servicios informáticos, mantenimiento y formación. Tiene los siguientes departamentos y equipos:

| Departamento | Equipos | Observaciones |
|-------------|---------|---------------|
| Dpto. Desarrollo | 5 PCs | — |
| Dpto. Soporte Técnico (Taller) | 3 PCs | + 1 impresora multifunción |
| Dpto. Administración | 3 PCs | + 2 equipos multifunción |
| Dpto. Comercial | 4 PCs | — |
| Aula de Formación | 11 equipos | 1 profesor + 10 alumnos |

**Hardware de red disponible:**

- 2 switches 24+2 (24 puertos Gigabit + 2 puertos 10 Gigabit)
- 1 router con acceso a internet
- 1 servidor con 2 tarjetas de red (una para la red del aula, otra para la red de empresa)

**Topología general:**

```
Internet
    │
  Router
    │
  ┌─┴──────────────────────┐
  │                        │
Switch-Aula           Switch-Empresa
  │                        │
Aula Formación        Servidor + 4 departamentos
(11 equipos)
```

El servidor está conectado a ambos switches (una NIC en cada red) y actúa como servidor de datos, DNS y dominio para toda la empresa. El router conecta ambas redes con internet.

!!! info "Simplificación respecto al enunciado original"
    En el enunciado original el servidor tiene 3 NICs (internet + aula + empresa). En Packet Tracer simplificamos: el router gestiona la conexión a internet y la interconexión de redes. El servidor tiene 1 NIC en la red empresa y da servicio a toda la empresa a través del router. En la 2ª evaluación con VirtualBox implementaremos la versión completa con el servidor multihomed.

## Paso 1 — Diseño del subnetting

Necesitamos **5 subredes** usando direccionamiento de **clase C**:

- 4 departamentos de empresa (Desarrollo, Taller, Administración, Comercial)
- 1 red de aula

Usaremos **dos bloques de clase C** separados para distinguir claramente la red del aula de la red de empresa, tal como indica el diagrama:

| Red | Bloque | Uso |
|-----|--------|-----|
| Red Aula | `192.168.0.0/24` | Aula de formación |
| Red Empresa | `192.168.100.0/24` | Departamentos + servidor |

Dentro de `192.168.100.0/24` no hacemos subnetting por departamento (todos comparten la /24) pero asignamos rangos de IPs diferenciados por departamento para organización:

### Tabla de direccionamiento completa

| Dispositivo | IP | Máscara | Gateway | DNS |
|------------|-----|---------|---------|-----|
| **Router — interfaz Aula** | 192.168.0.1 | 255.255.255.0 | — | — |
| **Router — interfaz Empresa** | 192.168.100.1 | 255.255.255.0 | — | — |
| **Servidor — NIC Empresa** | 192.168.100.2 | 255.255.255.0 | 192.168.100.1 | 127.0.0.1 |
| **PCAulaProfesor** | 192.168.0.10 | 255.255.255.0 | 192.168.0.1 | 192.168.100.2 |
| PCAula01–PCAula10 | 192.168.0.11–20 | 255.255.255.0 | 192.168.0.1 | 192.168.100.2 |
| **PCDev01–PCDev05** | 192.168.100.11–15 | 255.255.255.0 | 192.168.100.1 | 192.168.100.2 |
| **PCTaller01–PCTaller03** | 192.168.100.21–23 | 255.255.255.0 | 192.168.100.1 | 192.168.100.2 |
| ImpTaller01 | 192.168.100.24 | 255.255.255.0 | 192.168.100.1 | — |
| **PCAdmon01–PCAdmon03** | 192.168.100.31–33 | 255.255.255.0 | 192.168.100.1 | 192.168.100.2 |
| ImpAdmon01–ImpAdmon02 | 192.168.100.34–35 | 255.255.255.0 | — | — |
| **PCCom01–PCCom04** | 192.168.100.41–44 | 255.255.255.0 | 192.168.100.1 | 192.168.100.2 |

!!! tip "Convención de rangos de IP por departamento"
    Asignar rangos diferenciados (.11–.15 para Desarrollo, .21–.24 para Taller...) no es obligatorio técnicamente, pero es una buena práctica que facilita enormemente la administración y el diagnóstico de la red. Con solo ver la IP de un equipo sabes a qué departamento pertenece.

## Paso 2 — Topología en Packet Tracer

### Dispositivos necesarios

| Dispositivo PT | Cantidad | Modelo recomendado |
|---------------|---------|-------------------|
| Router | 1 | Router-PT o 1941 |
| Switch | 2 | Switch 2960 |
| PC | 18 | PC-PT |
| Servidor | 1 | Server-PT |
| Impresora | 3 | Printer-PT |
| Nube (internet) | 1 | Cloud-PT |

### Esquema de conexiones

```
Cloud-PT (internet)
      │ Serial/Ethernet
   Router-PT
   │         │
   │Gi0/0    │Gi0/1
   │         │
Switch-Aula  Switch-Empresa
   │              │
   ├─ PCAulaProfesor    ├─ Servidor
   ├─ PCAula01          ├─ PCDev01–05
   ├─ PCAula02          ├─ PCTaller01–03
   ├─ ...               ├─ ImpTaller01
   └─ PCAula10          ├─ PCAdmon01–03
                        ├─ ImpAdmon01–02
                        ├─ PCCom01–04
                        └─ (Servidor también conectado aquí)
```

<!-- IMG: captura del proyecto ISCA_DAM completo montado en Packet Tracer -->
<figure markdown="span" align="center">
  ![ISCA_DAM en Packet Tracer](./imgs/packet_tracer/pt_isca_dam_completo.png){ width="95%" }
  <figcaption>Red completa de ISCA_DAM implementada en Packet Tracer</figcaption>
</figure>

### Qué conectar a cada puerto del switch

Los switches son **24+2** (24 puertos Gigabit + 2 puertos 10 Gigabit). La distribución es:

**Switch-Aula:**

| Puerto | Velocidad | Dispositivo |
|--------|----------|-------------|
| Fa0/1 – Fa0/11 | 100 Mbps / 1 Gbps | PCAula01 – PCAula10 + PCAulaProfesor |
| Gi0/1 | 10 Gbps | Enlace al Router (uplink) |
| Gi0/2 | 10 Gbps | Libre (reserva) |

**Switch-Empresa:**

| Puerto | Velocidad | Dispositivo |
|--------|----------|-------------|
| Fa0/1 – Fa0/5 | 100 Mbps / 1 Gbps | PCDev01–05 |
| Fa0/6 – Fa0/9 | 100 Mbps / 1 Gbps | PCTaller01–03 + ImpTaller01 |
| Fa0/10 – Fa0/14 | 100 Mbps / 1 Gbps | PCAdmon01–03 + ImpAdmon01–02 |
| Fa0/15 – Fa0/18 | 100 Mbps / 1 Gbps | PCCom01–04 |
| Gi0/1 | 10 Gbps | Enlace al Router (uplink) |
| Gi0/2 | 10 Gbps | Servidor (conexión de alta velocidad) |

## Paso 3 — Configuración del router

La configuración del router se proporciona ya resuelta. Cópiala en la CLI del router de Packet Tracer:

```
Router> enable
Router# configure terminal

! Interfaz hacia la red del Aula
Router(config)# interface GigabitEthernet0/0
Router(config-if)# description Red-Aula
Router(config-if)# ip address 192.168.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Interfaz hacia la red de Empresa
Router(config)# interface GigabitEthernet0/1
Router(config-if)# description Red-Empresa
Router(config-if)# ip address 192.168.100.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Ruta por defecto hacia internet (nube PT)
Router(config)# ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/2

Router(config)# exit
Router# write
```

**Verificar que el router está bien configurado:**

```
Router# show ip interface brief
```

Debes ver las dos interfaces activas (Status: up, Protocol: up) con sus IPs correctas.

## Paso 4 — Montaje en Packet Tracer

Sigue este orden para montar la red de forma organizada:

1. **Coloca los dispositivos** — organízalos visualmente por zonas: aula a la izquierda, empresa a la derecha, router y switches en el centro.
2. **Renombra todos los dispositivos** — doble clic en el nombre para cambiarlo. Usa exactamente los nombres de la tabla.
3. **Conecta los cables** — usa cable Automatic entre todos los dispositivos.
4. **Configura el router** — pega los comandos de la CLI del paso anterior.
5. **Configura las IPs** — en cada PC y servidor: Desktop → IP Configuration → Static. Introduce IP, máscara, gateway y DNS según la tabla.
6. **Espera a que los enlaces estén en verde** antes de hacer pruebas.

## Paso 5 — Verificación completa

Una vez montada la red, verifica estos escenarios:

### Verificación dentro de la red del aula
```
Desde PCAula01:
  ping 192.168.0.20    → ✅ PCAula10 (misma red)
  ping 192.168.0.1     → ✅ Router (gateway)
```

### Verificación dentro de la red de empresa
```
Desde PCDev01:
  ping 192.168.100.44  → ✅ PCCom04 (misma red)
  ping 192.168.100.2   → ✅ Servidor
  ping 192.168.100.1   → ✅ Router (gateway)
```

### Verificación entre redes (pasa por el router)
```
Desde PCAula01:
  ping 192.168.100.11  → ✅ PCDev01 (otra red, pasa por router)
  tracert 192.168.100.2 → debe mostrar 192.168.0.1 como primer salto

Desde PCDev01:
  ping 192.168.0.10    → ✅ PCAulaProfesor (otra red, pasa por router)
```

### Verificación del servidor desde ambas redes
```
Desde PCAula01:
  ping 192.168.100.2   → ✅ Servidor accesible desde el aula

Desde PCAdmon01:
  ping 192.168.100.2   → ✅ Servidor accesible desde empresa
```

---

## Entrega del proyecto

**Ficheros:** `ud04_proyecto_isca_dam.pkt` + documento PDF/Word
**Entrega:** Aules

### Apartado 1 — Tabla de subnetting

Completa la siguiente tabla con los datos de las dos redes:

| Red | Dirección | Máscara | Primer host | Último host | Broadcast | Hosts útiles |
|-----|-----------|---------|------------|------------|-----------|-------------|
| Red Aula | | | | | | |
| Red Empresa | | | | | | |

### Apartado 2 — Tabla de asignación de IPs

Completa la tabla de todos los dispositivos con su IP, máscara, gateway y DNS (usa la tabla del enunciado como referencia y adapta si has hecho cambios justificados).

### Apartado 3 — Esquema de la red

Adjunta una captura del proyecto en Packet Tracer con todos los dispositivos visibles y nombrados. El esquema debe permitir identificar claramente:

- Las dos redes (Aula y Empresa)
- Los dos switches y el router
- Todos los departamentos con sus PCs
- El servidor

### Apartado 4 — Verificación de conectividad

Adjunta capturas de los siguientes pings:

1. Ping desde PCAula01 hasta PCAula10 (dentro del aula)
2. Ping desde PCDev01 hasta PCCom04 (dentro de empresa)
3. Ping desde PCAula01 hasta PCDev01 (entre redes, pasa por router)
4. Tracert desde PCAula01 hasta el Servidor (debe mostrar el router como primer salto)

### Apartado 5 — Reflexión

Responde en 3-4 líneas: ¿Qué cambiaría en este diseño si en lugar de un router usáramos el servidor con 3 tarjetas de red para conectar las redes? ¿Qué ventajas e inconvenientes tendría?

!!! info "Criterios de evaluación"
    - Tabla de subnetting correcta (1 punto)
    - Tabla de IPs completa y correcta (2 puntos)
    - Red montada en PT con dispositivos renombrados (3 puntos)
    - Verificación de conectividad con capturas (3 puntos)
    - Reflexión final razonada (1 punto)
