# Poke-holes — Historia G2 (Sincronización de tareas como eventos)

## Historia analizada

**Historia G2 — Como usuario con Google conectado, quiero que mis tareas con
fecha límite aparezcan como eventos en mi calendario, para no copiarlas a mano.**

- **Given** un usuario con Google conectado, **When** crea o ya tiene una tarea
  con fecha límite, **Then** aparece un evento correspondiente en su Google Calendar.
- **Given** una tarea sin fecha límite, **When** el usuario la crea, **Then** no
  se genera ningún evento en el calendario.

## Prompt de crítica usado

> Aquí tienes esta user story con sus criterios de aceptación: [G2].
> Tu tarea: identifica edge cases, supuestos implícitos, escenarios faltantes,
> y dependencias o riesgos no mencionados. No reescribas la story, solo lista
> lo que falta o lo que asumiste.

---

## Hallazgos genuinos (los que valen)

**1. ¿A qué hora se crea el evento? — el hueco de las zonas horarias.**
La historia dice que la tarea "con fecha límite" se convierte en evento, pero una
fecha límite es un día (ej: "15 de julio"), mientras que un evento de calendario
tiene hora de inicio y fin. No está definido si el evento es de todo el día o a
una hora concreta, ni en qué zona horaria. El propio PRD marca las zonas horarias
como riesgo conocido: sin resolver esto, se pueden crear eventos a horas erróneas
(la tarea del día 15 aparece el día 14 por la noche para usuarios en otra zona).

**2. ¿Qué pasa cuando falla la llamada a Google? — no está en ESTA historia.**
Los dos criterios de G2 describen solo el camino feliz (todo funciona). No dicen
qué ocurre si la API de Google devuelve error o no está disponible justo al crear
el evento. El PRD sí lo exige (la tarea debe guardarse igual y reintentar), pero
esa red de seguridad vive en otra historia (G5); G2 por sí sola parece prometer
que el evento "siempre aparece", lo cual no es cierto.

**3. Rate limits de la API de Google — riesgo no mencionado.**
Si un usuario tiene muchas tareas con fecha (el PRD contempla hasta 30 activas, y
hasta 200 en total), conectarse por primera vez podría intentar crear muchos
eventos de golpe y chocar con los límites de peticiones de Google. La historia no
dice nada sobre crear eventos en lote ni sobre qué pasa si se supera ese límite.

**4. Las tareas que YA existían antes de conectar Google — escenario faltante.**
El criterio dice "crea o ya tiene una tarea con fecha límite". Ese "ya tiene" es
enorme y está sin desarrollar: si el usuario tenía 25 tareas con fecha y recién
conecta Google, ¿se crean los 25 eventos retroactivamente? ¿Cuándo? ¿Todos a la
vez? Es un caso de uso central del producto (el usuario objetivo ya viene con
tareas de otra herramienta) y la historia lo despacha en tres palabras.

**5. ¿Qué identifica al evento como "de FlowSync"? — supuesto implícito.**
La historia asume que existe una forma de saber qué evento del calendario
corresponde a qué tarea, para poder actualizarlo o borrarlo después (historias G3
y G4). Pero eso no está dicho en ningún lado. Si no se define ese vínculo, el
sistema no sabrá qué evento tocar cuando la tarea cambie, o podría duplicar
eventos en cada sincronización.

---

## Nota de proceso

De la crítica salieron ~9 candidatos; varios eran ruido o repetición (p. ej.
"¿qué pasa si el usuario no tiene conexión a internet?", que aplica a toda la app,
no a esta historia). Estos 5 son los que representan gaps reales de producto que
el equipo tendría que resolver en refinamiento antes de tocar código.
