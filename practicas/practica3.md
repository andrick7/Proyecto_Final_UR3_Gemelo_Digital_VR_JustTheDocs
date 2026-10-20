
---
title: Práctica 3
layout: default
parent: Prácticas
nav_order: 3
---

# Práctica 3. Comunicación UART con ESP32

## 1. Introducción

La comunicación serial es un mecanismo ampliamente utilizado para intercambiar información entre microcontroladores y computadoras. Uno de los protocolos más comunes es UART (*Universal Asynchronous Receiver-Transmitter*), que permite transmitir datos mediante líneas de transmisión y recepción sin requerir una señal de reloj compartida.

En esta práctica se implementó una comunicación UART entre un ESP32 y una computadora mediante una interfaz USB-UART. Se desarrolló un programa en el microcontrolador para recibir mensajes y devolverlos al emisor, así como un programa en Python para automatizar las pruebas.

Se busca evaluar la integridad de los mensajes, analizar el tiempo de ida y vuelta y observar el comportamiento de la comunicación al modificar la velocidad de transmisión.

## 2. Objetivos

### 2.1 Objetivo general

Implementar y evaluar una comunicación UART entre un ESP32 y una computadora, utilizando Python para automatizar el envío y la recepción de mensajes.

### 2.2 Objetivos específicos

- Identificar el funcionamiento básico de la comunicación UART.
- Configurar diferentes velocidades de transmisión.
- Implementar un programa de eco en el ESP32.
- Automatizar el envío y la verificación de mensajes mediante Python.
- Calcular el porcentaje de mensajes correctos y errores.
- Medir el tiempo de ida y vuelta (*Round-Trip Time*, RTT).
- Registrar los resultados para generar tablas y gráficas comparativas.

## 3. Marco teórico

### 3.1 Comunicación UART

UART es un protocolo de comunicación serial asíncrona. Los dispositivos intercambian información utilizando principalmente dos señales:

- **TX:** línea por la que se transmiten los datos.
- **RX:** línea por la que se reciben los datos.

Para establecer una comunicación correcta, ambos dispositivos deben utilizar parámetros compatibles, como la velocidad de transmisión, el número de bits de datos, la paridad y los bits de parada.

Una configuración habitual es **8N1**, que significa:

- 8 bits de datos.
- N: sin bit de paridad.
- 1 bit de parada.

En esta configuración, cada byte requiere 10 bits transmitidos, considerando un bit de inicio, ocho bits de datos y un bit de parada.

### 3.2 Velocidad de transmisión

La velocidad de transmisión se expresa comúnmente en baudios. En una conexión UART convencional, donde cada símbolo representa un bit, el valor numérico de los baudios coincide con la tasa de bits por segundo.

Para una configuración 8N1, la tasa teórica de transmisión de bytes se calcula mediante:

$$
R_{\text{bytes}} = \frac{B}{10}
$$

donde:

- $R_{\text{bytes}}$ es la tasa teórica de transmisión, en bytes por segundo.
- $B$ es la velocidad de transmisión, en bits por segundo.

Por ejemplo, para una velocidad de transmisión de $115200$ baudios:

$$
R_{\text{bytes}} = \frac{115200}{10}
$$

$$
R_{\text{bytes}} = 11520\ \text{bytes/s}
$$

Este valor representa una tasa teórica para la transmisión continua de datos. El rendimiento real puede ser menor debido a los tiempos de procesamiento, las pausas entre mensajes, la interfaz USB-UART y el sistema operativo.

### 3.3 Integridad de los mensajes

La integridad de la comunicación se evalúa comparando cada mensaje recibido con el mensaje que se había enviado.

La tasa de mensajes correctos se calcula mediante:

$$
P_{\text{correctos}} =
\frac{N_{\text{correctos}}}{N_{\text{enviados}}}
\times 100
$$

El porcentaje de errores se calcula como:

$$
P_{\text{errores}} =
\frac{N_{\text{errores}}}{N_{\text{enviados}}}
\times 100
$$

El número total de errores se determina mediante:

$$
N_{\text{errores}} =
N_{\text{enviados}} - N_{\text{correctos}}
$$

donde:

- $N_{\text{enviados}}$ es el número total de mensajes transmitidos.
- $N_{\text{correctos}}$ es el número de respuestas que coinciden exactamente con los mensajes enviados.
- $N_{\text{errores}}$ es el número de mensajes incorrectos o sin respuesta.
- $P_{\text{correctos}}$ es el porcentaje de mensajes correctos.
- $P_{\text{errores}}$ es el porcentaje de errores.

Si todos los mensajes se clasifican como correctos o erróneos, se cumple que:

$$
P_{\text{correctos}} + P_{\text{errores}} = 100\%
$$

### 3.4 Tiempo de ida y vuelta (RTT)

El tiempo de ida y vuelta (*Round-Trip Time*, RTT) representa el intervalo entre el inicio del envío de un mensaje y la recepción completa de su respuesta.

Para el mensaje $i$, el RTT se calcula mediante:

$$
RTT_i = t_{\text{final},i} - t_{\text{inicial},i}
$$

donde:

- $t_{\text{inicial},i}$ es el instante en que comienza el envío del mensaje.
- $t_{\text{final},i}$ es el instante en que se recibe la respuesta completa.
- $RTT_i$ es el tiempo de ida y vuelta del mensaje, expresado en milisegundos si los instantes se convierten a esa unidad.

El RTT promedio se calcula mediante:

$$
RTT_{\text{promedio}} =
\frac{1}{N_{\text{RTT}}}
\sum_{i=1}^{N_{\text{RTT}}} RTT_i
$$

donde $N_{\text{RTT}}$ es el número de respuestas válidas consideradas para el cálculo.

El RTT incluye el tiempo de transmisión de ida y vuelta, el procesamiento en el ESP32, los retardos de la interfaz USB-UART y los tiempos asociados al sistema operativo. Por tanto, no representa exclusivamente el tiempo de procesamiento del microcontrolador ni la latencia unidireccional.

## 4. Materiales y herramientas

- Placa de desarrollo ESP32.
- Computadora con Windows.
- Cable USB para conectar el ESP32.
- Interfaz USB-UART CP210x, integrada en la placa o utilizada como adaptador.
- Arduino IDE.
- Python.
- Biblioteca `pySerial`.
- Terminal de comandos de Windows o PowerShell.

## 5. Desarrollo experimental

### 5.1 Configuración del ESP32

Se programó el ESP32 para recibir mensajes por UART y devolverlos al emisor. Este comportamiento se conoce como comunicación de eco (*echo*).

El siguiente código configura la comunicación a 115200 baudios:

```cpp
void setup() {
  Serial.begin(115200);
  Serial.setTimeout(1000);
}

void loop() {
  if (Serial.available() > 0) {
    String mensaje = Serial.readStringUntil('\n');
    Serial.println(mensaje);
  }
}
```

La instrucción `Serial.begin(115200)` establece la velocidad de comunicación. La función `Serial.readStringUntil('\n')` lee los caracteres recibidos hasta encontrar un salto de línea o alcanzar el tiempo de espera configurado.

Finalmente, `Serial.println(mensaje)` devuelve el mensaje recibido y agrega un salto de línea al final.

Para realizar las pruebas con otras velocidades, se debe modificar el valor de `Serial.begin()` y configurar la misma velocidad en el programa de Python.

### 5.2 Comunicación desde Python

Se utilizó la biblioteca `pySerial` para abrir el puerto COM asociado al ESP32, transmitir mensajes y leer las respuestas.

Cada mensaje se identifica mediante un número consecutivo, lo que permite verificar que el contenido recibido corresponde al enviado.

El formato de los mensajes es:

```text
MSG:0000
MSG:0001
MSG:0002
...
```

El programa compara cada respuesta con el mensaje original. Si ambos coinciden, el mensaje se clasifica como correcto. Si la respuesta no coincide o no se recibe dentro del tiempo de espera, se registra como error.

### 5.3 Prueba automatizada de 1000 mensajes

La prueba definitiva consiste en transmitir 1000 mensajes consecutivos. Para cada mensaje se registra:

- Número de mensaje.
- Contenido enviado.
- Contenido recibido.
- Estado de la comunicación.
- Tiempo de ida y vuelta.

El número de mensajes enviados se define como:

$$
N_{\text{enviados}} = 1000
$$

La tasa de mensajes correctos y el porcentaje de errores se calculan a partir de los resultados obtenidos.

Los datos se guardan en un archivo CSV, lo que permite procesarlos posteriormente para obtener estadísticas y generar gráficas.

**Importante:** para que la prueba sea válida, el Monitor Serial de Arduino IDE debe permanecer cerrado mientras Python utiliza el puerto COM. Además, el ESP32 y el programa de Python deben estar configurados con la misma velocidad de transmisión.

### 5.4 Procedimiento experimental

1. Conectar el ESP32 a la computadora mediante USB.
2. Verificar que el controlador USB-UART esté instalado y que Windows reconozca el puerto COM.
3. Cargar el programa de eco al ESP32.
4. Cerrar el Monitor Serial de Arduino IDE.
5. Ejecutar el programa de Python.
6. Seleccionar el puerto COM correspondiente al ESP32.
7. Ejecutar la prueba de 1000 mensajes a 115200 baudios.
8. Guardar el archivo CSV generado.
9. Repetir el procedimiento con otras velocidades, modificando la configuración tanto en el ESP32 como en Python.
10. Comparar la integridad de los mensajes y las estadísticas de RTT para cada velocidad.

## 6. Resultados experimentales

### 6.1 Pruebas diagnósticas preliminares

Antes de la prueba completa de 1000 mensajes, se realizaron ensayos diagnósticos de 100 mensajes para observar el comportamiento de la comunicación a distintas velocidades.

Los resultados registrados fueron los siguientes:

| Velocidad (baudios) | Mensajes enviados | Mensajes correctos | Errores |
|---:|---:|---:|---:|
| 115200 | 100 | 100 | 0 |
| 128000 | 100 | 0 | 100 |
| 153600 | 100 | 100 | 0 |
| 172800 | 100 | 0 | 100 |
| 230400, ensayo A | 100 | 46 | 54 |
| 230400, ensayo B | 100 | 68 | 32 |

Estos resultados corresponden a pruebas preliminares y no deben presentarse como los resultados de la prueba definitiva de 1000 mensajes.

Los resultados de los ensayos a 128000 y 172800 baudios requieren una revisión de la configuración utilizada. No es posible atribuir automáticamente los errores a una limitación física del ESP32 sin verificar la coincidencia de las velocidades, la configuración serial y el comportamiento del programa receptor.

En el caso de 230400 baudios, los dos ensayos presentaron resultados diferentes. Esto justifica repetir las pruebas bajo condiciones controladas antes de formular conclusiones sobre la confiabilidad de la comunicación.

### 6.2 Resultados de la prueba definitiva

Los resultados de la prueba de 1000 mensajes deben incorporarse en la siguiente tabla una vez que se ejecute el programa automatizado.

| Velocidad (baudios) | Enviados | Correctos | Errores | Correctos (%) | RTT promedio (ms) |
|---:|---:|---:|---:|---:|---:|
| 115200 | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |
| 153600 | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |
| 230400 | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |

Los valores pendientes deben sustituirse por los datos obtenidos experimentalmente. No deben estimarse a partir de las pruebas diagnósticas, puesto que los resultados pueden variar entre ejecuciones.

### 6.3 Análisis de latencia

El RTT promedio permite comparar el tiempo de respuesta de la comunicación a distintas velocidades de transmisión.

Para calcularlo se consideran únicamente las respuestas correctas que cuentan con una medición válida del tiempo de ida y vuelta.

Además del promedio, resulta útil registrar la mediana, el mínimo, el máximo, la desviación estándar y el percentil 95 del RTT. Estas métricas permiten evaluar tanto el comportamiento típico como la variabilidad de los tiempos de respuesta.

Los valores definitivos de latencia se incorporarán después de completar los ensayos de 1000 mensajes.

### 6.4 Gráficas

Con los datos del archivo CSV se recomienda generar las siguientes gráficas:

1. **Porcentaje de mensajes correctos por velocidad de transmisión.** Permite comparar la integridad de la comunicación.
2. **RTT promedio por velocidad de transmisión.** Permite comparar los tiempos medios de respuesta.
3. **RTT por número de mensaje.** Permite observar la variación temporal de las mediciones.
4. **Distribución de RTT.** Un histograma permite identificar la dispersión y posibles valores atípicos.

Las gráficas deben elaborarse con los resultados reales de cada prueba. No se presentan gráficas definitivas mientras no se cuente con los datos completos de los ensayos.

## 7. Discusión

La evaluación de una comunicación UART requiere distinguir entre la velocidad nominal configurada y el comportamiento real de la transmisión.

Una velocidad nominal mayor no garantiza por sí sola una comunicación más rápida o confiable. Los resultados dependen de la configuración de ambos dispositivos, la implementación del programa receptor, los retardos de la interfaz USB-UART y el sistema operativo.

El programa de eco permite evaluar la integridad de los mensajes y obtener una medición de RTT. Sin embargo, esta medición incluye componentes de tiempo que no pertenecen exclusivamente al ESP32.

También debe considerarse que un mensaje sin respuesta puede indicar un tiempo de espera agotado, un problema de configuración, una pérdida de datos o un fallo en el procesamiento. Por ello, los errores observados deben interpretarse junto con la configuración experimental y los registros de cada ensayo.

Finalmente, las pruebas de 100 mensajes sirven como diagnóstico inicial, mientras que los ensayos de 1000 mensajes proporcionan una muestra más amplia para analizar la consistencia de la comunicación.

## 8. Conclusiones

La práctica permitió implementar una comunicación UART entre un ESP32 y una computadora, utilizando un programa de eco en el microcontrolador y Python para automatizar el envío y la recepción de mensajes.

La comparación entre los mensajes enviados y recibidos proporciona una medida de la integridad de la comunicación. Asimismo, el registro del RTT permite evaluar el tiempo de respuesta del sistema completo.

Las pruebas diagnósticas preliminares mostraron diferencias en el comportamiento de la comunicación a distintas velocidades. Sin embargo, estos resultados deben verificarse mediante ensayos controlados y pruebas completas de 1000 mensajes antes de establecer conclusiones definitivas sobre el rendimiento.

La automatización de las mediciones facilita la repetición de los experimentos, la recopilación de datos y la elaboración de gráficas para comparar las condiciones de operación.

## 9. Trabajo futuro

Como continuación de la práctica, se propone:

- Completar las pruebas de 1000 mensajes para cada velocidad seleccionada.
- Repetir cada ensayo para comprobar la reproducibilidad de los resultados.
- Comparar el porcentaje de errores y las métricas de RTT.
- Generar gráficas a partir de los archivos CSV.
- Verificar la configuración serial antes de atribuir los errores a una limitación del hardware.
- Investigar el efecto del tamaño de los mensajes y del tiempo de espera sobre las mediciones.

## 10. Referencias

1. Espressif Systems. *ESP-IDF Programming Guide: UART*. Documentación oficial de ESP-IDF.
   https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/uart.html

2. pySerial. *pySerial Documentation*.
   https://pyserial.readthedocs.io/en/latest/

3. Arduino. *Serial*. Documentación de referencia de Arduino.
   https://docs.arduino.cc/language-reference/en/functions/communication/serial/
