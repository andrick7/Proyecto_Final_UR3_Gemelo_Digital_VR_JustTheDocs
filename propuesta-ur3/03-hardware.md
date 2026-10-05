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
| Procesamiento principal | PC de borde con GPU NVIDIA | Ejecutar Simulink y Python. |
| Procesamiento secundario | Raspberry Pi 5 | Ejecutar procesamiento de imágenes y modelos (YOLO) |
| Visión del robot | Cámara RGB-D RealSense D405 | Identificar productos y obtener información de profundidad cerca del efector final. |
| Manipulación | Gripper eléctrico ligero | Tomar y depositar los productos del almacén. |
| Red | Switch Ethernet + punto de acceso Wi-Fi | Comunicar robot, PC, Raspberry Pi y lentes a partir de su debido protocolo de comunicación.  |
| Nube | Base de datos FireBase en la nube | Mantener una copia remota del estado del almacén y proporcionar información a la aplicación móvil. |

*Tabla: Hardware principal seleccionado para el proyecto.*
