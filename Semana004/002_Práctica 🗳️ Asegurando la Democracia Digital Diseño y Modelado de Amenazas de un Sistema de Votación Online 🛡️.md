# **Práctica:** 🗳️ Asegurando la Democracia Digital: Diseño y Modelado de Amenazas de un Sistema de Votación Online 🛡️

## **Introducción:**

En nuestra práctica anterior, nos enfocamos en la implementación de requerimientos de seguridad a nivel de código en una aplicación simple. 

Ahora, vamos a elevar la mira y abordar un desafío mucho más complejo (conceptualmente): el diseño de un **Sistema de Votación Online Seguro**. 😮

No vamos a escribir código esta vez. Nuestro objetivo es **profundizar en la identificación de requerimientos de seguridad críticos, diseñar una arquitectura conceptual segura y realizar un modelado de amenazas más detallado**. 

Este tipo de sistema es extremadamente sensible y requiere un nivel de seguridad muy alto. ¡Prepárate para pensar como un verdadero arquitecto de seguridad!

## **Escenario del Sistema:**

Imagina que una universidad quiere implementar un sistema para que sus estudiantes voten online por sus representantes estudiantiles.

- **Funcionalidades Clave:**
  1. **Autenticación del Votante:** Solo estudiantes matriculados y habilitados pueden votar.
  2. **Presentación de Candidatos:** Los votantes ven la lista de candidatos para los puestos relevantes.
  3. **Emisión del Voto:** El votante selecciona sus opciones y emite su voto.
  4. **Anonimato del Voto:** El voto emitido no debe poder ser rastreado hasta el votante individual una vez registrado.
  5. **Integridad del Voto:** Cada voto debe ser contado correctamente, sin alteraciones. Un votante, un voto (por elección).
  6. **Disponibilidad del Sistema:** El sistema debe estar disponible durante el periodo de votación.
  7. **Auditoría y Verificabilidad (Transparencia):** Debe ser posible verificar (de alguna manera) que el conteo es correcto, sin comprometer el anonimato.

### **PASO 1: 🎯 IDENTIFICACIÓN DE ACTIVOS Y REQUERIMIENTOS DE SEGURIDAD CRÍTICOS**

Primero, ¿qué es lo más valioso que debemos proteger?

- **Activos Principales:**

  - 👤 **Identidad del Votante:** Información personal, credenciales.

  - 🗳️ **Voto Individual:** La elección específica de un votante.

  - 📊 **Conteo Agregado de Votos:** El resultado final de la elección.

  - 📜 **Padrón Electoral:** Lista de votantes elegibles.

  - ⚙️ **Disponibilidad e Integridad del Sistema de Votación:** La plataforma en sí.

    

- **Objetivos de Seguridad Fundamentales (Tríada CIA + Adicionales):**

  | Objetivo de Seguridad                 | Descripción para el Sistema de Votación                      | Riesgo si se Compromete                                      |
  | ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 🔒 **Confidencialidad**                | El voto debe ser secreto. La identidad del votante no se vincula al voto emitido. | Pérdida de anonimato, coacción, represalias.                 |
  | ✓ **Integridad**                      | Los votos no pueden ser alterados, ni antes, ni durante, ni después de la emisión. El padrón debe ser correcto. Un votante = un voto. | Fraude electoral, resultados incorrectos, votos duplicados o perdidos. |
  | **Disponibilidad**                    | El sistema debe funcionar correctamente durante el periodo de votación. | Supresión de votantes, incapacidad de votar.                 |
  | 🕵️ **Autenticidad**                    | Solo votantes legítimos y verificados pueden emitir votos.   | Votos fraudulentos de no elegibles, suplantación de identidad. |
  | ⚖️ **No Repudio**                      | (Para el sistema) Que el sistema pueda probar que un voto fue recibido y contado (sin revelar quién votó qué). (Para el votante) Que el votante no pueda negar haber votado (o que el sistema pueda detectar múltiples intentos de voto). | Disputas sobre si un voto fue contado. Dificultad para prevenir el voto múltiple. |
  | 🔍 **Verificabilidad / Auditabilidad** | Posibilidad de verificar que los votos fueron contados correctamente y que el proceso fue justo, sin comprometer el anonimato. | Falta de confianza en el resultado, acusaciones de manipulación. |

  

- **Requerimientos de Seguridad Específicos (RS) Clave:**

  - **RS1 (Autenticación Fuerte del Votante):** El sistema DEBE usar autenticación multifactor (MFA) para verificar la identidad de los estudiantes (ej. usuario/contraseña institucional + código SMS/App).

    

  - **RS2 (Separación Criptográfica Votante-Voto):** El sistema DEBE implementar mecanismos criptográficos (ej. firmas ciegas, sobres digitales) para desvincular la identidad del votante del contenido del voto una vez que este es validado como elegible.

    

  - **RS3 (Cifrado de Votos en Tránsito y Reposo):** Todos los votos DEBEN ser cifrados durante su transmisión (HTTPS/TLS) y mientras están almacenados antes del conteo (cifrado a nivel de base de datos o de archivo).

    

  - **RS4 (Registro de Voto Único):** El sistema DEBE asegurar que cada votante elegible pueda emitir un solo voto por cada elección.

    

  - **RS5 (Pistas de Auditoría Seguras e Inmutables):** El sistema DEBE generar pistas de auditoría detalladas de los eventos críticos (ej. inicio de sesión, intento de voto, recepción de voto – anonimizado), almacenadas de forma segura e idealmente inmutable (ej. blockchain o WORM storage).

    

  - **RS6 (Protección Anti-Coerción - Limitada):** El sistema DEBE permitir a los votantes cambiar su voto mientras la votación está abierta (el último voto emitido es el que cuenta). Esto puede ayudar a mitigar algunos tipos de coacción.

    

  - **RS7 (Resistencia a DoS/DDoS):** La infraestructura DEBE estar diseñada para resistir ataques de Denegación de Servicio.

    

  - **RS8 (Código Seguro y Validado):** El software DEBE ser desarrollado siguiendo prácticas de codificación segura y ser sometido a revisiones de código y pruebas de penetración por terceros independientes.

    

  - **RS9 (Transparencia del Conteo):** El proceso de conteo DEBE ser transparente y, si es posible, verificable por observadores (ej. publicando hashes de votos cifrados y permitiendo la verificación del proceso de descifrado y suma).

### **PASO 2: 🗺️ DISEÑO DE ARQUITECTURA CONCEPTUAL SEGURA**

Proponemos una arquitectura modular para separar responsabilidades y aplicar defensa en profundidad.

**Diagrama de Arquitectura Conceptual Simplificada:**

```css
    subgraph "Zona de Votante (Navegador del Usuario)"
        A[💻 Interfaz de Votación (UI/Frontend)]
    end

    A -- HTTPS/TLS (RS3) --> B{🌐 Servidor Web / Balanceador de Carga}
    B -- Protección DDoS (RS7) --> B

    subgraph "Zona de Aplicación (Servidores Seguros)"
        B --> C[🔑 Servicio de Autenticación (MFA - RS1)]
        C --> D[(🔒 Padrón Electoral Seguro)]
        B --> E[🗳️ Servicio de Emisión de Votos]
        E -- Desvinculación Votante-Voto (RS2) --> F[📬 Buzón de Votos Cifrados (RS3)]
        F --> G[⏳ Almacenamiento Temporal de Votos Cifrados (BD Segura)]
    end

    subgraph "Zona de Conteo y Auditoría (Acceso Restringido)"
        H[⏰ Servicio de Conteo (Post-Votación)]
        G --> H
        H --> I[📊 Almacenamiento de Resultados Agregados]
        H --> J[(📜 Logs de Auditoría Seguros e Inmutables - RS5)]
        D --> J
        E --> J
    end

    K[👨‍💻 Administradores del Sistema] -- Acceso Seguro / MFA --> C
    K -- Acceso Seguro / MFA --> H
    L[🧐 Auditores / Observadores] -- Acceso Controlado --> J
    L -- Acceso Controlado (a datos anonimizados/agregados) --> I
```

**Componentes Clave y sus Funciones de Seguridad:**

1. **Interfaz de Votación (UI/Frontend - A):**

   - Desarrollada con frameworks seguros (evitando XSS, CSRF).

   - Comunica exclusivamente sobre HTTPS/TLS.

     

2. **Servidor Web / Balanceador de Carga (B):**

   - Punto de entrada. Implementa protección Anti-DDoS (RS7).

   - Terminación TLS.

     

3. **Servicio de Autenticación (C):**

   - Maneja el login de los votantes usando MFA (RS1).

   - Valida contra el Padrón Electoral (D).

   - Genera un "token de votación" anónimo o una prueba de elegibilidad una vez autenticado, que no se vincula directamente al voto final.

     

4. **Padrón Electoral Seguro (D):**

   - Base de datos con la lista de votantes elegibles.

   - Acceso estrictamente controlado. Cifrado en reposo.

     

5. **Servicio de Emisión de Votos (E):**

   - Recibe la selección del candidato y el token de votación anónimo.

   - Aplica técnicas criptográficas (RS2) para asegurar que el voto se registre sin vincularlo a la identidad del votante. Ej: el votante "firma" el voto con una clave derivada del token anónimo, o el sistema emite un "recibo" ciego.

   - Asegura el voto único (RS4) invalidando el token después de su uso.

   - Registra eventos de auditoría (RS5).

     

6. **Buzón de Votos Cifrados (F) y Almacenamiento Temporal (G):**

   - Los votos se cifran individualmente (RS3) antes de almacenarse.

   - Nadie, ni siquiera los administradores, puede ver los votos individuales antes del conteo.

     

7. **Servicio de Conteo (H):**

   - Se activa solo después de que cierra la votación.

   - Requiere múltiples autorizaciones (ej. llaves criptográficas distribuidas) para descifrar los votos.

   - Realiza el conteo y almacena los resultados agregados (I).

   - Permite la verificabilidad del proceso (RS9).

     

8. **Logs de Auditoría Seguros (J):**

   - Registros detallados e inmutables de todas las acciones críticas del sistema.

     

9. **Administradores (K) y Auditores (L):**

   - Acceso basado en roles, con MFA, a los componentes relevantes.

### **PASO 3: 🕵️‍♂️ MODELADO DE AMENAZAS (USANDO STRIDE)**

Vamos a aplicar STRIDE a algunos componentes/flujos clave.

| Componente/Flujo Afectado    | Amenaza STRIDE               | Descripción de la Amenaza Específica                         | Contramedida(s) Clave(s) Implementada(s)                     | RS Relevante(s) |
| ---------------------------- | ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------- |
| **Proceso de Autenticación** | **S**poofing (Suplantación)  | Un atacante intenta hacerse pasar por un estudiante elegible para votar. | MFA, contraseñas robustas, bloqueo de cuentas tras intentos fallidos. | RS1             |
|                              | **D**enial of Service        | Ataque de fuerza bruta o bombardeo de logins para bloquear cuentas o sobrecargar el servicio. | Rate limiting, CAPTCHAs, protección Anti-DDoS en el balanceador. | RS7, RS1        |
| **Emisión del Voto**         | **T**ampering (Manipulación) | Un atacante (o malware en el PC del votante) modifica el voto en tránsito antes de que llegue al servidor. | HTTPS/TLS en toda la comunicación. Considerar firmas digitales del lado del cliente (más complejo). | RS3             |
|                              | **R**epudiation              | Un votante legítimo vota y luego niega haberlo hecho, o intenta votar de nuevo. | Token de votación de un solo uso, registros de auditoría del uso del token (anonimizados respecto al voto). | RS4, RS5        |
|                              | **I**nformation Disclosure   | Un atacante intercepta el voto y ve la elección del votante. | HTTPS/TLS. El voto se desvincula de la identidad antes de almacenarse permanentemente. | RS2, RS3        |
| **Almacenamiento de Votos**  | **T**ampering                | Un administrador deshonesto o un atacante con acceso a la BD modifica los votos almacenados. | Cifrado de votos individualmente, control de acceso estricto a la BD, logs de auditoría inmutables. | RS3, RS5        |
|                              | **I**nformation Disclosure   | Un atacante accede a la BD y logra descifrar los votos, vinculándolos a los votantes (si la desvinculación falló). | Fuerte separación criptográfica votante-voto, cifrado robusto. | RS2, RS3        |
| **Proceso de Conteo**        | **T**ampering                | Modificación del algoritmo de conteo o de los resultados durante el proceso. | Acceso multifactor para iniciar el conteo, código revisado y auditado, transparencia en el proceso. | RS8, RS9        |
|                              | **D**enial of Service        | Impedir que el conteo se realice o se complete.              | Infraestructura resiliente, backups.                         | RS7             |
| **Padrón Electoral**         | **T**ampering                | Añadir votantes falsos o eliminar votantes legítimos del padrón. | Controles de acceso estrictos, revisión y validación del padrón antes de la elección, pistas de auditoría. | RS5             |

**Diagrama de Flujo de Amenaza (Ejemplo: Coerción del Votante):**

```css
    X[👤 Coercitor] -- Observa/Presiona --> V[👩‍🎓 Votante]
    V -- Obligado a votar por X --> A[💻 Interfaz de Votación]
    A -- Voto Coaccionado --> E[🗳️ Servicio de Emisión de Votos]

    subgraph "Mitigación Parcial"
        M1[RS6: Permite cambiar el voto]
        M2[Educación: Votar en privado]
        E -.-> M1
    end
```

- **Notas del Diagrama:** La coerción es un problema difícil de resolver tecnológicamente. RS6 (permitir cambiar el voto) ofrece una mitigación parcial, ya que el votante podría cambiar su voto más tarde si tiene la oportunidad de votar en privado. La educación y la provisión de lugares seguros para votar también son contramedidas no técnicas.

### **PASO 4: 💡 DISCUSIÓN Y APRENDIZAJES SIGNIFICATIVOS**

- **Complejidad Extrema:** Diseñar un sistema de votación online seguro es uno de los mayores desafíos en ciberseguridad. Involucra equilibrar seguridad, usabilidad, anonimato y verificabilidad.
- **Defensa en Profundidad:** Ninguna medida de seguridad es infalible por sí sola. La arquitectura propuesta utiliza múltiples capas de defensa (MFA, cifrado, desvinculación, auditoría, etc.).
- **Confianza Cero (Zero Trust):** Los componentes del sistema no confían implícitamente entre sí. Por ejemplo, el servicio de conteo no confía en que los votos no hayan sido manipulados previamente, por eso se usan firmas o cifrado.
- **El Factor Humano:** Incluso el sistema tecnológicamente más seguro puede ser comprometido por errores humanos, ingeniería social o amenazas internas. La capacitación y los procesos son cruciales.
  - *Ejemplo:* Un administrador con privilegios excesivos o cuyas credenciales son robadas.
- **Transparencia vs. Anonimato:** Existe una tensión inherente. ¿Cómo hacer el proceso verificable sin revelar quién votó por quién? Las técnicas criptográficas avanzadas (como las pruebas de conocimiento cero o homomorphic encryption - aunque esta última más para el conteo sin descifrar) son áreas de investigación activa para esto.
- **No es solo tecnología:** La seguridad de un sistema de votación también depende de procesos físicos seguros (protección de servidores), personal confiable y auditorías independientes.

### **Conclusión de la Práctica:**

Esta práctica resalta que el S-SDLC, especialmente en sistemas sensibles:

- Comienza mucho antes de la primera línea de código, con un análisis riguroso de riesgos.
- Requiere un diseño arquitectónico que integre la seguridad en su núcleo.
- El modelado de amenazas es una herramienta indispensable para anticipar y mitigar problemas.

La seguridad perfecta es un ideal, pero un diseño robusto y un proceso de mejora continua nos acercan a sistemas más resilientes y confiables. ¡Sigue cuestionando, analizando y diseñando con la seguridad en mente! 🔎