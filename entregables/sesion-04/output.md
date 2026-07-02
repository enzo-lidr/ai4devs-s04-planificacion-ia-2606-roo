## Épica: Autenticación y cuenta

### Story 1: Registro de usuario

**User story**  
Como visitante, quiero crear una cuenta con email y contraseña, para empezar a usar FlowSync con mis propios datos.

**Criterios de aceptación**

Scenario: Registro exitoso  
Given un visitante no autenticado  
And proporciona un email válido y una contraseña de al menos 8 caracteres  
When envía el formulario de registro  
Then se crea una cuenta nueva  
And el usuario llega a una pantalla de bienvenida

Scenario: Contraseña demasiado corta  
Given un visitante no autenticado  
And proporciona una contraseña de menos de 8 caracteres  
When envía el formulario de registro  
Then el sistema rechaza el registro  
And muestra un error claro indicando que la contraseña debe tener al menos 8 caracteres

Scenario: Email ya registrado  
Given existe una cuenta registrada con el email ingresado  
When el visitante intenta registrarse con ese mismo email  
Then el sistema no crea una cuenta duplicada  
And ofrece ir al inicio de sesión

Scenario: Email inválido  
Given un visitante no autenticado  
And proporciona un email con formato inválido  
When envía el formulario de registro  
Then el sistema rechaza el registro  
And muestra un error comprensible sobre el formato del email

**Notas / supuestos**

- El PRD indica que no se requiere verificación de email ni recuperación de contraseña en el MVP.

### Story 2: Inicio y cierre de sesión

**User story**  
Como usuario registrado, quiero iniciar y cerrar sesión, para acceder de forma segura a mis tareas y proteger mi cuenta.

**Criterios de aceptación**

Scenario: Inicio de sesión exitoso  
Given existe una cuenta registrada  
And el usuario proporciona email y contraseña correctos  
When envía el formulario de inicio de sesión  
Then accede a FlowSync como usuario autenticado  
And puede ver su listado de tareas

Scenario: Credenciales incorrectas  
Given existe una cuenta registrada  
And el usuario proporciona una contraseña incorrecta  
When intenta iniciar sesión  
Then el sistema rechaza el acceso  
And muestra un error claro sin exponer detalles técnicos

Scenario: Cierre de sesión exitoso  
Given un usuario autenticado  
When selecciona cerrar sesión  
Then la sesión finaliza  
And el usuario vuelve a un estado no autenticado

Scenario: Acceso sin sesión  
Given un visitante no autenticado  
When intenta acceder al listado de tareas  
Then el sistema impide el acceso  
And solicita iniciar sesión

**Notas / supuestos**

- El PRD indica que la sesión se mantiene mediante tokens de acceso.
- La pantalla exacta posterior al login no está detallada; se asume el listado de tareas como destino principal **(asumido)**.

### Story 3: Privacidad de datos entre usuarios

**User story**  
Como usuario autenticado, quiero que mis tareas y conexión de Google no sean visibles para otros usuarios, para mantener privados mis datos personales.

**Criterios de aceptación**

Scenario: Usuario ve solo sus tareas  
Given existen tareas creadas por dos usuarios distintos  
When uno de ellos abre su listado de tareas  
Then solo ve las tareas asociadas a su propia cuenta  
And no ve tareas del otro usuario

Scenario: Usuario no puede editar tareas ajenas  
Given existe una tarea creada por otro usuario  
When un usuario autenticado intenta modificar esa tarea  
Then el sistema rechaza la operación  
And la tarea original no cambia

Scenario: Usuario no puede borrar tareas ajenas  
Given existe una tarea creada por otro usuario  
When un usuario autenticado intenta borrarla  
Then el sistema rechaza la operación  
And la tarea sigue disponible para su dueño

Scenario: Conexión de Google aislada  
Given dos usuarios tienen cuentas de FlowSync distintas  
When uno conecta o desconecta Google Calendar  
Then la conexión de Google del otro usuario no se modifica

**Notas / supuestos**

- El PRD explicita que los datos de un usuario y los tokens de Google nunca son accesibles por otros usuarios.

## Épica: Gestión de tareas

### Story 4: Crear tarea

**User story**  
Como usuario autenticado, quiero crear una tarea con título, descripción opcional y fecha límite opcional, para registrar mis pendientes en FlowSync.

**Criterios de aceptación**

Scenario: Crear tarea con título  
Given un usuario autenticado  
When crea una tarea indicando un título válido  
Then la tarea se guarda correctamente  
And queda en estado `pending`

Scenario: Crear tarea sin título  
Given un usuario autenticado  
When intenta crear una tarea sin título  
Then el sistema rechaza la operación  
And muestra un error claro indicando que el título es obligatorio

Scenario: Crear tarea con descripción  
Given un usuario autenticado  
And proporciona título y descripción  
When crea la tarea  
Then la tarea se guarda con la descripción indicada  
And aparece en el listado del usuario

Scenario: Crear tarea con fecha límite  
Given un usuario autenticado  
And proporciona título y fecha límite  
When crea la tarea  
Then la tarea se guarda con esa fecha límite  
And queda disponible para sincronización con Google Calendar si la cuenta está conectada

**Notas / supuestos**

- La disponibilidad para sincronización depende de que el usuario haya conectado Google Calendar.

### Story 5: Editar y borrar tarea

**User story**  
Como usuario autenticado, quiero editar y borrar mis tareas, para mantener mi lista actualizada.

**Criterios de aceptación**

Scenario: Editar título de tarea  
Given un usuario autenticado  
And existe una tarea propia  
When cambia el título por otro válido  
Then la tarea se guarda con el nuevo título  
And el cambio se refleja en el listado

Scenario: Editar descripción o fecha límite  
Given un usuario autenticado  
And existe una tarea propia  
When modifica la descripción o la fecha límite  
Then la tarea se guarda con los nuevos valores  
And los valores anteriores dejan de mostrarse

Scenario: Borrar tarea propia  
Given un usuario autenticado  
And existe una tarea propia  
When borra la tarea  
Then la tarea deja de aparecer en su listado  
And no queda disponible para edición

Scenario: Editar dejando título vacío  
Given un usuario autenticado  
And existe una tarea propia  
When intenta guardar la tarea con título vacío  
Then el sistema rechaza la modificación  
And muestra un error claro indicando que el título es obligatorio

**Notas / supuestos**

- El PRD permite editar cualquier campo de una tarea existente.
- El comportamiento visual exacto tras borrar no está detallado **(asumido)**.

### Story 6: Cambiar estado de tarea

**User story**  
Como usuario autenticado, quiero cambiar el estado de una tarea, para reflejar si está pendiente, completada o archivada.

**Criterios de aceptación**

Scenario: Tarea nueva nace pendiente  
Given un usuario autenticado  
When crea una tarea válida  
Then la tarea queda en estado `pending`  
And ese estado se muestra en el listado

Scenario: Marcar tarea como completada  
Given un usuario autenticado  
And existe una tarea propia en estado `pending`  
When la marca como completada  
Then la tarea queda en estado `completed`  
And el nuevo estado se refleja en el listado

Scenario: Archivar tarea  
Given un usuario autenticado  
And existe una tarea propia  
When cambia su estado a `archived`  
Then la tarea queda en estado `archived`  
And puede ser excluida del estado vacío de tareas activas

Scenario: Cambiar estado inválido  
Given un usuario autenticado  
And existe una tarea propia  
When intenta asignar un estado distinto de `pending`, `completed` o `archived`  
Then el sistema rechaza el cambio  
And muestra un error claro sobre los estados permitidos

**Notas / supuestos**

- La exclusión de tareas archivadas del estado vacío se infiere de “cuenta nueva o todas archivadas” **(asumido)**.

## Épica: Organización y filtrado

### Story 7: Listado, filtros y estado vacío

**User story**  
Como usuario autenticado, quiero ver, filtrar y reconocer rápidamente mis tareas, para encontrar lo que necesito sin revisar toda la lista.

**Criterios de aceptación**

Scenario: Ver listado de tareas  
Given un usuario autenticado con tareas propias existentes  
When abre FlowSync  
Then ve un listado con sus tareas  
And cada tarea muestra al menos título, estado y fecha límite si existe

Scenario: Filtrar por estado pendiente  
Given un usuario autenticado con tareas en distintos estados  
When filtra por estado `pending`  
Then el listado muestra solo tareas pendientes  
And oculta tareas completadas y archivadas

Scenario: Filtrar por estado completada o archivada  
Given un usuario autenticado con tareas completadas y archivadas  
When filtra por `completed` o `archived`  
Then el listado muestra solo las tareas del estado seleccionado  
And mantiene ocultas las tareas de otros estados

Scenario: Estado vacío  
Given un usuario autenticado sin tareas visibles  
When abre el listado de tareas  
Then ve un estado vacío  
And se muestra una invitación a crear la primera tarea

Scenario: Orden por relevancia para hoy  
Given un usuario autenticado con varias tareas  
When abre el listado sin aplicar filtros  
Then las tareas se muestran con las más relevantes para “hoy” primero  
And el criterio exacto queda pendiente de refinamiento

**Notas / supuestos**

- El PRD deja explícitamente abierto el criterio exacto de ordenación para “hoy”.
- Mostrar título, estado y fecha límite en el listado es una inferencia funcional mínima **(asumido)**.

## Épica: Exportación

### Story 8: Exportar tareas a CSV

**User story**  
Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos fuera de FlowSync.

**Criterios de aceptación**

Scenario: Exportación exitosa  
Given un usuario autenticado con tareas propias existentes  
When solicita exportar sus tareas  
Then se genera un archivo CSV  
And el archivo incluye título, descripción, estado y fecha límite de cada tarea

Scenario: Exportación sin tareas  
Given un usuario autenticado sin tareas  
When solicita exportar sus tareas  
Then se genera un CSV sin filas de tareas  
And el archivo conserva los encabezados esperados

Scenario: Exportación respeta privacidad  
Given existen tareas de otros usuarios  
When un usuario exporta sus tareas  
Then el CSV incluye solo las tareas propias  
And no incluye datos de otros usuarios

Scenario: Exportación con campos opcionales vacíos  
Given una tarea propia sin descripción o sin fecha límite  
When el usuario exporta sus tareas  
Then el CSV incluye la tarea  
And representa los campos opcionales vacíos sin fallar

**Notas / supuestos**

- El formato exacto de nombres de columnas no está definido en el PRD **(asumido)**.

## Épica: Sincronización con Google Calendar

### Story 9: Conectar y desconectar Google Calendar

**User story**  
Como usuario autenticado, quiero conectar y desconectar mi cuenta de Google, para controlar si mis tareas se sincronizan con Google Calendar.

**Criterios de aceptación**

Scenario: Conexión exitosa con Google  
Given un usuario autenticado sin cuenta de Google conectada  
When completa la autorización OAuth con Google  
Then FlowSync registra la conexión de Google  
And el usuario ve que Google Calendar está conectado

Scenario: Fallo o cancelación de autorización  
Given un usuario autenticado inicia la conexión con Google  
When Google devuelve un error o el usuario cancela la autorización  
Then FlowSync no registra la conexión  
And muestra un mensaje claro sobre la conexión no completada

Scenario: Desconectar Google  
Given un usuario autenticado con Google Calendar conectado  
When desconecta su cuenta de Google  
Then FlowSync deja de sincronizar nuevas operaciones con Google Calendar  
And las tareas ya creadas en FlowSync no se borran

Scenario: Reconocer estado de conexión  
Given un usuario autenticado  
When consulta la configuración o área de integración con Google  
Then puede distinguir si Google Calendar está conectado o desconectado

**Notas / supuestos**

- El PRD indica OAuth con Google, pero no define la ubicación exacta de la acción de conexión **(asumido)**.

### Story 10: Crear y actualizar eventos desde tareas con fecha límite

**User story**  
Como usuario autenticado con Google Calendar conectado, quiero que mis tareas con fecha límite aparezcan y se actualicen como eventos, para evitar duplicar manualmente mi planificación.

**Criterios de aceptación**

Scenario: Crear evento al crear tarea con fecha límite  
Given un usuario autenticado con Google Calendar conectado  
When crea una tarea con fecha límite  
Then la tarea se guarda en FlowSync  
And aparece un evento correspondiente en Google Calendar

Scenario: No crear evento para tarea sin fecha límite  
Given un usuario autenticado con Google Calendar conectado  
When crea una tarea sin fecha límite  
Then la tarea se guarda en FlowSync  
And no se crea un evento en Google Calendar

Scenario: Actualizar evento al cambiar fecha  
Given una tarea propia con fecha límite sincronizada con Google Calendar  
When el usuario cambia la fecha límite en FlowSync  
Then el evento correspondiente en Google Calendar se actualiza  
And la tarea conserva la nueva fecha en FlowSync

Scenario: Agregar fecha límite a tarea existente  
Given una tarea propia sin fecha límite  
And el usuario tiene Google Calendar conectado  
When agrega una fecha límite a la tarea  
Then se crea un evento correspondiente en Google Calendar  
And la tarea queda guardada con esa fecha límite

**Notas / supuestos**

- La representación exacta de la fecha límite como evento, incluyendo hora, duración y zona horaria, no está definida en el PRD **(asumido)**.
- La sincronización inversa completa desde Google Calendar no se incluye como story del MVP.

### Story 11: Completar o borrar tareas sincronizadas

**User story**  
Como usuario autenticado con tareas sincronizadas, quiero que completar o borrar una tarea actualice su evento de calendario, para que Google Calendar no quede desalineado.

**Criterios de aceptación**

Scenario: Completar tarea sincronizada  
Given una tarea propia pendiente con fecha límite y evento en Google Calendar  
When el usuario marca la tarea como completada en FlowSync  
Then la tarea queda en estado `completed`  
And el evento correspondiente en Google Calendar se elimina o se marca según corresponda

Scenario: Borrar tarea sincronizada  
Given una tarea propia con fecha límite y evento en Google Calendar  
When el usuario borra la tarea en FlowSync  
Then la tarea deja de aparecer en FlowSync  
And el evento correspondiente en Google Calendar se elimina o se marca según corresponda

Scenario: Completar tarea no sincronizada  
Given una tarea propia sin evento asociado en Google Calendar  
When el usuario la marca como completada  
Then la tarea queda en estado `completed`  
And no se intenta modificar un evento inexistente

Scenario: Borrar tarea después de desconectar Google  
Given un usuario desconectó Google Calendar  
And existe una tarea con fecha límite creada antes de la desconexión  
When borra la tarea en FlowSync  
Then la tarea se borra en FlowSync  
And FlowSync no realiza una nueva sincronización con Google Calendar

**Notas / supuestos**

- El PRD no define si completar una tarea debe eliminar o marcar el evento; esto requiere refinamiento.
- La expectativa de no sincronizar tras desconexión se deriva de “FlowSync deja de sincronizar” **(asumido)**.

### Story 12: Manejo de fallos y observabilidad de sincronización

**User story**  
Como usuario autenticado, quiero que mis tareas se guarden aunque Google Calendar falle, para no perder mi trabajo cuando la sincronización tenga problemas.

**Criterios de aceptación**

Scenario: Fallo de Google al crear evento  
Given un usuario autenticado con Google Calendar conectado  
And Google Calendar no está disponible o devuelve un error  
When crea una tarea con fecha límite  
Then la tarea se guarda en FlowSync  
And la sincronización queda pendiente para reintentarse más tarde

Scenario: Fallo de Google al actualizar evento  
Given una tarea propia sincronizada con Google Calendar  
And Google Calendar devuelve un error  
When el usuario cambia la fecha límite en FlowSync  
Then el cambio se guarda en FlowSync  
And la actualización del evento queda pendiente para reintentarse más tarde

Scenario: Error comprensible para el usuario  
Given ocurre un fallo de sincronización con Google Calendar  
When el usuario consulta la tarea afectada o el resultado de la operación  
Then ve un mensaje comprensible sobre el problema de sincronización  
And el mensaje no expone detalles técnicos internos

Scenario: Registro de operación de sincronización  
Given ocurre una operación de sincronización con Google Calendar  
When la operación termina con éxito o error  
Then FlowSync registra el resultado para diagnóstico  
And el registro permite distinguir operación exitosa de operación fallida

**Notas / supuestos**

- El PRD indica que la tarea se guarda aunque falle la sincronización y que se reintenta más tarde.
- La forma visible exacta del estado de sincronización para el usuario no está definida **(asumido)**.
- La estrategia concreta de reintentos no está definida en el PRD.

## Supuestos y puntos para refinamiento

- El criterio exacto de ordenación de tareas relevantes para “hoy” queda abierto por el PRD.
- La fecha límite necesita definición funcional: si representa solo día, día y hora, duración del evento, evento de todo el día y zona horaria.
- El comportamiento exacto al completar una tarea sincronizada debe decidirse: eliminar evento, marcarlo de alguna forma o mantenerlo.
- El comportamiento exacto al borrar una tarea sincronizada debe decidirse: eliminar evento o marcarlo según una regla explícita.
- La sincronización inversa Google Calendar → FlowSync aparece como deseable, pero requiere spike y no queda comprometida como user story del MVP.
- La estrategia de reintentos ante fallos de Google Calendar debe refinarse: cuándo reintentar, cuántas veces y cómo informar estados no recuperables.
- El PRD pide logs de sincronización, pero no define qué datos mínimos deben registrarse ni cómo se consultan.
- La interfaz debe ser responsive, pero el PRD no define breakpoints ni flujos específicos para móvil.
