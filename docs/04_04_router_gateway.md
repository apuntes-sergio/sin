---
title: El router — Gateway y conexión entre subredes
description: UD04 - Simulación de redes con Packet Tracer
---

En el ejercicio 2 del punto anterior vimos que dos subredes conectadas al mismo switch **no pueden comunicarse entre sí**. Los PCs de Desarrollo no podían hacer ping a los de Dirección aunque estuvieran en el mismo edificio y conectados al mismo switch. Eso es porque el switch trabaja en capa 2 y no sabe nada de direcciones IP.

Para conectar redes diferentes necesitamos un dispositivo de capa 3: el **router**. En este punto aprenderemos qué es el gateway, cómo funciona el router y cómo configurarlo en Packet Tracer para que dos subredes puedan comunicarse.

## El concepto de gateway (puerta de enlace)

Cuando un equipo quiere enviar un paquete a una IP de **su misma red**, lo envía directamente a través del switch — sin pasar por ningún router. Pero cuando el destino está en **otra red**, el equipo no puede llegar directamente: necesita entregárselo a alguien que sí pueda — el **gateway** o puerta de enlace.

El gateway es simplemente la **dirección IP del router** en esa subred. Es como el portal de un edificio: todos los paquetes que van "fuera" del edificio (de la subred) pasan por ahí.

<!-- IMG: diagrama mostrando dos subredes, un router entre ellas y las flechas de tráfico local vs tráfico entre redes -->
<figure markdown="span" align="center">
  ![Gateway diagrama](./imgs/packet_tracer/pt_gateway_diagrama.png){ width="80%" }
  <figcaption>El gateway es la IP del router en cada subred. El tráfico local no pasa por el router; el tráfico entre redes sí.</figcaption>
</figure>

**¿Cómo decide un equipo si el destino es local o remoto?**

Hace la operación AND entre su IP y su máscara, y entre la IP de destino y su misma máscara. Si el resultado es igual → destino local (no necesita router). Si es diferente → destino remoto (envía al gateway).

!!! example "Ejemplo"
    PC-Dev1 tiene IP `172.16.0.1/28` y quiere enviar un paquete a `172.16.0.17` (PC-Dir1, subred /29).

    ```
    172.16.0.1  AND 255.255.255.240  =  172.16.0.0   ← mi red
    172.16.0.17 AND 255.255.255.240  =  172.16.0.16  ← red del destino
    ```

    Los resultados son diferentes → el destino está en otra red → PC-Dev1 envía el paquete al gateway.

## El router en Packet Tracer

En Packet Tracer los routers tienen múltiples interfaces (puertos), cada una conectada a una red diferente. Cada interfaz tiene su propia dirección IP, que actuará como gateway para los equipos de esa red.

<!-- IMG: diagrama de un router con dos interfaces, cada una conectada a una subred diferente -->
<figure markdown="span" align="center">
  ![Router interfaces](./imgs/packet_tracer/pt_router_interfaces.png){ width="75%" }
  <figcaption>El router tiene una interfaz (con su IP) en cada red que conecta. Esa IP es el gateway de cada subred.</figcaption>
</figure>

El modelo más habitual en PT para prácticas es el **Router 1941** o el **Router-PT** genérico. Ambos tienen al menos dos interfaces GigabitEthernet.

### Convención para asignar la IP del gateway

Por convención, al router se le asigna la **primera IP utilizable** o la **última IP utilizable** de cada subred. Lo más habitual es usar la primera:

```
Subred 192.168.1.0/24:
  Gateway → 192.168.1.1   (primera IP)
  PCs     → 192.168.1.10, .20, .30...
```

## Configuración del router: CLI de Cisco IOS

A diferencia de los PCs, los routers no se configuran mediante formularios sino mediante la **CLI de Cisco IOS** — la línea de comandos del sistema operativo de los routers Cisco. No necesitamos conocerla en profundidad, solo los comandos básicos para configurar las interfaces.

Doble clic en el router → pestaña **CLI**.

Al abrir la CLI verás:

```
Would you like to enter the initial configuration dialog? [yes/no]: no
```

Escribe `no` y pulsa Enter. Después:

```
Router>
```

Este es el **modo usuario** (solo lectura). Para configurar necesitamos el **modo privilegiado** y después el **modo de configuración global**:

```
Router> enable
Router# configure terminal
Router(config)#
```

Ahora estamos en modo configuración y podemos configurar las interfaces.

### Configurar una interfaz del router

```
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

- `interface GigabitEthernet0/0` → selecciona la interfaz a configurar
- `ip address [IP] [máscara]` → asigna la IP y la máscara
- `no shutdown` → activa la interfaz (por defecto están apagadas)
- `exit` → vuelve al modo anterior

!!! warning "no shutdown es obligatorio"
    Las interfaces de los routers en Packet Tracer están administrativamente apagadas por defecto. Si olvidas el comando `no shutdown`, el enlace permanecerá en rojo y nada funcionará.

### Guardar la configuración

```
Router# copy running-config startup-config
```

O de forma abreviada:

```
Router# write
```

Esto guarda la configuración para que no se pierda al "apagar" el router.

## Ejemplo completo: conectar dos subredes

Vamos a resolver exactamente el problema del Ejercicio 2 del punto anterior: conectar la subred de Desarrollo con la de Dirección.

**Topología:**

```
PC-Dev1 (172.16.0.1/28) ──┐
PC-Dev2 (172.16.0.2/28) ──┤── Switch-Dev ──┐
...                        ┘                │
                                        Router
                                            │
PC-Dir1 (172.16.0.17/29) ──┐              │
PC-Dir2 (172.16.0.18/29) ──┤── Switch-Dir ─┘
PC-Dir3 (172.16.0.19/29) ──┘
```

**IPs del router:**
- GigabitEthernet0/0 → `172.16.0.14/28` (última IP de la subred de Desarrollo, conectada a Switch-Dev)
- GigabitEthernet0/1 → `172.16.0.22/29` (última IP de la subred de Dirección, conectada a Switch-Dir)

**Paso 1 — Montar la topología en PT:**

1. Añade 2 switches y 1 router (modelo 1941 o Router-PT).
2. Conecta los PCs de Desarrollo a Switch-Dev.
3. Conecta los PCs de Dirección a Switch-Dir.
4. Conecta Switch-Dev a la interfaz **GigabitEthernet0/0** del router.
5. Conecta Switch-Dir a la interfaz **GigabitEthernet0/1** del router.

<!-- IMG: captura de PT con la topología completa de dos subredes y el router en el centro -->
<figure markdown="span" align="center">
  ![Topología dos subredes](./imgs/packet_tracer/pt_dos_subredes_router.png){ width="85%" }
  <figcaption>Topología con dos subredes conectadas por un router en Packet Tracer</figcaption>
</figure>

**Paso 2 — Configurar el router:**

Doble clic en el router → CLI:

```
Router> enable
Router# configure terminal

Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 172.16.0.14 255.255.255.240
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 172.16.0.22 255.255.255.248
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# exit
Router# write
```

**Paso 3 — Configurar el gateway en cada PC:**

Ahora cada PC debe saber cuál es su gateway (la IP del router en su subred):

| PC | IP | Máscara | Gateway |
|----|-----|---------|---------|
| PC-Dev1 | 172.16.0.1 | 255.255.255.240 | **172.16.0.14** |
| PC-Dev2 | 172.16.0.2 | 255.255.255.240 | **172.16.0.14** |
| ... | ... | ... | **172.16.0.14** |
| PC-Dir1 | 172.16.0.17 | 255.255.255.248 | **172.16.0.22** |
| PC-Dir2 | 172.16.0.18 | 255.255.255.248 | **172.16.0.22** |
| PC-Dir3 | 172.16.0.19 | 255.255.255.248 | **172.16.0.22** |

En cada PC: Desktop → IP Configuration → Default Gateway → introducir la IP del router en esa subred.

**Paso 4 — Verificar:**

Desde PC-Dev1:
```
ping 172.16.0.17
```

Ahora debería funcionar. El paquete va de PC-Dev1 → Switch-Dev → Router → Switch-Dir → PC-Dir1.

!!! tip "Usa tracert para ver la ruta"
    El comando `tracert [IP]` muestra todos los saltos que da un paquete hasta llegar al destino. Es muy útil para verificar que el tráfico pasa realmente por el router:

    ```
    C:\> tracert 172.16.0.17
    
    Tracing route to 172.16.0.17 over a maximum of 30 hops:
    
      1    1 ms    172.16.0.14    ← el router (primer salto)
      2    1 ms    172.16.0.17    ← el destino
    
    Trace complete.
    ```

    Si solo hay 1 salto en el tracert (sin pasar por el router), algo está mal.

## Resumen: comandos básicos de verificación

| Comando | Dónde | Qué hace |
|---------|-------|---------|
| `ipconfig` | PC (CMD) | Muestra IP, máscara y gateway del equipo |
| `ping [IP]` | PC (CMD) | Verifica conectividad con otro equipo |
| `tracert [IP]` | PC (CMD) | Muestra la ruta hasta el destino |
| `show ip interface brief` | Router (CLI) | Muestra el estado de todas las interfaces |
| `show running-config` | Router (CLI) | Muestra la configuración activa completa |

---

## Actividad en clase

### 🖥️ Actividad conjunta: conectamos Desarrollo y Dirección

**Duración:** 50 minutos (1 hora de clase)
**Dinámica:** práctica guiada

El profesor va paso a paso construyendo la red del ejemplo anterior. Los alumnos replican en sus equipos simultáneamente. Al final todos deben tener ping funcionando entre las dos subredes.

**Puntos de verificación durante la práctica:**

1. ✅ Topología montada y todos los enlaces en verde
2. ✅ Router configurado (ambas interfaces activas)
3. ✅ Gateway configurado en todos los PCs
4. ✅ Ping entre subredes funcionando
5. ✅ Tracert mostrando el router como primer salto
