---
layout: default
title: Propuesta del Proyecto
nav_order: 2
has_toc: true
---

# Proyecto Final: Control de un UR3 mediante gemelo digital y realidad mixta
{: .no_toc }

Selección de hardware, protocolos y toma de decisiones
{: .fs-6 .fw-300 }

## Tabla de contenido
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Propuesta

El objetivo del proyecto es desarrollar un sistema en el que un **robot colaborativo UR3** pueda ser controlado a partir de la manipulación de su **gemelo digital en realidad mixta**.

El operador utilizará unos lentes de realidad mixta para observar una representación digital del robot y de un almacén previamente parametrizado.

El almacén será representado mediante una cuadrícula tridimensional. Por ejemplo, una estructura de:

$$
10 \times 10 \times 10
$$

permitiría representar hasta:

$$
10 \cdot 10 \cdot 10 = 1000
$$

posiciones lógicas diferentes.

Cada posición puede identificarse mediante una coordenada:

$$
P(i,j,k)
$$

donde $$i$$, $$j$$ y $$k$$ representan la posición del producto dentro del almacén virtual.

El usuario podrá seleccionar dentro de los lentes una celda o producto. El sistema calculará primero una trayectoria para el gemelo digital del UR3 y comprobará que el movimiento sea válido.

Si la trayectoria no presenta colisiones, límites articulares o singularidades, el usuario podrá presionar un botón de **Ejecutar** dentro de la interfaz de realidad mixta.

Solamente después de una segunda validación, la trayectoria será enviada al robot UR3 físico.

---

## Arquitectura seleccionada

La arquitectura se divide en cuatro partes principales:

1. **Interfaz de realidad mixta:** Meta Quest 3 ejecutando una aplicación desarrollada en Unity y OpenXR.
2. **Computadora de borde:** encargada de Simulink, MoveIt 2, planeación de trayectorias, inteligencia artificial y procesamiento de imágenes.
3. **Sistema físico:** robot UR3, gripper y cámara RGB-D colocada cerca del efector final.
4. **Sistema de inventario:** sensores en el almacén, ESP32, Raspberry Pi, base de datos local, nube y aplicación móvil.

---

## Hardware seleccionado

| Elemento | Hardware elegido | Función |
|:---|:---|:---|
| Robot | UR3 | Ejecutar físicamente las trayectorias previamente validadas en el gemelo digital. |
| Realidad mixta | Meta Quest 3 | Visualizar el gemelo digital, seleccionar productos o posiciones y autorizar movimientos. |
| Procesamiento principal | PC de borde con GPU NVIDIA | Ejecutar Simulink, MoveIt 2, YOLO, planeación de movimiento y agente de IA. |
| Visión del robot | Cámara RGB-D RealSense D405 | Identificar productos y obtener información de profundidad cerca del efector final. |
| Manipulación | Gripper eléctrico ligero | Tomar y depositar los productos del almacén. |
| Inventario | Celdas de carga + HX711 | Determinar la cantidad aproximada de productos mediante el peso almacenado en cada celda. |
| Adquisición de sensores | ESP32 | Leer los sensores de las diferentes secciones del almacén. |
| Servidor local | Raspberry Pi 5 + SSD | Ejecutar MQTT, API local y base de datos de inventario. |
| Red | Switch Ethernet + punto de acceso Wi-Fi | Comunicar robot, PC, Raspberry Pi y lentes dentro de la red local. |
| Nube | Base de datos PostgreSQL en la nube | Mantener una copia remota del estado del almacén y proporcionar información a la aplicación móvil. |

*Tabla: Hardware principal seleccionado para el proyecto.*

---

## Protocolos de comunicación

Cada tramo del sistema utiliza un protocolo diferente dependiendo de las necesidades de velocidad, confiabilidad y cantidad de información.

| Origen | Destino | Protocolo | Justificación |
|:---|:---|:---|:---|
| Meta Quest 3 | PC de borde | WebSocket / TCP-IP | Permite enviar coordenadas, comandos y recibir en tiempo real el estado del gemelo digital. |
| PC de borde | UR3 | Simulink + RTDE sobre Ethernet | Permite utilizar el driver oficial de Universal Robots y enviar trayectorias previamente calculadas. |
| Cámara RGB-D | PC de borde | USB 3.0 | Permite transmitir imágenes RGB y mapas de profundidad con baja latencia. |
| ESP32 | Raspberry Pi | MQTT | El inventario genera pequeños mensajes de telemetría y no necesita comunicación de tiempo real estricto. |
| Raspberry Pi | PC de borde | MQTT / REST | Permite compartir información del inventario con el sistema robótico. |
| Raspberry Pi | Nube | HTTPS / TLS | Sincroniza de manera segura los movimientos e inventario del almacén. |
| Aplicación móvil | Nube | HTTPS + WebSocket | Permite consultar inventario y recibir actualizaciones sin conectarse directamente al robot. |

*Tabla: Protocolos seleccionados para cada tramo del sistema.*

---

## Representación tridimensional del almacén

Cada celda del almacén tendrá asociada información como:

- Coordenada lógica $$(i,j,k)$$.
- Coordenada física $$(x,y,z)$$.
- Identificador del producto.
- Cantidad disponible.
- Peso unitario.
- Estado de ocupación.
- Orientación recomendada del efector final.
- Punto de aproximación del robot.

Si cada celda tiene dimensiones:

$$
\Delta x,\qquad \Delta y,\qquad \Delta z
$$

y el origen físico del almacén está definido por:

$$
(x_0,y_0,z_0)
$$

la posición central de una celda puede calcularse como:

$$
x=x_0+\left(i+\frac{1}{2}\right)\Delta x
$$

$$
y=y_0+\left(j+\frac{1}{2}\right)\Delta y
$$

$$
z=z_0+\left(k+\frac{1}{2}\right)\Delta z
$$

De esta forma, cuando el usuario selecciona una celda dentro de la realidad mixta, el sistema puede convertir automáticamente la posición virtual en una posición física respecto a la base del robot.

Sin embargo, para realizar el agarre correctamente no se utiliza únicamente $$(x,y,z)$$, sino una pose completa del efector:

$$
P = (x,y,z,R_x,R_y,R_z)
$$

donde los últimos tres valores representan su orientación.

---

## Detección de productos

En el efector final se propone colocar una **cámara RGB-D**.

La imagen RGB será procesada mediante un modelo de visión, por ejemplo **YOLO**, para identificar qué producto se encuentra frente al robot.

La información de profundidad permitirá estimar con mayor precisión la posición del producto antes de realizar el agarre.

El proceso básico será:

```
Cámara → YOLO → Identificación → Estimación 3D → Gripper
```

Una cámara RGB convencional podría identificar el objeto, pero no proporcionaría directamente información de profundidad, por lo que se selecciona una cámara RGB-D.

---

## Control del inventario

Para mantener actualizado el inventario se propone colocar **celdas de carga** en las diferentes posiciones o contenedores del almacén.

Si una celda contiene un único tipo de producto y el peso individual es $$m_u$$, mientras que el peso total detectado es $$m_t$$, se puede estimar la cantidad mediante:

$$
N \approx \frac{m_t}{m_u}
$$

El ESP32 realiza la adquisición de los sensores y envía los valores por MQTT hacia una Raspberry Pi.

La Raspberry Pi mantiene una base de datos local con información como:

- ID del producto.
- Ubicación $$(i,j,k)$$.
- Cantidad.
- Peso esperado.
- Último movimiento.
- Fecha y hora.
- Estado de la posición.

Cuando el UR3 retira un producto:

$$
N_{\text{nuevo}} = N_{\text{anterior}} - 1
$$

y posteriormente el valor se compara contra la lectura real de la celda de carga.

Esto permite tener dos fuentes de información:

1. El movimiento registrado por el robot.
2. El inventario físico obtenido mediante sensores.

---

## Inteligencia artificial y singularidades

La inteligencia artificial no tendrá autoridad directa para mover el robot.

Primero se utilizará el modelo matemático del UR3 para identificar configuraciones cercanas a una singularidad.

Una forma de evaluar esta condición es mediante el número de condición del Jacobiano:

$$
\kappa(J)=\frac{\sigma_{\max}(J)}{\sigma_{\min}(J)}
$$

Cuando:

$$
\sigma_{\min}(J)\rightarrow 0
$$

el robot se aproxima a una configuración singular.

El agente de inteligencia artificial recibe información como:

- posición objetivo;
- configuración articular;
- proximidad a singularidades;
- distancia a obstáculos;
- límites articulares;
- diferentes soluciones de cinemática inversa.

Su función será proponer una alternativa, por ejemplo:

- modificar la orientación del efector;
- utilizar otra solución de cinemática inversa;
- generar un punto intermedio;
- aproximarse al producto desde otra dirección.

Después, MoveIt vuelve a comprobar matemáticamente la trayectoria. Por lo tanto:

```
IA propone → MoveIt valida → Gemelo ejecuta → UR3 ejecuta
```

La IA funciona como un sistema de asistencia para encontrar mejores trayectorias, pero la validación final permanece en algoritmos determinísticos.

---

## Diagrama de funcionamiento

{: .note }
> El diagrama original en LaTeX usaba TikZ, que no es compatible con Markdown/Just the Docs. Aquí está representado como un diagrama de flujo equivalente. Si prefieres conservar el diagrama visual exacto, puedes exportar el TikZ como imagen (PDF → PNG) desde Overleaf y colocarla en `assets/images/`, insertándola con `![Diagrama de flujo](/assets/images/diagrama-flujo.png)`.

**Flujo principal:**

1. **Inicio**
2. Usuario selecciona producto o celda $$(i,j,k)$$ en realidad mixta
3. PC de borde recibe posición y orientación objetivo
4. MoveIt 2 calcula trayectoria del UR3
5. **¿Trayectoria válida?**
   - **Sí →** Gemelo digital ejecuta la trayectoria
   - **No →** IA analiza singularidad, colisión o mala configuración → IA propone nueva orientación, pose o punto intermedio → regresa al paso 4 (MoveIt)
6. **¿Simulación correcta?**
   - **Sí →** Usuario presiona **Ejecutar**
   - **No →** regresa al paso 4 (MoveIt)
7. Validación final con estado actual del UR3 físico
8. UR3 ejecuta la trayectoria
9. Cámara RGB-D + YOLO verifica el producto
10. Se actualiza el inventario en la Raspberry Pi
11. Raspberry Pi sincroniza la información con la nube
12. **Fin / ciclo continuo**

---

## Opciones descartadas y justificación

| Necesidad | Opción descartada | Motivo |
|:---|:---|:---|
| Comunicación VR–robot | Conectar Quest directamente al UR3 | Se perdería la capa intermedia de validación de colisiones, cinemática y singularidades. |
| Control del UR3 | MQTT | MQTT funciona muy bien para telemetría, pero no es la mejor opción para ejecutar trayectorias del robot. Se selecciona Simulink y RTDE. |
| Conexión UR3 | Wi-Fi | Puede introducir variaciones de latencia o pérdidas. El robot y la PC se conectarán mediante Ethernet. |
| Procesamiento principal | Raspberry Pi | Aunque puede ejecutar tareas básicas, Simulink, MoveIt, visión e IA simultáneamente requieren mayor capacidad de procesamiento. |
| Visión | Cámara RGB convencional | Identifica objetos pero no proporciona directamente información tridimensional. |
| Inventario | Sensor infrarrojo por celda | Detecta presencia, pero no determina correctamente la cantidad cuando existen varios productos en la misma posición. |
| Inventario | Ultrasonido | La lectura depende de la geometría y posición del producto y puede presentar reflexiones. |
| Inventario | RFID en todos los productos | Permitiría identificar individualmente cada pieza, pero aumenta costo y requiere colocar una etiqueta en cada producto. Se considera como mejora futura. |
| Base de datos | Solamente nube | Si se pierde Internet, el sistema perdería acceso al estado operativo. Por ello se mantiene una base local y posteriormente se sincroniza. |
| Realidad mixta | Meta Quest 3S | Aunque también permite MR, se prefiere Quest 3 por su mejor sistema óptico y sensor de profundidad. |
| Singularidades | IA como único método | Una IA puede proponer correcciones, pero no debe reemplazar la evaluación matemática del Jacobiano, límites y colisiones. |

*Tabla: Alternativas descartadas y razones de la toma de decisión.*

---

## Comunicación con la nube

La nube se utilizará exclusivamente para información de alto nivel, por ejemplo:

- inventario actual;
- entradas y salidas;
- historial de movimientos;
- productos con bajo inventario;
- productos más utilizados;
- posiciones ocupadas;
- alertas;
- estadísticas.

La nube **no enviará movimientos directamente al UR3**.

El flujo será:

```
UR3 / sensores → Servidor local → Nube → Aplicación móvil
```

De esta manera, el dueño puede consultar el estado del almacén desde cualquier lugar sin que la conexión a Internet forme parte del lazo de control crítico del robot.

---

## Limitación física del prototipo

El UR3 tiene aproximadamente **500 mm de alcance** y una carga útil nominal de aproximadamente **3 kg**.

Por esta razón, la cuadrícula $$10\times10\times10$$ debe entenderse principalmente como el modelo lógico del almacén.

El prototipo físico deberá utilizar inicialmente una sección reducida del almacén ubicada dentro del espacio de trabajo del UR3.

Además, dentro de la carga útil deben considerarse:

- peso del gripper;
- peso de la cámara;
- soportes;
- producto manipulado.

Una implementación futura a mayor escala podría utilizar:

- un robot con mayor alcance;
- un séptimo eje lineal;
- varios robots;
- o un robot montado sobre una plataforma móvil.

---

## Regla de seguridad del sistema

Una trayectoria correcta dentro del gemelo digital no significa automáticamente que la misma trayectoria sea segura en el sistema real.

Antes de ejecutar físicamente se debe verificar:

- calibración entre gemelo y robot físico;
- posición real del robot;
- posición real de los objetos;
- TCP del gripper;
- carga útil;
- límites articulares;
- colisiones;
- singularidades;
- estado de seguridad del UR3.

Por lo tanto, se utiliza el principio:

{: .important }
> **Seleccionar → Planear → Simular → Validar → Ejecutar**

---

## Conclusión

La propuesta utiliza una arquitectura distribuida en la que cada dispositivo realiza únicamente la función para la que resulta más adecuado.

El **Meta Quest 3** funciona como interfaz de realidad mixta; la **PC de borde** realiza la planeación, visión e inteligencia artificial; el **UR3** ejecuta únicamente trayectorias previamente validadas; y la **Raspberry Pi** administra el inventario local y la comunicación con la nube.

La información física del almacén se relaciona con una representación tridimensional parametrizada, permitiendo seleccionar virtualmente un producto, analizar la trayectoria del robot, corregir posibles singularidades y posteriormente ejecutar el movimiento sobre el robot real.

Finalmente, el sistema de inventario mantiene sincronizados el almacén físico, el gemelo digital, la base de datos y la aplicación móvil, permitiendo conocer de manera remota qué productos existen, cuántos quedan y qué movimientos se han realizado.
