# Analisis Poke-Holes: Epic "Infraestructura de Tareas"

## User Story/Epic Analizada

Epic: Infraestructura de Tareas (T1, T2, T3)
- T1: Crear modelo Task con campos: title (requerido), description (opcional), status, due_date
- T2: Crear migracion de base de datos
- T3: Configurar relacion User-Task

## Hallazgos No Triviales

### 1. Tipo de Dato de Status No Definido (asumido)
Gap: No se especifica el tipo de dato para el campo status.
Riesgo: Podria implementarse como string sin constraints, permitiendo valores invalidos.
Impacto: Validacion inconsistente, filtros por status no funcionarian correctamente.
Detalle: Deberia ser ENUM('pending', 'completed', 'archived') o string con validacion.

### 2. Formato de due_date No Especificado (asumido)
Gap: No se define el tipo de dato ni formato para due_date.
Riesgo: Diferentes interpretaciones (DATE, DATETIME, TIMESTAMP) causarian problemas de sincronizacion con Google Calendar.
Impacto: Errores en la integracion con Google Calendar (PRD 3.5), zonas horarias mal manejadas.
Detalle: Deberia ser DATE o DATETIME con timezone. PRD menciona fecha limite pero Google Calendar usa datetime.

### 3. Relacion User-Task: Cardinalidad No Aclarada (asumido)
Gap: Se asume hasMany/belongsTo pero no se especifica si un usuario puede tener muchas tareas o una tarea puede pertenecer a muchos usuarios.
Riesgo: Implementacion incorrecta de la relacion podria permitir acceso no autorizado.
Impacto: Violacion de privacidad (PRD 4: datos de un usuario nunca accesibles por otros).
Detalle: PRD 3.1 dice cada usuario tiene cuenta propia y datos no visibles para otros -> debe ser hasMany (1 usuario N tareas).

### 4. Constraints de Base de Datos No Definidos
Gap: La migracion no especifica constraints como NOT NULL, DEFAULT values.
Riesgo: Datos inconsistentes (tareas sin title, status NULL).
Impacto: Errores en tiempo de ejecucion, validaciones en aplicativo no son suficientes.
Detalle: title debe ser NOT NULL, status debe tener DEFAULT 'pending', due_date puede ser NULL.

### 5. Indices de Base de Datos No Mencionados
Gap: No se consideran indices para campos usados en filtros (status, user_id, due_date).
Riesgo: Consultas lentas al filtrar tareas por estado (PRD 3.3).
Impacto: Violacion de requisito no funcional: listado debe cargar en <1s para 200 tareas (PRD 4).
Detalle: Se necesitan indices en user_id, status, y due_date para performance.

---

## Priorizacion de Hallazgos

| # | Hallazgo | Severidad | Tipo | Relacion PRD |
|---|----------|-----------|------|--------------|
| 1 | Tipo de status no definido | Alta | Gap tecnico | 3.2, 3.3 |
| 2 | Formato due_date no especificado | Alta | Asuncion tecnica | 3.2, 3.5 |
| 3 | Cardinalidad User-Task no aclarada | Alta | Gap de diseno | 3.1, 4 |
| 4 | Constraints de BD no definidos | Media | Gap de implementacion | 3.2 |
| 5 | Indices de BD no considerados | Media | Riesgo de performance | 4 |
