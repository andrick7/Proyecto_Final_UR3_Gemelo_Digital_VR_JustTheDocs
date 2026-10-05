---
layout: default
title: Hardware seleccionado
parent: Propuesta del Proyecto
nav_order: 3
---

# Hardware seleccionado

| Elemento | Hardware elegido | Función |
|:---|:---|:---|
| Robot | UR3 | Ejecutar físicamente las trayectorias previamente validadas en el gemelo digital. |
| Realidad mixta | Meta Quest 3 | Visualizar el gemelo digital, seleccionar productos o posiciones y autorizar movimientos. |
| Procesamiento principal | PC de borde con GPU NVIDIA | Ejecutar Simulink, MoveIt 2, YOLO, planeación de movimiento y agente de IA. |
| Visión del robot | Cámara RGB-D RealSense D405 | Identificar productos y obtener información de profundidad cerca del efector final. |
| Manipulación | Gripper eléctrico ligero | Tomar y depositar los productos del almacén. |
| Inventario | Celdas de carga + HX711 | Determinar la cantidad aproximada de productos mediante el peso almacenado en cada celda. |
| Adquisición de sensores | ESP32 | Leer los sensores de las diferentes secciones del almacén. |
| Servidor local | Raspberry Pi 5 + SSD | Ejecutar MQTT, API local y base de datos de inventario. |
| Red | Switch Ethernet + punto de acceso Wi-Fi | Comunicar robot, PC, Raspberry Pi y lentes dentro de la red local. |
| Nube | Base de datos PostgreSQL en la nube | Mantener una copia remota del estado del almacén y proporcionar información a la aplicación móvil. |

*Tabla: Hardware principal seleccionado para el proyecto.*
