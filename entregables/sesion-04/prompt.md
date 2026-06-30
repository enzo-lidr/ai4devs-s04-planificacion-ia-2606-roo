## ROL
Sos un Product Owner senior con más de 8 años de experiencia en productos SaaS B2C.
Tu especialidad es traducir documentos de producto (PRDs) en backlogs accionables para
equipos ágiles. Escribís user stories precisas, con criterios de aceptación verificables
que el equipo de QA puede convertir directamente en casos de prueba.

## CONTEXTO DEL PRODUCTO
FlowSync es una aplicación web de gestión de tareas personales cuya propuesta de valor
central es la sincronización bidireccional con Google Calendar. El usuario objetivo es un
knowledge worker de 25–45 años que ya usa Google Calendar a diario y gestiona sus
pendientes en una herramienta separada. FlowSync elimina la doble gestión manual.

El MVP incluye exactamente cinco módulos:
  1. Autenticación y gestión de cuenta
  2. Gestión de tareas (CRUD)
  3. Organización y filtrado
  4. Exportación
  5. Sincronización con Google Calendar

A continuación encontrás el PRD completo del MVP. Tu trabajo es extraer las user stories
de este documento.

# PRD — FlowSync MVP

> **Producto**: FlowSync
> **Versión del documento**: 1.0 · Q3 2026
> **Estado**: aprobado para MVP
> **Owner de producto**: equipo FlowSync
> **Audiencia de este documento**: equipo de ingeniería (backend, frontend), QA, y cualquier agente de IA que vaya a decomponer este PRD en backlog.

---

## 1. Resumen ejecutivo

FlowSync es una aplicación web de gestión de tareas personales que **mantiene sincronizadas las tareas del usuario con su Google Calendar**. El problema que resuelve: las personas gestionan sus pendientes en una herramienta (listas de tareas) y su tiempo en otra (calendario), y mantener ambas alineadas a mano es tedioso y propenso a errores. FlowSync elimina ese trabajo manual: las tareas con fecha aparecen como eventos en el calendario, y los cambios fluyen en ambas direcciones.

El MVP busca validar una hipótesis central: **¿la gente con sobrecarga de herramientas adoptará un gestor de tareas si elimina la doble gestión tarea/calendario?**

### Qué incluye el MVP
- Registro y autenticación de usuarios.
- CRUD completo de tareas con estados.
- Filtrado y organización básica de tareas.
- Exportación de tareas.
- Sincronización con Google Calendar (lectura y escritura).

### Qué NO incluye el MVP (out of scope explícito)
- Equipos o tareas compartidas (FlowSync MVP es estrictamente individual).
- Sincronización con calendarios que no sean Google (Outlook, iCal: post-MVP).
- Notificaciones push o por email.
- Aplicación móvil nativa (solo web responsive).
- Etiquetas, proyectos, o jerarquías de tareas (subtareas): post-MVP.
- Recordatorios configurables más allá de los del propio Google Calendar.

---

## 2. Usuario objetivo

**Perfil primario**: profesional del conocimiento (knowledge worker) entre 25 y 45 años, que ya usa Google Calendar a diario para su trabajo y que actualmente gestiona sus pendientes en una herramienta separada (Todoist, Notion, una libreta, o post-its). Tiene entre 5 y 30 tareas activas en un momento dado. Valora la simplicidad y se frustra con herramientas que requieren mantenimiento manual.

**Job to be done**: *"Cuando tengo una tarea con fecha límite, quiero que aparezca en mi calendario sin que yo tenga que copiarla a mano, para no tener que mirar dos sitios distintos para saber qué tengo que hacer hoy."*

---

## 3. Requisitos funcionales

### 3.1 — Autenticación y gestión de cuenta

FlowSync requiere que cada usuario tenga una cuenta propia. Los datos de un usuario nunca son visibles para otro.

- Un visitante puede **crear una cuenta** con email y contraseña. La contraseña debe tener al menos 8 caracteres.
- Si el email ya está registrado, el sistema lo indica y ofrece ir al inicio de sesión.
- Un usuario registrado puede **iniciar sesión** con su email y contraseña.
- Un usuario autenticado puede **cerrar sesión**.
- Tras el registro exitoso, el usuario llega a una pantalla de bienvenida (onboarding mínimo) que explica en una frase qué hace FlowSync y le invita a crear su primera tarea.
- La sesión se mantiene mediante tokens de acceso. No se requiere "recordar contraseña" ni verificación de email por enlace en el MVP.

### 3.2 — Gestión de tareas (CRUD)

El núcleo de FlowSync. Una tarea tiene, como mínimo: título, descripción opcional, estado, y fecha límite opcional.

- Un usuario puede **crear una tarea** indicando al menos un título. La descripción y la fecha límite son opcionales.
- Un usuario puede **ver el listado** de sus tareas.
- Un usuario puede **editar** cualquier campo de una tarea existente.
- Un usuario puede **borrar** una tarea.
- Cada tarea tiene un **estado**: `pending` (pendiente), `completed` (completada) o `archived` (archivada). Una tarea recién creada nace en `pending`.
- Un usuario puede **cambiar el estado** de una tarea (por ejemplo, marcarla como completada).

### 3.3 — Organización y filtrado

Con varias tareas, el usuario necesita encontrarlas y agruparlas.

- Un usuario puede **filtrar sus tareas por estado** (ver solo pendientes, solo completadas, etc.).
- El listado de tareas se muestra, por defecto, ordenado de forma que las más relevantes para "hoy" sean visibles primero. *(El criterio exacto de ordenación queda a decisión del equipo durante el refinamiento.)*
- Cuando un usuario no tiene ninguna tarea (cuenta nueva o todas archivadas), ve un estado vacío con una invitación a crear la primera.

### 3.4 — Exportación

Los usuarios quieren poder llevarse sus datos.

- Un usuario puede **exportar sus tareas a un archivo CSV** que incluya, como mínimo, título, descripción, estado y fecha límite de cada tarea.

### 3.5 — Sincronización con Google Calendar

La funcionalidad diferenciadora de FlowSync. Es también la más compleja y arriesgada del MVP.

- Un usuario puede **conectar su cuenta de Google** a FlowSync (autorización OAuth con Google).
- Una vez conectado, las **tareas con fecha límite aparecen como eventos** en el Google Calendar del usuario.
- Si el usuario **cambia la fecha de una tarea** en FlowSync, el evento correspondiente en Google Calendar se actualiza.
- Si el usuario **completa o borra una tarea** en FlowSync, el evento correspondiente en Google Calendar se elimina o se marca según corresponda.
- El usuario puede **desconectar** su cuenta de Google en cualquier momento; al hacerlo, FlowSync deja de sincronizar pero no borra las tareas ya creadas.
- La sincronización debe manejar de forma razonable los casos en que la API de Google no esté disponible o devuelva errores (la tarea se guarda en FlowSync aunque la sincronización falle, y se reintenta más tarde).

> **Nota de producto**: la dirección de sincronización del MVP es primariamente **FlowSync → Google Calendar** (las tareas crean/actualizan eventos). La sincronización inversa completa (editar un evento en Google y que se refleje como cambio en la tarea) es deseable pero su alcance exacto debe validarse con un spike técnico antes de comprometerla, dado el coste de gestionar conflictos y el polling/webhooks de la API de Google.

---

## 4. Requisitos no funcionales

- **Privacidad**: los datos de un usuario (tareas, tokens de Google) nunca son accesibles por otros usuarios. Los tokens de Google se almacenan de forma segura.
- **Rendimiento**: el listado de tareas debe cargar en menos de 1 segundo para un usuario con hasta 200 tareas.
- **Responsive**: la interfaz funciona en navegadores de escritorio y móvil (no hay app nativa, pero la web es usable en móvil).
- **Errores claros**: cualquier error de validación (email inválido, contraseña corta, campos obligatorios) se muestra al usuario de forma comprensible, no como un error técnico.
- **Observabilidad**: las operaciones de sincronización con Google se registran (logs) para poder diagnosticar fallos.

---

## 5. Stack técnico (contexto para el equipo)

> Esta sección es informativa. El PRD no prescribe arquitectura; el "cómo" se define en las specs (OpenSpec) durante el desarrollo. Pero el equipo trabaja sobre un stack fijado:

- **Backend**: AdonisJS 7 + TypeScript + Lucid ORM + SQLite (vía `better-sqlite3`) + VineJS para validación + `@adonisjs/auth` (access tokens).
- **Frontend**: React 19 + TypeScript + Vite + React Router + Tailwind v4 + shadcn/ui.
- **Integración externa**: Google Calendar API (OAuth 2.0).

---

## 6. Criterios de éxito del MVP

El MVP se considera exitoso si, tras 4 semanas con usuarios reales:

- Un usuario puede completar el flujo entero — registrarse, crear tareas, conectar Google Calendar y ver sus tareas reflejadas como eventos — sin ayuda externa.
- Al menos el 40% de los usuarios que se registran conectan su Google Calendar (señal de que la propuesta de valor diferenciadora se entiende y se desea).
- La sincronización funciona de forma fiable: menos del 5% de las operaciones de sincronización fallan de forma no recuperable.

---

## 7. Riesgos conocidos

- **La sincronización con Google Calendar es el mayor riesgo técnico del MVP.** La API tiene rate limits, manejo de zonas horarias complejo, y casos límite (eventos recurrentes, eventos de todo el día) que pueden inflar el alcance. Se recomienda un spike técnico antes de comprometer su decomposición en tareas.
- **OAuth con Google** requiere configuración de proyecto en Google Cloud Console y revisión de permisos; el setup inicial puede llevar más tiempo del esperado.
- **Zonas horarias**: la relación entre la fecha límite de una tarea y la hora de un evento de calendario debe definirse con cuidado para no crear eventos a horas erróneas.

---

## 8. Glosario

- **Tarea**: unidad de trabajo del usuario. Tiene título, descripción opcional, estado y fecha límite opcional.
- **Estado**: situación de una tarea — `pending`, `completed` o `archived`.
- **Evento**: entrada en Google Calendar generada a partir de una tarea con fecha límite.
- **Sincronización**: el proceso por el que las tareas de FlowSync se reflejan como eventos en Google Calendar.
- **Conexión de Google**: la autorización OAuth que permite a FlowSync leer y escribir en el calendario del usuario.

## TU TAREA
Extraé todas las user stories del MVP descripto en el PRD y organizalas en el backlog
según las instrucciones de estructura, formato y restricciones que siguen.

## ESTRUCTURA DEL OUTPUT
Organizá las stories agrupadas por módulo/épica, en el mismo orden en que aparecen en
el PRD (secciones 3.1 a 3.5). Para cada módulo:

  ### ÉPICA: [Nombre del módulo]
  #### US-[N]: [Título corto de la story]
  [story en formato estándar]
  [criterios de aceptación]

Numerá las stories de forma correlativa global: US-01, US-02, … sin reiniciar el contador
entre épicas.

## FORMATO DE CADA USER STORY

**User story:**
Como [rol de usuario], quiero [acción concreta], para [beneficio o resultado esperado].

**Criterios de aceptación** (mínimo 3, máximo 5 por story; formato Given/When/Then):
- **AC1 — [Nombre descriptivo]:**
  - Given [precondición concreta]
  - When [acción del usuario o del sistema]
  - Then [resultado verificable y específico]

Reglas de formato:
- El [rol] debe ser específico al contexto: "usuario no autenticado", "usuario autenticado",
  "usuario con cuenta de Google conectada", etc. Nunca usar solo "usuario".
- El [beneficio] debe expresar el valor real para el usuario, no repetir la acción.
- Cada AC debe ser verificable por QA de forma independiente: sin palabras como "correctamente",
  "apropiadamente" o "de manera adecuada" sin especificar qué significa eso.
- Los AC deben ser específicos al contexto de FlowSync, no genéricos.

## RESTRICCIONES ESTRICTAS (NON-GOALS)
❌ No inventés features que no estén en el PRD. Si una funcionalidad no aparece en las
   secciones 1 a 3.5, no la incluyas.
❌ No incluyás funcionalidades marcadas como "out of scope" o "post-MVP" en el PRD.
❌ No estimés tiempos ni puntos de historia.
❌ No propongas arquitectura técnica ni decisiones de implementación.
❌ No uses el stack técnico de la sección 5 como fuente de requisitos funcionales.
❌ No agregués stories de requisitos no funcionales (performance, seguridad, logging);
   esos van a un backlog técnico separado.

## EJEMPLO DEL OUTPUT ESPERADO
El siguiente ejemplo muestra el formato que esperamos. No lo copies ni lo adaptes:
es solo una referencia de estructura y nivel de detalle.


### ÉPICA: Gestión de tareas (CRUD)

#### US-04: Crear una tarea con título obligatorio

**User story:**
Como usuario autenticado, quiero crear una tarea indicando solo un título, para empezar
a registrar mis pendientes sin tener que completar información que no siempre tengo disponible.

**Criterios de aceptación:**

- **AC1 — Creación exitosa con solo título:**
  - Given que soy un usuario autenticado en la pantalla de creación de tarea
  - When ingreso un título y envío el formulario sin completar descripción ni fecha límite
  - Then la tarea se crea con estado `pending` y aparece en mi listado de tareas

- **AC2 — Título obligatorio:**
  - Given que soy un usuario autenticado y estoy en el formulario de creación de tarea
  - When intento enviar el formulario con el campo título vacío
  - Then el formulario no se envía y se muestra un mensaje de error indicando que el
    título es obligatorio

- **AC3 — Fecha límite y descripción opcionales:**
  - Given que soy un usuario autenticado en la pantalla de creación de tarea
  - When creo una tarea con título, descripción y fecha límite completos
  - Then la tarea se crea y todos los campos ingresados se reflejan correctamente en el detalle

## INSTRUCCIÓN DE TRANSPARENCIA

Cuando un criterio de aceptación incluya un detalle que no está especificado literalmente
en el PRD (por ejemplo, un comportamiento de UI, un mensaje de error concreto, o una
regla de negocio que inferiste), marcalo con la etiqueta `(asumido)` al final del Then
correspondiente, para que el equipo pueda validarlo o ajustarlo durante el refinamiento.

Ejemplo:
  - Then el sistema muestra el mensaje "El email ya está registrado" con un enlace al
    inicio de sesión. `(asumido: texto exacto del mensaje)`

Ahora generá el backlog completo siguiendo todas las instrucciones anteriores.