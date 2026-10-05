---
layout: default
title: Control del inventario
parent: Propuesta del Proyecto
nav_order: 7
---

# Control del inventario

Para mantener actualizado el inventario se propone colocar **camara RGB** en una posición que permita observar todo el almacén.

Si una celda contiene o no un producto, se podrá observar en los visores VR, en la base de datos y en la aplicación móvil; de forma que en todo momento se tendrá actualización del estado del inventario. 

La Raspberry Pi mantiene un control del tipo de producto con información como:

- ID del producto.
- Ubicación $$(i,j,k)$$.
- Cantidad.
- Tipo de prodcuto.
- Último movimiento.
- Fecha y hora.
- Estado de la posición.

Cuando el UR3 retira un producto:

$$
N_{\text{nuevo}} = N_{\text{anterior}} - 1
$$

y posteriormente el valor se compara contra la lectura real que detecta la cámara RGB.

Esto permite tener dos fuentes de información:

1. El movimiento registrado por el robot.
2. El inventario físico obtenido mediante la captura de la cámara.
