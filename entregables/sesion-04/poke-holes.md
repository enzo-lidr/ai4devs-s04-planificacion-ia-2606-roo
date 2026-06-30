# Poke-holes — crítica de la Story 15 (GCal)

**Story analizada:** *GCal · Story 15 — "que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar".*

**Prompt usado para la crítica:**
> Eres un tech lead escéptico revisando esta story antes de meterla en el sprint. No la defiendas: busca huecos, asunciones implícitas que nadie ha validado y casos límite reales que romperían la implementación. Para cada hallazgo, di por qué es un problema y qué decisión humana falta. Ignora lo trivial.

A continuación, los hallazgos no triviales de esa crítica.

---

## 1. La story confunde "fecha" con "evento", y un evento necesita hora

**El hueco:** una tarea tiene `due_date` (una fecha límite), pero un evento de Google Calendar es, o bien un *evento de todo el día*, o bien un *evento con hora de inicio y fin*. La story dice "se crea un evento en su Google Calendar en la fecha indicada" sin resolver cuál de los dos. No es un detalle de implementación: cambia el modelo de datos (¿guardamos solo fecha o fecha+hora?), la UX del formulario de tarea (¿pedimos hora?) y el comportamiento de la API (`events.insert` con `date` vs. `dateTime`).

**Por qué importa:** el AC de zona horaria (el tercero) presupone que el evento tiene una hora ("no en una hora errónea por desfase UTC"), pero si los eventos son de todo el día, la zona horaria casi no aplica. Los dos AC se contradicen entre sí: uno asume timed, la decisión de producto aún no existe.

**Decisión humana que falta:** ¿los eventos generados son all-day o tienen una hora por defecto (p. ej. 9:00)? Si tienen hora, ¿de dónde sale?

---

## 2. El AC de zona horaria no es testable: FlowSync no captura la zona del usuario

**El hueco:** el tercer AC dice que el evento se sitúa "según la zona horaria definida (asumido: pendiente de spike)". Pero al revisar Auth · Story 1 (registro), **no existe ningún campo de zona horaria del usuario** — el registro solo pide email y contraseña. Así que el dato del que depende este AC no se recoge en ninguna parte del backlog.

**Por qué importa:** viola la propia restricción del prompt de que cada AC sea "directamente traducible a un test". Este AC no se puede testear porque su entrada (la timezone) no está definida ni capturada. Es un AC fantasma que parece cubrir el riesgo de zonas horarias del PRD, pero en realidad lo esconde.

**Decisión humana que falta:** ¿de dónde sale la zona horaria? ¿Del navegador en el momento de crear la tarea, de un campo en el perfil, de la timezone del calendario de Google del usuario? Cada opción es una sub-tarea distinta que ahora mismo no existe.

---

## 3. Las tareas que ya existían al conectar Google quedan en tierra de nadie

**El hueco:** la Story 15 solo describe el flujo "el usuario *crea* una tarea con fecha estando conectado". Pero el caso realista es el inverso: el usuario primero usa FlowSync, acumula 15 tareas con fecha, y *después* conecta Google (Story 14). Ni la Story 14 (que solo "establece la conexión, no sincroniza tareas todavía") ni la 15 cubren qué pasa con esas tareas preexistentes.

**Por qué importa:** es justo el flujo del criterio de éxito del PRD ("al menos el 40% conectan su Google Calendar" — gente que ya se registró y usó la app antes de conectar). Si al conectar no se sincronizan las tareas que ya tienen fecha, el usuario conecta Google y *no ve nada* en su calendario, y concluye que no funciona. Hay un agujero entre dos historias que ninguna reclama como suyo.

**Decisión humana que falta:** ¿conectar Google dispara una sincronización retroactiva de las tareas con fecha ya existentes? Si sí, eso es una story propia (backfill), con su propio manejo de volumen y rate limits.

---

## 4. Riesgo de eventos duplicados: no hay clave de idempotencia

**El hueco:** la Story 15 guarda el `google_event_id` *después* de que Google confirme la creación. Pero combínalo con la Story 18 (reintentos): si FlowSync llama a `events.insert`, Google **crea el evento** pero la respuesta se pierde por timeout, FlowSync lo interpreta como fallo, marca la sync como pendiente y reintenta → **segundo evento duplicado**, porque todavía no había `google_event_id` guardado para detectar que ya existía.

**Por qué importa:** es un caso límite real y silencioso (el usuario ve eventos duplicados en su calendario), exactamente el tipo de fallo que erosiona la confianza en la feature diferenciadora. El AC "menos del 5% de sincronizaciones fallan" del PRD no captura esto, porque desde el punto de vista de FlowSync el reintento "tuvo éxito".

**Decisión humana que falta:** usar una clave de idempotencia (el `requestId`/`id` que admite la API de Google en `events.insert`) derivada de la tarea, para que un reintento no cree un duplicado. Hay que decidirlo *antes* de implementar la Story 18.

---

## 5. Asunción implícita: el scope OAuth concedido permite escribir

**El hueco:** la story asume que, una vez conectado (Story 14), FlowSync puede escribir en el calendario. Pero OAuth con Google permite que el usuario conceda un subconjunto de permisos, o que se conceda un scope de solo lectura. La Story 14 verifica que "se almacenan los tokens", no que esos tokens incluyan permiso de escritura sobre el calendario.

**Por qué importa:** si el scope concedido no permite escritura, *toda* la Story 15 falla en tiempo de ejecución con un 403 de Google, pero el usuario aparece como "conectado". Ningún AC de la 14 ni de la 15 cubre el caso "conectado pero sin permiso de escritura", así que el fallo se manifestaría como sincronizaciones que fallan en bucle (alimentando la Story 18) sin causa diagnosticable para el usuario.

**Decisión humana que falta:** ¿verificamos los scopes concedidos en el callback de OAuth (Story 14) y bloqueamos/avisamos si falta el de escritura, en lugar de descubrirlo al fallar la primera sincronización?

---

## Síntesis

De los cinco hallazgos, dos (1 y 2) revelan que el manejo de zonas horarias —el riesgo nº 1 del PRD— está **aparentemente cubierto pero realmente vacío**: la story tiene ACs que lo mencionan sin que exista el dato ni la decisión de modelo subyacente. Dos (3 y 4) son agujeros *entre* historias, invisibles si se revisa cada story por separado. El quinto (5) es una asunción de permisos que solo se ve si cruzas la Story 14 con la 15. Ninguno se detecta leyendo la story de forma aislada y dándola por buena: todos requieren cruzarla con otras historias o con el dominio real de la API de Google.
