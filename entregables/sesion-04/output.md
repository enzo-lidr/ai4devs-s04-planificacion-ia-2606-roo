ÉPICA: Autenticación y gestión de cuenta

US-01: Registrar una cuenta con email y contraseña
User story:

Como visitante no autenticado, quiero crear una cuenta en FlowSync con mi email y una contraseña, para poder acceder a la aplicación y gestionar mis tareas de forma personal.
Criterios de aceptación:

AC1 — Registro exitoso:

Given que soy un visitante no autenticado en el formulario de registro
When ingreso un email válido y una contraseña de al menos 8 caracteres y envío el formulario
Then se crea mi cuenta y soy redirigido a la pantalla de bienvenida de FlowSync


AC2 — Contraseña con menos de 8 caracteres:

Given que soy un visitante no autenticado en el formulario de registro
When ingreso una contraseña con menos de 8 caracteres e intento enviar el formulario
Then el formulario no se envía y se muestra un mensaje de error indicando que la contraseña debe tener al menos 8 caracteres


AC3 — Email con formato inválido:

Given que soy un visitante no autenticado en el formulario de registro
When ingreso un valor que no tiene formato de email válido e intento enviar el formulario
Then el formulario no se envía y se muestra un mensaje de error indicando que el email no es válido (asumido: texto exacto del mensaje)


AC4 — Campos obligatorios vacíos:

Given que soy un visitante no autenticado en el formulario de registro
When intento enviar el formulario con el campo email o el campo contraseña vacíos
Then el formulario no se envía y se indica qué campo o campos están incompletos (asumido: la validación se muestra por campo)




US-02: Aviso cuando el email ya está registrado
User story:

Como visitante no autenticado, quiero ser informado cuando el email que ingreso para registrarme ya tiene una cuenta en FlowSync, para poder ir directamente al inicio de sesión sin confundirme.
Criterios de aceptación:

AC1 — Email duplicado detectado:

Given que soy un visitante no autenticado y el email que ingreso ya corresponde a una cuenta existente en FlowSync
When envío el formulario de registro con ese email
Then el sistema muestra un mensaje indicando que el email ya está registrado y ofrece una vía para ir al inicio de sesión (asumido: texto exacto del mensaje y forma del enlace al login)


AC2 — No se crea una cuenta duplicada:

Given que soy un visitante no autenticado y el email que ingreso ya está registrado
When envío el formulario de registro con ese email
Then no se crea ninguna cuenta nueva ni se sobreescribe la cuenta existente


AC3 — La cuenta existente no resulta afectada:

Given que existe una cuenta con el email X y sus datos son D
When un visitante intenta registrarse usando el email X
Then la contraseña y todos los datos de la cuenta existente permanecen sin cambios




US-03: Iniciar sesión
User story:

Como usuario registrado no autenticado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas en FlowSync.
Criterios de aceptación:

AC1 — Inicio de sesión exitoso:

Given que soy un usuario registrado en el formulario de inicio de sesión
When ingreso mi email y contraseña correctos y envío el formulario
Then quedo autenticado y accedo a mi listado de tareas (asumido: el destino post-login es el listado de tareas)


AC2 — Credenciales incorrectas:

Given que soy un usuario registrado en el formulario de inicio de sesión
When ingreso un email o contraseña incorrectos y envío el formulario
Then el formulario no me autentica y se muestra un mensaje de error que no especifica cuál de los dos campos es el incorrecto (asumido: mensaje genérico por seguridad)


AC3 — Campos vacíos:

Given que estoy en el formulario de inicio de sesión
When intento enviar el formulario con el campo email o contraseña vacío
Then el formulario no se envía y se indica qué campo está incompleto




US-04: Cerrar sesión
User story:

Como usuario autenticado, quiero poder cerrar sesión en cualquier momento, para proteger el acceso a mis tareas en dispositivos compartidos o cuando termine de trabajar.
Criterios de aceptación:

AC1 — Cierre de sesión exitoso:

Given que soy un usuario autenticado dentro de FlowSync
When activo la opción de cerrar sesión
Then mi sesión queda terminada y soy redirigido a la pantalla de inicio de sesión (asumido: destino post-logout)


AC2 — Sesión inválida tras cerrar sesión:

Given que cerré sesión en FlowSync
When intento acceder a cualquier pantalla que requiere autenticación directamente por URL
Then soy redirigido al inicio de sesión y no puedo ver mis tareas


AC3 — La opción de cerrar sesión es accesible desde la aplicación:

Given que soy un usuario autenticado navegando en FlowSync
When busco la opción de cerrar sesión
Then está disponible sin necesidad de acceder a secciones avanzadas de configuración (asumido: ubicación exacta en la UI)




US-05: Pantalla de bienvenida tras el registro
User story:

Como usuario que acaba de crear su cuenta en FlowSync, quiero ver una pantalla de bienvenida que me explique qué hace la aplicación y me invite a crear mi primera tarea, para entender el valor de la herramienta y saber cómo empezar.
Criterios de aceptación:

AC1 — La pantalla de bienvenida aparece tras el registro:

Given que soy un visitante no autenticado que completó el formulario de registro exitosamente
When mi cuenta es creada
Then soy dirigido a una pantalla de bienvenida (onboarding mínimo) antes de acceder al listado de tareas


AC2 — Contenido mínimo de la pantalla:

Given que llegué a la pantalla de bienvenida tras registrarme
When la pantalla carga
Then veo al menos una frase que describe qué hace FlowSync y un elemento accionable que me invita a crear mi primera tarea (asumido: texto exacto y diseño de la pantalla)


AC3 — La bienvenida no se repite en logins posteriores:

Given que soy un usuario registrado que ya vio la pantalla de bienvenida
When inicio sesión nuevamente en FlowSync
Then soy llevado directamente a mi listado de tareas, sin pasar por la pantalla de bienvenida (asumido)




ÉPICA: Gestión de tareas (CRUD)

US-06: Crear una tarea
User story:

Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar mis pendientes sin necesidad de completar toda la información de antemano.
Criterios de aceptación:

AC1 — Creación exitosa solo con título:

Given que soy un usuario autenticado en el formulario de creación de tarea
When ingreso únicamente un título y envío el formulario
Then la tarea se crea con estado pending y aparece en mi listado de tareas


AC2 — El título es obligatorio:

Given que soy un usuario autenticado en el formulario de creación de tarea
When intento enviar el formulario con el campo título vacío
Then el formulario no se envía y se muestra un mensaje indicando que el título es obligatorio


AC3 — Descripción y fecha límite son opcionales:

Given que soy un usuario autenticado en el formulario de creación de tarea
When creo una tarea completando título, descripción y fecha límite
Then la tarea se crea y los tres campos quedan guardados y visibles en su detalle




US-07: Ver el listado de tareas
User story:

Como usuario autenticado, quiero ver el listado de todas mis tareas, para tener una visión general de mis pendientes en un solo lugar.
Criterios de aceptación:

AC1 — El listado muestra las tareas propias:

Given que soy un usuario autenticado con tareas creadas
When accedo a la vista de tareas
Then veo todas mis tareas con al menos el título y el estado de cada una visibles


AC2 — Las tareas de otros usuarios no son visibles:

Given que existen tareas creadas por otros usuarios en FlowSync
When accedo a mi listado de tareas
Then solo aparecen mis propias tareas; las tareas de otros usuarios no son accesibles


AC3 — El detalle de una tarea es accesible desde el listado:

Given que soy un usuario autenticado con al menos una tarea
When selecciono una tarea del listado
Then puedo ver todos sus campos: título, descripción, estado y fecha límite (asumido: existe una vista de detalle o la tarea se expande inline)




US-08: Editar una tarea
User story:

Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para actualizar la información a medida que cambia mi trabajo.
Criterios de aceptación:

AC1 — Edición exitosa de los campos:

Given que soy un usuario autenticado con al menos una tarea
When modifico el título, la descripción o la fecha límite de una tarea y guardo los cambios
Then la tarea refleja los nuevos valores en el listado y en su detalle


AC2 — El título no puede quedar vacío:

Given que soy un usuario autenticado editando una tarea existente
When elimino el contenido del campo título e intento guardar
Then los cambios no se guardan y se muestra un mensaje indicando que el título es obligatorio


AC3 — Los campos opcionales pueden eliminarse:

Given que soy un usuario autenticado editando una tarea que tiene descripción y fecha límite
When elimino la descripción y/o la fecha límite y guardo los cambios
Then la tarea se guarda sin esos campos sin mostrar ningún error




US-09: Borrar una tarea
User story:

Como usuario autenticado, quiero borrar una tarea que ya no necesito, para mantener mi listado relevante y sin ruido.
Criterios de aceptación:

AC1 — Borrado exitoso:

Given que soy un usuario autenticado con al menos una tarea
When ejecuto la acción de borrar una tarea y confirmo la operación
Then la tarea desaparece de mi listado y deja de existir en el sistema (asumido: existe un paso de confirmación antes del borrado definitivo)


AC2 — El borrado es permanente:

Given que soy un usuario autenticado que acaba de borrar una tarea
When recargo la vista o vuelvo al listado
Then la tarea borrada no aparece en ningún filtro ni vista de la aplicación


AC3 — El borrado no afecta otras tareas:

Given que soy un usuario autenticado con varias tareas
When borro una tarea específica
Then el resto de mis tareas permanece sin cambios en contenido ni en estado




US-10: Cambiar el estado de una tarea
User story:

Como usuario autenticado, quiero cambiar el estado de una tarea entre pending, completed y archived, para reflejar el avance real de mi trabajo dentro de FlowSync.
Criterios de aceptación:

AC1 — Una tarea recién creada nace en estado pending:

Given que soy un usuario autenticado que acaba de crear una tarea
When la tarea es creada
Then su estado inicial es pending, independientemente de si tiene fecha límite o descripción


AC2 — Cambio de estado a completed:

Given que soy un usuario autenticado con una tarea en estado pending
When cambio su estado a completed
Then la tarea aparece con estado completed en el listado


AC3 — Cambio de estado a archived:

Given que soy un usuario autenticado con una tarea en cualquier estado
When cambio su estado a archived
Then la tarea aparece con estado archived y es recuperable al filtrar por ese estado (asumido: las tareas archivadas no se eliminan y siguen siendo accesibles mediante filtrado)




ÉPICA: Organización y filtrado

US-11: Filtrar el listado de tareas por estado
User story:

Como usuario autenticado con múltiples tareas en distintos estados, quiero filtrar el listado para ver únicamente las tareas de un estado determinado, para enfocarme en lo que es relevante en cada momento.
Criterios de aceptación:

AC1 — Filtro por pending:

Given que soy un usuario autenticado con tareas en distintos estados
When aplico el filtro para ver solo tareas pending
Then el listado muestra únicamente las tareas cuyo estado es pending


AC2 — Filtro por completed:

Given que soy un usuario autenticado con tareas en distintos estados
When aplico el filtro para ver solo tareas completed
Then el listado muestra únicamente las tareas cuyo estado es completed


AC3 — Filtro por archived:

Given que soy un usuario autenticado con tareas en distintos estados
When aplico el filtro para ver solo tareas archived
Then el listado muestra únicamente las tareas cuyo estado es archived


AC4 — Sin filtro activo se muestran todas las tareas:

Given que soy un usuario autenticado que tenía un filtro de estado activo
When desactivo o limpio el filtro
Then el listado vuelve a mostrar tareas de todos los estados (asumido: existe una opción explícita para limpiar el filtro activo)




US-12: Orden por defecto del listado de tareas
User story:

Como usuario autenticado, quiero que mi listado de tareas aparezca ordenado por defecto de forma que las más relevantes para hoy sean las primeras, para identificar mis prioridades inmediatas sin tener que ordenar manualmente.
Criterios de aceptación:

AC1 — El orden por defecto se aplica al cargar el listado:

Given que soy un usuario autenticado con varias tareas
When accedo al listado de tareas sin haber aplicado ningún criterio de ordenación manual
Then las tareas aparecen en un orden predefinido que prioriza las más relevantes para el día actual (asumido: el criterio exacto de ordenación —por ejemplo, fecha límite ascendente, tareas sin fecha al final— se define durante el refinamiento con el equipo)


AC2 — El orden no requiere ninguna acción del usuario:

Given que soy un usuario autenticado que acaba de iniciar sesión
When el listado carga
Then las tareas ya están ordenadas según el criterio por defecto, sin que yo tenga que configurar nada


AC3 — El orden por defecto se mantiene consistente entre sesiones:

Given que soy un usuario autenticado que cerró sesión y volvió a iniciarla sin modificar sus tareas
When accedo al listado de tareas
Then las tareas aparecen en el mismo orden que en la sesión anterior (asumido)




US-13: Estado vacío cuando no hay tareas visibles
User story:

Como usuario autenticado sin tareas en mi listado, quiero ver un estado vacío que me invite a crear mi primera tarea, para no encontrarme con una pantalla en blanco que no me dé ninguna orientación.
Criterios de aceptación:

AC1 — Estado vacío en cuenta nueva:

Given que soy un usuario recién registrado que aún no creó ninguna tarea
When accedo al listado de tareas
Then veo un estado vacío con una invitación a crear mi primera tarea, en lugar de una lista en blanco sin texto (asumido: diseño y texto exacto del estado vacío)


AC2 — Estado vacío cuando todas las tareas están archivadas:

Given que soy un usuario autenticado cuyas únicas tareas tienen estado archived
When accedo al listado sin ningún filtro activo
Then veo el estado vacío con la invitación a crear una nueva tarea (asumido: depende de si las tareas archivadas se muestran en la vista por defecto; a confirmar en refinamiento)


AC3 — El estado vacío desaparece al crear una tarea:

Given que soy un usuario autenticado viendo el estado vacío del listado
When creo una nueva tarea
Then el estado vacío es reemplazado por el listado con esa tarea visible




ÉPICA: Exportación

US-14: Exportar tareas a CSV
User story:

Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para poder usar mis datos fuera de FlowSync sin depender de la herramienta.
Criterios de aceptación:

AC1 — Descarga del archivo CSV:

Given que soy un usuario autenticado con al menos una tarea
When activo la opción de exportar mis tareas
Then se descarga en mi dispositivo un archivo con extensión .csv


AC2 — El CSV contiene los campos requeridos:

Given que soy un usuario autenticado que acaba de exportar sus tareas
When abro el archivo CSV descargado
Then el archivo contiene al menos cuatro columnas: título, descripción, estado y fecha límite de cada tarea


AC3 — El CSV incluye tareas de todos los estados:

Given que soy un usuario autenticado con tareas en estados pending, completed y archived
When exporto mis tareas
Then el CSV contiene todas mis tareas independientemente de su estado y de los filtros que esté aplicando en ese momento en el listado (asumido: la exportación no hereda los filtros activos)




ÉPICA: Sincronización con Google Calendar

US-15: Conectar la cuenta de Google a FlowSync
User story:

Como usuario autenticado sin cuenta de Google conectada, quiero autorizar a FlowSync para acceder a mi Google Calendar mediante OAuth, para que mis tareas con fecha límite se reflejen automáticamente como eventos en mi calendario.
Criterios de aceptación:

AC1 — Inicio del flujo de autorización OAuth:

Given que soy un usuario autenticado sin cuenta de Google conectada
When activo la opción de conectar mi cuenta de Google
Then soy redirigido al flujo de autorización de Google OAuth en una pantalla de Google


AC2 — Conexión exitosa tras autorizar:

Given que soy un usuario autenticado que completó la pantalla de autorización en Google
When Google redirige de vuelta a FlowSync con la autorización concedida
Then mi cuenta de Google queda vinculada a FlowSync y la interfaz indica que la conexión está activa (asumido: existe un indicador visual del estado de conexión)


AC3 — Autorización denegada por el usuario:

Given que soy un usuario autenticado que inició el flujo OAuth
When cancelo o deniego el permiso en la pantalla de Google
Then vuelvo a FlowSync sin que mi cuenta de Google quede conectada, y mis tareas no se ven afectadas




US-16: Tareas con fecha límite se crean como eventos en Google Calendar
User story:

Como usuario autenticado con cuenta de Google conectada, quiero que las tareas con fecha límite aparezcan automáticamente como eventos en mi Google Calendar, para ver mis compromisos sin copiarlos a mano.
Criterios de aceptación:

AC1 — Evento creado al crear una tarea con fecha límite:

Given que soy un usuario autenticado con cuenta de Google conectada
When creo una tarea con fecha límite
Then se crea un evento correspondiente en mi Google Calendar en la fecha indicada


AC2 — Tareas sin fecha no generan evento:

Given que soy un usuario autenticado con cuenta de Google conectada
When creo una tarea sin fecha límite
Then no se crea ningún evento en mi Google Calendar


AC3 — La tarea se guarda en FlowSync aunque la sincronización falle:

Given que soy un usuario autenticado con cuenta de Google conectada y la API de Google no está disponible en el momento de crear la tarea
When creo una tarea con fecha límite
Then la tarea queda guardada en FlowSync y la sincronización con Google Calendar se reintenta más tarde, sin que el usuario pierda la tarea




US-17: Cambio de fecha límite actualiza el evento en Google Calendar
User story:

Como usuario autenticado con cuenta de Google conectada, quiero que al modificar la fecha límite de una tarea el evento en Google Calendar se actualice automáticamente, para que mi calendario siempre refleje la información más reciente sin intervención manual.
Criterios de aceptación:

AC1 — Cambio de fecha actualiza el evento existente:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea con fecha límite cuyo evento ya existe en Google Calendar
When cambio la fecha límite de esa tarea
Then el evento en mi Google Calendar se mueve a la nueva fecha, sin que se cree un evento duplicado


AC2 — Agregar fecha a una tarea sin fecha crea un evento:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea que no tiene fecha límite
When le agrego una fecha límite a esa tarea
Then se crea un evento nuevo en mi Google Calendar para esa tarea (asumido)


AC3 — Eliminar la fecha de una tarea elimina su evento:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea con fecha límite sincronizada como evento
When elimino la fecha límite de esa tarea
Then el evento correspondiente en Google Calendar es eliminado (asumido: el PRD no especifica explícitamente este caso; se propone eliminar el evento al quitar la fecha)




US-18: Completar una tarea actualiza el evento en Google Calendar
User story:

Como usuario autenticado con cuenta de Google conectada, quiero que cuando complete una tarea con fecha límite el evento correspondiente en Google Calendar refleje ese cambio, para que mi calendario no muestre compromisos que ya cumplí.
Criterios de aceptación:

AC1 — Completar una tarea modifica su evento en Google Calendar:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea con fecha límite en estado pending
When cambio el estado de la tarea a completed
Then el evento correspondiente en Google Calendar es eliminado o marcado según corresponda (asumido: el PRD indica "se elimina o se marca según corresponda" — el comportamiento exacto se define en refinamiento)


AC2 — Completar una tarea sin evento no genera errores:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea sin fecha límite (sin evento en Google Calendar)
When cambio el estado de esa tarea a completed
Then el cambio de estado se guarda en FlowSync sin que se produzca ningún error ni intento de modificar Google Calendar


AC3 — El cambio de estado se guarda aunque la sincronización falle:

Given que soy un usuario autenticado con cuenta de Google conectada y la API de Google no está disponible
When marco una tarea como completed
Then el nuevo estado queda guardado en FlowSync y la actualización del evento en Google Calendar se reintenta más tarde




US-19: Borrar una tarea elimina el evento en Google Calendar
User story:

Como usuario autenticado con cuenta de Google conectada, quiero que al borrar una tarea con fecha límite el evento correspondiente en Google Calendar también se elimine, para no acumular eventos huérfanos en mi calendario.
Criterios de aceptación:

AC1 — Borrar una tarea con fecha elimina su evento:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea con fecha límite cuyo evento existe en Google Calendar
When borro esa tarea en FlowSync
Then el evento correspondiente en mi Google Calendar es eliminado


AC2 — Borrar una tarea sin fecha no afecta Google Calendar:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo una tarea sin fecha límite
When borro esa tarea
Then no se produce ningún cambio en mi Google Calendar


AC3 — La tarea se elimina de FlowSync aunque la sincronización falle:

Given que soy un usuario autenticado con cuenta de Google conectada y la API de Google no está disponible
When borro una tarea con fecha límite
Then la tarea queda eliminada de FlowSync de inmediato y la eliminación del evento en Google Calendar se reintenta más tarde (asumido: la tarea se borra de FlowSync aunque el evento no pueda eliminarse en ese momento)




US-20: Desconectar la cuenta de Google
User story:

Como usuario autenticado con cuenta de Google conectada, quiero poder desconectar mi cuenta de Google de FlowSync en cualquier momento, para revocar el acceso a mi calendario cuando lo decida sin perder mis tareas.
Criterios de aceptación:

AC1 — Desconexión exitosa:

Given que soy un usuario autenticado con cuenta de Google conectada
When activo la opción de desconectar mi cuenta de Google
Then FlowSync revoca la conexión y deja de sincronizar eventos hacia Google Calendar


AC2 — Las tareas existentes no se borran al desconectar:

Given que soy un usuario autenticado con cuenta de Google conectada y tengo tareas creadas
When desconecto mi cuenta de Google
Then todas mis tareas siguen existiendo en FlowSync sin cambios en su contenido ni en su estado


AC3 — La interfaz refleja que Google ya no está conectado:

Given que soy un usuario autenticado que acaba de desconectar su cuenta de Google
When accedo a la sección donde se gestiona la conexión con Google
Then la interfaz muestra que no hay cuenta de Google conectada y ofrece la opción de volver a conectar (asumido: existe una sección o pantalla de configuración de la cuenta donde se gestiona esta conexión)