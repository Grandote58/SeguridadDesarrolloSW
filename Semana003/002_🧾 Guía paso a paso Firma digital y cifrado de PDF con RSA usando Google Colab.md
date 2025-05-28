# 🧾 **Guía paso a paso: Firma digital y cifrado de PDF con RSA usando Google Colab**

## 🎯 **Objetivos del aprendizaje**

- Generar y almacenar una firma digital con RSA.
- Validar la integridad de un mensaje.
- Subir y cifrar un archivo PDF.
- Entender las aplicaciones prácticas de criptografía asimétrica.

## 🔧 **Paso 1: Instalar e importar las librerías necesarias**

```python
!pip install cryptography PyPDF2
```

```css
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.backends import default_backend
from google.colab import files
import PyPDF2
import os
```

## 🔐 **Paso 2: Generar par de claves RSA**

```python
# Generación de la clave privada
private_key = rsa.generate_private_key(
    public_exponent=65537,
    key_size=2048,
    backend=default_backend()
)

# Clave pública asociada
public_key = private_key.public_key()
```

## 🧾 **Paso 3: Crear y firmar un mensaje**

```python
mensaje = b"Este es un mensaje oficial firmado digitalmente."

# Crear firma digital
firma = private_key.sign(
    mensaje,
    padding.PSS(
        mgf=padding.MGF1(hashes.SHA256()),
        salt_length=padding.PSS.MAX_LENGTH
    ),
    hashes.SHA256()
)

# Guardar la firma en archivo .sig
with open("firma_digital.sig", "wb") as f:
    f.write(firma)

# Descargar la firma
files.download("firma_digital.sig")
```

## 📤 **Paso 4: Subir un archivo PDF**

```python
print("📎 Sube el archivo PDF a cifrar:")
uploaded = files.upload()

# Identificar nombre del archivo
for filename in uploaded.keys():
    pdf_file = filename
```

## 🔒 **Paso 5: Cifrar el contenido del PDF**

```python
# Leer el contenido del PDF (texto)
reader = PyPDF2.PdfReader(pdf_file)
contenido_pdf = ""
for page in reader.pages:
    contenido_pdf += page.extract_text()

# Cifrar el texto del PDF con la clave pública
contenido_cifrado = public_key.encrypt(
    contenido_pdf.encode('utf-8'),
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

# Guardar contenido cifrado
with open("contenido_cifrado_pdf.bin", "wb") as f:
    f.write(contenido_cifrado)

files.download("contenido_cifrado_pdf.bin")
```

## ✅ **Paso 6: Verificar la firma (opcional)**

```python
# Verificación de la firma original
try:
    public_key.verify(
        firma,
        mensaje,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    print("✅ Firma verificada correctamente.")
except Exception as e:
    print("❌ Error al verificar la firma:", e)
```

## 📌 **Resumen final**

| Acción                    | Archivo generado            |
| ------------------------- | --------------------------- |
| Firma digital             | `firma_digital.sig`         |
| Contenido cifrado del PDF | `contenido_cifrado_pdf.bin` |



------

## 📚 **Recursos complementarios**

- Python Cryptography Docs
- [PyPDF2 GitHub](https://github.com/py-pdf/pypdf)
- NIST SP 800-102 - **Recommendation for Digital Signatures**