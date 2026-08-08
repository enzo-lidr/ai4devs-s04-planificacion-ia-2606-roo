# Poke-holes — Story: "Sincronizar tarea con fecha como evento"

Story elegida (la de AC más completos a primera vista):

> Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi calendario, para ver todos mis pendientes en un solo lugar.
>
> - Given que tengo Google conectado, When creo una tarea con fecha límite, Then se crea un evento correspondiente en mi Google Calendar.
> - Given que tengo una tarea con fecha límite ya sincronizada, When cambio su fecha en FlowSync, Then el evento en Google Calendar se actualiza a la nueva fecha.
> - Given que la API de Google no está disponible al momento de sincronizar, When guardo o edito la tarea, Then la tarea igual se guarda en FlowSync y la sincronización se reintenta más tarde.

Prompt usado:

```
Aquí tienes esta user story con sus criterios de aceptación:
[story de arriba]

Tu tarea: identifica edge cases, supuestos implícitos,
escenarios faltantes, y dependencias o riesgos no
mencionados. No reescribas la story, solo lista lo
que falta o lo que asumiste.
```

## Hallazgos

1. **Zona horaria no cubierta en ningún AC.** El PRD marca explícitamente las zonas horarias como riesgo (sección 7), pero ningún criterio de esta story valida qué hora exacta toma el evento a partir de una fecha límite (¿todo el día? ¿una hora fija? ¿la hora local del usuario?). Es justo el riesgo que el propio PRD pide tratar con cuidado, y la story lo deja fuera.

2. **Falta el caso "tarea sin fecha que luego recibe una fecha".** Los AC cubren "crear con fecha" y "cambiar fecha de una tarea ya sincronizada", pero no qué pasa cuando una tarea que nació sin fecha límite se edita para agregarle una. ¿Ahí se crea el evento por primera vez? No está probado.

3. **Falta el caso inverso: quitar la fecha límite de una tarea ya sincronizada.** Si el usuario borra la fecha (la deja vacía) en una tarea que ya tenía evento en Calendar, no hay criterio que diga si el evento se elimina, se congela, o queda huérfano.

4. **El reintento tras fallo de API no tiene límite ni visibilidad para el usuario.** El AC dice "se reintenta más tarde" pero no especifica cuántos intentos, con qué frecuencia, ni qué pasa si sigue fallando indefinidamente — el usuario podría nunca enterarse de que una tarea nunca llegó a sincronizarse.

5. **No se menciona el rate limit de la API de Google**, que el propio PRD identifica como riesgo técnico (sección 7). Si un usuario con 200 tareas conecta Google por primera vez, la story no dice cómo se comporta la sincronización masiva inicial frente a ese límite.

Se descartaron hallazgos más "de ruido" (ej. "¿qué pasa si el usuario borra su cuenta de Google desde Google, no desde FlowSync?") por ser un caso más extremo y menos accionable para el refinamiento inmediato que los cinco de arriba.
