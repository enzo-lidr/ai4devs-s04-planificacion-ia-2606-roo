### Autenticación y gestión de cuenta

**US-01 — Registro de usuario**
Depende de: ninguna
Como knowledge worker que hoy gestiona sus pendientes en una herramienta separada de su calendario, quiero crear una cuenta con email y contraseña, para tener un espacio personal y privado donde centralizar todas mis tareas.

Criterios de aceptación:
- Given que soy un visitante en la pantalla de registro,
  When ingreso un email válido y una contraseña de al menos 8 caracteres y hago clic en "Registrarse",
  Then se crea mi cuenta y llego a una pantalla de bienvenida que explica en una frase qué hace FlowSync y me invita a crear mi primera tarea.
- Given que ingreso un email ya registrado,
  When envío el formulario,
  Then el sistema lo indica y me ofrece ir al inicio de sesión.
- Given que ingreso una contraseña de menos de 8 caracteres,
  When envío el formulario,
  Then el sistema muestra un mensaje de validación comprensible, no un error técnico.
- Given que mi cuenta ya existe y accedo a mis tareas,
  When navego por FlowSync,
  Then solo veo mis propios datos y nunca los de otro usuario.

**US-02 — Inicio y cierre de sesión**
Depende de: US-01
Como knowledge worker que entra y sale de FlowSync varias veces al día, quiero iniciar y cerrar sesión con mi email y contraseña, para acceder a mis tareas de forma segura y dejar la cuenta protegida cuando termino.

Criterios de aceptación:
- Given que soy un usuario registrado en la pantalla de inicio de sesión,
  When ingreso mi email y contraseña correctos,
  Then inicio sesión, accedo a mi listado de tareas y la sesión se mantiene mediante tokens de acceso.
- Given que ingreso credenciales incorrectas,
  When envío el formulario,
  Then el sistema muestra un mensaje de error comprensible y no inicia la sesión.
- Given que soy un usuario autenticado,
  When hago clic en "Cerrar sesión",
  Then mi sesión finaliza y debo volver a autenticarme para ver mis tareas.

---

### Gestión de tareas (CRUD)

**US-03 — Crear tarea**
Depende de: US-01
Como knowledge worker que mantiene entre 5 y 30 pendientes activos, quiero crear una tarea indicando al menos un título, para registrar al vuelo lo que tengo que hacer sin fricción ni mantenimiento.

Criterios de aceptación:
- Given que estoy autenticado en el formulario de nueva tarea,
  When ingreso un título y guardo,
  Then la tarea se crea con estado `pending` y aparece en mi listado.
- Given que añado una descripción y una fecha límite (ambas opcionales),
  When guardo la tarea,
  Then se guarda con esos campos; y si los omito, la tarea se crea igualmente solo con el título.
- Given que intento guardar sin título,
  When envío el formulario,
  Then el sistema muestra un mensaje de validación comprensible indicando que el título es obligatorio, no un error técnico, y no crea la tarea.

**US-04 — Editar tarea**
Depende de: US-03
Como knowledge worker cuyas prioridades cambian durante el día, quiero editar cualquier campo de una tarea existente, para mantener mis pendientes al día sin tener que borrarlos y recrearlos.

Criterios de aceptación:
- Given que tengo una tarea existente,
  When edito su título, descripción o fecha límite y guardo,
  Then los cambios quedan reflejados en el listado.
- Given que edito el título de una tarea y lo dejo vacío,
  When guardo,
  Then el sistema muestra un mensaje de validación comprensible y no guarda el cambio, porque el título sigue siendo obligatorio.
- Given que tengo una tarea en estado `completed` o `archived`,
  When la edito,
  Then puedo modificar sus campos igual que una tarea `pending`.
  (asumido: el PRD no restringe la edición según el estado de la tarea)

**US-05 — Borrar tarea**
Depende de: US-03
Como knowledge worker que acumula tareas que dejaron de ser relevantes, quiero borrar una tarea, para mantener mi listado limpio y enfocado en lo que de verdad importa.

Criterios de aceptación:
- Given que tengo una tarea en mi listado,
  When la borro,
  Then desaparece del listado y deja de aparecer en mis tareas.
- Given que borro una tarea concreta,
  When se ejecuta el borrado,
  Then la acción afecta únicamente a esa tarea y el resto de mis tareas permanece sin cambios.
  (asumido: el PRD no especifica una confirmación previa; se asume borrado directo de una sola tarea)
- Given que mi cuenta de Google está conectada y la tarea tiene un evento asociado,
  When la borro,
  Then la tarea se elimina en FlowSync y su evento en Google Calendar se elimina (comportamiento detallado en US-11).

**US-06 — Cambiar el estado de una tarea**
Depende de: US-03
Como knowledge worker que quiere ver su progreso de un vistazo, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para marcar lo que ya terminé y quitar de la vista lo que ya no necesito atender.

Criterios de aceptación:
- Given que tengo una tarea en estado `pending`,
  When la marco como completada,
  Then su estado cambia a `completed` y se refleja en el listado.
- Given que tengo una tarea,
  When la archivo,
  Then su estado cambia a `archived` y deja de aparecer entre las pendientes.
  (asumido: el PRD no define transiciones prohibidas; se asume permitido cualquier cambio entre los tres estados)
- Given que mi cuenta de Google está conectada y la tarea tiene un evento asociado,
  When la marco como `completed`,
  Then su evento en Google Calendar se elimina o se marca según corresponda (comportamiento detallado en US-11).

---

### Organización y filtrado

**US-07 — Ver y filtrar el listado de tareas**
Depende de: US-03
Como knowledge worker con varias tareas activas a la vez, quiero ver mi listado ordenado por relevancia y poder filtrarlo por estado, para identificar de un vistazo qué tengo que hacer hoy sin perderme entre lo ya hecho o archivado.

Criterios de aceptación:
- Given que tengo tareas,
  When abro el listado,
  Then se muestra cargando en menos de 1 segundo para un usuario con hasta 200 tareas, con las tareas más relevantes para "hoy" visibles primero.
  (asumido: el criterio exacto de ordenación queda a decisión del equipo; se asume ordenar por fecha límite ascendente, con las de hoy y las atrasadas primero)
- Given que tengo tareas en distintos estados,
  When filtro por un estado (por ejemplo, solo `pending` o solo `completed`),
  Then el listado muestra únicamente las tareas de ese estado.
- Given que no tengo ninguna tarea (cuenta nueva o todas archivadas),
  When abro el listado,
  Then veo un estado vacío con una invitación a crear la primera tarea.
- Given que accedo desde el navegador de un móvil,
  When abro el listado,
  Then la interfaz es usable en móvil mediante web responsive, sin requerir una app nativa.

---

### Exportación

**US-08 — Exportar tareas a CSV**
Depende de: US-03
Como knowledge worker que no quiere quedar atrapado en una sola herramienta, quiero exportar mis tareas a un archivo CSV, para conservar una copia de mis datos y poder llevármelos a otro sitio.

Criterios de aceptación:
- Given que tengo tareas,
  When exporto a CSV,
  Then se descarga un archivo CSV que incluye, como mínimo, las columnas título, descripción, estado y fecha límite de cada tarea.
- Given que tengo tareas en estados `pending`, `completed` y `archived`,
  When exporto,
  Then el CSV incluye todas mis tareas con su estado correspondiente.
  (asumido: el PRD no indica si la exportación respeta filtros activos; se asume que exporta todas las tareas del usuario)
- Given que no tengo ninguna tarea,
  When exporto,
  Then se genera un CSV con la fila de encabezados (título, descripción, estado, fecha límite) y sin filas de datos.
  (asumido: el PRD no especifica el comportamiento de exportación sin tareas)

---

### Sincronización con Google Calendar

**US-09 — Conectar cuenta de Google**
Depende de: US-01
Como knowledge worker que ya vive en Google Calendar a diario, quiero conectar mi cuenta de Google a FlowSync mediante autorización OAuth, para habilitar que mis tareas se reflejen en mi calendario.

Criterios de aceptación:
- Given que estoy autenticado y sin Google conectado,
  When inicio la conexión y autorizo a FlowSync en el flujo OAuth de Google,
  Then mi cuenta queda conectada y FlowSync puede leer y escribir en mi Google Calendar.
- Given que completo la autorización,
  When la conexión se establece,
  Then los tokens de Google se almacenan de forma segura y nunca son accesibles por otros usuarios.
- Given que cancelo o rechazo la autorización en la pantalla de Google,
  When vuelvo a FlowSync,
  Then mi cuenta no queda conectada, la sincronización permanece desactivada y el sistema me lo indica de forma comprensible.
  (asumido: el PRD no detalla el mensaje exacto ante una autorización rechazada)

**US-10 — Crear evento en Google Calendar al asignar fecha límite**
Depende de: US-09 (conectar Google) y US-03 (crear tarea)
Como knowledge worker que gestiona tareas y calendario en herramientas separadas, quiero que al guardar una tarea con fecha límite aparezca automáticamente como evento en mi Google Calendar, para no tener que copiarla a mano ni revisar dos sitios distintos para saber qué tengo pendiente hoy.

Criterios de aceptación:
- Given que mi cuenta de Google está conectada y creo o guardo una tarea con fecha límite,
  When guardo la tarea,
  Then aparece un evento en mi Google Calendar en la fecha indicada con el título de la tarea como nombre del evento.
  (asumido: el PRD no especifica el contenido del evento más allá de la fecha, ni la hora exacta; la relación fecha límite↔hora del evento debe definirse con cuidado por el riesgo de zonas horarias)
- Given que mi cuenta de Google está conectada y creo una tarea sin fecha límite,
  When guardo la tarea,
  Then no se crea ningún evento en Google Calendar.
- Given que mi cuenta de Google no está conectada y creo una tarea con fecha límite,
  When guardo la tarea,
  Then la tarea se guarda en FlowSync sin crear ningún evento en Google Calendar.
- Given que mi cuenta de Google está conectada y la API de Google no está disponible o devuelve error al guardar una tarea con fecha límite,
  When guardo la tarea,
  Then la tarea se guarda correctamente en FlowSync, la sincronización falla sin interrumpir al usuario, se reintenta más tarde y la operación queda registrada en los logs para diagnóstico.
  (asumido: el PRD no especifica el intervalo de reintento ni si se notifica al usuario cuando la sync falla)

**US-11 — Mantener el evento de Google sincronizado con los cambios de la tarea**
Depende de: US-10
Como knowledge worker que ajusta sus tareas a lo largo del día, quiero que al cambiar la fecha, completar o borrar una tarea su evento en Google Calendar se actualice o elimine en consecuencia, para que mi calendario siempre refleje la realidad sin mantenimiento manual.

Criterios de aceptación:
- Given que tengo una tarea con evento en Google Calendar,
  When cambio su fecha límite en FlowSync,
  Then el evento correspondiente se actualiza a la nueva fecha.
- Given que tengo una tarea con evento en Google Calendar,
  When la marco como `completed`,
  Then su evento se elimina o se marca según corresponda en Google Calendar.
  (asumido: el PRD deja "se elimina o se marca según corresponda"; se asume eliminar el evento al completar)
- Given que tengo una tarea con evento en Google Calendar,
  When borro la tarea,
  Then su evento correspondiente se elimina del calendario.
- Given que la API de Google no está disponible al propagar un cambio,
  When actualizo, completo o borro la tarea,
  Then el cambio se guarda en FlowSync, la sincronización se reintenta más tarde y la operación queda registrada en los logs.
  (asumido: el PRD no especifica el intervalo de reintento)

**US-12 — Desconectar cuenta de Google**
Depende de: US-09
Como knowledge worker que quiere controlar el acceso a su calendario, quiero desconectar mi cuenta de Google de FlowSync en cualquier momento, para revocar la sincronización sin perder las tareas que ya creé.

Criterios de aceptación:
- Given que tengo mi cuenta de Google conectada,
  When la desconecto,
  Then FlowSync deja de sincronizar con Google Calendar.
- Given que acabo de desconectar mi cuenta,
  When reviso mi listado,
  Then mis tareas ya creadas en FlowSync se conservan intactas y no se borran.
- Given que desconecté Google y luego creo o edito una tarea con fecha límite,
  When guardo la tarea,
  Then se guarda en FlowSync sin crear ni modificar eventos en Google Calendar.
  (asumido: el PRD no especifica si los eventos ya creados en el calendario se eliminan al desconectar; se asume que permanecen en Google Calendar)
