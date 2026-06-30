# Poke-holes — Story: Creación de eventos a partir de tareas con fecha límite

## Hallazgo 1 — ¿La fecha límite incluye hora o solo fecha?

El PRD dice que una tarea tiene "fecha límite opcional" pero nunca
aclara si esa fecha incluye hora o no. Esto es importante porque
determina si el evento que se crea en Google Calendar es de todo el
día o un evento con horario específico. La story asume que "se ubica
en la zona horaria correcta" pero ni siquiera define qué tipo de
evento se está creando. Es una decisión de diseño que afecta a todo
lo demás y nadie la tomó todavía.

## Hallazgo 2 — Token revocado desde Google, no desde FlowSync

El scenario de error solo cubre cuando la API de Google no está
disponible (fallo temporal). Pero si el usuario va a su cuenta de
Google y revoca el acceso manualmente desde ahí, el error que
devuelve la API no es transitorio — es de autorización. En ese
caso reintentar no sirve de nada. FlowSync necesitaría detectar
ese tipo de error específico y pedirle al usuario que reconecte
su cuenta, pero eso no está cubierto en ningún scenario.

## Hallazgo 3 — Nadie guarda el ID del evento de Google

Para poder actualizar o borrar un evento después, FlowSync necesita
saber qué evento de Google Calendar corresponde a cada tarea. Eso
significa que al crear el evento hay que guardar su ID en algún lado.
Pero eso no aparece en esta story ni en ninguna otra del backlog. Sin
ese dato, las stories de actualización y borrado de evento no se
pueden implementar — asumen que "el evento correspondiente" existe
pero no dicen cómo el sistema lo encuentra.

## Hallazgo 4 — ¿Qué pasa cuando se agotan los reintentos?

La story dice que si la API de Google falla, "el sistema reintenta
la sincronización más tarde". Pero no define cuántas veces reintenta,
cada cuánto tiempo, ni qué pasa si todos los intentos fallan. ¿El
usuario ve que su tarea no está sincronizada? ¿Se le avisa de alguna
forma? ¿Queda en un estado roto sin saberlo? Ese comportamiento
final necesita definirse porque es lo que el usuario va a vivir
cuando las cosas salen mal.

## Hallazgo 5 — El reintento puede crear eventos duplicados

Si el evento se crea en Google pero la respuesta llega con timeout
(FlowSync no sabe si se creó o no), el sistema va a reintentar y
podría crear un segundo evento para la misma tarea. La story no tiene
ningún scenario que cubra esto. En la práctica significaría que el
usuario ve su tarea duplicada en el calendario, y eso es peor que
no verla.