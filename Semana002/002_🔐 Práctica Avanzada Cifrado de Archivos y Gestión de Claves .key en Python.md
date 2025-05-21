# 🔐 **Práctica Avanzada: Cifrado de Archivos y Gestión de Claves .key en Python**

## 🎯 Objetivos de Aprendizaje

Al finalizar esta práctica, podrás:

✅ Generar claves seguras y guardarlas en archivos `.key`

✅ Leer archivos de texto y cifrarlos completamente

✅ Proteger y recuperar la clave para futuros descifrados

✅ Implementar cifrado de archivos en entornos reales (copias, respaldos, logs, etc.)

## 🛠️ Requisitos Previos

- Haber completado la práctica anterior de cifrado con AES (`Fernet`)
- Familiaridad básica con lectura/escritura de archivos en Python

## 🧩 Paso a Paso en Google Colab

### 1️⃣ Instalar la librería `cryptography`

```python
!pip install cryptography
```

### 2️⃣ Importar módulos necesarios

```python
from cryptography.fernet import Fernet
import os
```

### 3️⃣ Generar y guardar la clave en un archivo `.key`

```python
# Generar la clave
clave = Fernet.generate_key()

# Guardar la clave en un archivo
with open("mi_clave.key", "wb") as key_file:
    key_file.write(clave)

print("✅ Clave guardada en 'mi_clave.key'")
```

### 4️⃣ Leer la clave desde el archivo `.key`

```python
# Cargar la clave desde el archivo
with open("mi_clave.key", "rb") as key_file:
    clave_recuperada = key_file.read()

# Crear el objeto Fernet
f = Fernet(clave_recuperada)
```

### 5️⃣ Crear un archivo de texto simulado para cifrar 📝

```python
# Crear un archivo de texto
with open("secreto.txt", "w") as archivo:
    archivo.write("Este archivo contiene datos sensibles como contraseñas y tokens.")
print("📝 Archivo 'secreto.txt' creado.")
```

### 6️⃣ Cifrar el contenido del archivo

```python
# Leer el contenido del archivo
with open("secreto.txt", "rb") as archivo:
    datos = archivo.read()

# Cifrar los datos
datos_cifrados = f.encrypt(datos)

# Guardar los datos cifrados
with open("secreto_cifrado.txt", "wb") as archivo_cifrado:
    archivo_cifrado.write(datos_cifrados)

print("🔐 Archivo cifrado guardado como 'secreto_cifrado.txt'")
```

### 7️⃣ Descifrar el archivo cifrado 🔓

```python
# Leer el archivo cifrado
with open("secreto_cifrado.txt", "rb") as archivo_cifrado:
    datos_cifrados = archivo_cifrado.read()

# Descifrar los datos
datos_descifrados = f.decrypt(datos_cifrados)

# Guardar los datos descifrados en un nuevo archivo
with open("secreto_descifrado.txt", "wb") as archivo_descifrado:
    archivo_descifrado.write(datos_descifrados)

print("📂 Archivo descifrado guardado como 'secreto_descifrado.txt'")
```

## ✅ Resultado Esperado

- El archivo `secreto.txt` será cifrado como `secreto_cifrado.txt`
- Luego podrás recuperarlo en `secreto_descifrado.txt`
- La clave se guarda de forma segura en `mi_clave.key`

## 💡 Buenas Prácticas

🛡️ **No compartas archivos `.key` públicamente.**
 📁 **Guarda claves en lugares seguros**, como `AWS Secrets Manager`, `Vault by HashiCorp`, o entornos protegidos.
 📌 Puedes modificar esta práctica para cifrar imágenes, PDFs, o CSVs.

## 📚 ¿Qué aprendiste?

🔐 Cómo separar y gestionar claves
 📦 Cómo cifrar archivos reales de manera sencilla
 🔁 Cómo restaurar información desde un archivo cifrado