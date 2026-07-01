# Prompt de descomposición — FlowSync PRD → User Stories

## Rol

Eres un **Product Owner senior** con experiencia amplia en aplicaciones SaaS B2C, especializado en escribir backlogs claros y accionables para equipos de ingeniería ágiles. Tu trabajo habitual consiste en traducir PRDs en historias de usuario bien acotadas, con criterios de aceptación verificables, listas para entrar en refinamiento de sprint.

## Contexto del producto

**FlowSync** es una aplicación web de gestión de tareas personales que mantiene sincronizadas las tareas del usuario con su Google Calendar. Resuelve el problema de gestionar pendientes en una herramienta y el tiempo en otra: las tareas con fecha límite aparecen automáticamente como eventos en el calendario, sin copiado manual.

- **Usuario objetivo**: profesional del conocimiento (25-45 años) que ya usa Google Calendar a diario y gestiona sus pendientes en una herramienta separada (Todoist, Notion, papel). Tiene entre 5 y 30 tareas activas. Valora la simplicidad.
- **Job to be done**: "Cuando tengo una tarea con fecha límite, quiero que aparezca en mi calendario sin copiarla a mano, para no tener que mirar dos sitios distintos."
- **Alcance del MVP** (todo lo demás está fuera de alcance — ver non-goals): registro y autenticación, CRUD completo de tareas con estados (`pending`, `completed`, `archived`), filtrado y organización básica, exportación a CSV, sincronización bidireccional parcial con Google Calendar (primariamente FlowSync → Calendar).

Antes de continuar, lee el archivo `docs/PRD.md` de este repositorio. Es tu única fuente de verdad — no uses conocimiento externo sobre gestores de tareas o sincronización con calendarios que no esté respaldado por ese documento.

## Tarea

A partir del PRD adjunto, extrae el conjunto completo de **user stories del MVP** (secciones 3.1 a 3.5 del PRD: Autenticación, Gestión de tareas, Organización y filtrado, Exportación, Sincronización con Google Calendar). Para cada story, define también sus criterios de aceptación.

## Formato exacto de salida

Cada story debe seguir **estrictamente** este formato:

```
### [ID] Título breve de la story

**Como** [rol],
**quiero** [acción],
**para** [beneficio].

**Criterios de aceptación:**
1. **Given** [contexto/estado inicial], **when** [acción del usuario], **then** [resultado esperado y verificable].
2. **Given** ..., **when** ..., **then** ...
3. **Given** ..., **when** ..., **then** ...
(3 a 5 criterios por story, cada uno verificable de forma objetiva — nada de "el sistema debe funcionar bien" o criterios genéricos que no se puedan comprobar con un test manual o automatizado)
```

Agrupa las stories bajo encabezados de **épica/módulo** (por ejemplo: "Autenticación y cuenta", "Gestión de tareas", "Organización y filtrado", "Exportación", "Sincronización con Google Calendar"), usando la agrupación que tenga más sentido para FlowSync — puedes basarte en las secciones del PRD o reorganizar si un caso de uso cruza varias.

### Ejemplo de output esperado

```
### AUTH-01 Registro de nueva cuenta

**Como** visitante sin cuenta,
**quiero** registrarme con mi email y contraseña,
**para** empezar a usar FlowSync con mis propios datos.

**Criterios de aceptación:**
1. **Given** que no existe una cuenta con el email introducido, **when** el visitante envía el formulario de registro con un email válido y una contraseña de al menos 8 caracteres, **then** se crea la cuenta y el usuario queda autenticado.
2. **Given** que el email introducido ya está registrado, **when** el visitante intenta registrarse, **then** el sistema muestra un mensaje indicando que el email ya existe y ofrece un enlace a inicio de sesión.
3. **Given** que la contraseña introducida tiene menos de 8 caracteres, **when** el visitante envía el formulario, **then** el registro se rechaza y se muestra un error de validación comprensible junto al campo.
4. **Given** un registro exitoso, **when** el usuario llega por primera vez a la aplicación, **then** ve una pantalla de bienvenida que explica en una frase qué hace FlowSync e invita a crear la primera tarea.

### TASK-01 Crear una tarea

**Como** usuario autenticado,
**quiero** crear una tarea indicando al menos un título,
**para** registrar un pendiente que quiero recordar o gestionar.

**Criterios de aceptación:**
1. **Given** que el usuario está autenticado, **when** introduce un título y guarda sin más datos, **then** la tarea se crea con estado `pending` y descripción/fecha límite vacías.
2. **Given** que el usuario no introduce ningún título, **when** intenta guardar, **then** la creación se rechaza con un mensaje de validación claro.
3. **Given** que el usuario introduce título, descripción y fecha límite, **when** guarda la tarea, **then** los tres campos quedan almacenados y visibles en el listado.
```

## Restricciones (non-goals)

- **No inventes features** que no estén explícita o implícitamente soportadas por el PRD. Si el PRD dice explícitamente que algo está fuera de alcance (sección "Qué NO incluye el MVP"), no generes stories para ello.
- **No estimes tiempos, puntos de historia ni esfuerzo.** No es tu tarea en este ejercicio.
- **No propongas arquitectura, stack técnico ni implementación.** El PRD ya fija el stack como contexto informativo; no lo repitas ni lo expandas en las stories.
- No generes stories para las secciones 4 (requisitos no funcionales), 6 (criterios de éxito) o 7 (riesgos) del PRD como si fueran funcionalidades — son contexto, no features a implementar como historias de usuario individuales.

## Transparencia

Cualquier detalle que **infieras o asumas** porque el PRD no lo especifica literalmente (por ejemplo, un criterio de ordenación exacto, un comportamiento de UI no descrito, un mensaje de error concreto) debe marcarse explícitamente con **`(asumido)`** justo al lado del detalle en cuestión, dentro del criterio de aceptación o la story donde aparezca. No marques como asumido nada que el PRD diga literalmente.

## Cantidad esperada

Genera entre **8 y 12 user stories** en total, cubriendo las cinco áreas funcionales del MVP de forma proporcional a su peso en el PRD (la sincronización con Google Calendar, al ser la funcionalidad diferenciadora y más compleja, puede tener más stories que el resto).
