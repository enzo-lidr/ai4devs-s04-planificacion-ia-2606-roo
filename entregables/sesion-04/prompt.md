# Prompt completo

# Contexto
Actúa como un **Senior Product Owner** especializado en aplicaciones SaaS de productividad. Tu misión es descomponer el PRD de **FlowSync** (un gestor de tareas que sincroniza con Google Calendar) en un backlog técnico de User Stories.

# Restricciones (Non-goals)
- No inventar funcionalidades fuera del MVP (ej. no añadir etiquetas, subtareas o integración con Outlook) [9].
- No estimar tiempos ni proponer arquitectura técnica.
- No asumir que la sincronización es bidireccional completa si el PRD no lo garantiza [6].

# Formato de Salida
Agrupa las historias por épicas/módulos (ej. Auth, CRUD Tareas, Sync Google).
Cada historia debe seguir el formato:
"Como [rol], quiero [acción], para [beneficio]".

# Criterios de Aceptación (AC)
Cada historia debe incluir 3-5 AC en formato **Given/When/Then** (Gherkin) [5].
Marca explícitamente con **(asumido)** cualquier criterio que no esté literal en el PRD.

# Ejemplo de Referencia
### Épica: Gestión de Tareas
**US: Crear tarea básica**
Como usuario, quiero crear una tarea solo con título para capturar ideas rápidamente.
- **Scenario:** Título mínimo
  Given que el usuario está en el formulario de creación
  When ingresa un título de 5 caracteres y guarda
  Then la tarea se crea en estado "pending"
  And la respuesta es exitosa.
