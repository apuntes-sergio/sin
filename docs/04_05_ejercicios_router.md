---
title: Ejercicios con router — Escenarios de empresa
description: UD04 - Simulación de redes con Packet Tracer
---

Ya sabemos configurar un router básico y conectar dos subredes. Ahora vamos a consolidar ese conocimiento con escenarios más completos que simulan situaciones reales de empresa. Estos ejercicios combinan todo lo aprendido: subnetting, configuración de switches, configuración del router y verificación completa.

## Ejercicio 1 — Empresa con tres departamentos

**Escenario:** MediTech S.L. es una clínica con tres departamentos que necesitan estar en red pero separados lógicamente. Tienen asignado el bloque `192.168.50.0/24`.

**Requisitos:**

| Departamento | Equipos | Subred |
|-------------|---------|--------|
| Recepción | 6 PCs | Primera subred /27 |
| Médicos | 12 PCs | Segunda subred /26 |
| Administración | 4 PCs | Tercera subred /28 |

**Paso 1 — Calcular las subredes (VLSM, de mayor a menor):**

```
Médicos /26:       192.168.50.0/26    hosts: .1–.62    broadcast: .63
Recepción /27:     192.168.50.64/27   hosts: .65–.94   broadcast: .95
Administración /28:192.168.50.96/28   hosts: .97–.110  broadcast: .111
```

**Paso 2 — IPs del router:**

| Interfaz | Conectada a | IP del router |
|----------|------------|---------------|
| Gi0/0 | Switch-Médicos | 192.168.50.1/26 |
| Gi0/1 | Switch-Recepción | 192.168.50.65/27 |
| Gi0/2 | Switch-Admin | 192.168.50.97/28 |

!!! info "¿Mi router tiene suficientes interfaces?"
    El router 1941 de PT tiene 2 interfaces GigabitEthernet por defecto. Para tener una tercera necesitas añadir un módulo:

    1. Doble clic en el router → pestaña **Physical**
    2. Apaga el router (botón de encendido)
    3. Arrastra el módulo **HWIC-1GE** a un slot vacío
    4. Enciende el router de nuevo

    Alternativamente usa el **Router-PT** genérico que tiene más interfaces disponibles.

<!-- IMG: captura de PT con la topología de 3 departamentos y el router central -->
<figure markdown="span" align="center">
  ![Ejercicio 1 tres departamentos](./imgs/packet_tracer/pt_ejercicio_tres_dept.png){ width="85%" }
  <figcaption>Red de MediTech S.L. con tres departamentos conectados a través de un router central</figcaption>
</figure>

**Configuración del router:**

```
Router> enable
Router# configure terminal

Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.50.1 255.255.255.192
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 192.168.50.65 255.255.255.224
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface GigabitEthernet0/2
Router(config-if)# ip address 192.168.50.97 255.255.255.240
Router(config-if)# no shutdown
Router(config-if)# exit

Router# write
```

**Verifica con ping desde cada subred:**

```
Desde PC-Médicos1:
  ping 192.168.50.65   → ✅ Recepción (pasa por router)
  ping 192.168.50.97   → ✅ Administración (pasa por router)
  ping 192.168.50.1    → ✅ Router Gi0/0 (misma subred)

Desde PC-Recepción1:
  ping 192.168.50.1    → ✅ Médicos (pasa por router)
  tracert 192.168.50.97 → debe mostrar el router como primer salto
```

---

## Ejercicio 2 — Red con acceso a internet simulado

**Escenario:** Una empresa tiene su red local y necesita acceso a internet. En PT simulamos internet con una nube y un servidor web externo.

**Topología:**

```
PC-Oficina1 (10.0.0.1/24) ──┐
PC-Oficina2 (10.0.0.2/24) ──┤── Switch ── Router ── Nube ── Servidor-Web (1.1.1.1)
PC-Oficina3 (10.0.0.3/24) ──┘
```

**Configuración:**

| Elemento | IP | Notas |
|---------|-----|-------|
| PCs oficina | 10.0.0.1–3/24 | Gateway: 10.0.0.254 |
| Router Gi0/0 (LAN) | 10.0.0.254/24 | Gateway de los PCs |
| Router Gi0/1 (WAN) | 8.8.8.1/30 | Enlace hacia internet |
| Servidor-Web | 1.1.1.1/8 | Simula un servidor en internet |

**En Packet Tracer:**

1. Añade una **nube** (Network Cloud) entre el router y el servidor web.
2. Conecta el router a la nube con cable **Serial** o **Copper Straight-Through** según el tipo de nube.
3. Añade un **Server** como servidor web externo con IP `1.1.1.1`.

**Ruta estática en el router:**

Para que el router sepa cómo llegar al servidor web necesitamos una ruta estática:

```
Router(config)# ip route 1.0.0.0 255.0.0.0 8.8.8.2
```

Esto le dice al router: "para llegar a la red 1.0.0.0/8, envía los paquetes a 8.8.8.2".

**Verifica:**

```
Desde PC-Oficina1:
  ping 1.1.1.1    → ✅ servidor web externo accesible
  tracert 1.1.1.1 → debe mostrar: 10.0.0.254 (router) → 1.1.1.1
```

---

## Ejercicio 3 — Diagnóstico de red con errores

**Escenario:** Te entregan el fichero `ud04_empresa_errores.pkt` con la red de una empresa que tiene varios problemas. Los usuarios se quejan de que no pueden acceder a otros departamentos.

**La red debería ser:**

```
Dpto A: 172.20.1.0/25  → gateway: 172.20.1.126
Dpto B: 172.20.1.128/26 → gateway: 172.20.1.190
Router: Gi0/0 = 172.20.1.126/25
        Gi0/1 = 172.20.1.190/26
```

**Tu misión:**

1. Abre el fichero y mapea la red: anota la IP, máscara y gateway de cada PC con `ipconfig`.
2. Verifica los enlaces: ¿hay alguno en rojo?
3. Comprueba la configuración del router: `show ip interface brief` en la CLI.
4. Haz ping entre todos los PCs e identifica qué falla.
5. Documenta los errores encontrados y corrígelos.
6. Verifica que todo funciona después de las correcciones.

**Pista:** puede haber errores en los gateways de los PCs, en las IPs del router, en las máscaras o en los cables. Busca sistemáticamente.

---

## Práctica para entregar

**Fichero:** `ud04_practica05.pkt` + documento Word/PDF
**Entrega:** Aules

Diseña e implementa en Packet Tracer la siguiente red empresarial:

**EduTech S.L. — Red completa**

La empresa tiene asignado el bloque `10.10.0.0/24` y estos departamentos:

| Departamento | Equipos | Observaciones |
|-------------|---------|---------------|
| Aulas | 20 PCs | Mayor departamento |
| Profesores | 10 PCs | — |
| Secretaría | 5 PCs | — |
| Servidores | 3 servidores | IPs bajas de su subred |

**Requisitos:**

1. Aplica VLSM para aprovechar el espacio: asigna la máscara mínima necesaria a cada departamento (con margen del 20%).
2. Usa un router central que conecte todos los departamentos.
3. Asigna la primera IP de cada subred al router (gateway).
4. Renombra todos los dispositivos con nombres descriptivos.
5. Verifica conectividad completa:
    - Ping entre todos los departamentos (deben funcionar)
    - Tracert desde Aulas hasta Servidores (debe mostrar el router)

**Documento a entregar:**

| Apartado | Contenido |
|---------|-----------|
| Tabla VLSM | Subred, máscara, red, hosts, broadcast de cada departamento |
| Tabla de IPs | IP asignada a cada dispositivo |
| Configuración del router | Comandos CLI usados (copia y pega desde PT) |
| Capturas de verificación | Ping y tracert entre departamentos |

!!! info "Criterios de evaluación"
    - VLSM correcto y justificado (3 puntos)
    - Red correctamente implementada en PT (3 puntos)
    - Conectividad verificada entre todos los departamentos (2 puntos)
    - Documento completo con tabla de IPs y configuración del router (2 puntos)
