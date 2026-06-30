# Backlog de User Stories — FlowSync MVP

> Decomposición del PRD v1.0 (Q3 2026) en épicas y user stories.
> Los criterios de aceptación marcados con **(asumido)** son inferencias razonables no especificadas literalmente en el PRD; se recomienda validarlos en refinamiento.

---

## Épica 1: Autenticación y gestión de cuenta

### Story: Registro de cuenta

Como visitante de FlowSync,
quiero crear una cuenta con mi email y contraseña,
para poder gestionar mis tareas de forma privada.

##### Scenario: Registro exitoso
  Given el visitante está en la pantalla de registro
  When introduce un email no registrado y una contraseña de al menos 8 caracteres
  Then el sistema crea la cuenta
  And lo lleva a una pantalla de bienvenida que explica brevemente qué hace FlowSync e invita a crear la primera tarea

##### Scenario: Email ya registrado
  Given el visitante está en la pantalla de registro
  When introduce un email que ya tiene una cuenta asociada
  Then el sistema muestra un mensaje indicando que ese email ya está registrado
  And ofrece un enlace para ir a la pantalla de inicio de sesión

##### Scenario: Contraseña demasiado corta
  Given el visitante está en la pantalla de registro
  When introduce una contraseña de menos de 8 caracteres
  Then el sistema muestra un error de validación comprensible
  And no crea la cuenta

##### Scenario: Email con formato inválido
  Given el visitante está en la pantalla de registro
  When introduce un email con un formato inválido
  Then el sistema muestra un error de validación comprensible
  And no crea la cuenta

##### Scenario: Campos obligatorios vacíos
  Given el visitante está en la pantalla de registro
  When intenta enviar el formulario con el email o la contraseña vacíos
  Then el sistema muestra un error indicando que esos campos son obligatorios
  And no envía la solicitud de registro

---

### Story: Inicio de sesión

Como usuario registrado de FlowSync,
quiero iniciar sesión con mi email y contraseña,
para acceder a mis tareas.

##### Scenario: Inicio de sesión exitoso
  Given el usuario está registrado y en la pantalla de inicio de sesión
  When introduce su email y contraseña correctos
  Then el sistema lo autentica
  And lo lleva al listado de sus tareas **(asumido: pantalla de destino exacta no especificada en el PRD)**

##### Scenario: Credenciales incorrectas
  Given el usuario está en la pantalla de inicio de sesión
  When introduce un email no registrado o una contraseña incorrecta
  Then el sistema muestra un mensaje de error genérico, sin indicar cuál de los dos datos es incorrecto **(asumido, por buenas prácticas de seguridad no detalladas en el PRD)**
  And no inicia sesión

##### Scenario: Campos vacíos
  Given el usuario está en la pantalla de inicio de sesión
  When intenta enviar el formulario con el email o la contraseña vacíos
  Then el sistema muestra un error de validación de campos obligatorios
  And no envía la solicitud

---

### Story: Cierre de sesión

Como usuario autenticado de FlowSync,
quiero cerrar sesión,
para proteger el acceso a mis tareas en dispositivos compartidos.

##### Scenario: Cierre de sesión exitoso
  Given el usuario tiene una sesión activa
  When selecciona la opción de cerrar sesión
  Then el sistema invalida su token de acceso **(asumido: mecanismo de invalidación no detallado en el PRD)**
  And lo redirige a la pantalla de inicio de sesión

##### Scenario: Acción tras expiración de sesión
  Given la sesión del usuario ha expirado o fue cerrada en otro lugar **(asumido)**
  When intenta realizar una acción que requiere estar autenticado
  Then el sistema lo redirige a la pantalla de inicio de sesión indicando que su sesión expiró

##### Scenario: Fallo al cerrar sesión
  Given ocurre un error de red o de servidor al cerrar sesión **(asumido)**
  When el usuario selecciona cerrar sesión
  Then el sistema limpia la sesión localmente de todas formas
  And lo lleva a la pantalla de inicio de sesión **(asumido)**

---

## Épica 2: Gestión de tareas (CRUD)

### Story: Crear tarea

Como usuario autenticado,
quiero crear una tarea indicando al menos un título,
para registrar un pendiente.

##### Scenario: Creación exitosa con solo título
  Given el usuario está en el formulario de nueva tarea
  When introduce un título y guarda sin añadir descripción ni fecha límite
  Then el sistema crea la tarea con estado `pending`
  And la muestra en el listado de tareas

##### Scenario: Creación con todos los campos
  Given el usuario está en el formulario de nueva tarea
  When introduce título, descripción y fecha límite
  Then el sistema crea la tarea con esos datos y estado `pending`

##### Scenario: Título vacío
  Given el usuario está en el formulario de nueva tarea
  When intenta guardar sin introducir un título
  Then el sistema muestra un error de validación indicando que el título es obligatorio
  And no crea la tarea

##### Scenario: Fallo al guardar
  Given ocurre un error del sistema al intentar guardar **(asumido)**
  When el usuario intenta crear una tarea
  Then el sistema muestra un mensaje de error comprensible
  And no pierde los datos que el usuario ya había introducido en el formulario **(asumido)**

---

### Story: Ver listado de tareas

Como usuario autenticado,
quiero ver el listado de mis tareas,
para saber qué tengo pendiente.

##### Scenario: Listado con tareas existentes
  Given el usuario tiene tareas creadas
  When accede al listado de tareas
  Then ve todas sus tareas con título, estado y fecha límite (cuando la tenga)

##### Scenario: Privacidad entre usuarios
  Given existen dos usuarios distintos, cada uno con sus propias tareas
  When cada uno accede a su listado
  Then cada usuario ve únicamente sus propias tareas, nunca las de otro usuario

##### Scenario: Fallo al cargar el listado
  Given ocurre un error al cargar las tareas **(asumido)**
  When el usuario accede al listado
  Then el sistema muestra un mensaje de error con opción de reintentar **(asumido)**

---

### Story: Editar tarea

Como usuario autenticado,
quiero editar cualquier campo de una tarea existente,
para mantener actualizada su información.

##### Scenario: Edición exitosa
  Given el usuario tiene una tarea existente
  When modifica el título, la descripción o la fecha límite y guarda
  Then el sistema actualiza la tarea con los nuevos valores

##### Scenario: Vaciar el título
  Given el usuario está editando una tarea
  When intenta guardar dejando el título vacío
  Then el sistema muestra un error de validación
  And no guarda el cambio

##### Scenario: Eliminar una fecha límite existente
  Given una tarea tiene actualmente una fecha límite
  When el usuario elimina esa fecha límite y guarda
  Then el sistema actualiza la tarea para que quede sin fecha límite

##### Scenario: Fallo al guardar cambios
  Given ocurre un error del sistema al guardar **(asumido)**
  When el usuario intenta guardar los cambios de una tarea
  Then el sistema muestra un mensaje de error
  And conserva los datos editados en el formulario para que el usuario no los pierda **(asumido)**

---

### Story: Borrar tarea

Como usuario autenticado,
quiero borrar una tarea,
para eliminar pendientes que ya no necesito.

##### Scenario: Borrado exitoso
  Given el usuario tiene una tarea existente
  When confirma el borrado de esa tarea
  Then el sistema la elimina
  And deja de mostrarla en el listado

##### Scenario: Confirmación antes de borrar
  Given el usuario pulsa la opción de borrar una tarea
  When el sistema le solicita confirmación **(asumido: comportamiento de UX no especificado en el PRD)**
  Then la tarea solo se elimina si el usuario confirma la acción

##### Scenario: Fallo al borrar
  Given ocurre un error del sistema al borrar **(asumido)**
  When el usuario intenta borrar una tarea
  Then el sistema muestra un mensaje de error
  And la tarea permanece en el listado

---

### Story: Cambiar el estado de una tarea

Como usuario autenticado,
quiero cambiar el estado de una tarea,
para reflejar su progreso (pendiente, completada o archivada).

##### Scenario: Marcar como completada
  Given una tarea está en estado `pending`
  When el usuario la marca como completada
  Then el sistema actualiza su estado a `completed`

##### Scenario: Archivar una tarea
  Given una tarea existente
  When el usuario la archiva
  Then el sistema actualiza su estado a `archived`

##### Scenario: Reabrir una tarea completada
  Given una tarea está en estado `completed`
  When el usuario la marca nuevamente como pendiente **(asumido: el PRD no especifica explícitamente esta transición inversa)**
  Then el sistema actualiza su estado a `pending`

##### Scenario: Fallo al cambiar el estado
  Given ocurre un error del sistema **(asumido)**
  When el usuario intenta cambiar el estado de una tarea
  Then el sistema muestra un mensaje de error
  And el estado de la tarea no cambia

---

## Épica 3: Organización y filtrado

### Story: Filtrar tareas por estado

Como usuario autenticado,
quiero filtrar mis tareas por estado,
para encontrar rápidamente las que me interesan en cada momento.

##### Scenario: Filtro aplicado con resultados
  Given el usuario tiene tareas en distintos estados
  When selecciona el filtro de un estado concreto (pendientes, completadas o archivadas)
  Then el sistema muestra solo las tareas con ese estado

##### Scenario: Filtro sin resultados
  Given el usuario no tiene ninguna tarea en el estado seleccionado
  When aplica ese filtro
  Then el sistema muestra un estado vacío indicando que no hay tareas con ese estado **(asumido)**

##### Scenario: Quitar el filtro
  Given el usuario tiene un filtro aplicado
  When selecciona ver todas las tareas
  Then el sistema vuelve a mostrar todas las tareas sin filtrar **(asumido)**

##### Scenario: Fallo al aplicar el filtro
  Given ocurre un error del sistema **(asumido)**
  When el usuario aplica un filtro
  Then el sistema muestra un mensaje de error
  And mantiene visible el listado anterior **(asumido)**

---

### Story: Ver el listado ordenado por relevancia para hoy

Como usuario autenticado,
quiero ver mi listado de tareas ordenado de forma que las más relevantes para hoy aparezcan primero,
para identificar rápidamente qué hacer primero.

> Nota: el PRD deja el criterio exacto de ordenación a decisión del equipo durante el refinamiento. Los siguientes escenarios verifican el comportamiento observable, no el algoritmo.

##### Scenario: Orden por defecto
  Given el usuario tiene varias tareas, algunas con fecha límite y otras sin ella
  When accede al listado sin aplicar filtros
  Then el sistema muestra las tareas ordenadas de forma que las más relevantes para hoy aparecen primero (el criterio exacto se define en refinamiento)

##### Scenario: Tareas sin fecha límite en el orden
  Given existen tareas sin fecha límite
  When se muestra el listado ordenado
  Then estas tareas se incluyen en el listado según el criterio de ordenación que se defina **(asumido: posición exacta no especificada en el PRD)**

##### Scenario: Fallo al calcular el orden
  Given ocurre un error al determinar el orden **(asumido)**
  When el usuario accede al listado
  Then el sistema muestra igualmente las tareas, aunque sea con un orden por defecto **(asumido)**

---

### Story: Estado vacío del listado

Como usuario autenticado,
quiero ver una invitación a crear mi primera tarea cuando no tengo ninguna,
para saber cómo empezar a usar FlowSync.

##### Scenario: Cuenta nueva sin tareas
  Given un usuario recién registrado sin tareas creadas
  When accede al listado de tareas
  Then el sistema muestra un estado vacío con una invitación a crear la primera tarea

##### Scenario: Todas las tareas archivadas
  Given un usuario cuyas tareas están todas archivadas
  When accede al listado sin filtros
  Then el sistema muestra el mismo estado vacío con la invitación a crear una tarea **(asumido: el PRD no distingue explícitamente este caso del de cuenta nueva)**

##### Scenario: Filtro sin coincidencias
  Given un usuario con tareas pero un filtro que no tiene coincidencias
  When aplica ese filtro
  Then el sistema muestra un mensaje de estado vacío específico para el filtro, distinto del de "sin tareas en absoluto" **(asumido)**

##### Scenario: Error al determinar si hay tareas
  Given ocurre un error del sistema al consultar las tareas **(asumido)**
  When el usuario accede al listado
  Then el sistema muestra un mensaje de error en lugar de un estado vacío incorrecto **(asumido)**

---

## Épica 4: Exportación

### Story: Exportar tareas a CSV

Como usuario autenticado,
quiero exportar mis tareas a un archivo CSV,
para tener una copia de mis datos fuera de FlowSync.

##### Scenario: Exportación exitosa
  Given el usuario tiene tareas creadas
  When solicita exportar sus tareas
  Then el sistema genera un archivo CSV descargable que incluye título, descripción, estado y fecha límite de cada tarea

##### Scenario: Exportar sin tareas
  Given el usuario no tiene ninguna tarea creada
  When solicita exportar
  Then el sistema genera un CSV vacío (solo con cabeceras) o muestra un mensaje indicando que no hay tareas que exportar **(asumido)**

##### Scenario: Tareas con campos opcionales vacíos
  Given existen tareas sin descripción o sin fecha límite
  When se exportan a CSV
  Then el archivo incluye esas tareas con los campos opcionales vacíos, sin generar errores

##### Scenario: Fallo al generar el archivo
  Given ocurre un error del sistema al generar el CSV **(asumido)**
  When el usuario solicita exportar sus tareas
  Then el sistema muestra un mensaje de error
  And no se descarga ningún archivo

---


## Épica 5: Sincronización con Google Calendar

### Story: Conectar cuenta de Google

Como usuario autenticado,
quiero conectar mi cuenta de Google a FlowSync,
para que mis tareas con fecha límite se reflejen en mi calendario.

##### Scenario: Conexión exitosa
  Given el usuario autenticado no tiene una cuenta de Google conectada
  When completa el flujo de autorización OAuth con Google
  Then el sistema guarda la conexión de forma segura
  And muestra que la cuenta de Google está conectada

##### Scenario: El usuario cancela la autorización
  Given el usuario está en el flujo de autorización OAuth
  When cancela o deniega el permiso solicitado
  Then el sistema no establece la conexión
  And informa al usuario de que la conexión no se completó

##### Scenario: Ya existe una conexión activa
  Given el usuario ya tiene una cuenta de Google conectada **(asumido)**
  When intenta iniciar el flujo de conexión nuevamente
  Then el sistema le informa de que ya tiene una cuenta conectada **(asumido)**

##### Scenario: Fallo de la API de Google durante la autorización
  Given ocurre un error o caída de la API de Google durante el flujo OAuth
  When el usuario intenta conectar su cuenta
  Then el sistema muestra un mensaje de error comprensible
  And no deja la cuenta en un estado de conexión parcial **(asumido)**

---

### Story: Creación de eventos a partir de tareas con fecha límite

Como usuario con Google Calendar conectado,
quiero que mis tareas con fecha límite aparezcan como eventos en mi calendario,
para ver mis pendientes junto con el resto de mi agenda.

##### Scenario: Creación de evento
  Given el usuario tiene Google conectado
  When crea una tarea con fecha límite
  Then el sistema crea un evento correspondiente en su Google Calendar

##### Scenario: Tarea sin fecha límite
  Given el usuario tiene Google conectado
  When crea una tarea sin fecha límite
  Then el sistema no crea ningún evento en Google Calendar para esa tarea

##### Scenario: Zona horaria del evento
  Given el usuario crea una tarea con fecha límite
  When se crea el evento correspondiente en Google Calendar
  Then el evento se ubica en la zona horaria correcta del usuario, sin desplazamientos de hora **(asumido: el PRD señala esto como riesgo conocido pendiente de un spike técnico, sin definir el mecanismo exacto)**

##### Scenario: Fallo de la API de Google al crear el evento
  Given la API de Google no está disponible o devuelve un error
  When el usuario crea una tarea con fecha límite
  Then la tarea se guarda igualmente en FlowSync
  And el sistema reintenta la sincronización más tarde

---

### Story: Actualización del evento al cambiar la fecha de una tarea

Como usuario con Google Calendar conectado,
quiero que el evento de calendario se actualice cuando cambio la fecha límite de una tarea,
para que mi calendario siempre refleje la información correcta.

##### Scenario: Actualización exitosa
  Given una tarea ya está sincronizada con un evento existente
  When el usuario cambia su fecha límite
  Then el sistema actualiza el evento correspondiente en Google Calendar con la nueva fecha

##### Scenario: Primera fecha límite asignada a una tarea sin evento
  Given una tarea no tenía fecha límite y por tanto no tenía evento asociado
  When el usuario le asigna una fecha límite por primera vez
  Then el sistema crea un nuevo evento en Google Calendar **(asumido, en línea con el comportamiento de creación de eventos)**

##### Scenario: Eliminar la fecha límite de una tarea sincronizada
  Given una tarea sincronizada tiene un evento existente
  When el usuario elimina su fecha límite
  Then el sistema elimina el evento correspondiente en Google Calendar **(asumido)**

##### Scenario: Fallo al actualizar el evento
  Given la API de Google no está disponible al intentar actualizar
  When el usuario cambia la fecha de una tarea sincronizada
  Then el cambio se guarda en FlowSync
  And el sistema reintenta actualizar el evento más tarde

---

### Story: Eliminación o marcado del evento al completar o borrar una tarea

Como usuario con Google Calendar conectado,
quiero que el evento de calendario se elimine o marque adecuadamente cuando completo o borro una tarea,
para que mi calendario no muestre pendientes que ya no existen.

##### Scenario: Borrado de una tarea sincronizada
  Given una tarea sincronizada tiene un evento asociado
  When el usuario borra la tarea
  Then el sistema elimina el evento correspondiente en Google Calendar

##### Scenario: Tarea marcada como completada
  Given una tarea sincronizada tiene un evento asociado
  When el usuario la marca como completada
  Then el sistema elimina o marca el evento correspondiente, según se determine en refinamiento **(asumido: el PRD indica que el evento "se elimina o se marca según corresponda" sin especificar cuál de las dos opciones aplica)**

##### Scenario: Tarea archivada con evento asociado
  Given una tarea sincronizada tiene un evento asociado
  When el usuario la archiva
  Then el sistema también elimina o actualiza el evento correspondiente, de forma consistente con el comportamiento al completar o borrar **(asumido: caso no mencionado literalmente en el PRD)**

##### Scenario: Fallo al eliminar o marcar el evento
  Given la API de Google no está disponible
  When el usuario completa o borra una tarea sincronizada
  Then el cambio se aplica en FlowSync
  And el sistema reintenta la operación sobre el evento más tarde

---


### Story: Desconectar cuenta de Google

Como usuario con Google Calendar conectado,
quiero poder desconectar mi cuenta de Google en cualquier momento,
para dejar de compartir mi calendario con FlowSync cuando lo desee.

##### Scenario: Desconexión exitosa
  Given el usuario tiene Google conectado
  When solicita desconectar su cuenta
  Then el sistema deja de sincronizar tareas con Google Calendar
  And conserva todas las tareas ya creadas en FlowSync sin borrarlas

##### Scenario: Eventos existentes tras la desconexión
  Given existen tareas con eventos ya creados en Google Calendar
  When el usuario desconecta su cuenta
  Then esos eventos permanecen en Google Calendar sin cambios **(asumido: el PRD no indica que se borren al desconectar)**

##### Scenario: Reconexión posterior
  Given un usuario que había desconectado previamente su cuenta
  When vuelve a conectar Google Calendar
  Then el sistema retoma la sincronización de las tareas con fecha límite existentes **(asumido)**

##### Scenario: Fallo al procesar la desconexión
  Given ocurre un error del sistema **(asumido)**
  When el usuario solicita desconectar su cuenta de Google
  Then el sistema muestra un mensaje de error
  And la conexión permanece activa hasta que la desconexión se complete correctamente **(asumido)**

---


## Resumen

| Épica | # Stories |
|---|---|
| Autenticación y gestión de cuenta | 3 |
| Gestión de tareas (CRUD) | 5 |
| Organización y filtrado | 3 |
| Exportación | 1 |
| Sincronización con Google Calendar | 5 |
| **Total** | **17** |

## Notas de alcance

- No se incluyen stories para equipos/tareas compartidas, sincronización con Outlook/iCal, notificaciones push o email, app móvil nativa, etiquetas/proyectos/subtareas, ni recordatorios configurables más allá de los de Google Calendar, por estar explícitamente fuera del scope del MVP.
- No se han estimado tiempos ni story points, ni se ha propuesto arquitectura o implementación técnica, conforme a las restricciones indicadas.
- Las stories de la épica de sincronización con Google Calendar tienen una proporción más alta de criterios marcados como **(asumido)**, reflejando que el propio PRD señala esta área como el mayor riesgo técnico del MVP y recomienda un spike técnico antes de comprometer su decomposición definitiva.
