# **Práctica:**        🛡️      Construyendo un Mini-Gestor de Tareas Seguro 🚀

# **Introducción:**

¡Hola, e esta práctica, vamos a embarcarnos en un viaje emocionante: tomaremos una idea simple para una aplicación, definiremos sus requerimientos de seguridad, diseñaremos una arquitectura básica pensando en la protección y, finalmente, ¡crearemos un pequeño prototipo funcional! 💻

El objetivo NO es construir una aplicación lista para producción, sino **experimentar el proceso de pensar en la seguridad desde el inicio** y ver cómo esos pensamientos se traducen en decisiones de diseño y código.

### **Escenario de la Aplicación:**

Vamos a diseñar un "Mini-Gestor de Tareas Personales" muy simple.

- **Funcionalidad Básica:**

  1. Los usuarios pueden ver una lista de sus tareas.

  2. Los usuarios pueden añadir nuevas tareas a su lista.

  3. Los usuarios pueden marcar tareas como completadas.

  4. Los usuarios pueden eliminar tareas.

     

- **Suposición Simplificadora (para fines educativos):** 

  Inicialmente, asumiremos que es para un solo usuario en su navegador (no hay cuentas de usuario complejas ni base de datos real en este prototipo, usaremos localStorage del navegador). Esto nos permite enfocarnos en ciertos aspectos de seguridad sin la complejidad de un backend completo.

### **PASO 1: 🧐 IDENTIFICACIÓN DE REQUERIMIENTOS SEGUROS**

Antes de escribir una sola línea de código, pensemos en qué podría salir mal y qué necesitamos proteger.

1. **Activo Principal:** La lista de tareas del usuario.

   - 💭 **Pensamiento:** Las tareas pueden contener información personal o sensible. No queremos que otros vean o modifiquen nuestras tareas.

2. **Amenazas Potenciales (usando un mini-STRIDE simplificado):**

   - **(S) Spoofing (Suplantación):**

     - *Amenaza:* Si esto fuera multiusuario, alguien podría intentar actuar como otro usuario.

     - *Mitigación (conceptual, ya que es mono-usuario local):* Autenticación fuerte. Para nuestro prototipo, la "sesión" está implícita en el navegador del usuario.

       

   - **(T) Tampering (Manipulación):**

     - *Amenaza:* Alguien (o un script malicioso en otra pestaña/extensión) podría intentar modificar las tareas almacenadas en el navegador sin nuestro consentimiento. Un usuario podría intentar inyectar scripts maliciosos a través del campo de entrada de la tarea.

     - *Mitigación:* Validar y sanitizar todas las entradas. Usar mecanismos de almacenamiento seguros si aplica (aunque localStorage tiene sus limitaciones).

       

   - **(R) Repudiation (Repudio):**

     - *Amenaza:* Menos relevante en este contexto mono-usuario simple, pero si hubiera acciones críticas (ej. borrar todas las tareas), querríamos confirmación.

     - *Mitigación (conceptual):* Logs de auditoría (en sistemas más grandes), confirmaciones de acciones destructivas.

       

   - **(I) Information Disclosure (Revelación de Información):**

     - *Amenaza:* Que las tareas se filtren o sean visibles para quien no debe. Si el usuario comparte su ordenador y no cierra el navegador, otro podría verlas. Un script malicioso podría intentar leer el localStorage.

     - *Mitigación:* Aunque localStorage es específico del dominio, la protección contra XSS es crucial. En un sistema real, cifrado en reposo si las tareas son muy sensibles.

       

   - **(D) Denial of Service (Denegación de Servicio):**

     - *Amenaza:* Alguien podría intentar añadir tantas tareas o tareas tan largas que el navegador se ralentice o la aplicación deje de funcionar.

     - *Mitigación:* Límites en la cantidad de tareas, longitud del texto de la tarea, validación de entradas.

       

   - **(E) Elevation of Privilege (Elevación de Privilegios):**

     - *Amenaza:* No aplica directamente en este modelo mono-usuario simple sin roles. En un sistema con roles (admin, usuario), sería un riesgo que un usuario normal obtenga permisos de admin.

3. **Definiendo Requerimientos de Seguridad Específicos (RS) para nuestro Prototipo:**

   - **RS1 (Prevención de XSS):** La aplicación DEBE sanitizar todas las entradas del usuario antes de mostrarlas en la página o almacenarlas, para prevenir ataques de Cross-Site Scripting (XSS).

     - *Razón:* Evitar que un atacante inyecte código JavaScript malicioso a través del campo de texto de la tarea.

       

   - **RS2 (Validación de Entrada - Longitud):** La aplicación DEBE limitar la longitud del texto de una tarea a un máximo razonable (ej. 255 caracteres).

     - *Razón:* Prevenir DoS simple por sobrecarga de datos y asegurar la integridad de la interfaz.

       

   - **RS3 (Confirmación de Eliminación):** La aplicación DEBE solicitar confirmación al usuario antes de eliminar una tarea.

     - *Razón:* Prevenir eliminación accidental (un tipo de "integridad de datos" y usabilidad).

       

   - **RS4 (No Confiar en el Cliente para Lógica Crítica):** Aunque la lógica reside en el cliente en este prototipo, se DEBE ser consciente de que un usuario avanzado podría manipular el JavaScript. En una aplicación real cliente-servidor, la validación crítica SIEMPRE se realiza en el servidor.

     - *Razón:* Principio de diseño fundamental.

       

   - **RS5 (Uso de HTTPS - Conceptual para Prototipo):** En un despliegue real, la aplicación DEBE ser servida sobre HTTPS para proteger los datos en tránsito (incluso si son "solo" tareas).

     - *Razón:* Prevenir sniffing y manipulación de datos entre el cliente y el servidor. (No implementable directamente en localStorage local, pero es un requerimiento de arquitectura vital).

### **PASO 2: 🏗️ DISEÑO DE ARQUITECTURA SEGURA (SIMPLIFICADA)**

Para nuestro prototipo, la "arquitectura" es muy simple, basada en el navegador.

- **Componentes:**

  1. **Interfaz de Usuario (UI) - index.html:** Presenta la lista de tareas y los controles (añadir, completar, eliminar).

  2. **Lógica de la Aplicación - app.js:** Maneja las interacciones del usuario, la validación, la sanitización y la persistencia de datos.

  3. **Almacenamiento Local - localStorage del Navegador:** Guarda las tareas del usuario.

     

- **Flujo de Datos y Puntos de Control de Seguridad:**

  ```css
  Usuario --(1. Introduce Tarea)--> [Input Field (HTML)]
      |
      --(2. Evento 'Submit')--> [app.js: funcion agregarTarea()]
                                  |
                                  --(3. VALIDACIÓN: Longitud (RS2))--> ¿Válido?
                                  |      | (Sí)
                                  |      --(4. SANITIZACIÓN: Anti-XSS (RS1))--> Tarea Sanitizada
                                  |            |
                                  |            --(5. Almacenar)--> [localStorage]
                                  |            |
                                  |            --(6. Actualizar UI)--> [Mostrar Tarea Sanitizada en Lista (HTML)]
                                  |
                                  --(Si No es Válido en 3)--> [Mostrar Error al Usuario]
  
  Usuario --(7. Clic 'Eliminar')--> [app.js: funcion eliminarTarea()]
                                  |
                                  --(8. CONFIRMACIÓN (RS3))--> ¿Confirmado?
                                        | (Sí)
                                        --(9. Eliminar de localStorage)
                                        |
                                        --(10. Actualizar UI)
  ```

  **Decisiones de Diseño basadas en Requerimientos de Seguridad:**

  - **RS1 (Anti-XSS):** La función que añade tareas a la lista en el DOM *nunca* usará innerHTML con datos del usuario directamente. En su lugar, creará elementos DOM mediante document.createElement() y asignará el contenido del usuario a textContent, que automáticamente escapa caracteres HTML.

    

  - **RS2 (Validación Longitud):** Antes de procesar una nueva tarea, se verificará su longitud en JavaScript.

    

  - **RS3 (Confirmación Eliminación):** Se usará window.confirm() antes de eliminar una tarea.

### **PASO 3: 🛠️ CREACIÓN DEL PROTOTIPO (HTML5 y JavaScript)**

Aquí está el código. Crea dos archivos: index.html y app.js en la misma carpeta.

**index.html:**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🛡️ Mini Gestor de Tareas Seguro</title>
    <style>
        body { font-family: sans-serif; margin: 20px; background-color: #f4f4f4; }
        .container { background-color: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        h1 { color: #333; text-align: center; }
        input[type="text"] { width: calc(100% - 80px); padding: 10px; margin-right: 10px; border: 1px solid #ddd; border-radius: 4px; }
        button { padding: 10px 15px; background-color: #5cb85c; color: white; border: none; border-radius: 4px; cursor: pointer; }
        button:hover { background-color: #4cae4c; }
        ul { list-style-type: none; padding: 0; }
        li { background-color: #eee; margin-bottom: 8px; padding: 10px; border-radius: 4px; display: flex; justify-content: space-between; align-items: center; }
        li.completed span { text-decoration: line-through; color: #777; }
        .task-text { flex-grow: 1; }
        .delete-btn { background-color: #d9534f; margin-left: 5px; }
        .delete-btn:hover { background-color: #c9302c; }
        .complete-btn { background-color: #337ab7; }
        .complete-btn:hover { background-color: #286090; }
        .error-message { color: red; font-size: 0.9em; margin-top: 5px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>📝 Mini Gestor de Tareas Seguro</h1>
        
        <form id="task-form">
            <input type="text" id="task-input" placeholder="Añadir nueva tarea...">
            <button type="submit">Añadir</button>
        </form>
        <p id="error-message" class="error-message"></p>

        <ul id="task-list">
            <!-- Las tareas se añadirán aquí mediante JavaScript -->
        </ul>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

**app.js:**

```css
document.addEventListener('DOMContentLoaded', () => {
    const taskForm = document.getElementById('task-form');
    const taskInput = document.getElementById('task-input');
    const taskList = document.getElementById('task-list');
    const errorMessageElement = document.getElementById('error-message');

    const MAX_TASK_LENGTH = 255; // RS2: Límite de longitud

    // Cargar tareas desde localStorage al iniciar
    loadTasks();

    taskForm.addEventListener('submit', function(event) {
        event.preventDefault(); // Evitar que el formulario se envíe de la manera tradicional
        const taskText = taskInput.value.trim();
        errorMessageElement.textContent = ''; // Limpiar mensajes de error previos

        // RS2: Validación de longitud de la tarea
        if (taskText.length === 0) {
            errorMessageElement.textContent = '⚠️ La tarea no puede estar vacía.';
            return;
        }
        if (taskText.length > MAX_TASK_LENGTH) {
            errorMessageElement.textContent = `⚠️ La tarea es muy larga (máx ${MAX_TASK_LENGTH} caracteres).`;
            return;
        }

        addTask(taskText);
        taskInput.value = ''; // Limpiar el campo de entrada
        saveTasks();
    });

    function addTask(taskText, isCompleted = false, fromLoad = false) {
        const listItem = document.createElement('li');
        if (isCompleted) {
            listItem.classList.add('completed');
        }

        // RS1: Prevención de XSS usando textContent en lugar de innerHTML
        const taskSpan = document.createElement('span');
        taskSpan.classList.add('task-text');
        taskSpan.textContent = taskText; // ¡IMPORTANTE PARA PREVENIR XSS!
        listItem.appendChild(taskSpan);

        const completeButton = document.createElement('button');
        completeButton.textContent = isCompleted ? 'Deshacer' : 'Completar ✅';
        completeButton.classList.add('complete-btn');
        completeButton.addEventListener('click', () => {
            listItem.classList.toggle('completed');
            completeButton.textContent = listItem.classList.contains('completed') ? 'Deshacer' : 'Completar ✅';
            saveTasks();
        });

        const deleteButton = document.createElement('button');
        deleteButton.textContent = 'Eliminar 🗑️';
        deleteButton.classList.add('delete-btn');
        deleteButton.addEventListener('click', () => {
            // RS3: Confirmación antes de eliminar
            if (confirm('❓ ¿Estás seguro de que quieres eliminar esta tarea?')) {
                taskList.removeChild(listItem);
                saveTasks();
            }
        });
        
        const buttonDiv = document.createElement('div');
        buttonDiv.appendChild(completeButton);
        buttonDiv.appendChild(deleteButton);
        listItem.appendChild(buttonDiv);
        
        if (fromLoad) {
            taskList.appendChild(listItem); // Si carga, simplemente añade
        } else {
            taskList.insertBefore(listItem, taskList.firstChild); // Añade nuevas tareas al principio
        }
    }

    function saveTasks() {
        const tasks = [];
        document.querySelectorAll('#task-list li').forEach(listItem => {
            tasks.push({
                text: listItem.querySelector('.task-text').textContent, // Se guarda el texto ya sanitizado
                completed: listItem.classList.contains('completed')
            });
        });
        localStorage.setItem('tasks', JSON.stringify(tasks));
        // RS4 (Conceptual): En una app real, esto sería una llamada a un API seguro.
        //                 La validación de 'tasks' debería ocurrir en el servidor.
    }

    function loadTasks() {
        const storedTasks = localStorage.getItem('tasks');
        if (storedTasks) {
            const tasks = JSON.parse(storedTasks);
            tasks.forEach(task => addTask(task.text, task.completed, true));
        }
    }

    // RS5 (Conceptual): Recordar que para una app real, servir sobre HTTPS es crucial.
    console.log("Prototipo cargado. Recuerda: ¡HTTPS en producción! 🔒");
});
```

#### **Para Probar el Prototipo:**

1. Guarda ambos archivos (index.html y app.js) en la misma carpeta.
2. Abre index.html en tu navegador web (Chrome, Firefox, Edge, etc.).

#### **Pruebas de Seguridad (¡Intenta Romperlo!):**

- **Prueba RS1 (XSS):**

  - Intenta añadir una tarea con el siguiente texto: <script>alert('XSS Exploit!')</script>

  - Intenta añadir: <img src="invalid" onerror="alert('XSS por imagen!')">

  - **Resultado Esperado:** El texto se mostrará tal cual, sin ejecutar el script. Verás <script>alert(...)</script> como texto literal en tu lista de tareas. Esto es gracias a textContent. Si hubiéramos usado innerHTML directamente con taskInput.value, ¡el alert se ejecutaría! 😱

    

- **Prueba RS2 (Validación de Longitud):**

  - Intenta añadir una tarea muy larga (más de 255 caracteres).

  - **Resultado Esperado:** Deberías ver un mensaje de error indicando que la tarea es muy larga.

  - Intenta añadir una tarea vacía.

  - **Resultado Esperado:** Mensaje de error.

    

- **Prueba RS3 (Confirmación de Eliminación):**

  - Añade una tarea y luego intenta eliminarla.

  - **Resultado Esperado:** Debería aparecer un cuadro de diálogo pidiendo confirmación.

    

- **Persistencia:** Añade algunas tareas, ciérra la pestaña del navegador y vuelve a abrir index.html.

  - **Resultado Esperado:** Tus tareas deberían seguir ahí, cargadas desde localStorage.

### **PASO 4: 🧠 REFLEXIÓN Y APRENDIZAJES SIGNIFICATIVOS**

- **Conexión Directa:** ¿Ves cómo los requerimientos de seguridad (RS1, RS2, RS3) se tradujeron directamente en código y comportamiento específico?

  - textContent en lugar de innerHTML (para RS1).

  - Comprobación taskText.length (para RS2).

  - Uso de window.confirm() (para RS3).

    

- **"Security by Design":** Aunque simple, pensamos en la seguridad *antes* y *durante* la codificación, no como una idea de último momento.

  

- **Limitaciones del Prototipo:**

  - **Solo Cliente:** Toda la lógica está en el navegador. Un usuario avanzado podría abrir las herramientas de desarrollador y manipular localStorage directamente o modificar el JavaScript en tiempo de ejecución. Esto refuerza **RS4**: en una aplicación real cliente-servidor, la validación y la lógica de negocio crítica DEBEN estar en el servidor. El cliente es inherentemente no confiable.

    

  - **localStorage:** Es susceptible a XSS si otra parte de la página (no nuestro código de tareas) es vulnerable y permite a un atacante ejecutar JavaScript en el mismo origen.

    

- **Siguientes Pasos (en una aplicación real):**

  - Backend seguro con autenticación de usuarios.
  - Base de datos para almacenar tareas.
  - API RESTful segura para comunicar cliente y servidor.
  - Validaciones del lado del servidor (¡crucial!).
  - Mecanismos de control de acceso (que un usuario solo vea sus tareas).
  - Pruebas de seguridad más exhaustivas (SAST, DAST).
  - Despliegue sobre HTTPS (RS5).

### **Conclusión de la Práctica:**

¡Felicidades! 🎉 Has completado un ciclo S-SDLC en miniatura. Has identificado activos, pensado en amenazas, definido requerimientos de seguridad, tomado decisiones de diseño basadas en ellos y creado un prototipo funcional que implementa esas defensas básicas.

Este ejercicio demuestra que integrar la seguridad desde el principio no tiene por qué ser abrumador. Se trata de hacer las preguntas correctas en el momento adecuado y construir con una mentalidad de resiliencia. ¡Sigue practicando y aprendiendo! 💪

