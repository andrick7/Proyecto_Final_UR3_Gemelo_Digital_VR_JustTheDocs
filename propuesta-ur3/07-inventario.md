---
layout: default
title: Control del inventario
parent: Propuesta del Proyecto
nav_order: 7
---

# Control del inventario

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
