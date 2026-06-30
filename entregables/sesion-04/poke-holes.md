# Poke-holes — US-11: Mantener el evento de Google sincronizado con los cambios de la tarea

Story analizada: US-11 del output.md

Prompt utilizado:
> Aquí tienes esta user story con sus criterios de aceptación:
>
> **US-11 — Mantener el evento de Google sincronizado con los cambios de la tarea**
> Depende de: US-10
> Como knowledge worker que ajusta sus tareas a lo largo del día, quiero que al
> cambiar la fecha, completar o borrar una tarea su evento en Google Calendar se
> actualice o elimine en consecuencia, para que mi calendario siempre refleje la
> realidad sin mantenimiento manual.
>
> Criterios de aceptación:
> - Given que tengo una tarea con evento en Google Calendar,
>   When cambio su fecha límite en FlowSync,
>   Then el evento correspondiente se actualiza a la nueva fecha.
> - Given que tengo una tarea con evento en Google Calendar,
>   When la marco como `completed`,
>   Then su evento se elimina o se marca según corresponda en Google Calendar.
>   (asumido: el PRD deja "se elimina o se marca según corresponda"; se asume
>   eliminar el evento al completar)
> - Given que tengo una tarea con evento en Google Calendar,
>   When borro la tarea,
>   Then su evento correspondiente se elimina del calendario.
> - Given que la API de Google no está disponible al propagar un cambio,
>   When actualizo, completo o borro la tarea,
>   Then el cambio se guarda en FlowSync, la sincronización se reintenta más tarde
>   y la operación queda registrada en los logs.
>   (asumido: el PRD no especifica el intervalo de reintento)
>
> Tu tarea: identifica edge cases, supuestos implícitos, escenarios faltantes,
> y dependencias o riesgos no mencionados. No reescribas la story, solo lista
> lo que falta o lo que asumiste.

---

## Hallazgo 1 — Quitar la fecha límite no está cubierto

Los ACs cubren "cambiar la fecha" pero no "borrarla". Si el usuario edita la
tarea y le quita la fecha límite, ningún AC define qué pasa con el evento en
Google Calendar. Lo razonable sería eliminarlo, pero la story no lo especifica.

## Hallazgo 2 — El estado `archived` no tiene comportamiento definido

US-06 contempla archivar tareas. US-11 solo menciona `completed` y borrar.
Si una tarea con evento se archiva, no hay ningún AC que defina si el evento
se elimina, se mantiene, o queda en estado indefinido.

## Hallazgo 3 — Cambiar el título de la tarea no está cubierto

US-10 establece que el título del evento = título de la tarea. Pero US-11
solo sincroniza cambios de fecha. Si el usuario edita el título vía US-04,
el evento en Google Calendar queda con el título anterior. Ninguna story
ni AC cubre esa sincronización.

## Hallazgo 4 — La story agrupa tres operaciones independientes

Cambiar fecha, completar y borrar tienen comportamientos distintos en Calendar
y pueden fallar de forma independiente. Agrupadas, un error en "completar"
contamina el alcance de "cambiar fecha". Según INVEST (Independent), deberían
ser stories separadas.

## Hallazgo 5 — El conflicto con ediciones directas en Google Calendar no está marcado como out-of-scope

El PRD señala este caso como riesgo (spike pendiente), pero la story no lo
registra como supuesto explícito ni como exclusión. Un QA que pruebe "el
usuario mueve el evento en Google y luego cambia la fecha en FlowSync" no
tiene ningún AC que defina el comportamiento esperado — ni siquiera un
`(asumido: fuera de alcance)`.
