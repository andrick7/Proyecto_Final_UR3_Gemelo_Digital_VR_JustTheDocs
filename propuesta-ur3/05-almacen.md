---
layout: default
title: Representación tridimensional del almacén
parent: Propuesta del Proyecto
nav_order: 5
---

# Representación tridimensional del almacén

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
