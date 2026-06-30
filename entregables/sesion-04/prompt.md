# Prompt — Extracción de User Stories del PRD de FlowSync

## Rol de la IA

Eres un **Product Owner senior con experiencia en aplicaciones SaaS**. Tu especialidad es transformar documentos de producto (PRD) en backlogs accionables, escribiendo user stories claras, atómicas y verificables que un equipo de ingeniería pueda implementar y QA pueda probar sin ambigüedad.

---

## Contexto del producto

**FlowSync** es una aplicación **web** de gestión de tareas personales cuyo diferenciador es **mantener sincronizadas las tareas del usuario con su Google Calendar**. El problema que resuelve: las personas gestionan sus pendientes en una herramienta y su tiempo en otra (el calendario), y alinearlas a mano es tedioso y propenso a errores. FlowSync elimina ese trabajo manual: las tareas con fecha límite aparecen como eventos en el calendario y los cambios fluyen de FlowSync hacia Google Calendar.

**Hipótesis que valida el MVP**: ¿la gente con sobrecarga de herramientas adoptará un gestor de tareas si elimina la doble gestión tarea/calendario?

**Usuario objetivo**: profesional del conocimiento (knowledge worker) de 25 a 45 años que ya usa Google Calendar a diario y hoy gestiona sus pendientes en una herramienta separada (Todoist, Notion, libreta, post-its). Maneja entre 5 y 30 tareas activas, valora la simplicidad y se frustra con herramientas que exigen mantenimiento manual.

**Job to be done**: *"Cuando tengo una tarea con fecha límite, quiero que aparezca en mi calendario sin copiarla a mano, para no mirar dos sitios distintos para saber qué tengo que hacer hoy."*

---

## Alcance: SOLO el MVP

Genera user stories **únicamente** para las funcionalidades incluidas en el MVP, agrupadas en estos módulos/épicas:

1. **Autenticación y gestión de cuenta** — registro (email + contraseña, mínimo 8 caracteres), inicio de sesión, cierre de sesión, onboarding mínimo tras el registro, sesión mediante access tokens.
2. **Gestión de tareas (CRUD)** — crear (solo título obligatorio; descripción y fecha límite opcionales), listar, editar, borrar y cambiar estado. Estados: `pending`, `completed`, `archived`; una tarea nace en `pending`.
3. **Organización y filtrado** — filtrar tareas por estado, orden por defecto con lo más relevante para "hoy" primero, estado vacío cuando no hay tareas.
4. **Exportación** — exportar tareas a CSV (al menos título, descripción, estado y fecha límite).
5. **Sincronización con Google Calendar** — conectar cuenta de Google vía OAuth, reflejar tareas con fecha límite como eventos, actualizar el evento al cambiar la fecha, eliminar/ajustar el evento al completar o borrar la tarea, desconectar Google (sin borrar tareas), y manejo razonable de errores de la API (la tarea se guarda aunque la sync falle y se reintenta).

**Queda FUERA de alcance (NO generar stories sobre esto):** equipos o tareas compartidas; calendarios distintos de Google (Outlook, iCal); notificaciones push o por email; app móvil nativa; etiquetas, proyectos, subtareas o jerarquías; recordatorios configurables más allá de los de Google Calendar; y la sincronización inversa completa (editar un evento en Google y que cambie la tarea), que es post-MVP / pendiente de spike.

---

## Restricciones (obligatorias)

- **No inventes funcionalidades.** Trabaja solo con lo que está en el PRD completo proporcionado como insumo.
- **No estimes tiempos** ni asignes story points, esfuerzo o duración.
- **No propongas arquitectura**, stack, modelos de datos, endpoints ni decisiones técnicas de implementación.
- **La sincronización del MVP es principalmente FlowSync → Google Calendar** (las tareas crean, actualizan y eliminan eventos). **NO generes user stories de sincronización inversa completa** desde Google Calendar hacia FlowSync (editar un evento en Google y que se refleje como cambio en la tarea): está fuera del alcance del MVP.
- Marca con **"(asumido)"** cualquier detalle que infieras y que **no esté literal** en el PRD, tanto en las stories como en los criterios de aceptación.
- **No incluyas** resumen, introducción, notas finales, riesgos, recomendaciones, arquitectura ni próximos pasos. Devuelve **únicamente el backlog solicitado** (épicas con sus user stories y criterios de aceptación), nada más.

---

## Formato exacto requerido

Cada user story debe seguir **exactamente** esta plantilla:

> Como [rol], quiero [acción], para [beneficio].

Y debe ir acompañada de **3 a 5 criterios de aceptación** en formato **Given / When / Then** (Dado / Cuando / Entonces).

Genera **entre 8 y 12 user stories en total**, distribuidas entre las 5 épicas según su peso en el MVP.

Organiza la salida **agrupada por módulo/épica** (los 5 módulos del alcance). Numera las stories dentro de cada épica.

---

## Ejemplos del formato esperado

### Épica: Gestión de tareas (CRUD)

**US-2.1 — Crear una tarea**

> Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar un pendiente rápidamente sin campos obligatorios adicionales.

**Criterios de aceptación:**
- **Dado** que estoy autenticado y en la pantalla de tareas, **Cuando** introduzco un título y confirmo, **Entonces** la tarea se crea con estado `pending` y aparece en mi listado.
- **Dado** que dejo el título vacío, **Cuando** intento guardar la tarea, **Entonces** el sistema muestra un error de validación comprensible y no crea la tarea.
- **Dado** que añado una descripción y una fecha límite opcionales, **Cuando** guardo la tarea, **Entonces** ambos valores quedan persistidos en la tarea.
- **Dado** que solo indico el título, **Cuando** guardo la tarea, **Entonces** la tarea se crea correctamente con descripción y fecha límite vacías.

### Épica: Autenticación y gestión de cuenta

**US-1.1 — Registro con email y contraseña**

> Como visitante, quiero crear una cuenta con mi email y una contraseña, para tener un espacio privado donde gestionar mis tareas.

**Criterios de aceptación:**
- **Dado** que introduzco un email no registrado y una contraseña de al menos 8 caracteres, **Cuando** envío el formulario, **Entonces** la cuenta se crea y llego a la pantalla de bienvenida (onboarding mínimo).
- **Dado** que el email ya está registrado, **Cuando** intento crear la cuenta, **Entonces** el sistema lo indica y me ofrece ir al inicio de sesión.
- **Dado** que introduzco una contraseña de menos de 8 caracteres, **Cuando** envío el formulario, **Entonces** el sistema muestra un error de validación comprensible y no crea la cuenta.

---

## Instrucción final

Genera el backlog completo de user stories del MVP de FlowSync siguiendo todo lo anterior: rol asignado, formato exacto de la story, 3–5 criterios Given/When/Then por story, agrupación por épica, las restricciones explícitas, y la marca **"(asumido)"** en todo lo que infieras y no esté literal en el PRD.
