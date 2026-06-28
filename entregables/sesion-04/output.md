# Task Breakdown: Endpoint de Creacion de Tareas con Filtro por Estado

## Contexto
User Story: **Como dev de FlowSync, quiero un endpoint que permita crear tareas** (basado en PRD seccion 3.2 y prompt.md)

## Tasks Desglosados con Puntos Fibonacci

### Epic: Infraestructura de Tareas (13 puntos)

| ID | Task | Descripcion | Puntos | Entregable | DoD Related |
|----|------|-------------|--------|------------|-------------|
| T1 | Crear modelo Task | Definir modelo con campos: title (requerido), description (opcional), status (pending/completed/archived), due_date (opcional). Relacion con User (asumido: belongsTo). | 5 | app/models/task.ts | Endpoint implementado |
| T2 | Crear migracion de base de datos | Migracion para tabla tasks con todos los campos y constraints. | 3 | database/migrations/xxxx_create_tasks_table.ts | Endpoint implementado |
| T3 | Configurar relacion User-Task | Establecer relacion hasMany en User y belongsTo en Task para asociar tareas a usuarios. | 5 | Actualizacion app/models/user.ts y app/models/task.ts | - |

### Epic: Endpoint POST /api/tasks (16 puntos)

| ID | Task | Descripcion | Puntos | Entregable | DoD Related |
|----|------|-------------|--------|------------|-------------|
| T4 | Implementar controlador create | Metodo POST en TasksController que crea tarea con datos del request. | 5 | app/controllers/tasks_controller.ts | Endpoint implementado |
| T5 | Validacion con VineJS | Schema de validacion: title requerido, status valido (pending/completed/archived), due_date formato ISO. | 3 | app/validators/task_validator.ts | Validacion de tests |
| T6 | Manejo de errores de validacion | Retornar 400 BAD REQUEST con mensajes claros cuando falte title. | 3 | Parte de T4 | Validacion corner cases |
| T7 | Respuesta 201 CREATED | Retornar tarea creada con todos los campos enviados por el usuario. | 2 | Parte de T4 | Tests funcionales |
| T8 | Tests funcionales - Happy Path | Probar creacion exitosa con todos los campos (title, description, status, due_date). | 3 | tests/functional/tasks/create.spec.ts | Tests funcionales |

### Epic: Endpoint GET /api/tasks con Filtro (11 puntos)

| ID | Task | Descripcion | Puntos | Entregable | DoD Related |
|----|------|-------------|--------|------------|-------------|
| T9 | Implementar controlador index | Metodo GET en TasksController que lista tareas del usuario autenticado. | 5 | app/controllers/tasks_controller.ts | Endpoint implementado |
| T10 | Implementar filtro por status | Query param ?status=pending|completed|archived para filtrar tareas. | 5 | Parte de T9 | Tests funcionales |
| T11 | Tests funcionales - Filtro | Probar que GET /api/tasks?status=pending retorne solo tareas con ese estado. | 3 | tests/functional/tasks/list.spec.ts | Tests funcionales |
| T12 | Tests funcionales - Conteo | Validar que retorne exactamente N tareas segun filtro (ej: 5 pendientes de 12 totales). | 2 | Parte de T11 | Tests funcionales |

### Epic: Documentacion y Calidad (6 puntos)

| ID | Task | Descripcion | Puntos | Entregable | DoD Related |
|----|------|-------------|--------|------------|-------------|
| T13 | Documentacion OpenAPI/Swagger | Auto-generar docs para POST y GET /api/tasks con schemas, ejemplos y respuestas. | 5 | docs/swagger.json o decoradores | Documentacion en OpenAPI |
| T14 | Archivar OpenSpec change | Crear archivo de cambio en OpenSpec con la nueva funcionalidad. | 1 | openspec/changes/tasks-endpoint.yaml | OpenSpec change archivado |

### Epic: Verificacion de Non-Goals (3 puntos)

| ID | Task | Descripcion | Puntos | Entregable | DoD Related |
|----|------|-------------|--------|------------|-------------|
| T15 | Verificar no tocar otros endpoints | Revision de codigo: solo tasks_controller.ts modificado, no auth_controller.ts. | 1 | - | Non-goals |
| T16 | Verificar no cambiar modelo User | Revision: User model solo se actualiza con relacion hasMany, no cambios estructurales. | 1 | - | Non-goals |
| T17 | Verificar sin optimizacion de queries | Revision: no se implementan eager loads o optimizaciones adicionales. | 1 | - | Non-goals |

---

## Resumen de Puntos

| Epic | Puntos | % del Total |
|------|--------|-------------|
| Infraestructura de Tareas | 13 | 28% |
| POST /api/tasks | 16 | 34% |
| GET /api/tasks con Filtro | 11 | 23% |
| Documentacion y Calidad | 6 | 13% |
| Verificacion de Non-Goals | 3 | 6% |
| **TOTAL** | **49** | **100%** |

---

## Mapeo a Criterios de Aceptacion (GWT)

### Scenario: Usuario crea tarea correctamente
- Given: usuario quiere crear tarea con titulo, descripcion, estado, fecha limite
- When: solicita /api/tasks con todas las informaciones
- Then: respuesta es 201 CREATED
- And: contiene exactamente todas las informaciones enviadas
- Tasks relacionados: T4, T5, T7, T8

### Scenario: Usuario intenta crear tarea con error
- Given: usuario quiere crear tarea con descripcion, estado, fecha limite (sin titulo)
- When: solicita /api/tasks con apenas estas informaciones
- Then: respuesta es 400 BAD REQUEST
- And: retornar error de que la tarea tiene que tener titulo
- Tasks relacionados: T5, T6, T8 (tests de error)

### Scenario: Usuario filtra lista de tareas por estado
- Given: usuario tiene 12 tareas (5 pendientes, 7 completadas)
- When: solicita /api/tasks?status=pending
- Then: respuesta es 200 OK
- And: contiene exactamente 5 tareas
- And: todas tienen status="pending"
- Tasks relacionados: T9, T10, T11, T12

---

## Dependencias entre Tasks

Infraestructura -> Endpoints -> Tests -> Documentacion

T1, T2, T3 deben completarse antes de T4, T5, T6, T7
T4, T5, T6, T7 deben completarse antes de T8
T9, T10 deben completarse antes de T11, T12
T4, T9 deben completarse antes de T13
T13 debe completarse antes de T14
T15, T16, T17 son verificaciones finales

---

## Notas Tecnicas (asumido)

- Autenticacion: Todos los endpoints requieren usuario autenticado (usando @adonisjs/auth). (asumido)
- Relacion User-Task: Cada tarea pertenece a un usuario (user_id foreign key). (asumido)
- Estado por defecto: Tareas nuevas se crean con status="pending" si no se especifica. (asumido)
- Formato de fechas: due_date en formato ISO 8601 (YYYY-MM-DD). (asumido)
- Validacion de status: Solo se aceptan "pending", "completed", "archived". (asumido)
