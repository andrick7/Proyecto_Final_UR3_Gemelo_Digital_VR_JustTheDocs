---
layout: default
title: Conclusión
parent: Propuesta del Proyecto
nav_order: 14
---

# Conclusión

La propuesta utiliza una arquitectura distribuida en la que cada dispositivo realiza únicamente la función para la que resulta más adecuado.

El **Meta Quest 3** funciona como interfaz de realidad mixta; la **PC de borde** realiza la planeación, visión e inteligencia artificial; el **UR3** ejecuta únicamente trayectorias previamente validadas; y la **Raspberry Pi** administra el inventario local y la comunicación con la nube.

La información física del almacén se relaciona con una representación tridimensional parametrizada, permitiendo seleccionar virtualmente un producto, analizar la trayectoria del robot, corregir posibles singularidades y posteriormente ejecutar el movimiento sobre el robot real.

Finalmente, el sistema de inventario mantiene sincronizados el almacén físico, el gemelo digital, la base de datos y la aplicación móvil, permitiendo conocer de manera remota qué productos existen, cuántos quedan y qué movimientos se han realizado.
