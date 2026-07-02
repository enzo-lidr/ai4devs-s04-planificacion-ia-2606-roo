# prompt.md — Descomposición del PRD de FlowSync en user stories

---

## ROL

Actúa como **Product Owner senior con 10+ años descomponiendo PRDs de productos SaaS** en
backlogs accionables. Conoces INVEST, escribes criterios de aceptación en Given/When/Then
(Gherkin) y sabes que una IA completara el contexto que falta con sus propias ideas, así que, eres
explícito y marcas lo que asumes.

## CONTEXTO

FlowSync es una **aplicación web de gestión de tareas personales** que mantiene las tareas del
usuario sincronizadas con su Google Calendar. El problema: las personas gestionan pendientes en
una herramienta y su tiempo en otra, y alinearlas a mano es tedioso. FlowSync hace que las
tareas con fecha aparezcan como eventos de calendario, eliminando la doble gestión.

- **Usuario objetivo**: Profesional del conocimiento (knowledge worker) entre 25 y 45 años que ya usa Google Calendar a diario y
  gestiona sus tareas pendientes en una herramienta separada. Tiene entre 5 y 30 tareas activas.
- **Producto**: web responsive (sin app nativa), estrictamente individual en el MVP.
- El PRD completo está pegado al final de este prompt. **Trabaja solo sobre él.**

## TAREA

Extrae las **user stories del MVP** a partir de la sección de requisitos funcionales del PRD.
Cada story debe ser implementable por un equipo en 1-2 días (granularidad de story, no de épica).

## REQUISITOS DE SALIDA (obligatorios)

1. **Formato de story**: `Como [rol], quiero [acción], para [beneficio]`.
2. **Alcance acotado al MVP**: usa solo lo que está en la sección "Qué incluye el MVP" y en los
   requisitos funcionales (Punto 3 del PRD). **No inventes features.** Lo marcado como out-of-scope en el
   PRD **no** genera stories.
3. **Agrupación por épica**: agrupa las stories por módulo funcional del PRD (Autenticación,
   Gestión de tareas, Organización y filtrado, Exportación, Sincronización con Google Calendar).
4. **AC en Given/When/Then**: cada story con **3-5 criterios de aceptación verificables y
   concretos** (con valores, estados y códigos cuando aplique), nunca genéricos del tipo
   "debe funcionar". Deben ser directamente traducibles a un test.
5. **Trazabilidad**: cada story cita la subsección del PRD de la que proviene (p. ej. `[PRD 3.2]`).

## RESTRICCIONES (non-goals del ejercicio)

- **No inventes** funcionalidades, campos ni reglas que no estén literalmente en el PRD.
- **No estimes** tiempos, puntos de historia ni esfuerzo.
- **No propongas arquitectura, stack ni diseño de base de datos** (eso vive en las specs, no aquí).
- **No** escribas tasks técnicas; quédate en el nivel de story + AC.
- **Marca con `(asumido)`** cualquier detalle que infieras y que **no esté literal en el PRD**
  (p. ej. expiración de tokens, hora exacta de un evento de calendario, formato CSV concreto).

## FORMATO EXACTO DEL OUTPUT (sigue esta plantilla por cada story)

```
## ÉPICA X — <nombre>

### US-XX — <título corto>
**Como** <rol>, **quiero** <acción>, **para** <beneficio>.  [PRD x.x]

**Criterios de aceptación**
- **Escenario: <nombre>**
  - Given <contexto>
  - When <acción>
  - Then <resultado verificable>
  - And <resultado adicional>
```

### Ejemplo de output esperado (escrito a mano, sigue exactamente este nivel de detalle)

```
## ÉPICA C — Organización y filtrado

### US-10 — Filtrar tareas por estado
**Como** usuario con varias tareas, **quiero** filtrar por estado, **para** ver solo lo
pendiente de hoy.  [PRD 3.3]

**Criterios de aceptación**
- **Escenario: filtro estándar por estado pendiente**
  - Given el usuario tiene 12 tareas (5 pending, 7 completed)
  - When solicita el listado filtrado por status="pending"
  - Then la respuesta es 200 OK
  - And contiene exactamente 5 tareas, todas con status="pending"
- **Escenario: filtro con valor inválido**
  - Given el usuario filtra por status="banana"
  - When la petición llega al backend
  - Then responde 400 Bad Request
  - And el mensaje incluye los valores válidos: "pending" | "completed" | "archived"
```

## INSTRUCCIÓN FINAL DE TRANSPARENCIA

Genera **entre 10 y 14 stories** que cubran todo el MVP. Si para escribir un AC necesitas un
dato que el PRD no fija (umbrales, orden por defecto, hora del evento, columnas exactas del CSV),
**no lo inventes**: escribe tu mejor supuesto razonable y etiquétalo con `(asumido)`.
Al final, añade una sección **"Supuestos y preguntas abiertas"** listando lo que marcaste como
asumido y lo que recomendarías validar con Producto antes de entrar a sprint.

---

A continuación se indica el PRD de FlowSync MVP:

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
