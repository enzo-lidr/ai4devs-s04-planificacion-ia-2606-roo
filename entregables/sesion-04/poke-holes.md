# Poke-holes — Análisis crítico

**Story analizada:** #11 *"Reflejar y actualizar tareas con fecha como eventos de
Google Calendar"* (la de AC más completos a primera vista, y el núcleo
diferenciador y más arriesgado del MVP).

Le pedí a la IA que criticara su propia story (sin reescribirla, solo listar lo
que falta o asume). Devolvió ~24 observaciones; abajo van las **5 genuinas y no
triviales** tras descartar el ruido.

---

### 1. El AC "se elimina o se marca según corresponda" no decide nada — y Google Calendar no tiene concepto de "hecho"
El tercer criterio junta **completar** y **borrar** en la misma frase con un
"según corresponda" que no es verificable. Además, un evento de Google Calendar
**no tiene estado "completado"**: hay que elegir entre borrarlo, prefijar el
título (`✓ …`), cambiar el color o poner `status: cancelled`. Y completar y
borrar probablemente quieren comportamientos **distintos** (una tarea completada
quizá se conserva como registro; una borrada se elimina). La story heredó la
propia ambigüedad del PRD en vez de resolverla.

### 2. El estado `archived` queda fuera de la sincronización
FlowSync tiene tres estados (`pending`/`completed`/`archived`, story #6), pero la
#11 solo razona sobre `completed` y borrada. **¿Archivar una tarea sincronizada
borra su evento, lo conserva o lo marca?** Hueco directo entre dos stories del
mismo backlog. Lo mismo con **reabrir** una tarea completada → `pending`: si al
completar se borró el evento, reabrir debe recrearlo, y eso no está cubierto.

### 3. Falta el vínculo tarea↔evento en el modelo de datos (`google_event_id`)
El modelo de tarea del PRD (título, descripción, estado, fecha) **no tiene ningún
campo para guardar el ID del evento de Google** ni el calendario destino. Sin
`google_event_id`/`calendar_id` persistidos, **actualizar o borrar el evento
correcto es literalmente imposible** — los AC 2 y 3 no son implementables de forma
fiable. Es un cambio de esquema que ninguna story menciona.

### 4. Quitar la fecha de una tarea ya sincronizada deja un evento huérfano
La #11 cubre *crear* evento (tiene fecha) y *no crear* (sin fecha), pero **no el
caso de transición**: una tarea que tenía fecha (y evento) y a la que el usuario
**le quita la fecha**. El AC4 dice "sin fecha → no se genera evento", pero no dice
**borrar el evento existente**. Resultado: un evento fantasma en el calendario que
ya no corresponde a ninguna tarea. La story #5 (editar) tampoco lo contempla.

### 5. "Fecha límite": ¿evento de día completo o con hora? + zona horaria sin definir
El PRD marca esto como **no resuelto**, y condiciona todo lo demás. Si la fecha es
solo día → evento *all-day* (sin zona horaria, sin líos). Si lleva hora → el
evento necesita inicio/fin (¿duración por defecto?) y se ancla a una **zona
horaria** que el modelo de usuario/tarea **no almacena**. Cerca de medianoche, un
desfase de tz crea el evento **el día equivocado** — el riesgo que el propio PRD
señala, sin mitigación en la story.

---

> **Los 3 que desbloquearía antes de estimar:** (1) definir el AC3 separando
> completar/archivar/borrar; (2) decidir all-day vs. con-hora + zona horaria
> (condiciona el modelo de datos); (3) añadir `google_event_id`/`calendar_id` al
> esquema con idempotencia, sin lo cual actualizar/borrar eventos no es fiable.
