---
title: Práctica 3
layout: default
parent: Prácticas
nav_order: 3
---

# Práctica 3: Bases de datos con Firebase y Flask

## Objetivo

Crear una API en Flask que lea y escriba datos de sensores en Cloud Firestore, usando `firebase-admin` desde Python.

```text
Navegador o dispositivo  →  Flask (Python)  →  Cloud Firestore
```

## Conceptos clave

| SQL (relacional) | Firestore (no relacional) |
|---|---|
| Tabla | Colección (`sensores`) |
| Fila | Documento |
| Columna | Campo (`nombre`, `tipo`, `cantidad`) |

## Parte 1: Preparar Firebase y Firestore

1. En la [consola de Firebase](https://console.firebase.google.com) se usó el proyecto **ProyectoFinalVR-UR3**.
2. En **Firestore Database** se creó la base de datos `(default)`.
3. Se creó la colección `sensores` con un documento de ID automático:

| Campo | Tipo | Valor |
|---|---|---|
| `nombre` | string | `MPU-6050` |
| `tipo` | string | `IMU` |
| `cantidad` | number | `5` |

## Parte 2: Clave de servicio

1. En **Configuración del proyecto → Cuentas de servicio** se generó una clave privada.
2. El archivo se guardó junto a `app.py` con el nombre `serviceAccountKey.json`.

{: .warning }
La clave da acceso total al proyecto. Nunca se sube a GitHub ni se incluye en la entrega.

## Parte 3: Entorno virtual e instalación

```powershell
python -m venv entorno
entorno\Scripts\Activate.ps1
python -m pip install Flask firebase-admin
```

## Parte 4: Código (`app.py`)

```python
import firebase_admin
from firebase_admin import credentials, firestore
from flask import Flask, jsonify, request

# 1. Inicializar Firebase con la clave de servicio
cred = credentials.Certificate("serviceAccountKey.json")
firebase_admin.initialize_app(cred)
db = firestore.client()

# 2. Crear la aplicación Flask
app = Flask(__name__)


# 3. GET /inventario: lee la colección "sensores" y devuelve JSON
@app.route("/inventario", methods=["GET"])
def obtener_inventario():
    sensores = []
    for doc in db.collection("sensores").stream():
        dato = doc.to_dict()
        dato["id"] = doc.id
        sensores.append(dato)
    return jsonify(sensores)


# 4. POST /inventario: valida y guarda un sensor nuevo
@app.route("/inventario", methods=["POST"])
def agregar_sensor():
    datos = request.get_json(silent=True)
    if not isinstance(datos, dict):
        return jsonify({"error": "Se espera un JSON con nombre, tipo y cantidad"}), 400

    nombre = datos.get("nombre")
    tipo = datos.get("tipo")
    cantidad = datos.get("cantidad")

    if not isinstance(nombre, str) or not nombre.strip():
        return jsonify({"error": "'nombre' debe ser texto no vacío"}), 400
    if not isinstance(tipo, str) or not tipo.strip():
        return jsonify({"error": "'tipo' debe ser texto no vacío"}), 400
    if isinstance(cantidad, bool) or not isinstance(cantidad, int) or cantidad < 0:
        return jsonify({"error": "'cantidad' debe ser un entero >= 0"}), 400

    sensor = {"nombre": nombre.strip(), "tipo": tipo.strip(), "cantidad": cantidad}
    _, ref = db.collection("sensores").add(sensor)
    sensor["id"] = ref.id
    return jsonify(sensor), 201


if __name__ == "__main__":
    app.run(debug=True)
```

### Validaciones del POST

| Campo | Regla |
|---|---|
| Cuerpo | JSON válido (objeto) |
| `nombre` | Texto no vacío |
| `tipo` | Texto no vacío |
| `cantidad` | Entero mayor o igual a 0 |

## Parte 5: Pruebas

### A. Consulta (GET)

Se ejecutó `python app.py` y se abrió `http://127.0.0.1:5000/inventario`. Al cambiar `cantidad` en Firestore y recargar, el JSON reflejó el nuevo valor.

![Respuesta del GET en el navegador]({{ '/assets/img/practica3/respuestaGET.png' | relative_url }})

### B. Escritura (POST)

```powershell
Invoke-RestMethod -Uri http://127.0.0.1:5000/inventario -Method Post -ContentType "application/json" -Body '{"nombre":"DHT22","tipo":"Temperatura","cantidad":10}'
```

![Respuesta del POST en la terminal]({{ '/assets/img/practica3/respuestaPOST.png' | relative_url }})

El documento quedó guardado en la colección `sensores`:

![Documento nuevo en Firestore]({{ '/assets/img/practica3/respuestaPOST_FireBase.png' | relative_url }})

## Conclusiones

- Firestore organiza los datos en colecciones y documentos, sin esquema fijo.
- La clave de servicio solo debe vivir en el servidor Flask, nunca en el navegador ni en el dispositivo.
- La validación de tipos y campos antes de guardar evita datos incorrectos en la base de datos.