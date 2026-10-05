---
layout: default
title: Propuesta
parent: Propuesta del Proyecto
nav_order: 1
---

# Propuesta

El objetivo del proyecto es desarrollar un sistema en el que un **robot colaborativo UR3** pueda ser controlado a partir de la manipulación de su **gemelo digital en realidad mixta**.

El operador utilizará unos lentes de realidad mixta para observar una representación digital del robot y de un almacén previamente parametrizado.

El almacén será representado mediante una cuadrícula bidimensional. Por ejemplo, una estructura de:

$$
4 \times 3 \times 1
$$

permitiría representar hasta:

$$
4 \cdot 3 \cdot 1= 12
$$

posiciones lógicas diferentes.

Cada posición puede identificarse mediante una coordenada:

$$
P(i,j,k)
$$

donde $$i$$, $$j$$ y $$k$$ representan la posición del producto dentro del almacén virtual.

El usuario podrá seleccionar dentro de los lentes una celda o producto. El sistema calculará primero una trayectoria para el gemelo digital del UR3 y comprobará que el movimiento sea válido.

Si la trayectoria no presenta colisiones, límites articulares o singularidades, el usuario podrá presionar un botón de **Ejecutar** dentro de la interfaz de realidad mixta, misma que será enviada al robot UR3 físico.
