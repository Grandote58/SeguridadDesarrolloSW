# 🧠🔐 **Práctica 3: ¡Cifra tu propio mensaje secreto!**

## 🎯 Objetivos de Aprendizaje

Al finalizar esta práctica, podrás:

✅ Utilizar `input()` para ingresar mensajes secretos personalizados

✅ Aplicar cifrado y descifrado dinámico en Python

✅ Ver el flujo completo de seguridad: mensaje → cifrado → descifrado

✅ Comprender cómo se transforma el texto claro en texto cifrado y viceversa

## 🧩 Paso a Paso en Google Colab

### 1️⃣ Instalar la librería necesaria

```python
!pip install cryptography
```

### 2️⃣ Importar los módulos y generar la clave

```python
from cryptography.fernet import Fernet

# Generar una clave y crear objeto Fernet
clave = Fernet.generate_key()
f = Fernet(clave)

# Mostrar la clave generada
print("🔑 Clave generada (guárdala si quieres descifrar luego):\n", clave.decode())
```

### 3️⃣ Solicitar el mensaje secreto al usuario 🧑‍💻

```python
# Entrada del usuario
mensaje = input("📝 Escribe tu mensaje secreto: ")

# Convertir a bytes
mensaje_bytes = mensaje.encode()
```

### 4️⃣ Cifrar el mensaje

```python
# Cifrado
mensaje_cifrado = f.encrypt(mensaje_bytes)
print("\n🔐 Tu mensaje cifrado es:\n", mensaje_cifrado.decode())
```

### 5️⃣ Descifrar el mensaje

```python
# Descifrado
mensaje_descifrado = f.decrypt(mensaje_cifrado).decode()
print("\n📩 Tu mensaje original recuperado es:\n", mensaje_descifrado)
```

## ✅ Resultado Esperado

- El usuario introduce un mensaje como `"Mis contraseñas están en un archivo .csv"`
- El mensaje es cifrado y mostrado como una cadena de texto ilegible
- Luego, se muestra el mensaje descifrado original

