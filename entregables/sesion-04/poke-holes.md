# Analisis Poke-Holes: Epic "Infraestructura de Tareas"

## User Story/Epic Analizada

Epic: Infraestructura de Tareas (T1, T2, T3)
- T1: Crear modelo Task con campos: title (requerido), description (opcional), status, due_date
- T2: Crear migracion de base de datos
- T3: Configurar relacion User-Task

## Hallazgos

### Relacion User-Task: Cardinalidad No Aclarada (asumido)
Gap: Se asume hasMany/belongsTo pero no se especifica si un usuario puede tener muchas tareas o una tarea puede pertenecer a muchos usuarios.
Riesgo: Implementacion incorrecta de la relacion podria permitir acceso no autorizado.
Impacto: Violacion de privacidad (PRD 4: datos de un usuario nunca accesibles por otros).
Detalle: PRD 3.1 dice cada usuario tiene cuenta propia y datos no visibles para otros -> debe ser hasMany (1 usuario N tareas).

### Constraints de Base de Datos No Definidos
Gap: La migracion no especifica constraints como NOT NULL, DEFAULT values.
Riesgo: Datos inconsistentes (tareas sin title, status NULL).
Impacto: Errores en tiempo de ejecucion, validaciones en aplicativo no son suficientes.
Detalle: title debe ser NOT NULL, status debe tener DEFAULT 'pending', due_date puede ser NULL.

### Indices de Base de Datos No Mencionados
Gap: No se consideran indices para campos usados en filtros (status, user_id, due_date).
Riesgo: Consultas lentas al filtrar tareas por estado (PRD 3.3).
Impacto: Violacion de requisito no funcional: listado debe cargar en <1s para 200 tareas (PRD 4).
Detalle: Se necesitan indices en user_id, status, y due_date para performance.