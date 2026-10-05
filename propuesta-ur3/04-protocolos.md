---
layout: default
title: Protocolos de comunicación
parent: Propuesta del Proyecto
nav_order: 4
---

# Protocolos de comunicación

Cada tramo del sistema utiliza un protocolo diferente dependiendo de las necesidades de velocidad, confiabilidad y cantidad de información.

| Origen | Destino | Protocolo | Justificación |
|:---|:---|:---|:---|
| Meta Quest 3 | PC de borde | WebSocket / TCP-IP | Permite enviar coordenadas, comandos y recibir en tiempo real el estado del gemelo digital. |
| PC de borde | UR3 | Simulink + RTDE sobre Ethernet | Permite utilizar el driver oficial de Universal Robots y enviar trayectorias previamente calculadas. |
| Cámara RGB-D | PC de borde | USB 3.0 | Permite transmitir imágenes RGB y mapas de profundidad con baja latencia. |
| ESP32 | Raspberry Pi | MQTT | El inventario genera pequeños mensajes de telemetría y no necesita comunicación de tiempo real estricto. |
| Raspberry Pi | PC de borde | MQTT / REST | Permite compartir información del inventario con el sistema robótico. |
| Raspberry Pi | Nube | HTTPS / TLS | Sincroniza de manera segura los movimientos e inventario del almacén. |
| Aplicación móvil | Nube | HTTPS + WebSocket | Permite consultar inventario y recibir actualizaciones sin conectarse directamente al robot. |

*Tabla: Protocolos seleccionados para cada tramo del sistema.*
