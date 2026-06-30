### Auth · Story 1

**Como** visitante no autenticado,
**quiero** crear una cuenta con email y contraseña,
**para** tener un espacio propio donde gestionar mis tareas.

**Acceptance Criteria:**

* Given el visitante rellena un email válido y una contraseña de 8 o más caracteres
* When envía el formulario de registro
* Then se crea la cuenta y el usuario queda autenticado y es redirigido a la pantalla de onboarding


* Given el visitante introduce una contraseña de 7 caracteres o menos
* When intenta enviar el formulario
* Then aparece el mensaje "La contraseña debe tener al menos 8 caracteres" y no se llama al servidor


* Given el visitante introduce un email que ya está registrado
* When envía el formulario
* Then aparece el mensaje "Este email ya está registrado" junto a un enlace a inicio de sesión, y no se crea ninguna cuenta

**Contexto técnico:** `POST /api/auth/register` — `app/controllers/auth_controller.ts`, validación con VineJS (email + min 8 caracteres), hash de contraseña y emisión de access token con `@adonisjs/auth`, tabla `users` en SQLite vía Lucid. Frontend: formulario React 19 con validación de longitud en cliente antes de enviar (asumido).

**Non-goals:**
- No incluye verificación de email por enlace (fuera de alcance MVP)
- No incluye login social ni "recordar contraseña"
- No incluye confirmación de contraseña repetida (asumido, no está en el PRD)

---

### Auth · Story 2

**Como** usuario registrado,
**quiero** iniciar sesión con mi email y contraseña,
**para** acceder a mis tareas desde cualquier dispositivo.

**Acceptance Criteria:**

* Given un usuario con cuenta existente introduce su email y contraseña correctos
* When envía el formulario de inicio de sesión
* Then se emite un access token, queda autenticado y es redirigido al listado de tareas


* Given un usuario introduce un email registrado pero con contraseña incorrecta
* When envía el formulario
* Then aparece el mensaje "Email o contraseña incorrectos" sin revelar cuál de los dos campos falló


* Given un usuario introduce un email que no está registrado
* When envía el formulario
* Then aparece el mismo mensaje genérico "Email o contraseña incorrectos" y no se emite ningún token

**Contexto técnico:** `POST /api/auth/login` — `app/controllers/auth_controller.ts`, verificación de credenciales y emisión de access token con `@adonisjs/auth`. Frontend almacena el token para autenticar peticiones posteriores (asumido: localStorage o header Authorization).

**Non-goals:**
- No incluye recuperación de contraseña
- No incluye bloqueo por intentos fallidos (asumido, no está en el PRD)
- No incluye autenticación multifactor

---

### Auth · Story 3

**Como** usuario autenticado,
**quiero** cerrar sesión,
**para** dejar mi cuenta protegida en dispositivos compartidos.

**Acceptance Criteria:**

* Given un usuario autenticado con un access token válido
* When pulsa "Cerrar sesión"
* Then el token se invalida en el servidor y el usuario es redirigido a la pantalla de inicio de sesión


* Given un usuario que acaba de cerrar sesión
* When intenta acceder a una ruta protegida (p. ej. el listado de tareas) con el token ya invalidado
* Then la API responde 401 y el frontend lo redirige a inicio de sesión


* Given una petición de cierre de sesión sin token o con token ya inválido
* When llega al servidor
* Then la API responde 401 sin alterar ningún estado

**Contexto técnico:** `POST /api/auth/logout` — `app/controllers/auth_controller.ts`, revocación del access token actual con `@adonisjs/auth`. Middleware de autenticación de AdonisJS protege las rutas privadas.

**Non-goals:**
- No incluye cierre de sesión en todos los dispositivos a la vez
- No incluye expiración automática por inactividad (asumido)
- No borra datos locales más allá del token

---

### Auth · Story 4

**Como** usuario recién registrado,
**quiero** ver una pantalla de bienvenida que explique qué hace FlowSync,
**para** entender el producto y crear mi primera tarea sin fricción.

**Acceptance Criteria:**

* Given un usuario que acaba de completar el registro con éxito
* When es redirigido tras el registro
* Then ve una pantalla de onboarding con una frase que explica qué hace FlowSync y un botón para crear su primera tarea


* Given un usuario en la pantalla de onboarding
* When pulsa el botón "Crear mi primera tarea"
* Then es llevado al formulario de creación de tarea


* Given un usuario que ya pasó por el onboarding e inicia sesión de nuevo
* When entra en la aplicación
* Then va directo al listado de tareas y no vuelve a ver el onboarding (asumido)

**Contexto técnico:** Vista React 19 servida tras el registro; el flujo de "no repetir onboarding" requiere una marca por usuario (asumido: campo `onboarded_at` en `users` o equivalente — necesita revisión en refinamiento).

**Non-goals:**
- No incluye tour guiado multi-paso
- No incluye vídeo ni tutorial interactivo
- No incluye personalización del mensaje de bienvenida

---

⚠ **Asunciones a validar (Auth):**
- Cómo se almacena el access token en el cliente (localStorage vs. header) — Story 2.
- Confirmación de contraseña repetida en el registro — Story 1.
- Bloqueo/limitación por intentos fallidos de login — Story 2.
- Expiración de sesión por inactividad — Story 3.
- Mecanismo para no repetir el onboarding (campo en `users`) — Story 4.

---

### Tasks · Story 5

**Como** usuario autenticado,
**quiero** crear una tarea indicando al menos un título,
**para** registrar algo que tengo que hacer.

**Acceptance Criteria:**

* Given un usuario autenticado en el formulario de creación
* When introduce un título y envía el formulario (con descripción y fecha límite vacías)
* Then se crea la tarea con estado `pending` y aparece en el listado


* Given un usuario que envía el formulario con el título vacío
* When intenta crear la tarea
* Then aparece el mensaje "El título es obligatorio" y no se crea ninguna tarea


* Given un usuario que introduce título, descripción y una fecha límite válida
* When envía el formulario
* Then se crea la tarea con esos tres campos y estado `pending`

**Contexto técnico:** `POST /api/tasks` — `app/controllers/tasks_controller.ts`, validación VineJS (título requerido, descripción y fecha opcionales), modelo `Task` en Lucid con `user_id`, `title`, `description`, `status`, `due_date`. La tarea nace en `pending`.

**Non-goals:**
- No dispara sincronización con Google Calendar (cubierto en épica GCal)
- No incluye etiquetas, proyectos ni subtareas (out of scope MVP)
- No incluye adjuntos ni recordatorios propios

---

### Tasks · Story 6

**Como** usuario autenticado,
**quiero** ver el listado de mis tareas,
**para** saber qué tengo pendiente.

**Acceptance Criteria:**

* Given un usuario autenticado con tareas creadas
* When abre el listado de tareas
* Then ve únicamente sus propias tareas, mostrando al menos título, estado y fecha límite de cada una


* Given un usuario A que está autenticado
* When solicita el listado de tareas
* Then no aparece ninguna tarea perteneciente a otro usuario B


* Given un usuario con hasta 200 tareas
* When carga el listado
* Then la respuesta se completa en menos de 1 segundo

**Contexto técnico:** `GET /api/tasks` — `app/controllers/tasks_controller.ts`, consulta Lucid filtrada por `user_id` del usuario autenticado. El requisito de <1s con 200 tareas exige índice por `user_id` (asumido).

**Non-goals:**
- No incluye paginación (asumido: el MVP asume 5-30 tareas activas, máx. 200)
- No incluye búsqueda por texto libre
- No incluye filtrado (cubierto en épica Filter)

---

### Tasks · Story 7

**Como** usuario autenticado,
**quiero** editar cualquier campo de una tarea existente,
**para** corregir o actualizar su información.

**Acceptance Criteria:**

* Given un usuario autenticado dueño de una tarea
* When edita el título, la descripción o la fecha límite y guarda
* Then la tarea se actualiza con los nuevos valores y se reflejan en el listado


* Given un usuario que borra el título al editar y deja el campo vacío
* When intenta guardar
* Then aparece el mensaje "El título es obligatorio" y la tarea conserva sus valores anteriores


* Given un usuario que intenta editar una tarea que no le pertenece o que no existe
* When envía la petición de edición
* Then la API responde 404 (o 403) y no se modifica ninguna tarea

**Contexto técnico:** `PUT /api/tasks/:id` (o `PATCH`) — `app/controllers/tasks_controller.ts`, validación VineJS y comprobación de propiedad (`task.user_id === auth.user.id`) antes de actualizar. Cambiar la fecha límite puede disparar sincronización (ver épica GCal).

**Non-goals:**
- No incluye historial de cambios ni versionado
- No gestiona la sincronización del cambio de fecha (cubierto en GCal)
- No incluye edición masiva de varias tareas

---

### Tasks · Story 8

**Como** usuario autenticado,
**quiero** borrar una tarea,
**para** quitar de mi lista algo que ya no es relevante.

**Acceptance Criteria:**

* Given un usuario autenticado dueño de una tarea
* When confirma el borrado de esa tarea
* Then la tarea se elimina y deja de aparecer en el listado


* Given un usuario que pulsa "borrar"
* When aparece el diálogo de confirmación y el usuario cancela
* Then la tarea no se borra y permanece en el listado


* Given un usuario que intenta borrar una tarea que no le pertenece o ya no existe
* When envía la petición
* Then la API responde 404 (o 403) y no se borra ninguna tarea

**Contexto técnico:** `DELETE /api/tasks/:id` — `app/controllers/tasks_controller.ts`, comprobación de propiedad antes de borrar. El borrado puede requerir eliminar el evento asociado en Google Calendar (ver épica GCal). Diálogo de confirmación en cliente (asumido).

**Non-goals:**
- No incluye papelera ni borrado reversible (soft delete) (asumido)
- No gestiona la eliminación del evento en Google (cubierto en GCal)
- No incluye borrado en lote

---

### Tasks · Story 9

**Como** usuario autenticado,
**quiero** cambiar el estado de una tarea entre `pending`, `completed` y `archived`,
**para** reflejar en qué punto está cada pendiente.

**Acceptance Criteria:**

* Given una tarea en estado `pending`
* When el usuario la marca como completada
* Then su estado pasa a `completed` y el cambio se refleja en el listado


* Given una tarea ya `completed`
* When el usuario la archiva
* Then su estado pasa a `archived` y deja de mostrarse en la vista de pendientes por defecto


* Given una petición de cambio de estado a un valor no permitido (distinto de `pending`, `completed`, `archived`)
* When llega al servidor
* Then la API rechaza la petición con un error de validación y el estado no cambia

**Contexto técnico:** `PATCH /api/tasks/:id/status` (asumido) — `app/controllers/tasks_controller.ts`, validación VineJS con enum `['pending','completed','archived']` y comprobación de propiedad. Completar una tarea puede afectar a su evento en Google Calendar (ver épica GCal).

**Non-goals:**
- No incluye estados personalizados por el usuario (out of scope MVP)
- No gestiona la sincronización del estado con Google (cubierto en GCal)
- No define transiciones prohibidas entre estados (asumido: cualquier transición permitida)

---

⚠ **Asunciones a validar (Tasks):**
- Necesidad de índice por `user_id` para cumplir el <1s con 200 tareas — Story 6.
- Ausencia de paginación dado el volumen esperado — Story 6.
- Diálogo de confirmación y borrado físico vs. soft delete — Story 8.
- Endpoint y forma del cambio de estado (`PATCH /status` vs. edición general) — Story 9.
- Si se permiten todas las transiciones de estado o hay restricciones — Story 9.

---

### Filter · Story 10

**Como** usuario autenticado,
**quiero** filtrar mis tareas por estado,
**para** centrarme solo en las pendientes, completadas o archivadas según necesite.

**Acceptance Criteria:**

* Given un usuario con tareas en varios estados
* When selecciona el filtro "pendientes"
* Then el listado muestra únicamente las tareas en estado `pending`


* Given un usuario que selecciona el filtro "completadas" y no tiene ninguna tarea en ese estado
* When se aplica el filtro
* Then ve un estado vacío específico del filtro (p. ej. "No tienes tareas completadas")


* Given una petición de filtrado con un valor de estado no válido
* When llega al servidor
* Then la API responde con error de validación y no devuelve resultados arbitrarios

**Contexto técnico:** `GET /api/tasks?status=pending|completed|archived` — `app/controllers/tasks_controller.ts`, validación del query param con VineJS y consulta Lucid filtrada por `user_id` + `status`. Frontend con control de filtro (asumido: tabs o select).

**Non-goals:**
- No incluye filtros combinados ni por rango de fechas (asumido)
- No incluye búsqueda por texto
- No persiste el filtro elegido entre sesiones (asumido)

---

### Filter · Story 11

**Como** usuario autenticado,
**quiero** que el listado aparezca ordenado mostrando primero lo más relevante para hoy,
**para** ver de un vistazo qué debo atender sin reordenar manualmente.

**Acceptance Criteria:**

* Given un usuario con tareas con distintas fechas límite
* When abre el listado sin aplicar ningún orden manual
* Then las tareas se muestran según el criterio de relevancia "hoy" definido en refinamiento (asumido: por fecha límite ascendente, las sin fecha al final)


* Given un usuario con tareas sin fecha límite y tareas con fecha
* When carga el listado
* Then las tareas con fecha aparecen antes que las que no tienen fecha, en un orden estable y determinista


* Given dos tareas con la misma fecha límite
* When se ordena el listado
* Then el desempate sigue una regla determinista (asumido: por fecha de creación) y no varía entre recargas

**Contexto técnico:** Orden aplicado en la consulta `GET /api/tasks` (`ORDER BY due_date ASC NULLS LAST, created_at ASC` — asumido). El PRD deja el criterio exacto a decisión del equipo en refinamiento.

**Non-goals:**
- No incluye ordenación manual por arrastre
- No incluye múltiples criterios de orden elegibles por el usuario
- No define el criterio exacto (pendiente de refinamiento)

---

### Filter · Story 12

**Como** usuario sin tareas visibles,
**quiero** ver un estado vacío con una invitación a crear la primera,
**para** saber qué hacer en una cuenta nueva o cuando todo está archivado.

**Acceptance Criteria:**

* Given un usuario autenticado sin ninguna tarea
* When abre el listado
* Then ve un estado vacío con un mensaje de invitación y un botón para crear la primera tarea


* Given un usuario cuyas tareas están todas en estado `archived`
* When abre la vista por defecto (no archivadas)
* Then ve el estado vacío invitándole a crear una tarea, no una lista en blanco sin contexto


* Given un usuario que está en el estado vacío
* When pulsa el botón de crear tarea
* Then es llevado al formulario de creación

**Contexto técnico:** Componente de estado vacío en React 19; el frontend distingue "sin tareas" de "el filtro actual no devuelve resultados" (este último cubierto en Story 10). No requiere endpoint nuevo.

**Non-goals:**
- No incluye sugerencias de tareas de ejemplo (asumido)
- No incluye ilustraciones complejas (decisión de diseño)
- No cubre el vacío por filtro concreto (cubierto en Story 10)

---

⚠ **Asunciones a validar (Filter):**
- Criterio exacto de ordenación "relevante para hoy" y regla de desempate — Story 11 (el PRD lo delega a refinamiento).
- Si los filtros pueden combinarse o persistir entre sesiones — Story 10.
- Control de UI para el filtro (tabs vs. select) — Story 10.

---

### Export · Story 13

**Como** usuario autenticado,
**quiero** exportar mis tareas a un archivo CSV,
**para** poder llevarme mis datos fuera de FlowSync.

**Acceptance Criteria:**

* Given un usuario autenticado con tareas
* When pulsa "Exportar a CSV"
* Then se descarga un archivo CSV que incluye, como mínimo, las columnas título, descripción, estado y fecha límite de cada una de sus tareas


* Given un usuario cuyas tareas tienen comas, comillas o saltos de línea en el título o la descripción
* When exporta el CSV
* Then esos campos quedan correctamente escapados/entrecomillados y el CSV se puede abrir sin romper columnas


* Given un usuario sin ninguna tarea
* When pulsa "Exportar a CSV"
* Then se descarga un CSV con solo la fila de cabecera (asumido) o se le informa de que no hay tareas que exportar

**Contexto técnico:** `GET /api/tasks/export` — `app/controllers/tasks_controller.ts`, devuelve `Content-Type: text/csv` con `Content-Disposition: attachment`. Consulta filtrada por `user_id`; escapado de campos según RFC 4180 (asumido).

**Non-goals:**
- No incluye exportación a Excel/JSON/PDF (solo CSV en MVP)
- No incluye filtrar qué tareas exportar (asumido: exporta todas)
- No incluye importación desde CSV

---

⚠ **Asunciones a validar (Export):**
- Comportamiento exacto al exportar sin tareas (CSV con cabecera vs. aviso) — Story 13.
- Estándar de escapado y codificación (RFC 4180, UTF-8 con BOM para Excel) — Story 13.
- Si la exportación respeta el filtro activo o siempre exporta todas — Story 13.

---

### GCal · Story 14

**Como** usuario autenticado,
**quiero** conectar mi cuenta de Google a FlowSync mediante OAuth,
**para** habilitar la sincronización de mis tareas con Google Calendar.

**Acceptance Criteria:**

* Given un usuario autenticado sin Google conectado
* When pulsa "Conectar Google Calendar" y completa el consentimiento OAuth con permisos de calendario
* Then FlowSync almacena de forma segura los tokens de Google y muestra el estado "conectado"


* Given un usuario que inicia el flujo OAuth y deniega el permiso (o cancela) en la pantalla de Google
* When vuelve a FlowSync
* Then se muestra un mensaje de que la conexión no se completó y el estado permanece "no conectado", sin tokens almacenados


* Given el callback de OAuth llega con un `state` inválido o ausente
* When el servidor lo procesa
* Then rechaza la conexión (protección CSRF) y no almacena ningún token

**Contexto técnico:** `GET /api/google/connect` (redirige a Google) y `GET /api/google/callback` — `app/controllers/google_auth_controller.ts` (asumido). OAuth 2.0 con Google Calendar API; tokens (access + refresh) cifrados en reposo, asociados al `user_id`. Requiere proyecto en Google Cloud Console.

**Non-goals:**
- No sincroniza tareas todavía (solo establece la conexión)
- No soporta calendarios que no sean Google (out of scope MVP)
- No gestiona selección de qué calendario usar (asumido: calendario primario)

---

### GCal · Story 15

**Como** usuario con Google conectado,
**quiero** que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar,
**para** ver mis pendientes junto a mi agenda sin copiarlos a mano.

**Acceptance Criteria:**

* Given un usuario con Google conectado que crea una tarea con fecha límite
* When la tarea se guarda
* Then se crea un evento en su Google Calendar en la fecha indicada y FlowSync guarda el identificador del evento asociado a la tarea


* Given un usuario que crea una tarea sin fecha límite
* When la tarea se guarda
* Then no se crea ningún evento en Google Calendar


* Given un usuario con Google conectado cuya zona horaria debe respetarse
* When se crea el evento a partir de la fecha límite
* Then el evento se sitúa en la hora correcta según la zona horaria definida (asumido: pendiente de spike) y no en una hora errónea por desfase UTC

**Contexto técnico:** Servicio de sincronización invocado al crear/guardar tareas con `due_date`; llamada a Google Calendar API (`events.insert`), almacenando `google_event_id` en la tabla `tasks` (asumido). El manejo de zonas horarias es un riesgo conocido del PRD. Dirección primaria FlowSync → Google.

**Non-goals:**
- No cubre sincronización inversa (editar evento en Google) — requiere spike
- No gestiona eventos recurrentes ni de todo el día (riesgo conocido, fuera de MVP salvo decisión)
- No reintenta en este flujo los fallos de API (cubierto en Story 18)

---

### GCal · Story 16

**Como** usuario con Google conectado,
**quiero** que al cambiar la fecha de una tarea se actualice su evento en Google Calendar,
**para** que mi calendario siempre refleje la fecha real de la tarea.

**Acceptance Criteria:**

* Given una tarea con evento ya creado en Google Calendar
* When el usuario cambia su fecha límite en FlowSync
* Then el evento correspondiente en Google Calendar se actualiza a la nueva fecha usando el `google_event_id` guardado


* Given una tarea sin fecha límite (sin evento asociado)
* When el usuario le asigna por primera vez una fecha límite
* Then se crea un nuevo evento en Google Calendar (no se intenta actualizar uno inexistente)


* Given una tarea con evento asociado
* When el usuario le quita la fecha límite (la deja vacía)
* Then el evento correspondiente se elimina de Google Calendar y la tarea queda sin evento

**Contexto técnico:** Servicio de sincronización en la edición de tareas (`PUT /api/tasks/:id`); usa `events.update`, `events.insert` o `events.delete` de Google Calendar API según el cambio de `due_date` y la existencia de `google_event_id`.

**Non-goals:**
- No gestiona conflictos por edición simultánea en Google (sincronización inversa, fuera de MVP)
- No cubre cambios de zona horaria del usuario (asumido)
- No reintenta aquí los fallos de API (cubierto en Story 18)

---

### GCal · Story 17

**Como** usuario con Google conectado,
**quiero** que al completar o borrar una tarea su evento en Google Calendar se actualice o elimine,
**para** que mi calendario no muestre eventos de tareas que ya no están activas.

**Acceptance Criteria:**

* Given una tarea con evento en Google Calendar
* When el usuario borra la tarea en FlowSync
* Then el evento correspondiente se elimina de Google Calendar


* Given una tarea con evento en Google Calendar
* When el usuario la marca como `completed`
* Then el evento se actualiza o se elimina según la regla definida (asumido: se elimina al completar) de forma consistente


* Given una tarea sin evento asociado (sin fecha límite)
* When el usuario la borra o la completa
* Then la operación se realiza en FlowSync sin intentar ninguna llamada a Google Calendar

**Contexto técnico:** Servicio de sincronización invocado en `DELETE /api/tasks/:id` y en el cambio de estado a `completed`; usa `events.delete` (o `events.update`) con el `google_event_id`. La regla exacta para "completed" debe confirmarse.

**Non-goals:**
- No reintenta aquí los fallos de API (cubierto en Story 18)
- No archiva el evento de forma especial al estado `archived` (asumido: sin definir)
- No cubre sincronización inversa

---

### GCal · Story 18

**Como** usuario que crea o edita tareas,
**quiero** que mi tarea se guarde aunque Google Calendar falle, y que la sincronización se reintente,
**para** no perder datos cuando la API de Google no esté disponible.

**Acceptance Criteria:**

* Given un usuario con Google conectado y la API de Google devolviendo error o no disponible
* When crea o edita una tarea con fecha límite
* Then la tarea se guarda correctamente en FlowSync, la sincronización queda marcada como pendiente y se registra el fallo en logs


* Given una sincronización que quedó pendiente por un fallo previo
* When se ejecuta el reintento
* Then el evento se crea/actualiza en Google Calendar y la tarea deja de estar marcada como pendiente de sincronizar


* Given una operación de sincronización que falla de forma repetida tras los reintentos definidos
* When se agotan los reintentos
* Then el fallo queda registrado en logs de forma diagnosticable y la tarea permanece intacta en FlowSync

**Contexto técnico:** Marca de estado de sincronización por tarea (asumido: campo `sync_status`/`sync_pending` en `tasks`) y mecanismo de reintento (asumido: cola o job en background de AdonisJS — necesita revisión en refinamiento). Logging de operaciones de sincronización (requisito no funcional del PRD).

**Non-goals:**
- No define la estrategia exacta de backoff/cola (pendiente de spike/refinamiento)
- No notifica al usuario del fallo por push/email (out of scope MVP)
- No cubre sincronización inversa

---

### GCal · Story 19

**Como** usuario con Google conectado,
**quiero** desconectar mi cuenta de Google cuando quiera,
**para** dejar de sincronizar sin perder las tareas que ya tengo.

**Acceptance Criteria:**

* Given un usuario con Google conectado
* When pulsa "Desconectar Google Calendar"
* Then FlowSync revoca/elimina los tokens de Google, deja de sincronizar y muestra el estado "no conectado"


* Given un usuario que acaba de desconectar Google
* When revisa su listado de tareas
* Then todas sus tareas (incluidas las que tenían evento) siguen existiendo intactas en FlowSync


* Given un usuario desconectado que crea o edita una tarea con fecha límite
* When guarda la tarea
* Then la tarea se guarda sin intentar ninguna llamada a Google Calendar y sin error de sincronización

**Contexto técnico:** `POST /api/google/disconnect` (asumido) — `app/controllers/google_auth_controller.ts`; elimina los tokens almacenados y limpia el estado de conexión del usuario. Las tareas y sus `google_event_id` previos no se borran (asumido: se conservan o se limpian — revisar en refinamiento).

**Non-goals:**
- No borra de Google Calendar los eventos ya creados al desconectar (asumido)
- No reconecta automáticamente
- No exporta ni migra los eventos a otro calendario

---

⚠ **Asunciones a validar (GCal):**
- Manejo de zonas horarias entre `due_date` y la hora del evento (riesgo conocido del PRD) — Stories 15, 16.
- Endpoints y controlador exactos del flujo OAuth y desconexión — Stories 14, 19.
- Dónde se guarda `google_event_id` y cómo se asocia a la tarea — Stories 15-17.
- Regla al completar una tarea: ¿se elimina o se actualiza el evento? — Story 17.
- Qué ocurre con el evento `archived` — Story 17.
- Estrategia de reintento/cola y campo de estado de sincronización — Story 18.
- Si al desconectar se conservan o limpian los `google_event_id` previos — Story 19.
- Alcance de la sincronización inversa (Google → FlowSync): requiere spike técnico antes de comprometer (nota explícita del PRD).
- Selección de calendario (primario vs. elegible) — Story 14.
