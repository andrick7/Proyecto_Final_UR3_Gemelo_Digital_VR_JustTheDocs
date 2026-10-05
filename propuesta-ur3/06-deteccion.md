---
layout: default
title: Detección de productos
parent: Propuesta del Proyecto
nav_order: 6
---

# Detección de productos

En el efector final se propone colocar una **cámara RGB-D**.

La imagen RGB será procesada mediante un modelo de visión, por ejemplo **YOLO**, para identificar qué producto se encuentra frente al robot.

La información de profundidad permitirá estimar con mayor precisión la posición del producto antes de realizar el agarre.

El proceso básico será:

```
Cámara → YOLO → Identificación → Estimación 3D → Gripper
```

Una cámara RGB convencional podría identificar el objeto, pero no proporcionaría directamente información de profundidad, por lo que se selecciona una cámara RGB-D.
