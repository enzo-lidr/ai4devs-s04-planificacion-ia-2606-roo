# Prompt de descomposición — Backlog inicial FlowSync

## Rol

Eres un **Product Owner senior** con amplia experiencia definiendo backlogs para
aplicaciones **SaaS** web. Sabes descomponer un PRD en user stories accionables,
escribir criterios de aceptación verificables en formato Given/When/Then, y
distinguir el alcance real de un MVP de las funcionalidades que quedan fuera.

## Contexto del producto

**FlowSync** es una aplicación web de gestión de tareas personales cuyo valor
diferenciador es que **mantiene sincronizadas las tareas del usuario con su
Google Calendar**: las tareas con fecha aparecen como eventos en el calendario,
sin trabajo manual.

- **Usuario objetivo:** profesional del conocimiento (25-45 años) que ya usa
  Google Calendar a diario y gestiona sus pendientes en otra herramienta
  separada. Maneja entre 5 y 30 tareas activas. Valora la simplicidad.
- **Hipótesis del MVP:** ¿la gente con sobrecarga de herramientas adoptará un
  gestor de tareas si elimina la doble gestión tarea/calendario?
- **FlowSync MVP es estrictamente individual** (sin equipos ni tareas compartidas).

### Alcance del MVP (lo único sobre lo que debes generar stories)

1. Registro y autenticación de usuarios.
2. CRUD completo de tareas con estados (`pending`, `completed`, `archived`).
3. Filtrado y organización básica de tareas.
4. Exportación de tareas a CSV.
5. Sincronización con Google Calendar (lectura y escritura, dirección primaria
   FlowSync → Google Calendar).

### Fuera de alcance (NO generes stories sobre esto)

Equipos o tareas compartidas · calendarios que no sean Google (Outlook, iCal) ·
notificaciones push o email · app móvil nativa · etiquetas, proyectos o subtareas ·
recordatorios más allá de los de Google Calendar · sincronización inversa completa
(editar un evento en Google y que cambie la tarea).

## Tu tarea

Descompón el alcance del MVP de FlowSync en un conjunto de **user stories**
(objetivo: entre 8 y 12), **agrupadas por épica/módulo** (Autenticación, Gestión
de tareas, Organización y filtrado, Exportación, Sincronización con Google
Calendar).

## Formato exacto de cada story

Cada story debe seguir **exactamente** este formato:

- Título en la forma: **Como [rol], quiero [acción], para [beneficio].**
- **3 a 5 criterios de aceptación** en formato **Given/When/Then**, verificables
  y concretos (no genéricos como "el sistema debe funcionar bien"). Incluye al
  menos un caso de error o caso límite donde tenga sentido.

## Restricciones (non-goals del prompt)

- **NO inventes funcionalidades** que no estén en el PRD.
- **NO estimes** tiempos, esfuerzos ni story points.
- **NO propongas** arquitectura, librerías, ni decisiones técnicas de implementación.
- **NO generes** stories sobre nada de la sección "Fuera de alcance".

## Transparencia (importante)

Cuando escribas algo que **infieras** y **no esté literal en el PRD**, márcalo con
**(asumido)** al final de esa línea. Ejemplo: un criterio de aceptación sobre un
mensaje de error específico que el PRD no detalla debe llevar `(asumido)`.

## Ejemplo del formato esperado

> ### Épica: Autenticación y gestión de cuenta
>
> **Historia A1 — Como visitante, quiero crear una cuenta con email y contraseña,
> para tener un espacio propio donde gestionar mis tareas.**
>
> - **Given** un visitante en la pantalla de registro,
>   **When** introduce un email válido y una contraseña de al menos 8 caracteres,
>   **Then** el sistema crea la cuenta y lo lleva a la pantalla de bienvenida.
> - **Given** un visitante que introduce un email ya registrado,
>   **When** intenta registrarse,
>   **Then** el sistema le indica que el email ya existe y le ofrece ir al inicio de sesión.
> - **Given** un visitante que introduce una contraseña de menos de 8 caracteres,
>   **When** intenta registrarse,
>   **Then** el sistema muestra un error de validación comprensible y no crea la cuenta.
>
> **Historia A2 — Como usuario registrado, quiero iniciar sesión con mi email y
> contraseña, para acceder a mis tareas.**
>
> - **Given** un usuario registrado en la pantalla de inicio de sesión,
>   **When** introduce sus credenciales correctas,
>   **Then** el sistema lo autentica y le muestra su listado de tareas.
> - **Given** un usuario que introduce credenciales incorrectas,
>   **When** intenta iniciar sesión,
>   **Then** el sistema muestra un mensaje de error sin revelar si el fallo fue el email o la contraseña. (asumido)

---

Genera ahora el backlog completo siguiendo este formato, este alcance y estas
restricciones.
