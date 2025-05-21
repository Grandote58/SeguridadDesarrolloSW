# 🛡️💣 **Práctica: Protección de PDF con Contraseña AES y Cracking con John the Ripper**

## 🎯 Objetivos de Aprendizaje

Al finalizar esta práctica, el estudiante podrá:

✅ Proteger un archivo PDF con cifrado AES usando una contraseña

✅ Comprender cómo se generan claves seguras (y no tan seguras)

✅ Usar John the Ripper para romper la protección de un PDF débilmente cifrado

✅ Aplicar principios de **hardening** y concientización sobre contraseñas

## 🛠️ Herramientas Requeridas

| Entorno      | Herramientas                     |
| ------------ | -------------------------------- |
| Kali Linux   | `John the Ripper`, `pdf2john`    |
| Google Colab | `PyPDF2` o `pikepdf`             |
| Archivos     | PDF de ejemplo (`documento.pdf`) |

## 🔧 Parte 1: Crear un PDF protegido con contraseña AES

### 🧪 En Google Colab o en tu entorno local de Python:

```python
!pip install pikepdf

import pikepdf

# Crear un PDF base
from fpdf import FPDF
pdf = FPDF()
pdf.add_page()
pdf.set_font("Arial", size=12)
pdf.cell(200, 10, txt="Este es un documento protegido", ln=1)
pdf.output("documento_base.pdf")
```

### 🔐 Proteger el PDF con contraseña AES

```python
# Usamos PikePDF para cifrarlo con una contraseña débil (para pruebas)
password = "123456"

pdf = pikepdf.Pdf.open("documento_base.pdf")
pdf.save("documento_protegido.pdf", encryption=pikepdf.Encryption(owner=password, user=password, R=6))  # R=6 es AES-256
```

> 🎯 Ahora tienes un archivo `documento_protegido.pdf` cifrado con AES y contraseña débil.

# 💣 **Parte 2: Romper la contraseña con John the Ripper en Kali Linux**

## 🛠️ Paso 1: Verificar e Instalar John the Ripper y scripts auxiliares

### ✅ Verificar si `john` ya está instalado (Kali suele traerlo por defecto):

```bash
john --help
```

🔍 Si el comando funciona, ¡ya tienes John instalado! Si **no aparece**, instálalo así:

### 🧱 Instalar John the Ripper (versión jumbo con soporte para PDF):

```bash
sudo apt update
sudo apt install john
```

## 🧩 Paso 2: Instalar y preparar `pdf2john.pl`

Este script convierte archivos `.pdf` protegidos en hashes entendibles por John.

### 📥 Clonar el repositorio jumbo (si deseas la última versión):

```bash
sudo apt install git -y
git clone https://github.com/openwall/john.git
cd john/run
```

O, si estás en una instalación típica de Kali, puedes acceder a él así:

```bash
cd /usr/share/john
```

### 📍 Verifica que `pdf2john.pl` exista:

```bash
ls pdf2john.pl
```

> Si no está, puedes descargarlo directamente:

```bash
wget https://raw.githubusercontent.com/openwall/john/bleeding-jumbo/run/pdf2john.pl
chmod +x pdf2john.pl
```

## 📤 Paso 3: Extraer el hash del PDF protegido

Asegúrate de tener el archivo `documento_protegido.pdf` en el mismo directorio.

```bash
perl pdf2john.pl documento_protegido.pdf > hash.txt
```

✅ Esto creará un archivo `hash.txt` con una línea tipo:

```bash
documento_protegido.pdf:$pdf$...$...
```

## 🧨 Paso 4: Ejecutar John the Ripper para romper el PDF

Usamos un diccionario de contraseñas para intentar el crack (fuerza bruta con diccionario).

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

📍 Este diccionario está **comprimido por defecto**, así que descomprímelo si es la primera vez que lo usas:

```bash
gunzip /usr/share/wordlists/rockyou.txt.gz
```

✅ John comenzará a probar contraseñas del diccionario contra el hash del PDF.

## 🔍 Paso 5: Ver el resultado

Después de un rato (¡o en segundos si la contraseña era débil!), puedes ver la contraseña:

```bash
john --show hash.txt
```

📌 El resultado mostrará algo como:

```bash
documento_protegido.pdf:123456
```

## 🧠 Consejos de análisis y reflexión

- Usa contraseñas largas y complejas que **no estén en diccionarios públicos**
- Las contraseñas como `admin`, `123456`, `qwerty`, `letmein` son extremadamente vulnerables
- **John the Ripper** no rompe el cifrado, sino que **adivina la clave** usando diccionarios y reglas

## 🚨 Ética y legalidad

> Esta práctica está diseñada **exclusivamente para entornos educativos y éticos**. Utilizar estas técnicas sin consentimiento puede ser ilegal y sancionado.

## 🧪 ¿Qué sigue?

- Probar con contraseñas más complejas (`J@ck3t2025!`)
- Crear tus propios diccionarios
- Intentar romper ZIPs, RARs o claves Linux (`/etc/shadow`) en laboratorios controlados