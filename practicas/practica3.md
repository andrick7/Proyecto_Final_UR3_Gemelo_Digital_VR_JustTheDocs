
---
title: Práctica 3
layout: default
parent: Prácticas
nav_order: 3
---

# Práctica 3

## Comunicación UART con ESP32

### 1. Introducción

En esta práctica se evaluó la comunicación serial UART entre un ESP32 y una computadora mediante una interfaz USB-UART CP210x.

### 2. Objetivos

- Evaluar la transmisión y recepción de mensajes.
- Comprobar la integridad de los mensajes recibidos.
- Medir el tiempo de ida y vuelta (RTT).
- Comparar los resultados obtenidos con diferentes velocidades de comunicación.

### 3. Configuración experimental

- **Microcontrolador:** ESP32.
- **Interfaz:** CP210x USB-UART.
- **Protocolo:** UART, configuración 8N1.
- **Herramientas:** Python y pySerial.

### 4. Resultados

Aquí se presentan las tablas de mensajes enviados, mensajes recibidos correctamente y errores de comunicación.

### 5. Conclusiones

Se analizará la relación entre la velocidad de transmisión, la integridad de los mensajes y el tiempo de respuesta.
