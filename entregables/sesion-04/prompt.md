# Prompt de descomposición — Backlog inicial de FlowSync

> Diseñado para ejecutarse con el PRD de FlowSync (`docs/PRD.md`) adjunto/pegado
> a continuación del prompt. Sigue el patrón **estructura → restricciones →
> ejemplo → transparencia**.

---

## Rol

Actúa como **Product Owner senior** con experiencia en productos SaaS y en
descomponer PRDs en backlogs accionables. Tu especialidad son las user stories
pequeñas, verificables y sin ambigüedad, con criterios de aceptación que QA
pueda convertir en pruebas directamente.

## Contexto

**FlowSync** es una aplicación web de gestión de tareas personales cuyo
diferenciador es **mantener las tareas sincronizadas con Google Calendar** (las
tareas con fecha aparecen como eventos; los cambios fluyen principalmente
FlowSync → Calendar). Va dirigida a *knowledge workers* (25-45 años) que ya
viven en Google Calendar y gestionan sus pendientes en una herramienta aparte.
El MVP es **estrictamente individual** (sin equipos) y busca validar si eliminar
la doble gestión tarea/calendario impulsa la adopción.

El PRD completo está adjunto. **Trabaja exclusivamente a partir de él.**

## Tarea

Convierte los **requisitos funcionales del MVP** (sección **3** del PRD, y solo
esa para el alcance de features) en un conjunto de **user stories con criterios
de aceptación**.

## Alcance (acótalo estrictamente)

- Incluye **solo funcionalidades del MVP** de la sección 3 del PRD: autenticación,
  CRUD de tareas, organización/filtrado, exportación y sync con Google Calendar.
- **No inventes features** que no estén literalmente en el documento. Todo lo que
  aparece en "Qué NO incluye el MVP" (§1) queda **fuera** del backlog.
- Los requisitos no funcionales (§4), el stack (§5) y los criterios de éxito (§6)
  son **contexto**, no los conviertas en stories, salvo que un requisito
  funcional lo implique directamente (p. ej. un mensaje de error claro dentro de
  una story de validación).

## Formato exacto (obligatorio)

Cada story con esta estructura:

- **Título** corto.
- **Historia**: `Como [rol], quiero [acción], para [beneficio]`.
- **Criterios de aceptación**: **3-5**, en formato **Given / When / Then**,
  **verificables y específicos** (nada de "el sistema funciona correctamente").
  Cubre el camino feliz y al menos un caso de error/límite cuando el PRD lo
  mencione.

## Agrupación

Agrupa las stories por **módulo/épica**, siguiendo la estructura del PRD:
`Autenticación`, `Gestión de tareas (CRUD)`, `Organización y filtrado`,
`Exportación`, `Sincronización con Google Calendar`.

## Restricciones (non-goals)

- ❌ No estimes tiempos, puntos ni esfuerzo.
- ❌ No propongas arquitectura, endpoints, esquema de datos ni tecnología.
- ❌ No inventes reglas de negocio que el PRD no exprese.
- ❌ No reescribas ni "mejores" el PRD; descomponlo tal cual.

## Transparencia (importante)

Cuando **infieras** algo que no esté literal en el PRD (un AC razonable, un rol,
un valor por defecto), márcalo con **`(asumido)`** al final de esa línea.
Prefiero un AC marcado `(asumido)` a un hueco silencioso.

## Ejemplo del formato esperado (referencia de estilo)

> ### [Autenticación] Registro con email y contraseña
> **Como** visitante, **quiero** crear una cuenta con mi email y contraseña,
> **para** empezar a usar FlowSync con mis datos privados.
>
> **Criterios de aceptación:**
> - **Given** un visitante en la pantalla de registro, **when** introduce un email
>   no registrado y una contraseña de ≥8 caracteres, **then** se crea la cuenta y
>   llega a la pantalla de bienvenida (onboarding).
> - **Given** un visitante, **when** introduce un email ya registrado, **then** el
>   sistema lo indica y ofrece ir a inicio de sesión.
> - **Given** un visitante, **when** la contraseña tiene menos de 8 caracteres,
>   **then** se muestra un error de validación comprensible y no se crea la cuenta.

## Salida

- Devuelve **entre 8 y 12 user stories** en total, agrupadas por módulo.
- Solo el backlog en Markdown. No añadas introducción, resumen ni conclusiones.
