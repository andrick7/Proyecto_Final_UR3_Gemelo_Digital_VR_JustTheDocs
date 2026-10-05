---
layout: default
title: Diagrama de funcionamiento
parent: Propuesta del Proyecto
nav_order: 9
---

# Diagrama de funcionamiento

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
