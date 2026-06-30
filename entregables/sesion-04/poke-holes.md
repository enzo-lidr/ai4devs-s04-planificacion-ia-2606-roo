# Poke-holes — Crítica a HDU-012 (Sincronización de tareas como eventos)

> Patrón "AI as poke-holes": se le pide a la IA que critique una de sus propias
> historias. Objetivo: edge cases, supuestos implícitos, escenarios faltantes y
> dependencias/riesgos no mencionados. **No se reescribe la story.**
> Story analizada: **HDU-012**.

---

## 🔴 Top 5 hallazgos no triviales

### 1. No hay *backfill* al conectar Google — rompe el flujo y la métrica del 40%
La story solo crea eventos para tareas creadas/editadas **después** de conectar
(Escenario 1). Pero el usuario objetivo del PRD **ya gestiona tareas** y conecta
Google *después* de empezar a usar FlowSync. ¿Qué pasa con sus tareas con fecha
ya existentes en el momento de conectar? Hoy: **nada**, no aparecen en el
calendario. Eso contradice la propuesta de valor ("ver mis tareas reflejadas como
eventos") y ataca directamente la métrica de éxito **≥40% conectan Google** (PRD
§6): el usuario conecta, no ve nada, y abandona. **Falta escenario de
sincronización inicial de tareas preexistentes.**

### 2. Reintento sin idempotencia → eventos duplicados
"La operación se reintenta más tarde" (Escenario 4) asume que reintentar es
seguro. No lo es: si la llamada a Google **sí creó el evento** pero se perdió la
respuesta (timeout, 5xx tras commit), el reintento crea un **segundo evento
duplicado**. No se menciona clave de idempotencia, almacenamiento del `eventId`
devuelto, ni deduplicación. Sin esto, el reintento *empeora* la fiabilidad en
lugar de mejorarla.

### 3. "Token expirado/revocado" no está cubierto y es distinto de "no conectado"
Los escenarios contemplan "API caída" (4) y "sin conectar" (6), pero **no** el
caso real más frecuente: el **access token caduca** (OAuth siempre expira) o el
usuario **revoca el acceso desde Google** sin pasar por FlowSync. ¿Se refresca el
token automáticamente? ¿Si el refresh falla, se trata como fallo recuperable
(reintento infinito que nunca funcionará) o se marca la cuenta como desconectada
y se avisa al usuario? Sin definirlo, estas tareas entran en bucle de reintento.

### 4. "Fallo no recuperable" no está definido → la métrica <5% no es verificable
El criterio de aceptación fija "<5% de operaciones fallan de forma no
recuperable", pero **no define cuándo una operación pasa de "reintentando" a "no
recuperable"** (¿nº máximo de intentos? ¿ventana de tiempo? ¿dead-letter?). Un
criterio que no se puede medir no se puede aceptar. Además **no hay escenario de
observabilidad** (los logs se mencionan, pero nada permite *medir* esa tasa ni
**avisar al usuario** de que un evento no se creó). El usuario cree que su tarea
está en el calendario y no lo está: fallo silencioso.

### 5. Concurrencia y orden de operaciones — un reintento puede "resucitar" un evento borrado
No hay tratamiento del orden entre operaciones de una misma tarea. Caso real:
el usuario cambia la fecha (lanza *update*, falla, queda en cola de reintento) y
acto seguido **borra** la tarea (lanza *delete*, tiene éxito). Cuando el *update*
se reintenta, **recrea el evento que ya se había borrado** → evento fantasma. Lo
mismo si se completa y reabre rápido. Falta garantizar orden/cancelación de
operaciones encoladas por tarea.

---

## 🟡 Supuestos implícitos (no literales en el PRD ni en la story)

- **Mapeo tarea↔evento:** se asume que FlowSync **guarda el `eventId` de Google
  por tarea**. Sin ese dato, los Escenarios 2 y 3 (actualizar/borrar el "evento
  correspondiente") son imposibles. Nunca se declara como requisito.
- **Infra de cola/worker en background:** "se reintenta más tarde" asume un
  sistema de jobs/cola asíncrono. AdonisJS + SQLite no trae una cola robusta de
  serie → es una **dependencia técnica nueva no listada** (¿qué cola? ¿persistente
  entre reinicios del proceso?).
- **Calendario destino:** se asume el calendario **primario** del usuario. No se
  define qué pasa si tiene varios calendarios ni se le deja elegir.
- **Contenido del evento:** se asume que el evento refleja el **título** de la
  tarea. No se define si la **descripción** (posiblemente sensible) se vuelca al
  calendario — implicación de privacidad no discutida.
- **Evento "todo el día":** la propuesta de TZ es un supuesto del PO, sin validar
  (pendiente de spike).

## 🟠 Escenarios faltantes

- **Editar título/descripción** de una tarea **con evento**: el Escenario 2 solo
  cubre cambio de **fecha**. Si cambio el título, ¿se actualiza el evento? No
  cubierto.
- **Archivar** una tarea con evento: HDU-008 contempla `archived`, pero aquí solo
  se trata "completar o borrar". ¿Archivar elimina el evento? Indefinido.
- **Quitar la fecha límite** de una tarea con evento (existe el inverso en HDU-006
  como *asumido*, pero aquí no hay escenario): ¿se borra el evento?
- **Fecha límite en el pasado** (tarea vencida): ¿se crea un evento en una fecha
  pasada? ¿Se omite? Sin regla.
- **Evento borrado/movido manualmente en Google** por el usuario: el siguiente
  *update* de FlowSync intentará actualizar un evento inexistente → error 404.
  ¿Se recrea? ¿Se ignora? Aunque la sync inversa esté fuera de alcance, **esta
  realidad ocurre igual** y la story no la maneja.
- **Rate limit (HTTP 429)** de Google: se menciona como riesgo pero no hay
  escenario; ¿429 cuenta como recuperable? ¿hay backoff/batching?
- **Desconexión deja eventos huérfanos:** HDU-011 dice que al desconectar no se
  borran las tareas, pero **nada dice de los eventos ya creados en Google**:
  quedan huérfanos para siempre (nunca se actualizan ni borran). Y al
  **reconectar**, ¿se vuelven a crear duplicados de los que ya existen?

## ⚠️ Dependencias y riesgos no mencionados

- **Dependencia dura de HDU-011** para el ciclo de vida del token (refresh,
  revocación). El éxito de HDU-012 depende de cómo HDU-011 gestione el *refresh
  token*, que tampoco está detallado allí.
- **El spike técnico (PRD §7) bloquea la estimación**: con zonas horarias,
  recurrentes y "todo el día" sin resolver, la story **no es estimable de forma
  fiable** todavía. Riesgo de compromiso de alcance sobre incertidumbre.
- **Testeo del DoD (>90% cobertura) contra una API externa**: requiere
  mocks/sandbox de Google; tests de integración propensos a *flakiness*. No
  trivial y no contemplado.
- **"Responsive design para desktop y móvil" en el DoD encaja mal**: HDU-012 es
  mayoritariamente backend/asíncrona. Si hay UI (indicador de estado de sync),
  **no existe escenario que la describa**; si no la hay, el ítem del DoD sobra.
- **Privacidad/consentimiento del volcado de datos** de la tarea hacia Google
  (más allá del OAuth): no se trata.


## Hallazgos geninos

Teneindo en cuenta lo que seria una situacion real pueden ser los siguientes basadao en la respuesta que la IA me dijo me quedo con:
1. No hay sincronización inicial al conectar Google (Deberia ser forzoza?)
2. El reintento puede crear eventos duplicados, no hay certeza en la comunicaicon.
3. Token caducado o revocado dieferente a no conectado
4. El Fallo no recuperable no está definido entra en el manejo de errores.
