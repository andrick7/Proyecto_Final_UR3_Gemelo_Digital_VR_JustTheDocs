---
layout: default
title: Diagrama de funcionamiento
parent: Propuesta del Proyecto
nav_order: 9
---

# Diagrama de funcionamiento

Imagen de la arquitectura del sistema. 

**Flujo principal:**

1. **Inicio**
2. Usuario selecciona producto o celda $$(i,j,k)$$ en realidad mixta
3. PC de borde recibe posición y orientación objetivo
4. Simulink (cinemática inversa geométrica) calcula trayectoria del UR3
5. **¿Trayectoria válida?**
   - **Sí →** Gemelo digital ejecuta la trayectoria.
   - **No →** Proponer nueva trayectoria. 
6. **¿Simulación correcta en roboDK?**
   - **Sí →** Usuario presiona **Ejecutar**
   - **No →** regresa al paso 4 (Simulink)
7. Validación final con estado actual del UR3 físico
8. UR3 ejecuta la trayectoria
9. Cámara RGB-D + YOLO verifica el producto
10. Cámara RGB + Modelo verifica cantidad de productos en almacén. 
11. Se actualiza el inventario en base de datos (FireBase), App Móvil y visor. 
12. Raspberry Pi sincroniza la información con la nube
13. **Fin / ciclo continuo**
