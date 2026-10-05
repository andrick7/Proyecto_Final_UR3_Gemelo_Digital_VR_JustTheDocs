---
layout: default
title: Comunicación con la nube
parent: Propuesta del Proyecto
nav_order: 11
---

# Comunicación con la nube

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
