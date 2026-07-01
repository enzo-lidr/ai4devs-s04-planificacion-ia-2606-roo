# 🕳️ AI as poke-holes — Hallazgos sobre la sincronización con Google Calendar

> Parte 3 del ejercicio S4. Tomé la historia más completa y arriesgada del backlog —
> en su momento, **`HUSR-014` · Sincronizar tareas con fecha como eventos del
> calendario** — y le pedí a la IA que la criticara: *"identifica edge cases, supuestos
> implícitos, escenarios faltantes y dependencias o riesgos no mencionados. No
> reescribas la story, solo lista lo que falta o lo que asumiste."*
>
> Abajo, los **6 hallazgos genuinos** (descarté el ruido).
>
> ⚠️ **Nota de actualización:** tras este ejercicio, dividimos esa historia gigante en
> 6 historias de máximo 2 días (`HUSR-013` a `HUSR-018`, regla INVEST "Small") para
> respetar la granularidad recomendada. Los hallazgos siguen siendo válidos; al final
> de este documento hay un mapeo a las nuevas historias.

---

## 1. Zonas horarias: ¿a qué HORA cae el evento de una tarea con solo fecha?
La tarea tiene **fecha límite**, pero un evento de calendario necesita **hora** (o ser de día completo). La historia no define la regla de conversión. ¿La tarea sin hora se crea como evento *all-day*? ¿En la zona horaria del usuario, del servidor, o la de Google? El propio PRD §7 marca esto como riesgo y no está resuelto en ningún criterio de aceptación.
→ **Tipo:** edge case + supuesto implícito. **Impacto:** alto (eventos a horas erróneas = pérdida de confianza, justo el valor central del producto).

## 2. No hay manejo de la revocación de permisos DESDE Google
La historia cubre desconectar desde FlowSync (HUSR-013), pero no qué pasa si el usuario **revoca el acceso desde su cuenta de Google** o el **token caduca/expira**. En ese caso las llamadas a la API empezarán a fallar con error de autorización, no de disponibilidad. El "se reintenta más tarde" del Escenario 4 entraría en un bucle infinito de reintentos que nunca van a funcionar.
→ **Tipo:** escenario faltante. **Impacto:** alto (reintentos inútiles + el usuario no se entera de que dejó de sincronizar).

## 3. "Se reintenta más tarde" no tiene límites ni política definida
El Escenario 4 dice que la sync se reintenta, pero no especifica **cuántas veces**, **con qué intervalo**, ni **cuándo se considera "fallo no recuperable"**. Sin esa definición, la métrica del módulo (*"<5% de operaciones fallan de forma no recuperable"*) no se puede medir: no hay umbral que distinga "reintentando" de "falló definitivamente".
→ **Tipo:** supuesto implícito + métrica no medible. **Impacto:** medio-alto.

## 4. Orden y consistencia de operaciones rápidas (race conditions)
Si el usuario crea una tarea con fecha, cambia la fecha y la borra en pocos segundos —o lo hace sin conexión y todo se encola— los reintentos pueden ejecutarse **en orden distinto** al esperado: por ejemplo, crear el evento *después* de que la tarea ya fue borrada, dejando un evento huérfano en el calendario. La historia trata cada operación de forma aislada.
→ **Tipo:** edge case no contemplado. **Impacto:** medio (eventos fantasma que el usuario no puede borrar desde FlowSync).

## 5. ¿Qué pasa al RECONECTAR? Backfill y duplicados
Tras desconectar y volver a conectar Google (o tras un periodo largo de fallos), la historia no dice si las tareas con fecha que se crearon/editaron mientras no había conexión se **sincronizan retroactivamente**. Y si se reintenta crear un evento que en realidad ya existía, se pueden generar **eventos duplicados**. No hay criterio de idempotencia ni de reconciliación.
→ **Tipo:** escenario faltante + supuesto implícito. **Impacto:** medio-alto.

## 6. "Se marca según corresponda" es ambiguo y no verificable
El Escenario 3 dice que al completar la tarea el evento "se elimina **o** se marca según corresponda". Eso es dos comportamientos distintos sin criterio de cuál aplica. Un AC debe ser verificable: ¿completar borra el evento o lo deja con algún indicador? Tal como está, dos desarrolladores lo implementarían diferente y ambos pasarían "el criterio".
→ **Tipo:** criterio de aceptación ambiguo (no testeable). **Impacto:** medio.

---

## 📌 Lectura rápida

| # | Hallazgo | Categoría | Impacto |
|---|----------|-----------|:-------:|
| 1 | Regla de zona horaria / hora del evento sin definir | Edge case + supuesto | Alto |
| 2 | Revocación de permisos desde Google no contemplada | Escenario faltante | Alto |
| 3 | "Reintenta más tarde" sin política ni umbral de fallo | Supuesto + métrica no medible | Medio-alto |
| 4 | Race conditions en operaciones rápidas → eventos huérfanos | Edge case | Medio |
| 5 | Reconexión: backfill y eventos duplicados (idempotencia) | Escenario faltante | Medio-alto |
| 6 | "Se elimina o se marca" — AC ambiguo, no verificable | AC no testeable | Medio |

---

## 🔗 Mapeo a las historias divididas (post-división)

| # Hallazgo | Pasó a vivir en | Estado en el backlog actual |
|---|---|---|
| 1 · Zona horaria / hora del evento | `HUSR-015` (crear evento) | **No cubierto** — señalado como vacío de información en sus Notas adicionales, marcado para spike; no resuelto. |
| 2 · Revocación de permisos desde Google | `HUSR-013` / `HUSR-018` / `HUSR-014` | **Parcialmente resuelto** — el Escenario 4 de `HUSR-018` distingue el error de autorización del de disponibilidad y evita el bucle de reintentos inútiles para *esa operación de sync*. Pero qué pasa con el **estado de conexión de la cuenta** cuando se detecta la revocación en background sigue sin decidirse — señalado como "Vacío de información 2" en las Notas de `HUSR-014` tras una segunda auditoría. |
| 3 · Política de reintentos sin umbral | `HUSR-018` (manejo de fallos) | **Resuelto** — se fijó un umbral concreto *(asumido)*: 3 reintentos con backoff 5/15/60 min; el Escenario 3 ya es testeable y la métrica <5% ya es medible. |
| 4 · Race conditions / eventos huérfanos | `HUSR-016`, `HUSR-017` | **No cubierto** — ninguna historia define el orden de reintentos concurrentes. Confirmado en dos auditorías independientes. |
| 5 · Backfill y duplicados al reconectar | `HUSR-014` (desconectar) | **No cubierto** — las Notas adicionales de `HUSR-014` (no el Escenario 2, que es afirmativo y no deja nada abierto) dejan señalado qué pasa con los eventos *al desconectar*, pero el backfill/duplicados *al reconectar* — que es el hallazgo original — no tiene ningún escenario que lo trate. |
| 6 · "Se elimina o se marca" ambiguo | `HUSR-017` | **Parcialmente resuelto** — acotado a que la ambigüedad vive **solo en el Escenario 1** (completar); los Escenarios 2 (borrar) y 3 (archivar) tienen un único resultado y no la comparten. La ambigüedad del Escenario 1 en sí sigue sin resolverse a propósito (no se inventa la regla que el PRD no da). |

**Lectura:** dividir la historia gigante en 6 más chicas no resolvió los hallazgos por sí sola — los hizo más **visibles y rastreables** (cada hueco vive ahora en una historia concreta en vez de perderse dentro de una historia de 5 días). Dos auditorías posteriores resolvieron por completo el hallazgo 3 y acotaron el 2 y el 6 a su alcance real; los hallazgos 1, 4 y 5 siguen **sin ninguna historia que los cubra** y son los candidatos más claros para la siguiente ronda de refinamiento.

**Conclusión:** los hallazgos 1, 2 y 5 refuerzan la recomendación del PRD §7 de hacer un **spike técnico antes de comprometer la decomposición fina de MOD-005**. Ninguno invalida la historia, pero todos deben resolverse en refinamiento antes de estimar en firme.
