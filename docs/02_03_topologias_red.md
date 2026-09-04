---
title: Topologías de red
description: UD02 - Fundamentos de redes
---

Cuando diseñamos una red, una de las primeras decisiones que debemos tomar es cómo vamos a conectar físicamente los dispositivos entre sí. La **topología de red** describe la forma en que están organizados y conectados los nodos (dispositivos) y los enlaces (cables o conexiones inalámbricas) de una red. Es, en esencia, el "mapa" de la red.

Distinguimos dos tipos de topología:

- **Topología física**: cómo están conectados los dispositivos en la realidad, con cables reales y hardware real.
- **Topología lógica**: cómo fluyen los datos por la red, que puede ser diferente a la disposición física.

Por ejemplo, una red puede estar físicamente cableada en estrella (todos los cables van a un switch central) pero lógicamente comportarse como un bus (los datos se difunden a todos los dispositivos). En este apartado nos centraremos principalmente en la **topología física**, que es la que vemos y montamos.

## Topología en bus

En una **topología en bus**, todos los dispositivos están conectados a un único cable compartido llamado **bus** o **troncal**. Los datos que envía cualquier dispositivo viajan por ese cable en ambas direcciones y llegan a todos los demás nodos. Cada dispositivo decide si los datos van dirigidos a él o los ignora.

<!-- IMG: diagrama topología en bus con varios equipos conectados a un cable central horizontal -->
<figure markdown="span" align="center">
  ![Topología en bus](./imgs/redes/topologia_bus.png){ width="75%" }
  <figcaption>Topología en bus: todos los dispositivos comparten un único cable troncal</figcaption>
</figure>

Para evitar que los datos reboten infinitamente por el cable, en los extremos del bus se colocan **terminadores** que absorben la señal.

**Ventajas:**
- Muy sencilla y económica de implementar: solo necesita un cable.
- Fácil de añadir nuevos dispositivos.

**Inconvenientes:**
- Si el cable central falla, **toda la red deja de funcionar**.
- Solo un dispositivo puede transmitir a la vez. Si varios intentan transmitir simultáneamente se produce una **colisión** y los datos se corrompen.
- El rendimiento cae drásticamente a medida que aumenta el número de dispositivos.
- Difícil de diagnosticar averías.

**Estado actual:** La topología en bus fue muy popular en los años 80 y 90 con las redes coaxiales (Ethernet 10Base2 y 10Base5). Hoy está prácticamente **obsoleta** en redes cableadas modernas, aunque el concepto sigue presente en algunas redes industriales y en el bus CAN de los vehículos.

## Topología en anillo

En una **topología en anillo**, cada dispositivo está conectado exactamente a otros dos: el anterior y el siguiente, formando un círculo cerrado. Los datos viajan en una sola dirección (o en ambas en anillos dobles) pasando por cada nodo hasta llegar al destino.

<!-- IMG: diagrama topología en anillo con equipos formando un círculo y flechas indicando dirección del tráfico -->
<figure markdown="span" align="center">
  ![Topología en anillo](./imgs/redes/topologia_anillo.png){ width="60%" }
  <figcaption>Topología en anillo: los datos circulan pasando por cada dispositivo hasta llegar al destino</figcaption>
</figure>

Para controlar quién puede transmitir en cada momento y evitar colisiones, se usa un mecanismo llamado **token** (testigo): un paquete especial que circula por el anillo y solo quien lo tiene puede transmitir datos.

**Ventajas:**
- Sin colisiones gracias al mecanismo de token.
- Rendimiento predecible y constante.
- Funciona bien con muchos dispositivos.

**Inconvenientes:**
- Si un dispositivo o enlace falla, **toda la red puede verse afectada** (aunque los anillos dobles lo mitigan).
- Añadir o retirar dispositivos interrumpe la red.
- Más compleja de gestionar que bus o estrella.

**Estado actual:** También prácticamente **obsoleta** en redes LAN de propósito general. Su uso más conocido fue Token Ring (IBM) en los años 80-90. Sin embargo, el concepto de anillo sigue siendo muy relevante en redes de fibra óptica de alta disponibilidad como **SONET/SDH** y en algunos protocolos de redes industriales.

## Topología en estrella

La **topología en estrella** es la dominante en las redes LAN modernas. Todos los dispositivos están conectados a un **dispositivo central** (un switch o un hub) mediante su propio cable independiente. Los datos no viajan de dispositivo a dispositivo directamente: siempre pasan por el nodo central.

<!-- IMG: diagrama topología en estrella con un switch central y varios equipos conectados mediante cables individuales -->
<figure markdown="span" align="center">
  ![Topología en estrella](./imgs/redes/topologia_estrella.png){ width="65%" }
  <figcaption>Topología en estrella: cada dispositivo tiene su propio cable hasta el switch central</figcaption>
</figure>

**Ventajas:**
- Si un cable o un dispositivo falla, **el resto de la red sigue funcionando** perfectamente. Solo el dispositivo afectado pierde conectividad.
- Muy fácil de diagnosticar averías: si un equipo no tiene red, el problema está en su cable o en su puerto del switch.
- Fácil de añadir nuevos dispositivos sin interrumpir la red.
- Alto rendimiento: con un switch moderno, cada par de dispositivos tiene su propio canal de comunicación dedicado y no comparte ancho de banda.

**Inconvenientes:**
- Si el **dispositivo central falla**, toda la red deja de funcionar.
- Requiere más cable que el bus (un cable por dispositivo en lugar de uno compartido).
- El coste del switch central es un elemento adicional.

**Estado actual:** Es la topología estándar en cualquier red LAN moderna. Todas las redes que montaremos en este curso usan topología en estrella con switches en el centro.

## Topología en malla

En una **topología en malla**, cada dispositivo está conectado directamente a varios (o todos) los demás dispositivos de la red. Existen dos variantes:

- **Malla completa** (*full mesh*): cada nodo tiene conexión directa con todos los demás. Si hay N nodos, se necesitan N×(N-1)/2 enlaces.
- **Malla parcial** (*partial mesh*): solo algunos nodos tienen múltiples conexiones redundantes.

<!-- IMG: diagrama de malla completa con 5 nodos y todas las conexiones posibles entre ellos -->
<figure markdown="span" align="center">
  ![Topología en malla](./imgs/redes/topologia_malla.png){ width="65%" }
  <figcaption>Topología en malla completa: máxima redundancia, cada nodo conectado directamente con todos los demás</figcaption>
</figure>

**Ventajas:**
- **Máxima redundancia y tolerancia a fallos**: si un enlace falla, los datos pueden llegar al destino por rutas alternativas.
- Sin punto único de fallo.
- Alto rendimiento: múltiples rutas disponibles simultáneamente.

**Inconvenientes:**
- Muy costosa y compleja: el número de enlaces crece exponencialmente con el número de nodos.
- Difícil de gestionar y mantener.

**Estado actual:** No se usa en redes LAN de área local por su coste. Sin embargo, es la topología de referencia en:

- **Internet**: los routers que forman el núcleo de internet están interconectados en malla parcial para garantizar que la red funcione aunque fallen múltiples enlaces.
- **Redes de operadores**: los ISP y operadores de telecomunicaciones usan topología de malla en su infraestructura troncal.
- **Redes Wi-Fi Mesh**: los sistemas Wi-Fi de malla domésticos (Google Nest, Eero, TP-Link Deco) usan este concepto para dar cobertura sin puntos muertos en el hogar.

## Topología en árbol

La **topología en árbol** es una extensión jerárquica de la estrella. Existe un nodo raíz (el switch o router principal) del que cuelgan varios switches secundarios, y de estos cuelgan los dispositivos finales. Forma una estructura jerárquica como las ramas de un árbol.

<!-- IMG: diagrama topología en árbol con un switch raíz, switches secundarios y equipos en los extremos -->
<figure markdown="span" align="center">
  ![Topología en árbol](./imgs/redes/topologia_arbol.png){ width="70%" }
  <figcaption>Topología en árbol: estructura jerárquica que combina múltiples estrellas</figcaption>
</figure>

**Ventajas:**
- Escalable: se pueden añadir nuevas ramas sin afectar al resto.
- Bien organizada y fácil de gestionar por segmentos.
- Permite segmentar la red por departamentos o plantas.

**Inconvenientes:**
- Si falla un switch intermedio, todos los dispositivos conectados a él quedan sin red.
- Más compleja que una estrella simple.

**Estado actual:** Es la topología más habitual en redes corporativas medianas y grandes. Una empresa con varias plantas puede tener un switch por planta (nivel de acceso), conectados a switches de distribución por planta, que a su vez se conectan al switch o router principal (núcleo). En el proyecto de este curso, la red de la empresa ficticia usará una topología en árbol.

## Topología híbrida

En la práctica, la mayoría de redes reales son **híbridas**: combinan elementos de varias topologías. Una empresa puede tener una topología en estrella dentro de cada oficina, conectada con otras oficinas mediante una topología en malla parcial para redundancia, formando en conjunto una topología en árbol a nivel global.

<!-- IMG: diagrama de topología híbrida combinando estrella en LANs locales y malla entre sedes -->
<figure markdown="span" align="center">
  ![Topología híbrida](./imgs/redes/topologia_hibrida.png){ width="75%" }
  <figcaption>Topología híbrida: combinación de estrella dentro de cada sede y malla parcial entre sedes</figcaption>
</figure>

## Comparativa de topologías

| Topología | Tolerancia a fallos | Coste | Escalabilidad | Uso actual |
|-----------|-------------------|-------|---------------|-----------|
| **Bus** | Muy baja | Muy bajo | Baja | Obsoleta |
| **Anillo** | Baja | Bajo | Media | Obsoleta en LAN |
| **Estrella** | Media (falla si cae switch) | Medio | Alta | **Estándar en LAN** |
| **Malla** | Muy alta | Muy alto | Baja | Internet, WAN |
| **Árbol** | Media | Medio-alto | Muy alta | Redes corporativas |

---

## Actividad en clase

### 🖥️ Actividad conjunta: dibuja la topología de nuestra red

**Duración:** 15 minutos
**Dinámica:** trabajo en grupo + puesta en común

**Objetivo:** identificar la topología real de la red del aula y del instituto.

**Desarrollo:**

1. El profesor pregunta: ¿qué topología creéis que tiene la red de esta aula? ¿Y la del instituto completo?

2. Por grupos de 3-4, los alumnos intentan dibujar en papel el esquema de red del aula tal como la ven: ¿hay cables? ¿Dónde van? ¿Hay algún dispositivo central visible (switch, router, patch panel)?

3. Puesta en común: cada grupo explica su esquema. El profesor muestra o describe la topología real del aula y del centro, identificando:
    - El switch del aula y los cables que van a cada equipo → **estrella**
    - El switch del aula conectado al switch de planta → **árbol**
    - La conexión del centro al operador de internet → **WAN**

4. Reflexión final: ¿qué pasaría si el switch central del aula se estropeara? ¿Y si se rompiera el cable de un solo equipo?

!!! tip "Para el profesor"
    Si es posible, abre el armario de red del aula o muestra una fotografía del patch panel y el switch para que los alumnos vean físicamente la topología en estrella. Ver el hardware real refuerza enormemente la comprensión del concepto.