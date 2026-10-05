---
layout: default
title: Opciones descartadas y justificación
parent: Propuesta del Proyecto
nav_order: 10
---

# Opciones descartadas y justificación

| Necesidad | Opción descartada | Motivo |
|:---|:---|:---|
| Comunicación VR–robot | Conectar Quest directamente al UR3 | Se perdería la capa intermedia de validación de colisiones, cinemática y singularidades. |
| Control del UR3 | MQTT | MQTT funciona muy bien para telemetría, pero no es la mejor opción para ejecutar trayectorias del robot. Se selecciona Simulink y RTDE. |
| Conexión UR3 | Wi-Fi | Puede introducir variaciones de latencia o pérdidas. El robot y la PC se conectarán mediante Ethernet. |
| Procesamiento principal | Raspberry Pi | Aunque puede ejecutar tareas básicas, Simulink, MoveIt, visión e IA simultáneamente requieren mayor capacidad de procesamiento. |
| Visión | Cámara RGB convencional | Identifica objetos pero no proporciona directamente información tridimensional. |
| Inventario | Sensor infrarrojo por celda | Detecta presencia, pero no determina correctamente la cantidad cuando existen varios productos en la misma posición. |
| Inventario | Ultrasonido | La lectura depende de la geometría y posición del producto y puede presentar reflexiones. |
| Inventario | RFID en todos los productos | Permitiría identificar individualmente cada pieza, pero aumenta costo y requiere colocar una etiqueta en cada producto. Se considera como mejora futura. |
| Base de datos | Solamente de forma local | Optamos por una base en tiempo real, el sistema no perdería acceso al estado operativo. Por ello se mantiene una base en tiempo real y en la nube, para posteriormente sincronizarla con la aplicación móvil y nuestro visor (Meta Quest 3). |
| Realidad mixta | Meta Quest 3S | Aunque también permite MR, se prefiere Quest 3 por su mejor sistema óptico y sensor de profundidad. |
| Singularidades | IA como único método | Una IA puede proponer correcciones, pero no debe reemplazar la evaluación matemática del Jacobiano, límites y colisiones. |

*Tabla: Alternativas descartadas y razones de la toma de decisión.*
