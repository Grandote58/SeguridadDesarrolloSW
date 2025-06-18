# 🔐 **Práctica: Explorando la Madurez en Seguridad con BSIMM – Diagnóstico y Mejora Continua**

### 🎯 **Objetivo de la Práctica**

Aplicar el modelo **BSIMM** en un entorno simulado para identificar el nivel de madurez en seguridad de una organización de desarrollo de software, evaluando prácticas reales dentro de sus **4 dominios** y sus respectivas funciones: *Gobernanza, Inteligencia, Touchpoints del SDLC y Despliegue*.

### 🌐 **Escenario Simulado**

La empresa **CyberApps Ltda.**, una fintech con más de 50 empleados, desarrolla aplicaciones de pagos móviles. El equipo técnico trabaja con metodologías ágiles y despliega continuamente código en producción.
 Sin embargo, recientemente sufrieron un intento de inyección SQL y descubrieron que no existía un inventario claro de actividades de seguridad implementadas.

**Tu rol:** Consultor de seguridad encargado de aplicar BSIMM para **diagnosticar, documentar y priorizar acciones** de mejora en la madurez del programa de seguridad del software.

### 📋 **Lista de Chequeo Inicial – Diagnóstico BSIMM**

Evalúa si en la empresa se realiza cada una de las siguientes prácticas observables de los **4 dominios de BSIMM**:

| 🧩 Dominio        | 🛠️ Práctica Observable                           | ¿Se cumple? |
| ---------------- | ----------------------------------------------- | ----------- |
| Governance       | Existencia de estrategia formal de seguridad    | ✅ / 🟥       |
| Intelligence     | Análisis de vulnerabilidades históricas         | ✅ / 🟥       |
| SSDL Touchpoints | Validación de requisitos de seguridad           | ✅ / 🟥       |
| Deployment       | Automatización de pruebas de seguridad en CI/CD | ✅ / 🟥       |



💡 **Instrucciones**: Revisa documentos, entrevista líderes de equipo, y analiza procesos para responder de forma realista.

### 🧭 **Paso a Paso – Aplicación del Modelo BSIMM**

#### 1️⃣ **Governance – Liderazgo y Planeación Estratégica**

**Actividad:** Identificar si la empresa cuenta con políticas y métricas de seguridad en el desarrollo.

**Tareas:**

- 🧾 Revisar si existe una política de seguridad de software.
- 📊 Verificar si se realiza seguimiento a métricas de riesgo técnico.
- 🧑‍💼 Confirmar si hay responsables asignados a seguridad.

**📌 Tip útil:**

> Comienza por mapear las políticas existentes en otras áreas. ¡La seguridad puede integrarse en lo que ya funciona! 🔎



#### 2️⃣ **Intelligence – Aprendizaje Basado en Experiencias**

**Actividad:** Analizar cómo la empresa aprende de sus propios errores y del ecosistema de amenazas.

**Tareas:**

- 🔁 Documentar lecciones aprendidas de fallos de seguridad anteriores.
- 🗂️ Verificar si existe una base de conocimiento de incidentes.
- 🌐 Investigar si el equipo sigue fuentes externas (CVE, OWASP, etc.).

**📌 Tip útil:**

> Una sesión quincenal de “inteligencia de amenazas” puede ser tan poderosa como cualquier herramienta. 🧠

#### 3️⃣ **SSDL Touchpoints – Integración en el Desarrollo**

**Actividad:** Evaluar cómo y en qué punto del SDLC se integran las prácticas de seguridad.

**Tareas:**

- 🧱 Revisar si se definen requisitos no funcionales de seguridad.
- 🧪 Validar si hay revisión manual o automática de código.
- 🤝 Analizar si los desarrolladores reciben retroalimentación de pruebas de seguridad.

**📌 Tip útil:**

> BSIMM enfatiza lo que se hace *en realidad*, no lo que se planea. ¡Pide ejemplos reales de código o historias de usuario con seguridad! 📘

#### 4️⃣ **Deployment – Seguridad en la Entrega Continua**

**Actividad:** Determinar el nivel de madurez en prácticas de seguridad en entornos de producción.

**Tareas:**

- ⚙️ Verifica si hay pruebas de seguridad en pipelines CI/CD.
- 🚨 Investiga si hay alertas automáticas de fallos de seguridad.
- 🔁 Comprueba si existe rollback ante errores críticos.

**📌 Tip útil:**

> Las herramientas como GitHub Actions, Jenkins y GitLab CI pueden incorporar escáneres como ZAP o Snyk con muy poco esfuerzo. 🧪⚡

### 🧮 **Rúbrica de Evaluación (Autoevaluación/Docente)**

| Criterio                                 | Nivel Inicial | Nivel Medio | Nivel Alto |
| ---------------------------------------- | ------------- | ----------- | ---------- |
| Evaluación completa de prácticas BSIMM   | ⬜             | ⬜           | ⬜          |
| Documentación del análisis por dominio   | ⬜             | ⬜           | ⬜          |
| Propuestas de mejora justificadas        | ⬜             | ⬜           | ⬜          |
| Aplicación contextualizada del modelo    | ⬜             | ⬜           | ⬜          |
| Reflexión crítica sobre el estado actual | ⬜             | ⬜           | ⬜          |



### 🧠 **Reflexión Final**

- ¿Qué prácticas observaste que no se estaban implementando pero eran críticas?
- ¿Qué dominio te pareció más descuidado y por qué?
- ¿Cómo podrías usar BSIMM como una guía de mejora continua en tu organización o proyecto?

### 📌 Recursos Recomendados

- 🔗 [Sitio oficial de BSIMM](https://www.bsimm.com/)
- 📘 *BSIMM13 Report* (última versión con métricas y análisis por sector)
- 🎥 Video: “How BSIMM Improves Real-World Security Programs” (YouTube, Synopsys)
- 📄 Plantilla editable de hoja de ruta de madurez: Descargar desde BSIMM Toolkit

