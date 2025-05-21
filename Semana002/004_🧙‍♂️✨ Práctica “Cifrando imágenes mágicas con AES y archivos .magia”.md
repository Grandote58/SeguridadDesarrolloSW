# 🧙‍♂️✨ **Práctica: “Cifrando imágenes mágicas con AES y archivos .magia”**

## 🎯 Objetivos de Aprendizaje

Al completar esta práctica, el estudiante será capaz de:

✅ Cifrar múltiples imágenes en Python y almacenarlas en un solo archivo

✅ Añadir y leer metadatos personalizados (nombre, tamaño)

✅ Guardar y reutilizar una clave privada para cifrado y descifrado

✅ Comprender flujos de cifrado simétrico con objetos binarios complejos

## 📁 Archivos generados

| Tipo de archivo       | Descripción                                      |
| --------------------- | ------------------------------------------------ |
| `.key`                | Clave secreta privada (para AES)                 |
| `.magia`              | Archivo mágico con imágenes cifradas y metadatos |
| `.jpg` descomprimidos | Imágenes descifradas reconstruidas               |

## 🛠️ Paso a Paso en Google Colab

### 1️⃣ Instalar librería y configurar entorno

```python
!pip install cryptography
import os
from cryptography.fernet import Fernet
```

### 2️⃣ Subir 3 imágenes desde tu equipo (pueden ser `.jpg`, `.png`)

```python
from google.colab import files

print("📤 Sube 3 imágenes para cifrar")
uploaded = files.upload()
imagenes = list(uploaded.keys())[:3]  # Nos quedamos solo con las 3 primeras
```

### 3️⃣ Generar y guardar clave secreta en archivo `.key` 🔑

```python
clave = Fernet.generate_key()

with open("clave_magica.key", "wb") as file:
    file.write(clave)

print("🔐 Clave guardada en 'clave_magica.key'")
```

### 4️⃣ Cifrar las imágenes y almacenarlas con metadatos en `.magia` ✨

```python
import struct

# Cargar clave
with open("clave_magica.key", "rb") as file:
    clave_recuperada = file.read()

f = Fernet(clave_recuperada)

# Crear archivo .magia
with open("imagenes.magia", "wb") as archivo_magia:
    for idx, imagen in enumerate(imagenes):
        with open(imagen, "rb") as img_file:
            contenido = img_file.read()
            cifrado = f.encrypt(contenido)
            
            nombre = f"imagen_{idx+1}.jpg"
            tamaño = len(cifrado)

            # Guardar metadatos: nombre (longitud fija de 100 bytes), tamaño (int)
            nombre_bytes = nombre.encode().ljust(100, b'\0')  # rellenamos a 100 bytes
            archivo_magia.write(nombre_bytes)
            archivo_magia.write(struct.pack("I", tamaño))  # empaquetamos tamaño como entero
            archivo_magia.write(cifrado)

print("📦 Imágenes cifradas y almacenadas en 'imagenes.magia'")
```

### 5️⃣ Visualizar contenido del archivo `.magia` (estructura básica)

```python
print("📁 Tamaño final del archivo 'imagenes.magia':", os.path.getsize("imagenes.magia"), "bytes")
```

### 6️⃣ Desencriptar y reconstruir las imágenes desde el archivo `.magia` 🔓

```python
imagenes_recuperadas = []

with open("clave_magica.key", "rb") as file:
    clave = file.read()
f = Fernet(clave)

with open("imagenes.magia", "rb") as archivo_magia:
    while True:
        nombre_bytes = archivo_magia.read(100)
        if not nombre_bytes:
            break  # fin del archivo

        nombre = nombre_bytes.strip(b'\0').decode()
        tamaño_bytes = archivo_magia.read(4)
        tamaño = struct.unpack("I", tamaño_bytes)[0]

        cifrado = archivo_magia.read(tamaño)
        original = f.decrypt(cifrado)

        with open(f"restaurada_{nombre}", "wb") as img_out:
            img_out.write(original)
            imagenes_recuperadas.append(f"restaurada_{nombre}")

print("✅ Imágenes restauradas correctamente:")
for nombre in imagenes_recuperadas:
    print("📷", nombre)
```

## 🎨 ¿Qué aprendiste?

| Concepto clave            | Aplicación en la práctica          |
| ------------------------- | ---------------------------------- |
| Criptografía simétrica    | AES a través de Fernet             |
| Gestión de claves         | Guardar y leer archivos `.key`     |
| Lectura/escritura binaria | Archivos `.jpg`, `.magia`          |
| Estructura personalizada  | Metadatos manuales (nombre/tamaño) |
| Vectores                  | Lista `imagenes[]` y bucles        |

