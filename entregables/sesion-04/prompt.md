# Prompt

## Story: Como dev de FlowSync, quiero un endpoint que permita crear tareas (definicion en `docs/PRD.md`)

## AC (Given/When/Then)
Scenario: Usuario cria tarea correctamiente
  Given el usuario quiere crear una tarea con título, descripción, estado, y fecha límite.
  When solicita /api/tasks con todas informaciones
  Then la respuesta es 201 CREATED
  And contiene exactamente todas las informaciones que envio el usuario

Scenario: Usuario intenta crear tarea con error
  Given el usuario quiere crear una tarea con descripción, estado, y fecha límite.
  When solicita /api/tasks con apenas estas informaciones
  Then la respuesta es 400 BAD REQUEST
  And retorna error de que la tarea tiene que tener titulo

Scenario: Usuario filtra lista de tareas por estado
  Given el usuario tiene 12 tareas (5 pendientes, 7 completadas)
  When solicita /api/tasks?status=pending
  Then la respuesta es 200 OK
  And contiene exactamente 5 tareas
  And todas tienen status="pending"

## Contexto técnico (para el agente)
- Endpoint actual: backend/app/controllers/auth_controller.ts:getMe()
- Modelo User tiene relación @hasOne con Role en app/models/user.ts
- Permisos vienen de RolePermission (revisar app/services/permission_service.ts)
- Tests previos similares: tests/functional/auth/me.spec.ts
- marca con (asumido) lo que infieras y no esté literal en el PRD

## DoD
- [ ] Endpoint implementado
- [ ] Validación de tests de happy path
- [ ] Validación corner cases
- [ ] Tests funcionales cubriendo todos los AC del GWT
- [ ] Documentación en OpenAPI auto-generada
- [ ] OpenSpec change archivado tras merge

## Non-goals (explícito)
- No tocar otros endpoints del controller
- No cambiar el modelo User
- No optimizar queries existentes (eso va en su propio ticket)
- No hacer otras features
- No cambiar ninguno codigo de forma desnecesaria
- No crear comentarios en el codigo

## Agent request

```
@prompt.md @docs/PRD.md
Use the prompt tp create the tasks needed to compelte this user story. Separate the taks when it makes sense to the context and complecity and add a fibbonacci pontuation to each task. Taks should have a value deliverable. Follow all rules on prompt and access PRD to know the project and tecnical definitions. 

The outup should be added dirextly at the file output.md
```