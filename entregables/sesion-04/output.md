## Autenticación y cuenta

### AUTH-01 Registro de nueva cuenta

**Como** visitante sin cuenta,
**quiero** registrarme con mi email y contraseña,
**para** empezar a usar FlowSync con mis propios datos.

**Criterios de aceptación:**
1. **Given** que no existe una cuenta con el email introducido, **when** el visitante envía el formulario de registro con un email válido y una contraseña de al menos 8 caracteres, **then** se crea la cuenta y el usuario queda autenticado.
2. **Given** que el email introducido ya está registrado, **when** el visitante intenta registrarse, **then** el sistema muestra un mensaje indicando que el email ya existe y ofrece un enlace a inicio de sesión.
3. **Given** que la contraseña introducida tiene menos de 8 caracteres, **when** el visitante envía el formulario, **then** el registro se rechaza y se muestra un error de validación comprensible junto al campo.
4. **Given** un registro exitoso, **when** el usuario llega por primera vez a la aplicación, **then** ve una pantalla de bienvenida que explica en una frase qué hace FlowSync e invita a crear la primera tarea.

### AUTH-02 Iniciar y cerrar sesión

**Como** usuario registrado,
**quiero** iniciar sesión con mi email y contraseña, y poder cerrarla cuando quiera,
**para** acceder de forma segura a mis tareas y proteger mi cuenta en dispositivos compartidos.

**Criterios de aceptación:**
1. **Given** un usuario registrado con credenciales válidas, **when** introduce su email y contraseña correctos, **then** queda autenticado y accede al listado de sus tareas.
2. **Given** un usuario que introduce credenciales incorrectas, **when** envía el formulario de login, **then** el acceso se rechaza con un mensaje de error genérico y comprensible **(asumido: no se especifica si el mensaje distingue email inexistente de contraseña incorrecta; se asume un mensaje genérico por buenas prácticas de seguridad)**.
3. **Given** un usuario autenticado, **when** selecciona "cerrar sesión", **then** su sesión se termina **(asumido: se invalida el token de acceso)** y es redirigido a la pantalla de inicio de sesión.
4. **Given** un usuario no autenticado, **when** intenta acceder a cualquier pantalla de la aplicación distinta de login o registro, **then** es redirigido a la pantalla de inicio de sesión **(asumido, comportamiento estándar de rutas protegidas no detallado en el PRD)**.

---

## Gestión de tareas

### TASK-01 Crear una tarea

**Como** usuario autenticado,
**quiero** crear una tarea indicando al menos un título,
**para** registrar un pendiente que quiero recordar o gestionar.

**Criterios de aceptación:**
1. **Given** que el usuario está autenticado, **when** introduce un título y guarda sin más datos, **then** la tarea se crea con estado `pending` y descripción/fecha límite vacías.
2. **Given** que el usuario no introduce ningún título, **when** intenta guardar, **then** la creación se rechaza con un mensaje de validación claro.
3. **Given** que el usuario introduce título, descripción y fecha límite, **when** guarda la tarea, **then** los tres campos quedan almacenados y visibles en el listado.
4. **Given** que el usuario introduce una fecha límite en un formato inválido **(asumido: el PRD no detalla validación de formato de fecha)**, **when** intenta guardar, **then** el sistema rechaza la entrada con un mensaje de error claro.

### TASK-02 Editar o eliminar una tarea existente

**Como** usuario autenticado,
**quiero** editar cualquier campo de una tarea existente o eliminarla,
**para** mantener mi lista de pendientes actualizada y correcta.

**Criterios de aceptación:**
1. **Given** una tarea existente del usuario, **when** edita cualquier campo (título, descripción o fecha límite) y guarda, **then** los cambios se reflejan inmediatamente en el listado.
2. **Given** una tarea existente, **when** el usuario confirma su eliminación **(asumido: existe un paso de confirmación para evitar borrados accidentales, no detallado en el PRD)**, **then** la tarea desaparece del listado de forma permanente.
3. **Given** una tarea que pertenece a otro usuario, **when** alguien distinto al propietario intenta editarla o eliminarla, **then** el sistema lo impide, ya que los datos de un usuario nunca son accesibles por otro.
4. **Given** una tarea con evento ya sincronizado en Google Calendar, **when** el usuario cambia su fecha límite, **then** el nuevo valor queda registrado como referencia para la sincronización posterior con el calendario.

### TASK-03 Cambiar el estado de una tarea

**Como** usuario autenticado,
**quiero** cambiar el estado de una tarea entre pendiente, completada y archivada,
**para** reflejar su progreso real.

**Criterios de aceptación:**
1. **Given** una tarea en estado `pending`, **when** el usuario la marca como completada, **then** su estado cambia a `completed` y se refleja visualmente en el listado.
2. **Given** una tarea en cualquier estado, **when** el usuario la archiva, **then** su estado cambia a `archived` y deja de aparecer en el listado por defecto **(asumido: el PRD no especifica si las tareas archivadas se excluyen del listado principal)**.
3. **Given** una tarea recién creada, **when** se consulta su estado sin haberlo modificado, **then** el estado es `pending`.
4. **Given** una tarea completada, **when** el usuario la vuelve a marcar como pendiente **(asumido: el PRD no aclara si esta transición inversa está permitida; se asume que sí, al no definirse restricciones de transición entre estados)**, **then** su estado vuelve a `pending`.

---

## Organización y filtrado

### ORG-01 Filtrar tareas por estado

**Como** usuario autenticado,
**quiero** filtrar mis tareas por estado,
**para** centrarme solo en las que son relevantes en cada momento.

**Criterios de aceptación:**
1. **Given** que el usuario tiene tareas en distintos estados, **when** selecciona el filtro "pendientes", **then** solo ve las tareas con estado `pending`.
2. **Given** que el usuario tiene tareas en distintos estados, **when** selecciona el filtro "completadas", **then** solo ve las tareas con estado `completed`.
3. **Given** que el usuario tiene tareas archivadas, **when** quiere consultarlas, **then** debe seleccionar explícitamente el filtro "archivadas" **(asumido: el PRD no detalla si aparecen por defecto en algún filtro)**.
4. **Given** que el usuario no aplica ningún filtro, **when** accede al listado, **then** ve sus tareas según el criterio de ordenación por defecto descrito en ORG-02.

### ORG-02 Ver el listado de tareas ordenado y manejar el estado vacío

**Como** usuario autenticado,
**quiero** ver mis tareas ordenadas por relevancia para hoy y un mensaje claro cuando no tengo ninguna,
**para** saber rápidamente qué hacer sin tener que buscar entre todas mis tareas.

**Criterios de aceptación:**
1. **Given** que el usuario tiene varias tareas pendientes con distintas fechas límite, **when** accede al listado, **then** las más relevantes para "hoy" aparecen primero **(asumido: el criterio exacto de ordenación no está definido en el PRD; queda a decisión del equipo durante el refinamiento)**.
2. **Given** una cuenta nueva sin tareas creadas, **when** el usuario accede al listado, **then** ve un estado vacío con una invitación a crear su primera tarea.
3. **Given** que todas las tareas del usuario están archivadas, **when** accede al listado por defecto, **then** ve el mismo estado vacío que una cuenta sin tareas **(asumido, el PRD no lo especifica literalmente)**.
4. **Given** un usuario con hasta 200 tareas, **when** carga el listado, **then** este se muestra en menos de 1 segundo, conforme al requisito de rendimiento.

---

## Exportación

### EXP-01 Exportar tareas a CSV

**Como** usuario autenticado,
**quiero** exportar mis tareas a un archivo CSV,
**para** tener una copia de mis datos fuera de FlowSync.

**Criterios de aceptación:**
1. **Given** que el usuario tiene tareas creadas, **when** solicita la exportación, **then** se genera un archivo CSV descargable que incluye, como mínimo, título, descripción, estado y fecha límite de cada tarea.
2. **Given** que el usuario no tiene ninguna tarea, **when** solicita la exportación, **then** recibe un CSV válido que contiene únicamente la fila de cabecera **(asumido: el PRD no especifica el comportamiento en este caso límite)**.
3. **Given** que el usuario tiene tareas con campos opcionales vacíos (sin descripción o sin fecha límite), **when** se exportan, **then** esas celdas aparecen vacías en el CSV sin causar errores.
4. **Given** que la exportación se solicita, **when** se genera el archivo, **then** solo incluye las tareas del usuario autenticado, nunca las de otros usuarios.

---

## Sincronización con Google Calendar

### SYNC-01 Conectar la cuenta de Google

**Como** usuario autenticado,
**quiero** conectar mi cuenta de Google a FlowSync mediante OAuth,
**para** habilitar la sincronización de mis tareas con mi calendario.

**Criterios de aceptación:**
1. **Given** un usuario autenticado sin conexión de Google activa, **when** inicia el flujo de conexión, **then** es redirigido a la pantalla de autorización OAuth de Google.
2. **Given** que el usuario autoriza los permisos solicitados en Google, **when** vuelve a FlowSync, **then** su cuenta queda marcada como conectada y puede consultar ese estado **(asumido: existe una pantalla de ajustes que muestra el estado de la conexión, no detallada en el PRD)**.
3. **Given** que el usuario cancela o rechaza la autorización en Google, **when** vuelve a FlowSync, **then** la cuenta permanece sin conectar y se le informa de que la conexión no se completó.
4. **Given** que se obtienen tokens de acceso de Google tras la autorización, **when** se almacenan, **then** se guardan de forma segura y nunca son accesibles por otro usuario, conforme al requisito de privacidad.

### SYNC-02 Crear eventos en Google Calendar a partir de tareas con fecha límite

**Como** usuario con Google Calendar conectado,
**quiero** que mis tareas con fecha límite aparezcan automáticamente como eventos en mi calendario,
**para** ver en un solo sitio todo lo que tengo que hacer.

**Criterios de aceptación:**
1. **Given** un usuario con Google Calendar conectado, **when** crea una tarea con fecha límite, **then** se genera automáticamente un evento correspondiente en su Google Calendar.
2. **Given** una tarea sin fecha límite, **when** se crea o se guarda, **then** no se genera ningún evento en Google Calendar.
3. **Given** una tarea con fecha límite creada antes de conectar Google, **when** el usuario conecta su cuenta, **then** esa tarea también genera su evento correspondiente **(asumido: el PRD no aclara si la conexión sincroniza retroactivamente las tareas existentes)**.
4. **Given** un evento generado a partir de una tarea, **when** se compara su hora con la fecha límite de la tarea, **then** la hora asignada respeta la zona horaria del usuario sin desplazamientos erróneos, dado el riesgo de zonas horarias señalado en el PRD.

### SYNC-03 Mantener el evento sincronizado ante cambios en la tarea

**Como** usuario con Google Calendar conectado,
**quiero** que el evento de calendario se actualice o elimine cuando cambio, completo o borro la tarea correspondiente,
**para** no tener información desactualizada en mi calendario.

**Criterios de aceptación:**
1. **Given** una tarea con evento ya creado, **when** el usuario cambia su fecha límite, **then** el evento correspondiente en Google Calendar se actualiza a la nueva fecha.
2. **Given** una tarea con evento ya creado, **when** el usuario la marca como completada, **then** el evento se elimina o se marca en consecuencia **(asumido: el PRD deja abierta cuál de las dos opciones aplica, solo indica "se elimina o se marca según corresponda")**.
3. **Given** una tarea con evento ya creado, **when** el usuario la elimina, **then** el evento correspondiente se elimina también de Google Calendar.
4. **Given** que la API de Google no está disponible o devuelve error al intentar actualizar o eliminar un evento, **when** ocurre el fallo, **then** la tarea conserva su cambio en FlowSync, el sistema reintenta la sincronización más tarde y el fallo queda registrado en logs.

### SYNC-04 Desconectar la cuenta de Google

**Como** usuario con Google Calendar conectado,
**quiero** poder desconectar mi cuenta de Google en cualquier momento,
**para** dejar de compartir mi calendario con FlowSync sin perder mis tareas.

**Criterios de aceptación:**
1. **Given** un usuario con Google conectado, **when** selecciona "desconectar" y confirma la acción, **then** FlowSync deja de sincronizar sus tareas con Google Calendar.
2. **Given** que el usuario desconecta su cuenta de Google, **when** se completa la desconexión, **then** las tareas ya creadas en FlowSync permanecen intactas y no se eliminan.
3. **Given** que el usuario desconecta su cuenta, **when** la desconexión se completa, **then** los eventos ya creados en Google Calendar no se eliminan automáticamente **(asumido: el PRD no especifica si se limpian los eventos existentes al desconectar)**.
4. **Given** un usuario que se desconectó previamente, **when** vuelve a conectar su cuenta de Google, **then** debe completar de nuevo el flujo de autorización OAuth descrito en SYNC-01 **(asumido: no se conserva una autorización previa expirada o revocada)**.
