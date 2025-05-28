# 🛡️ **Proyecto Completo: Firma y Cifrado Asimétrico de PDF con RSA (Interfaz interactiva)**

## 🧩 Paso 1: Instalar librerías necesarias

```python
!pip install cryptography PyPDF2 ipywidgets
```

------

## 📥 Paso 2: Importar librerías

```python
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.backends import default_backend
from google.colab import files
import PyPDF2
import ipywidgets as widgets
from IPython.display import display
import os
```

## 🔐 Paso 3: Generar claves RSA

```python
private_key = rsa.generate_private_key(
    public_exponent=65537,
    key_size=2048,
    backend=default_backend()
)

public_key = private_key.public_key()
```

## 📎 Paso 4: Subir archivo PDF con interfaz

```python
upload_btn = widgets.Button(description="📂 Subir PDF")
output_upload = widgets.Output()

def subir_pdf(b):
    with output_upload:
        uploaded = files.upload()
        global pdf_filename
        for name in uploaded.keys():
            pdf_filename = name
        print(f"✅ Archivo cargado: {pdf_filename}")

upload_btn.on_click(subir_pdf)
display(upload_btn, output_upload)
```

## ✍️ Paso 5: Firmar mensaje y guardar firma

```python
mensaje_original = b"Este es un mensaje firmado digitalmente por el remitente."

firma = private_key.sign(
    mensaje_original,
    padding.PSS(
        mgf=padding.MGF1(hashes.SHA256()),
        salt_length=padding.PSS.MAX_LENGTH
    ),
    hashes.SHA256()
)

# Guardar la firma
with open("firma_digital.sig", "wb") as f:
    f.write(firma)

files.download("firma_digital.sig")
```

## 🔒 Paso 6: Cifrar contenido del PDF

```python
reader = PyPDF2.PdfReader(pdf_filename)
texto_pdf = "".join([page.extract_text() for page in reader.pages])

cifrado = public_key.encrypt(
    texto_pdf.encode(),
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

with open("pdf_cifrado.bin", "wb") as f:
    f.write(cifrado)

files.download("pdf_cifrado.bin")
```

## 🔓 Paso 7: Desencriptar PDF y verificar firma (opcional)

```python
# Leer contenido cifrado
with open("pdf_cifrado.bin", "rb") as f:
    cifrado_leido = f.read()

# Desencriptar
try:
    descifrado = private_key.decrypt(
        cifrado_leido,
        padding.OAEP(
            mgf=padding.MGF1(algorithm=hashes.SHA256()),
            algorithm=hashes.SHA256(),
            label=None
        )
    )
    print("✅ Contenido descifrado exitosamente:")
    print(descifrado.decode())

    # Verificar firma
    public_key.verify(
        firma,
        mensaje_original,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    print("✅ Firma verificada correctamente. El contenido es confiable.")
except Exception as e:
    print("❌ Error al descifrar o verificar firma:", e)
```

------

## 🎓 Conclusión del Proyecto

Este proyecto demuestra:

- Cómo aplicar **criptografía asimétrica (RSA)** para firmar y cifrar.
- Cómo integrar una **interfaz simple con widgets** para hacerlo más didáctico.
- Cómo garantizar la **autenticidad y confidencialidad** de archivos sensibles (como PDF).