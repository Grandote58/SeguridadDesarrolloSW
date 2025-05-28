# 🧾 **Guía Paso a Paso: Firma Digital con RSA usando Google Colab**

## 🎯 Objetivo de Aprendizaje

- Comprender el proceso de creación y verificación de una firma digital.
- Implementar el algoritmo RSA con Python en Google Colab.
- Usar funciones hash para garantizar integridad y autenticidad del mensaje.

## 🔧 **Paso 1: Preparar el entorno en Google Colab**

```python
!pip install cryptography
```

## 📥 **Paso 2: Importar las bibliotecas necesarias**

```python
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.backends import default_backend
```

## 🔐 **Paso 3: Generar un par de claves (pública y privada)**

```python
# Clave privada
private_key = rsa.generate_private_key(
    public_exponent=65537,
    key_size=2048,
    backend=default_backend()
)

# Clave pública
public_key = private_key.public_key()
```

## 🧾 **Paso 4: Crear un mensaje original**

```python
message = b"Este es el mensaje que quiero firmar digitalmente."
```

## ✍️ **Paso 5: Firmar el mensaje con la clave privada**

```python
signature = private_key.sign(
    message,
    padding.PSS(
        mgf=padding.MGF1(hashes.SHA256()),
        salt_length=padding.PSS.MAX_LENGTH
    ),
    hashes.SHA256()
)
```

## ✅ **Paso 6: Verificar la firma con la clave pública**

```python
try:
    public_key.verify(
        signature,
        message,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    print("✅ Firma verificada con éxito. El mensaje es auténtico.")
except Exception as e:
    print("❌ La firma no es válida:", e)
```

## 🔄 **Paso 7 (opcional): Probar manipulación del mensaje**

```python
message_alterado = b"Este es un mensaje modificado."

try:
    public_key.verify(
        signature,
        message_alterado,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    print("⚠️ La firma sigue siendo válida.")
except Exception as e:
    print("🛑 Alerta: La firma es inválida para el mensaje alterado:", e)
```

## 🎓 **Conclusión**

- Las firmas digitales permiten verificar que un mensaje **no ha sido alterado** y que **proviene del remitente legítimo**.
- El uso de RSA y funciones hash como SHA-256 garantiza la seguridad de este proceso.
- El uso de `verify()` nos permite detectar manipulaciones de datos o intentos de suplantación.

## 📚 **Bibliografía y recursos recomendados (2025)**

- Cryptography Library (Python)
- RFC 3447 – PKCS #1: RSA Cryptography Standard
- [NIST Digital Signature Guidelines](https://csrc.nist.gov/publications)