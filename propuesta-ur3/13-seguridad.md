---
layout: default
title: Regla de seguridad del sistema
parent: Propuesta del Proyecto
nav_order: 13
---

# Regla de seguridad del sistema

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
