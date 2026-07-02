# poke-holes.md — Crítica de una story (patrón "AI as poke-holes")

## Story elegida

**US-13 — Crear evento de calendario a partir de una tarea con fecha** (Épica E).
La elijo porque es la funcionalidad diferenciadora y la de mayor riesgo técnico del MVP
(el propio PRD lo señala en las secciones 5 y 7), y porque a primera vista sus AC parecían
de los más completos.

## Prompt de crítica usado

```
Aquí tienes esta user story con sus criterios de aceptación:
### US-13 — Crear evento de calendario a partir de una tarea con fecha
**Como** usuario con Google conectado, **quiero** que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, **para** no copiarlas a mano.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: tarea con fecha crea evento**
  - Given un usuario con Google conectado
  - When crea (o asigna fecha límite a) una tarea
  - Then se crea un evento en su Google Calendar asociado a esa tarea
  - And el evento queda vinculado a la tarea para futuras actualizaciones
- **Escenario: tarea sin fecha no crea evento**
  - Given un usuario con Google conectado
  - When crea una tarea sin fecha límite
  - Then no se crea ningún evento de calendario
- **Escenario: hora del evento (asumido)**
  - Given una fecha límite sin hora concreta
  - When se crea el evento
  - Then el evento se crea como evento de día completo o a una hora por defecto a definir con Producto (asumido)


Tu tarea: identifica edge cases, supuestos implícitos, escenarios faltantes,
y dependencias o riesgos no mencionados. No reescribas la story, solo lista
lo que falta o lo que asumiste.
```

## Hallazgos (filtrados — descartado el ruido genérico)

1. **Zona horaria fecha → evento.** La tarea tiene una *fecha límite* (un día), pero un evento de
   Google necesita hora e instante con zona horaria. Si se asume la TZ del servidor en lugar de la
   del usuario, un usuario en GMT+1 puede ver el evento el día anterior o a las 01:00. El PRD lo
   avisa en la sección 7 pero ningún AC lo cubre. **Gap real, alto impacto.**

2. **Idempotencia / eventos duplicados en los reintentos.** US-13 crea un evento y US-17 reintenta
   tras fallos de la API. Pero la API de Google puede haber creado el evento y fallar *al devolver
   la respuesta*: el reintento crearía un segundo evento. Hace falta una clave de idempotencia o
   guardar el `eventId` de Google antes de dar la sync por buena. Ningún AC lo contempla.

3. **Tareas con fecha creadas ANTES de conectar Google (backfill).** US-12 conecta Google y US-13
   habla de tareas que se crean *después*. ¿Qué pasa con las 5-30 tareas con fecha que el usuario
   ya tenía al conectar? ¿Se vuelcan al calendario, no se tocan, o se le pregunta? Escenario
   faltante y muy probable en el día 1 de cada usuario.

4. **Rate limits de la API de Google.** El PRD (sección 7) cita los rate limits como riesgo
   conocido, pero ningún AC define el comportamiento: si un usuario crea 20 tareas de golpe (perfil
   de 5-30 tareas activas), ¿se encolan, se agrupan, se hace backoff? Dependencia no resuelta entre
   US-13 y US-17.

5. **Edición manual o borrado del evento directamente en Google.** Si el usuario borra en Google
   el evento que FlowSync creó, la tarea queda apuntando a un `eventId` inexistente. La próxima
   actualización (US-14/US-15) fallará con 404. El MVP es FlowSync→Google, pero este caso ocurre
   sin necesidad de sync inversa y no está cubierto.

6. **Supuesto implícito: una tarea = un evento.** Todo el diseño asume relación 1:1 tarea↔evento.
   No es un gap urgente, pero conviene hacerlo explícito porque condiciona el modelo de datos y
   futuras features (p. ej. tareas recurrentes, fuera del MVP).


