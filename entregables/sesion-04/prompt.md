Eres un Product Owner senior con experiencia en aplicaciones SaaS. Tu especialidad es escribir user stories que un agente de IA pueda implementar sin ambigüedad.

## Tu tarea

Descompón el PRD de FlowSync en user stories listas para sprint.

## Restricciones estrictas

1. Solo features explícitamente en el PRD. No inventes nada que no esté en el documento.
2. No estimes tiempos ni asignes story points.
3. No propongas arquitectura más allá del stack ya definido en el PRD.
4. Marca con (asumido) cualquier detalle que estés infiriendo y no esté literal en el PRD.
5. Granularidad: cada story implementable en 1-2 días con copiloto. Si es más grande, divídela.
6. Mínimo 3 AC por story: happy path + 1 edge case + 1 caso de error. Cada AC debe ser directamente traducible a un test. Nada genérico ("el sistema funciona correctamente").

## Formato obligatorio por story

### [ÉPICA] · Story N

**Como** [rol concreto],
**quiero** [acción específica],
**para** [beneficio observable].

**Acceptance Criteria:**

* Given [contexto]
* When [acción]
* Then [resultado verificable]
* And [condición adicional si aplica]

**Contexto técnico:** [endpoints, controladores, modelos o archivos del stack relevantes — AdonisJS 7 + React 19 + SQLite + Google Calendar API. Si no tienes info suficiente escribe "(asumido): necesita revisión en refinamiento".]

**Non-goals:** [qué NO cubre esta story — máximo 3 bullets]

## Agrupación en épicas

Agrupa todas las stories en estas cinco épicas exactas, en este orden:
- ÉPICA: Auth — registro, login, sesión, onboarding
- ÉPICA: Tasks — CRUD completo, estados
- ÉPICA: Filter — filtrado, ordenación, estado vacío
- ÉPICA: Export — exportación CSV
- ÉPICA: GCal — sincronización con Google Calendar

Al final de cada épica añade una sección:
⚠ Asunciones a validar: lista los puntos marcados (asumido) que necesitan confirmación humana antes de entrar al sprint.

## Ejemplo de formato (NO generes stories sobre este ejemplo)

### Auth · Story 0 (EJEMPLO DE FORMATO)

**Como** visitante no autenticado,
**quiero** crear una cuenta con email y contraseña,
**para** acceder a mis tareas desde cualquier dispositivo.

**Acceptance Criteria:**

* Given el visitante rellena email válido y contraseña de 8+ caracteres
* When envía el formulario de registro
* Then se crea la cuenta, se inicia sesión automáticamente y se redirige al onboarding


* Given el visitante introduce una contraseña de 7 caracteres
* When envía el formulario
* Then aparece el mensaje "La contraseña debe tener al menos 8 caracteres" sin llamar al servidor


* Given el visitante introduce un email ya registrado
* When envía el formulario
* Then aparece "Este email ya está registrado" con enlace a inicio de sesión

**Contexto técnico:** `POST /api/auth/register` — `app/controllers/auth_controller.ts`, validación VineJS, hash con `@adonisjs/auth`, tabla `users` en SQLite.

**Non-goals:**
- No incluye verificación de email por enlace (out of scope MVP)
- No incluye login social

Empieza directamente con las stories. Sin introducción, sin repetir el PRD.
Genera en la carpeta `entregables/sesion-04` el documento `output.md` con el resultado