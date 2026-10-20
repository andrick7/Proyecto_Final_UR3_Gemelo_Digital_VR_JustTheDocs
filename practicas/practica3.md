
---
title: Práctica 3
layout: default
parent: Prácticas
nav_order: 3
---

# Práctica 3: Comunicación UART con ESP32

## 1. Introducción

La comunicación serial es un mecanismo ampliamente utilizado para intercambiar información entre microcontroladores y computadoras. Uno de los protocolos más comunes para este propósito es UART (*Universal Asynchronous Receiver-Transmitter*), que permite transmitir datos mediante líneas de transmisión y recepción sin necesidad de una señal de reloj compartida.

En esta práctica se evaluó la comunicación UART entre una tarjeta ESP32 y una computadora, utilizando un puente USB-UART CP210x. Se implementó un programa en el ESP32 para recibir mensajes enviados desde la computadora y devolverlos como respuesta. Posteriormente, se desarrolló una prueba en Python para enviar múltiples mensajes, verificar su integridad y medir el tiempo de ida y vuelta (*Round-Trip Time*, RTT).

Se realizaron pruebas con diferentes velocidades de transmisión para observar su efecto sobre la integridad de los mensajes y el comportamiento de la comunicación serial.

## 2. Objetivos

### 2.1 Objetivo general

Evaluar el funcionamiento de la comunicación UART entre un ESP32 y una computadora, analizando la integridad de los mensajes y el tiempo de respuesta para distintas velocidades de transmisión.

### 2.2 Objetivos específicos

- Implementar un sistema de recepción y transmisión de mensajes UART en el ESP32.
- Establecer una comunicación serial entre la computadora y el microcontrolador.
- Automatizar el envío de mensajes mediante Python.
- Verificar que los mensajes recibidos coincidan con los mensajes enviados.
- Medir el tiempo de ida y vuelta de los mensajes.
- Comparar los resultados obtenidos con diferentes velocidades de transmisión.

## 3. Fundamentos teóricos

### 3.1 Protocolo UART

UART es un protocolo de comunicación serial asíncrona que transmite los datos de manera secuencial. A diferencia de los protocolos síncronos, no necesita una línea de reloj compartida entre el transmisor y el receptor.

Una trama UART normalmente contiene:

- **Bit de inicio:** indica el comienzo de la transmisión.
- **Bits de datos:** contienen la información transmitida.
- **Bit de paridad:** opcional, empleado para detectar ciertos errores.
- **Bit de parada:** indica el final de la trama.

En esta práctica se utilizó una configuración de 8N1, que corresponde a ocho bits de datos, sin paridad y un bit de parada. Por lo tanto, cada byte requiere diez bits transmitidos, considerando el bit de inicio.

### 3.2 Velocidad de transmisión

La velocidad de transmisión se expresa en baudios. En una comunicación UART convencional, cada símbolo representa un bit, por lo que la velocidad en baudios coincide numéricamente con la tasa de bits.

La configuración del transmisor y del receptor debe coincidir. Una diferencia entre las velocidades configuradas puede provocar que los datos se interpreten incorrectamente.

Para una comunicación UART configurada en 8N1, la tasa teórica máxima de transmisión de bytes, sin considerar pausas ni sobrecargas adicionales, puede aproximarse mediante:

\[
R_{\text{bytes}} = \frac{B}{10}
\]

donde:

- \(R_{\text{bytes}}\) es la tasa teórica de bytes por segundo.
- \(B\) es la velocidad de transmisión en baudios.

Por ejemplo, a 115200 baudios:

\[
R_{\text{bytes}} = \frac{115200}{10}
= 11520\ \text{bytes/s}
\]

Este valor es teórico y no representa necesariamente el rendimiento efectivo de una aplicación que utiliza USB, un sistema operativo y procesamiento adicional.

### 3.3 Tiempo de ida y vuelta (RTT)

El tiempo de ida y vuelta o RTT es el intervalo entre el envío de un mensaje y la recepción de su respuesta.

En esta práctica, la medición incluye:

- El envío de datos desde Python.
- La transmisión por USB-UART.
- La recepción y el procesamiento en el ESP32.
- La transmisión de la respuesta.
- La recepción y el procesamiento en la computadora.

Por esta razón, el RTT medido no corresponde exclusivamente a la latencia del protocolo UART ni a la latencia en un solo sentido.

### 3.4 Integridad de los mensajes

Para evaluar la integridad de la comunicación, se comparó cada mensaje recibido con el mensaje que se había enviado.

La tasa de mensajes correctos se calcula mediante:

\[
P_{\text{correctos}} =
\frac{N_{\text{correctos}}}{N_{\text{enviados}}}
\times 100
\]

El porcentaje de errores se calcula como:

\[
P_{\text{errores}} =
\frac{N_{\text{errores}}}{N_{\text{enviados}}}
\times 100
\]

donde \(N_{\text{correctos}}\) representa el número de mensajes recibidos correctamente, \(N_{\text{errores}}\) el número de mensajes incorrectos o no recibidos y \(N_{\text{enviados}}\) el total de mensajes enviados.

## 4. Materiales y herramientas

- Tarjeta de desarrollo ESP32.
- Computadora con sistema operativo Windows.
- Puente de comunicación USB-UART CP210x.
- Cable USB compatible con la tarjeta.
- Arduino IDE para programar el microcontrolador.
- Python para automatizar las pruebas.
- Biblioteca `pySerial` para acceder al puerto serial.

## 5. Arquitectura del sistema

El sistema está compuesto por una computadora que genera los mensajes, una interfaz USB-UART y el ESP32, que devuelve cada mensaje recibido.

El flujo de comunicación es el siguiente:

1. Python genera un mensaje con un identificador consecutivo.
2. La computadora transmite el mensaje por el puerto serial.
3. El ESP32 recibe los caracteres hasta detectar el salto de línea.
4. El ESP32 devuelve el mensaje recibido.
5. Python lee la respuesta y la compara con el mensaje original.
6. El programa registra el resultado y calcula el tiempo de ida y vuelta.

## 6. Metodología experimental

### 6.1 Configuración de la comunicación

Se conectó el ESP32 a la computadora mediante USB. La interfaz CP210x permitió que el sistema operativo identificara el dispositivo como un puerto serial.

Se configuraron los parámetros de comunicación para utilizar ocho bits de datos, sin paridad y un bit de parada (8N1). La velocidad de transmisión se modificó durante las pruebas, manteniendo la misma configuración en ambos extremos.

### 6.2 Implementación del programa en el ESP32

Se programó el ESP32 para recibir los datos transmitidos desde la computadora y devolverlos como respuesta. La recepción se organizó por líneas, utilizando el carácter de nueva línea como delimitador de cada mensaje.

El siguiente código representa una implementación básica de eco serial:

```cpp
void setup() {
  Serial.begin(115200);
  Serial.setTimeout(1000);
}

void loop() {
  if (Serial.available()) {
    String mensaje = Serial.readStringUntil('\n');
    Serial.println(mensaje);
  }
}
```

La función `Serial.begin()` configura la velocidad inicial de comunicación. La función `Serial.available()` permite verificar si existen datos disponibles y `Serial.readStringUntil('\n')` lee el mensaje hasta encontrar un salto de línea o alcanzar el tiempo de espera configurado.

Finalmente, `Serial.println()` transmite el mensaje recibido seguido de un salto de línea.

**Nota:** la velocidad indicada en `Serial.begin()` debe coincidir con la configurada en el programa de Python para cada prueba.

### 6.3 Prueba automatizada desde Python

Se utilizó Python junto con la biblioteca `pySerial` para automatizar el envío de mensajes.

Cada mensaje contiene un identificador consecutivo, lo que permite distinguirlo de los demás y verificar si la respuesta corresponde al mensaje original.

El procedimiento de prueba consiste en:

1. Abrir el puerto serial con la velocidad seleccionada.
2. Generar un mensaje con un identificador único.
3. Registrar el instante de envío.
4. Transmitir el mensaje.
5. Esperar la respuesta del ESP32.
6. Registrar el instante de recepción.
7. Comparar el mensaje recibido con el enviado.
8. Registrar el resultado y calcular el RTT.
9. Repetir el proceso para el número de mensajes establecido.

La medición del RTT se realiza con la diferencia entre el instante de recepción y el instante de envío:

\[
RTT = t_{\text{recepción}} - t_{\text{envío}}
\]

La medición depende de la configuración del programa, del tiempo de espera y del comportamiento del sistema operativo. Si una respuesta no llega antes del tiempo límite, el programa puede registrar un tiempo cercano al *timeout*, que no debe interpretarse como una medición normal de latencia.

## 7. Resultados experimentales

### 7.1 Integridad de los mensajes

Durante las pruebas diagnósticas se evaluó el comportamiento de la comunicación a diferentes velocidades de transmisión. Los resultados disponibles corresponden a ensayos de 100 mensajes por prueba.

| Velocidad (baudios) | Mensajes enviados | Correctos | Errores | Mensajes correctos (%) |
|---:|---:|---:|---:|---:|
| 115200 | 100 | 100 | 0 | 100 % |
| 128000 | 100 | 0 | 100 | 0 % |
| 153600 | 100 | 100 | 0 | 100 % |
| 172800 | 100 | 0 | 100 | 0 % |
| 230400, prueba A | 100 | 46 | 54 | 46 % |
| 230400, prueba B | 100 | 68 | 32 | 68 % |

**Importante:** estos resultados corresponden a pruebas diagnósticas de 100 mensajes. No deben confundirse con una validación final de 1000 mensajes.

Las pruebas a 115200 y 153600 baudios mostraron una recepción correcta de todos los mensajes durante los ensayos registrados. En cambio, a 230400 baudios se observaron errores y respuestas incompletas en ambas pruebas.

Los resultados de 128000 y 172800 baudios también presentaron errores en las pruebas registradas. Sin embargo, antes de atribuirlos exclusivamente a la velocidad de transmisión, se debe comprobar la configuración de ambos extremos, el comportamiento del programa y el manejo de los tiempos de espera.

### 7.2 Interpretación de los resultados

Los resultados muestran que la comunicación no tuvo el mismo comportamiento para todas las configuraciones probadas.

En las pruebas registradas a 115200 y 153600 baudios se obtuvo una tasa de mensajes correctos del 100 %. En las dos pruebas a 230400 baudios se presentaron respuestas incorrectas, incompletas o ausentes.

Una posible causa es una incompatibilidad entre la configuración del transmisor y la del receptor. También deben considerarse el manejo de los saltos de línea, los tiempos de espera, la lectura de los datos y los retardos introducidos por la interfaz USB-UART y el sistema operativo.

La tasa de mensajes correctos observada en las pruebas no permite concluir, por sí sola, que exista un límite físico de velocidad del ESP32 o del CP210x. Para establecer una velocidad máxima confiable es necesario repetir las pruebas bajo condiciones controladas y verificar que los parámetros de ambos extremos coincidan.

### 7.3 Medición del tiempo de respuesta

El RTT se obtiene a partir del tiempo transcurrido entre el envío de un mensaje desde Python y la recepción de su respuesta.

En las mediciones disponibles se observaron tiempos del orden de 1.4 a 2.2 ms en respuestas normales, mientras que algunas mediciones iniciales alcanzaron aproximadamente 51 ms. Estos valores deben interpretarse con cautela, ya que el tiempo de espera, la inicialización del puerto y el sistema operativo pueden afectar la medición.

Una respuesta que tarda cerca de 1000 ms puede corresponder al tiempo de espera configurado cuando el programa no recibe el salto de línea esperado. En ese caso, no representa necesariamente la latencia real de una respuesta correcta.

No se incluye una gráfica estadística completa de RTT porque para ello se requiere el conjunto íntegro de mediciones de cada prueba. Con los valores aislados disponibles no es posible calcular de forma confiable la distribución, la mediana, los percentiles ni la variabilidad del tiempo de respuesta.

## 8. Discusión

Las pruebas permitieron comprobar el funcionamiento de la comunicación serial y observar cómo cambia la integridad de los mensajes al modificar la velocidad de transmisión.

La comparación entre velocidades mostró que algunas configuraciones presentaron una recepción correcta durante los ensayos registrados, mientras que otras tuvieron errores. No obstante, estos resultados deben interpretarse considerando que la comunicación depende tanto de los parámetros físicos y de configuración como de la implementación del programa receptor.

La utilización de mensajes identificados mediante números consecutivos facilitó la detección de respuestas incorrectas. Asimismo, la medición de RTT permitió observar el tiempo de respuesta del sistema completo, aunque no separa los retardos correspondientes a cada componente.

Para obtener resultados experimentales más sólidos, se recomienda repetir cada configuración varias veces, utilizar el mismo número de mensajes en todas las pruebas y guardar los tiempos de respuesta individuales en un archivo CSV. También es importante distinguir entre respuestas correctas, respuestas incompletas, mensajes incorrectos y tiempos de espera agotados.

Finalmente, la velocidad máxima confiable debe definirse a partir de pruebas repetibles y de una tasa de errores aceptable, no solamente a partir de la velocidad nominal configurada.

## 9. Conclusiones

- Se implementó una comunicación serial UART entre un ESP32 y una computadora mediante una interfaz USB-UART CP210x.
- Se utilizó un mecanismo de eco para comprobar la transmisión y recepción de mensajes.
- Las pruebas diagnósticas registraron una recepción correcta del 100 % a 115200 y 153600 baudios.
- Las dos pruebas a 230400 baudios presentaron errores de comunicación, por lo que se requiere analizar la configuración y el manejo de los mensajes antes de establecer una conclusión definitiva.
- El RTT medido incluye el envío, la recepción, el procesamiento y los retardos asociados a la interfaz USB y al sistema operativo.
- Los resultados disponibles son preliminares y no permiten establecer de forma definitiva la velocidad máxima confiable del sistema.

## 10. Trabajo futuro

Para ampliar la evaluación se propone:

1. Realizar pruebas de 1000 mensajes por configuración.
2. Verificar que el ESP32 y Python utilicen exactamente la misma velocidad de transmisión.
3. Registrar por separado los mensajes correctos, incorrectos y las respuestas que excedan el tiempo de espera.
4. Guardar las mediciones de RTT en archivos CSV.
5. Calcular el promedio, la mediana, la desviación estándar y los percentiles del RTT.
6. Generar gráficas que permitan comparar la tasa de errores y los tiempos de respuesta entre configuraciones.
7. Repetir cada ensayo para evaluar la reproducibilidad de los resultados.

## 11. Referencias

1. Espressif Systems. *ESP-IDF Programming Guide: UART*. Documentación oficial del fabricante: https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/uart.html

2. pySerial. *pySerial Documentation*. Documentación de la biblioteca utilizada para la comunicación serial desde Python: https://pyserial.readthedocs.io/en/latest/
