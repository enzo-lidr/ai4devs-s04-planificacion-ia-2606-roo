I'll start by reading the PRD to derive the backlog.

Nota: el PRD real describe FlowSync como un **gestor de tareas personales sincronizado con Google Calendar**, no como la plataforma de orquestación de workflows del enunciado de rol. He derivado el backlog exclusivamente del contenido real del MVP en `docs/PRD.md`.

---

BACKLOG INICIAL — FlowSync MVP

LISTA DE EPÍGRAFES (MÓDULOS FUNCIONALES DEL MVP)

1. Autenticación y gestión de cuenta
2. Gestión de tareas (CRUD y estados)
3. Organización y filtrado
4. Exportación de datos
5. Sincronización con Google Calendar

===================================================================
EPÍGRAFE 1: AUTENTICACIÓN Y GESTIÓN DE CUENTA
===================================================================

Historia de usuario 1.1

Como visitante, quiero crear una cuenta con email y contraseña, para empezar a gestionar mis tareas en FlowSync.

Criterios de aceptación (Given/When/Then):

1. Given que soy un visitante en la pantalla de registro
   When introduzco un email válido y una contraseña de al menos 8 caracteres
   Then el sistema crea mi cuenta y me da acceso autenticado.

2. Given que introduzco una contraseña de menos de 8 caracteres
   When intento registrarme
   Then el sistema rechaza el registro y muestra un mensaje comprensible indicando el requisito mínimo de 8 caracteres.

3. Given que el email que introduzco ya está registrado
   When intento crear la cuenta
   Then el sistema lo indica y me ofrece ir al inicio de sesión.

4. Given que introduzco un email con formato inválido
   When intento registrarme
   Then el sistema muestra un error de validación comprensible y no crea la cuenta.

Historia de usuario 1.2

Como usuario recién registrado, quiero llegar a una pantalla de bienvenida que explique qué hace FlowSync, para entender el producto y crear mi primera tarea.

Criterios de aceptación (Given/When/Then):

1. Given que acabo de completar el registro con éxito
   When se completa el alta
   Then soy dirigido a una pantalla de bienvenida (onboarding mínimo) que explica en una frase qué hace FlowSync.

2. Given que estoy en la pantalla de bienvenida
   When la visualizo
   Then veo una invitación explícita a crear mi primera tarea.

3. Given que estoy en la pantalla de bienvenida
   When acepto la invitación a crear mi primera tarea
   Then soy llevado al flujo de creación de tarea.

Historia de usuario 1.3

Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.

Criterios de aceptación (Given/When/Then):

1. Given que tengo una cuenta registrada
   When introduzco mi email y contraseña correctos
   Then el sistema me autentica y me da acceso a mis tareas.

2. Given que introduzco credenciales incorrectas
   When intento iniciar sesión
   Then el sistema rechaza el acceso y muestra un mensaje de error comprensible.

3. Given que inicio sesión con éxito
   When la autenticación se completa
   Then la sesión se mantiene mediante un token de acceso.

Historia de usuario 1.4

Como usuario autenticado, quiero cerrar sesión, para dejar mi cuenta inaccesible en el dispositivo.

Criterios de aceptación (Given/When/Then):

1. Given que tengo una sesión activa
   When selecciono cerrar sesión
   Then el sistema invalida mi acceso y me lleva a un estado no autenticado.

2. Given que he cerrado sesión
   When intento acceder a una vista de tareas
   Then el sistema me exige volver a iniciar sesión.

Historia de usuario 1.5

Como usuario, quiero que mis datos sean privados y no visibles para otros usuarios, para proteger la confidencialidad de mis tareas.

Criterios de aceptación (Given/When/Then):

1. Given que existen varios usuarios con cuentas propias
   When un usuario consulta su listado de tareas
   Then solo ve las tareas asociadas a su propia cuenta y ninguna de otro usuario.

2. Given que un usuario ha conectado su cuenta de Google
   When se almacenan sus tokens de Google
   Then dichos tokens no son accesibles por otros usuarios y se guardan de forma segura.

===================================================================
EPÍGRAFE 2: GESTIÓN DE TAREAS (CRUD Y ESTADOS)
===================================================================

Historia de usuario 2.1

Como usuario, quiero crear una tarea indicando al menos un título, para registrar un pendiente rápidamente.

Criterios de aceptación (Given/When/Then):

1. Given que estoy autenticado
   When creo una tarea introduciendo únicamente un título
   Then el sistema guarda la tarea y la muestra en mi listado.

2. Given que intento crear una tarea sin título
   When confirmo la creación
   Then el sistema rechaza la operación y muestra un mensaje indicando que el título es obligatorio.

3. Given que creo una tarea
   When se guarda con éxito
   Then la tarea nace en estado `pending`.

4. Given que creo una tarea
   When añado opcionalmente descripción y fecha límite
   Then el sistema guarda esos campos junto con la tarea.

Historia de usuario 2.2

Como usuario, quiero ver el listado de mis tareas, para saber qué pendientes tengo.

Criterios de aceptación (Given/When/Then):

1. Given que tengo tareas creadas
   When abro la vista de listado
   Then veo mis tareas con al menos su título y su estado.

2. Given que tengo hasta 200 tareas
   When cargo el listado
   Then el listado se muestra en menos de 1 segundo.

3. Given que no tengo ninguna tarea (cuenta nueva o todas archivadas)
   When abro el listado
   Then veo un estado vacío con una invitación a crear la primera tarea.

Historia de usuario 2.3

Como usuario, quiero editar cualquier campo de una tarea existente, para corregir o actualizar su información.

Criterios de aceptación (Given/When/Then):

1. Given que tengo una tarea existente
   When edito su título, descripción o fecha límite y guardo
   Then el sistema persiste los cambios y los muestra en el listado y en el detalle.

2. Given que edito el título y lo dejo vacío
   When intento guardar
   Then el sistema rechaza el cambio y muestra un mensaje indicando que el título es obligatorio.

3. Given que he editado una tarea con éxito
   When vuelvo a abrirla
   Then veo los valores actualizados.

Historia de usuario 2.4

Como usuario, quiero borrar una tarea, para eliminar pendientes que ya no necesito.

Criterios de aceptación (Given/When/Then):

1. Given que tengo una tarea existente
   When la borro
   Then el sistema elimina la tarea y deja de mostrarla en mi listado.

2. Given que he borrado una tarea
   When consulto de nuevo el listado
   Then la tarea borrada no aparece.

Historia de usuario 2.5

Como usuario, quiero cambiar el estado de una tarea entre pending, completed y archived, para reflejar su situación real.

Criterios de aceptación (Given/When/Then):

1. Given que tengo una tarea en estado `pending`
   When la marco como `completed`
   Then el sistema actualiza su estado a `completed` y lo refleja en el listado.

2. Given que tengo una tarea
   When la archivo
   Then su estado pasa a `archived`.

3. Given que cambio el estado de una tarea
   When el cambio se guarda
   Then solo se admiten los valores `pending`, `completed` o `archived`.

===================================================================
EPÍGRAFE 3: ORGANIZACIÓN Y FILTRADO
===================================================================

Historia de usuario 3.1

Como usuario, quiero filtrar mis tareas por estado, para centrarme solo en las que me interesan en un momento dado.

Criterios de aceptación (Given/When/Then):

1. Given que tengo tareas en distintos estados
   When aplico el filtro por estado `pending`
   Then el listado muestra únicamente las tareas en estado `pending`.

2. Given que tengo tareas completadas y pendientes
   When filtro por `completed`
   Then el listado muestra únicamente las tareas `completed`.

3. Given que he aplicado un filtro por estado
   When quito el filtro
   Then el listado vuelve a mostrar las tareas sin restricción de estado.

Historia de usuario 3.2

Como usuario, quiero que las tareas más relevantes para "hoy" se muestren primero por defecto, para ver de un vistazo lo que necesito atender. (asumido: el criterio exacto de ordenación queda a decisión del equipo en refinamiento, según el PRD)

Criterios de aceptación (Given/When/Then):

1. Given que tengo varias tareas con distintas fechas límite
   When abro el listado sin aplicar ningún orden manual
   Then las tareas más relevantes para "hoy" aparecen primero (asumido: según el criterio de ordenación definido en refinamiento).

2. Given que abro el listado por defecto
   When se cargan las tareas
   Then el orden aplicado es consistente y determinista entre recargas.

===================================================================
EPÍGRAFE 4: EXPORTACIÓN DE DATOS
===================================================================

Historia de usuario 4.1

Como usuario, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos.

Criterios de aceptación (Given/When/Then):

1. Given que tengo tareas creadas
   When solicito la exportación
   Then el sistema genera un archivo CSV descargable.

2. Given que se genera el CSV
   When lo abro
   Then cada tarea incluye, como mínimo, título, descripción, estado y fecha límite.

3. Given que no tengo tareas
   When solicito la exportación
   Then el sistema genera un CSV con las cabeceras de columna y sin filas de datos (asumido).

===================================================================
EPÍGRAFE 5: SINCRONIZACIÓN CON GOOGLE CALENDAR
===================================================================

Historia de usuario 5.1

Como usuario, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para autorizar la sincronización con mi calendario.

Criterios de aceptación (Given/When/Then):

1. Given que estoy autenticado y no he conectado Google
   When inicio la conexión de Google
   Then el sistema me lleva al flujo de autorización OAuth 2.0 de Google.

2. Given que completo la autorización OAuth con éxito
   When vuelvo a FlowSync
   Then el sistema registra la conexión y almacena los tokens de Google de forma segura.

3. Given que cancelo o deniego la autorización en Google
   When vuelvo a FlowSync
   Then la cuenta de Google no queda conectada y se me informa de forma comprensible.

Historia de usuario 5.2

Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, para no tener que copiarlas a mano.

Criterios de aceptación (Given/When/Then):

1. Given que tengo Google conectado
   When creo una tarea con fecha límite
   Then el sistema crea el evento correspondiente en mi Google Calendar.

2. Given que tengo Google conectado
   When creo una tarea sin fecha límite
   Then no se crea ningún evento en el calendario.

3. Given que una tarea con fecha límite ha generado un evento
   When consulto mi Google Calendar
   Then existe un evento asociado a esa tarea.

Historia de usuario 5.3

Como usuario con Google conectado, quiero que al cambiar la fecha de una tarea se actualice el evento en Google Calendar, para mantener ambos sitios alineados.

Criterios de aceptación (Given/When/Then):

1. Given que una tarea con fecha límite ya tiene un evento en el calendario
   When cambio la fecha límite de la tarea en FlowSync
   Then el evento correspondiente en Google Calendar se actualiza a la nueva fecha.

2. Given que una tarea sin fecha límite no tenía evento
   When le asigno una fecha límite
   Then se crea el evento correspondiente en el calendario (asumido).

Historia de usuario 5.4

Como usuario con Google conectado, quiero que al completar o borrar una tarea el evento del calendario se elimine o marque según corresponda, para que mi calendario no muestre pendientes ya resueltos.

Criterios de aceptación (Given/When/Then):

1. Given que una tarea con fecha límite tiene un evento en el calendario
   When borro la tarea en FlowSync
   Then el evento correspondiente en Google Calendar se elimina.

2. Given que una tarea con fecha límite tiene un evento en el calendario
   When marco la tarea como `completed`
   Then el evento correspondiente se elimina o se marca según corresponda.

Historia de usuario 5.5

Como usuario, quiero desconectar mi cuenta de Google en cualquier momento, para dejar de sincronizar sin perder mis tareas.

Criterios de aceptación (Given/When/Then):

1. Given que tengo Google conectado
   When desconecto mi cuenta de Google
   Then FlowSync deja de sincronizar con el calendario.

2. Given que he desconectado Google
   When consulto mi listado de tareas
   Then las tareas ya creadas siguen existiendo y no se borran.

Historia de usuario 5.6

Como usuario, quiero que mis tareas se guarden aunque la sincronización con Google falle, para no perder información cuando la API de Google no esté disponible.

Criterios de aceptación (Given/When/Then):

1. Given que la API de Google no está disponible o devuelve un error
   When creo o modifico una tarea con fecha límite
   Then la tarea se guarda correctamente en FlowSync aunque la sincronización no se complete.

2. Given que una operación de sincronización ha fallado
   When el sistema lo detecta
   Then la sincronización se reintenta más tarde.

3. Given que ocurre una operación de sincronización con Google
   When se ejecuta (con éxito o error)
   Then queda registrada en los logs para poder diagnosticar fallos.

===================================================================
NOTAS SOBRE ALCANCE (FUERA DEL MVP SEGÚN EL PRD)
===================================================================

Las siguientes funcionalidades NO se han incluido en el backlog porque el PRD las marca explícitamente como fuera del MVP:

1. Equipos o tareas compartidas (FlowSync MVP es estrictamente individual).
2. Sincronización con calendarios que no sean Google (Outlook, iCal): post-MVP.
3. Notificaciones push o por email.
4. Aplicación móvil nativa (solo web responsive).
5. Etiquetas, proyectos o jerarquías de tareas (subtareas): post-MVP.
6. Recordatorios configurables más allá de los del propio Google Calendar.
7. Sincronización inversa completa (editar un evento en Google y reflejarlo en la tarea): el PRD la describe como deseable pero pendiente de validar con un spike técnico antes de comprometerla; no forma parte del alcance comprometido del MVP.