# AI as poke-holes — US-SYNC-02 (Crear evento al tener fecha límite)

Story analizada: **US-SYNC-02**, la de AC más completos a primera vista. Pedí a la IA que listara edge cases, supuestos implícitos, escenarios faltantes y riesgos no mencionados, sin reescribir la story.

## Hallazgos genuinos

1. **Zona horaria sin definir → eventos a la hora equivocada.** La story dice "tarea con fecha límite → evento", pero una fecha límite es típicamente un *día* y un evento de calendario tiene *hora de inicio/fin*. No se define si se crea un evento de todo el día o a una hora concreta, ni en qué zona horaria. El propio PRD marca esto como riesgo (sección 7) y la story lo ignora. Es un gap, no un detalle.

2. **¿Qué pasa si la tarea YA estaba sincronizada y se le quita la fecha límite?** Los AC cubren crear evento al *añadir* fecha y no crear si *no hay* fecha, pero no el caso de **editar una tarea para borrar su fecha límite**: ¿se elimina el evento existente? Escenario faltante que cae entre US-SYNC-02 y US-SYNC-03.

3. **Reintento sin política definida = supuesto implícito peligroso.** "Se reintenta más tarde" no dice cuántas veces, con qué backoff, ni qué ve el usuario mientras tanto (¿indicador de "sin sincronizar"?). Sin esto, el AC es no verificable y choca con el criterio de éxito del PRD (<5% de fallos no recuperables): no hay forma de medir "no recuperable".

4. **Rate limits de Google no contemplados.** El PRD avisa de rate limits (riesgo principal). Si el usuario crea/edita muchas tareas seguidas (alta plausible: import inicial de 30 tareas), se puede superar la cuota. La story trata "API falla" de forma genérica pero no distingue error transitorio (429, reintentar) de error permanente (403/auth revocada, no reintentar) — distinción clave para no reintentar en bucle.

5. **Token OAuth expirado / revocado a mitad de sesión.** Supuesto implícito: que la conexión sigue válida. No se cubre el caso de **refresh token caducado o permiso revocado desde la consola de Google**. ¿La sincronización falla silenciosamente? ¿Se avisa al usuario para reconectar? Dependencia con US-SYNC-01 no explicitada.

## Bonus (menor, no contado en los 5)

- No se define la **idempotencia**: si el reintento se ejecuta dos veces, ¿se crean dos eventos duplicados? Falta un identificador de mapeo tarea↔evento.
