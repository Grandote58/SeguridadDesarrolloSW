# 🧩 MÓDULO DE APRENDIZAJE: **Construcción Ética de un Ransomware Educativo con RSA**

## 🎯 Objetivo General

Comprender la lógica, estructura y técnicas utilizadas por el ransomware moderno, implementando un **simulador educativo con cifrado RSA**, sin causar daños reales.

## 🧠 Objetivos Específicos

✅ Entender el funcionamiento interno de un ransomware.

✅ Aplicar cifrado asimétrico (RSA) para bloquear archivos.

✅ Implementar un "simulador inofensivo" de ransomware en Python.

✅ Fomentar el pensamiento crítico y ético en ciberseguridad.

## 📚 Escenario Didáctico

> 💼 Eres un Analista de Ciberseguridad en una empresa de tecnología. Tu tarea es crear un **entorno controlado de simulación** para enseñar cómo funciona un ataque de ransomware. El objetivo es **aprender a defenderse**.

## 🧰 Herramientas Necesarias

- 🔗 Google Colab o VSCode con Python instalado
- 📦 Bibliotecas: `cryptography`, `os`, `base64`
- 🗃️ Archivos de prueba `.txt`, `.pdf` o `.docx`
- ⚠️ ¡Nunca usar con archivos reales de producción!

## 🔨 Actividad Paso a Paso

### 🔐 Paso 1: Instalación de librerías

```python
!pip install cryptography
```

### 📦 Paso 2: Importar módulos necesarios

```python
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import serialization, hashes
import os
```

### 🛠️ Paso 3: Generar par de claves RSA

```python
private_key = rsa.generate_private_key(
    public_exponent=65537,
    key_size=2048,
)

public_key = private_key.public_key()
```

### 📂 Paso 4: Crear archivos de prueba

```python
with open("documento_confidencial.txt", "w") as f:
    f.write("⚠️ Este es un documento importante que será cifrado.")
```

### 🔏 Paso 5: Cifrar el archivo (simulación del ataque)

```python
# Leer archivo
with open("documento_confidencial.txt", "rb") as f:
    datos = f.read()

# Cifrar
datos_cifrados = public_key.encrypt(
    datos,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

# Guardar archivo cifrado
with open("documento_confidencial.cifrado", "wb") as f:
    f.write(datos_cifrados)

# Eliminar el archivo original (simulado)
os.remove("documento_confidencial.txt")
```

🧨 ¡BOOM! El archivo fue **cifrado** y el original **eliminado**. Este es el efecto típico de un ransomware real (pero aquí de forma segura) 🔒💣

### 📝 Paso 6: Simular la "nota de rescate"

```python
with open("LEEME.txt", "w") as f:
    f.write("""
    🧠 Simulador Educativo de Ransomware
    
    Tus archivos han sido cifrados usando RSA 🔐.
    Esto es solo una práctica de ciberseguridad 🧑‍💻.
    Para restaurarlos, ejecuta el código de descifrado.
    """)

print("📝 Nota de rescate creada con éxito.")
```

### 🔓 Paso 7: Recuperar archivo con la clave privada

```python
# Leer archivo cifrado
with open("documento_confidencial.cifrado", "rb") as f:
    datos_encriptados = f.read()

# Descifrar
datos_descifrados = private_key.decrypt(
    datos_encriptados,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

# Restaurar el archivo original
with open("documento_recuperado.txt", "wb") as f:
    f.write(datos_descifrados)

print("✅ Archivo restaurado exitosamente.")
```

## 🧭 Conclusiones Éticas

⚠️ Este módulo no busca enseñar a hacer daño, sino **entender para proteger mejor**. El conocimiento sin ética puede ser un arma, pero con responsabilidad, es una defensa poderosa. 💡🛡️

## 🧠 ¿Qué aprendiste?

| Competencia         | Logro       |
| ------------------- | ----------- |
| Cifrado RSA         | ✔️ Aplicado  |
| Simulación segura   | ✔️ Ejecutada |
| Pensamiento crítico | ✔️ Promovido |
| Defensa activa      | ✔️ Reforzada |