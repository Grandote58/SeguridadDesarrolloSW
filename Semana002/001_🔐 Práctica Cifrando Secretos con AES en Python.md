# 🔐 **Práctica: Cifrando Secretos con AES en Python**

## 🎯 Objetivos de Aprendizaje

Al finalizar esta práctica, serás capaz de:

✅ Comprender cómo funciona el cifrado simétrico con AES

✅ Implementar cifrado y descifrado en Python utilizando la librería `cryptography`

✅ Aplicar buenas prácticas para proteger datos confidenciales

✅ Probar interactivamente en Google Colab de forma segura y educativa

## 🛠️ Requisitos Previos

- Tener acceso a Google Colab
- Conocimientos básicos de Python
- Curiosidad por la seguridad digital 😎🔐

## 🧩 Paso a Paso en Google Colab

### 1️⃣ Instalar librería necesaria

```python
!pip install cryptography
```

### 2️⃣ Importar módulos necesarios

```python
from cryptography.fernet import Fernet
```

### 3️⃣ Generar una clave secreta 🔑

```python
# Generamos una clave secreta para cifrar y descifrar
clave = Fernet.generate_key()
print(f"🔑 Clave generada: {clave}")
```

### 4️⃣ Crear un objeto de cifrado

```python
# Crear objeto Fernet con la clave
f = Fernet(clave)
```

### 5️⃣ Cifrar un mensaje secreto ✉️

```python
mensaje = "Los datos del usuario están en riesgo si no se cifran."
mensaje_bytes = mensaje.encode()  # Convertimos a bytes
mensaje_cifrado = f.encrypt(mensaje_bytes)

print(f"🔐 Mensaje cifrado: {mensaje_cifrado}")
```

### 6️⃣ Descifrar el mensaje 🔓

```python
mensaje_descifrado = f.decrypt(mensaje_cifrado)
print(f"📤 Mensaje original: {mensaje_descifrado.decode()}")
```

## 📌 Explicación

| Paso             | Descripción                          |
| ---------------- | ------------------------------------ |
| `generate_key()` | Genera una clave secreta segura      |
| `Fernet(key)`    | Crea el objeto para cifrar/descifrar |
| `encrypt()`      | Cifra el mensaje (bytes)             |
| `decrypt()`      | Recupera el mensaje original (bytes) |

## ✅ Resultado Esperado

Tu mensaje cifrado será ilegible para cualquier persona sin la clave. Al descifrarlo con `decrypt()`, recuperarás el mensaje original.

## 📚 Reflexión final

🔍 **¿Qué aprendimos?**

- AES (implementado a través de `Fernet`) es una forma segura y sencilla de cifrar datos en Python.
- Proteger la **clave secreta** es tan importante como el cifrado mismo.
- En proyectos reales, es importante usar **almacenamiento seguro** para las claves (como un vault o AWS Secrets Manager).