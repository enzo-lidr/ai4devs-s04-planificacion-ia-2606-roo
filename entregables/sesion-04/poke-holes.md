# Análisis de gaps — US8: Cambiar estado de tarea

> **User Story analizada**: US8 — Como usuario autenticado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar el progreso real de mis pendientes.

---

## Transiciones de estado no definidas

Los criterios de aceptación solo cubren `pending → completed` y `cualquiera → archived`. Faltan:

- `completed → pending` (deshacer completado — ¿permitido?)
- `archived → pending` (restaurar — ¿permitido?)
- `completed → archived` (¿válido?)
- `archived → completed` (¿válido?)

Sin una matriz de transiciones explícita, QA no puede probar ni rechazar combinaciones. El backend podría aceptar transiciones inválidas sin que ningún test las cubra.

---

## Suposiciones implícitas sin marcar

- `archived` podría ser estado terminal o reversible — el PRD no lo especifica.
- AC3 ("tarea recién creada → estado `pending`") pertenece a US4 (Crear tarea), no a esta story. Riesgo de duplicación y desincronización entre criterios.
- "Vista principal" no está definida — ¿es el listado sin filtro activo? ¿El filtro por defecto `pending`? Ambiguo para QA y desarrollo.
- El cambio de estado se asume como acción atómica e inmediata, sin estado intermedio ni confirmación.

---

## Escenarios faltantes

- **Error de red al guardar**: AC4 establece que el estado se refleja sin recargar, pero no define qué hace la UI si el guardado falla. ¿Revierte visualmente? ¿Muestra mensaje de error?
- **Feedback visual durante el guardado**: no se especifica si existe indicador de carga mientras la petición está en vuelo.
- **Dos pestañas abiertas simultáneamente**: el usuario cambia estado en la pestaña A; la pestaña B muestra estado obsoleto. Comportamiento no definido.

---

## Dependencias no declaradas

- **US12 / US14 (Google Calendar sync)**: cambiar a `completed` o `archived` debería eliminar o actualizar el evento en Calendar. No se define si un fallo de sincronización bloquea el cambio de estado o si procede de forma independiente.
- **US9 (Filtrado)**: AC2 establece que las tareas `archived` desaparecen del listado principal — comportamiento acoplado a la lógica de filtrado, pero la dependencia no está declarada en esta story.

---

## Riesgos

- Sin matriz de transiciones definida, el backend podría permitir combinaciones inválidas sin cobertura de tests.
- El AC4 marcado como **(asumido)** sobre reactividad sin recarga implica una decisión de arquitectura frontend. Si esa premisa falla, el criterio se vuelve inverificable en QA.
