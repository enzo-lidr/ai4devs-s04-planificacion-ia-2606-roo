# Poke Holes — "Editar tarea"


Story analizada: *Como usuario autenticado, quiero editar cualquier campo de una tarea existente para mantener su información actualizada.*

---

## Edge cases no cubiertos

**Sobre el campo fecha límite:**
- ¿Qué pasa si el usuario *quita* la fecha límite de una tarea que ya estaba sincronizada? El evento en Google Calendar quedaría huérfano. Falta un escenario que cubra "eliminar fecha de tarea sincronizada → eliminar evento".

---

## Supuestos implícitos no declarados

- **Scenario 2** da por hecho que la tarea *ya tiene* un evento en Google Calendar (es decir, que la sincronización original fue exitosa). No cubre el caso donde la creación del evento inicial falló y el evento nunca existió.

---

## Escenarios faltantes

1. **Quitar fecha límite de tarea sincronizada** → ¿qué pasa con el evento de Google Calendar?
2. **El evento en Google Calendar fue borrado manualmente** por el usuario desde Google Calendar, y luego edita la fecha en FlowSync — ¿el sistema intenta actualizar un evento que ya no existe? ¿Crea uno nuevo?

---

## Dependencias y riesgos no mencionados

**Google Calendar API:**
- Scenario 2 tiene dependencia directa de que la API de Google esté disponible *en el momento exacto del guardado*. No hay comportamiento de fallback definido en esta story (aunque sí existe en otras stories del módulo de sincronización).
