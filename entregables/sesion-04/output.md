# FlowSync MVP — User Stories

---

## Módulo 1: Autenticación y gestión de cuenta

---

#Story: Como usuario nuevo, quiero crear una cuenta en FlowSync para poder gestionar mis tareas y sincronizarlas con mi Google Calendar.

Scenario 1: Crear cuenta exitosamente
Given: usuario proporciona un email válido y una contraseña de 8 o más caracteres
When: el email no está registrado en el sistema
Then: crea la cuenta, inicia sesión automáticamente [asumido] y lleva al usuario a la pantalla de bienvenida

Scenario 2: Intento de crear cuenta con email ya existente
Given: usuario proporciona email y contraseña
When: el email ya está registrado
Then: muestra mensaje "la cuenta ya existe"
AND muestra link para ir al inicio de sesión

Scenario 3: Contraseña demasiado corta
Given: usuario proporciona email y contraseña
When: la contraseña tiene menos de 8 caracteres
Then: muestra error de validación indicando que la contraseña debe tener al menos 8 caracteres
AND no crea la cuenta

Scenario 4: Email con formato inválido [asumido]
Given: usuario proporciona un email con formato incorrecto (sin @, sin dominio, etc.)
When: intenta enviar el formulario de registro
Then: muestra error de validación indicando que el formato del email es inválido
AND no crea la cuenta

---

#Story: Como usuario registrado, quiero iniciar sesión con mi email y contraseña para acceder a mis tareas en FlowSync.

Scenario 1: Inicio de sesión exitoso
Given: usuario proporciona email y contraseña
When: las credenciales coinciden con una cuenta existente
Then: autentica al usuario, genera un token de acceso [asumido] y lo redirige al listado de tareas

Scenario 2: Credenciales incorrectas [asumido]
Given: usuario proporciona email y contraseña
When: las credenciales no coinciden con ninguna cuenta
Then: muestra mensaje de error indicando que las credenciales son incorrectas
AND no concede acceso

Scenario 3: Sesión persistente mediante token [asumido]
Given: usuario ha iniciado sesión correctamente
When: navega entre las pantallas de la aplicación
Then: su sesión se mantiene activa mediante el token de acceso sin necesidad de volver a autenticarse

---

#Story: Como usuario autenticado, quiero cerrar sesión para proteger el acceso a mi cuenta cuando termine de usarla.

Scenario 1: Cerrar sesión exitosamente
Given: usuario está autenticado en FlowSync
When: selecciona la opción "cerrar sesión"
Then: el token de sesión es invalidado
AND el usuario es redirigido a la pantalla de inicio de sesión [asumido]

Scenario 2: Acceso denegado tras cerrar sesión [asumido]
Given: usuario ha cerrado sesión
When: intenta acceder a una ruta protegida (ej. listado de tareas)
Then: es redirigido a la pantalla de inicio de sesión

---

## Módulo 2: Gestión de tareas (CRUD)

---

#Story: Como usuario autenticado, quiero crear una tarea indicando al menos un título para registrar un nuevo pendiente en FlowSync.

Scenario 1: Crear tarea solo con título
Given: usuario proporciona únicamente un título
When: guarda la nueva tarea
Then: la tarea se crea con estado "pending" y aparece en el listado

Scenario 2: Crear tarea con todos los campos
Given: usuario proporciona título, descripción y fecha límite
When: guarda la nueva tarea
Then: la tarea se crea con estado "pending" y todos los campos almacenados correctamente

Scenario 3: Intento de crear tarea sin título [asumido]
Given: usuario deja el campo de título vacío
When: intenta guardar la tarea
Then: muestra error de validación indicando que el título es obligatorio
AND no crea la tarea

---

#Story: Como usuario autenticado, quiero ver el listado de mis tareas para conocer mis pendientes actuales.

Scenario 1: Ver tareas existentes
Given: usuario tiene tareas creadas
When: accede a la pantalla principal
Then: ve el listado de sus tareas ordenado por relevancia para hoy

Scenario 2: Estado vacío — cuenta nueva
Given: usuario no tiene ninguna tarea creada
When: accede a la pantalla principal
Then: ve un estado vacío con un mensaje de invitación a crear su primera tarea

Scenario 3: Estado vacío — todas archivadas
Given: todas las tareas del usuario están archivadas
When: accede al listado sin filtros aplicados [asumido]
Then: ve el estado vacío con invitación a crear una nueva tarea

---

#Story: Como usuario autenticado, quiero editar cualquier campo de una tarea existente para mantener su información actualizada.

Scenario 1: Editar título
Given: usuario selecciona una tarea y modifica su título
When: guarda los cambios
Then: el nuevo título se refleja en el listado de tareas

Scenario 2: Editar fecha límite con Google Calendar conectado
Given: usuario tiene Google Calendar conectado y cambia la fecha límite de una tarea sincronizada
When: guarda los cambios
Then: la tarea muestra la nueva fecha en FlowSync
AND el evento correspondiente en Google Calendar se actualiza

Scenario 3: Editar descripción [asumido]
Given: usuario modifica la descripción de una tarea
When: guarda los cambios
Then: la descripción actualizada se almacena y se muestra correctamente al consultar la tarea

Scenario 4: Editar campo con valor inválido [asumido]
Given: usuario elimina el título de una tarea existente dejándolo vacío
When: intenta guardar
Then: muestra error de validación y la tarea conserva su valor anterior

---

#Story: Como usuario autenticado, quiero eliminar una tarea para quitarla definitivamente de mi listado.

Scenario 1: Eliminar tarea exitosamente
Given: usuario selecciona una tarea y elige eliminarla
When: confirma la eliminación [asumido]
Then: la tarea desaparece del listado de FlowSync

Scenario 2: Eliminar tarea sincronizada con Google Calendar
Given: la tarea tiene un evento asociado en Google Calendar
When: usuario confirma la eliminación
Then: la tarea se elimina de FlowSync
AND el evento correspondiente se elimina de Google Calendar

Scenario 3: Cancelar eliminación [asumido]
Given: usuario selecciona eliminar una tarea
When: cancela la acción de confirmación [asumido]
Then: la tarea permanece en el listado sin cambios

---

#Story: Como usuario autenticado, quiero cambiar el estado de una tarea para reflejar su progreso.

Scenario 1: Marcar tarea como completada
Given: tarea está en estado "pending"
When: usuario la marca como completada
Then: el estado cambia a "completed"
AND si la tarea estaba sincronizada, el evento en Google Calendar se elimina o se marca según corresponda

Scenario 2: Archivar tarea
Given: tarea existe en cualquier estado
When: usuario la archiva
Then: el estado cambia a "archived"
AND la tarea deja de aparecer en el listado principal sin filtro aplicado [asumido]

Scenario 3: Revertir tarea completada a pendiente [asumido]
Given: tarea está en estado "completed"
When: usuario la vuelve a marcar como pendiente
Then: el estado cambia a "pending"
AND si el usuario tiene Google Calendar conectado y la tarea tiene fecha límite, se recrea el evento [asumido]

---

## Módulo 3: Organización y filtrado

---

#Story: Como usuario autenticado, quiero filtrar mis tareas por estado para ver solo las que me interesan en un momento dado.

Scenario 1: Filtrar por pendientes
Given: usuario tiene tareas en distintos estados
When: selecciona el filtro "pendiente"
Then: el listado muestra únicamente las tareas con estado "pending"

Scenario 2: Filtrar por completadas
Given: usuario tiene tareas con estado "completed"
When: selecciona el filtro "completada"
Then: el listado muestra únicamente las tareas completadas

Scenario 3: Filtrar por archivadas
Given: usuario tiene tareas con estado "archived"
When: selecciona el filtro "archivada"
Then: el listado muestra únicamente las tareas archivadas

Scenario 4: Ver todas las tareas [asumido]
Given: usuario tiene un filtro activo
When: selecciona la opción "todas" o elimina el filtro activo
Then: el listado muestra tareas de todos los estados

Scenario 5: Filtro sin resultados [asumido]
Given: usuario aplica un filtro y no hay tareas con ese estado
When: el filtro se aplica
Then: se muestra un estado vacío indicando que no hay tareas para ese filtro

---

## Módulo 4: Exportación

---

#Story: Como usuario autenticado, quiero exportar mis tareas a un archivo CSV para llevarme mis datos fuera de FlowSync.

Scenario 1: Exportar tareas exitosamente
Given: usuario tiene tareas registradas en FlowSync
When: solicita exportar a CSV
Then: descarga un archivo CSV con al menos las columnas: título, descripción, estado y fecha límite de cada tarea

Scenario 2: Exportar respetando filtro activo [asumido]
Given: usuario tiene un filtro de estado activo en el listado
When: solicita exportar a CSV
Then: el archivo CSV incluye únicamente las tareas visibles bajo el filtro activo

Scenario 3: Exportar sin tareas [asumido]
Given: usuario no tiene tareas creadas (o ninguna visible bajo el filtro activo)
When: solicita exportar a CSV
Then: descarga un CSV que contiene solo la fila de encabezados de columnas, sin filas de datos

---

## Módulo 5: Sincronización con Google Calendar

---

#Story: Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync para que mis tareas con fecha límite aparezcan automáticamente en mi Google Calendar.

Scenario 1: Conectar cuenta exitosamente vía OAuth
Given: usuario inicia el flujo de conexión con Google desde FlowSync
When: autoriza a FlowSync mediante el flujo OAuth de Google
Then: la cuenta de Google queda conectada
AND las tareas con fecha límite comienzan a sincronizarse como eventos en Google Calendar

Scenario 2: Cancelar autorización en pantalla de Google [asumido]
Given: usuario inicia el flujo OAuth de Google
When: cancela la autorización en la pantalla de permisos de Google
Then: regresa a FlowSync sin que su cuenta quede conectada
AND no se muestra ningún error

Scenario 3: Token de Google almacenado de forma segura
Given: usuario ha conectado su cuenta de Google
When: FlowSync necesita usar el token para sincronizar
Then: el token se almacena de forma segura
AND nunca es accesible por otros usuarios

---

#Story: Como usuario autenticado con Google Calendar conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi calendario para ver mis pendientes sin cambiar de herramienta.

Scenario 1: Nueva tarea con fecha límite crea evento en Google Calendar
Given: usuario tiene Google Calendar conectado y crea una tarea con fecha límite
When: guarda la tarea
Then: se crea un evento en su Google Calendar con el título y la fecha de la tarea

Scenario 2: Nueva tarea sin fecha límite no crea evento [asumido]
Given: usuario crea una tarea sin fecha límite
When: guarda la tarea
Then: no se crea ningún evento en Google Calendar

Scenario 3: Fallo temporal de la API de Google
Given: la API de Google Calendar no está disponible al momento de guardar
When: usuario crea una tarea con fecha límite
Then: la tarea se guarda correctamente en FlowSync
AND el sistema reintenta la sincronización más tarde
AND el error queda registrado en los logs para diagnóstico

---

#Story: Como usuario autenticado con Google Calendar conectado, quiero que al cambiar la fecha límite de una tarea su evento en Google Calendar se actualice automáticamente para mantener el calendario al día.

Scenario 1: Actualizar fecha — evento se sincroniza
Given: tarea sincronizada tiene un evento en Google Calendar
When: usuario cambia la fecha límite de la tarea y guarda
Then: el evento en Google Calendar se actualiza con la nueva fecha

Scenario 2: Agregar fecha a tarea que antes no tenía [asumido]
Given: tarea existente no tenía fecha límite y por tanto no tenía evento en Google Calendar
When: usuario agrega una fecha límite y guarda
Then: se crea un nuevo evento en Google Calendar para esa tarea

Scenario 3: Quitar fecha límite de una tarea sincronizada [asumido]
Given: tarea sincronizada tiene un evento en Google Calendar
When: usuario elimina la fecha límite de la tarea y guarda
Then: el evento en Google Calendar es eliminado

---

#Story: Como usuario autenticado con Google Calendar conectado, quiero que al completar o eliminar una tarea su evento en Google Calendar desaparezca para mantener el calendario limpio y sin duplicados.

Scenario 1: Completar tarea elimina o marca evento en Google Calendar
Given: tarea sincronizada tiene un evento en Google Calendar
When: usuario marca la tarea como completada
Then: el evento correspondiente en Google Calendar se elimina o se marca como completado, según corresponda

Scenario 2: Eliminar tarea borra su evento de Google Calendar
Given: tarea tiene un evento asociado en Google Calendar
When: usuario elimina la tarea en FlowSync
Then: el evento correspondiente en Google Calendar también se elimina

Scenario 3: Fallo de sincronización al completar o borrar [asumido]
Given: la API de Google no está disponible al marcar una tarea como completada o borrarla
When: FlowSync intenta eliminar el evento
Then: la tarea se actualiza correctamente en FlowSync
AND el sistema reintenta eliminar el evento más tarde
AND el intento fallido queda registrado en los logs

---

#Story: Como usuario autenticado, quiero desconectar mi cuenta de Google de FlowSync para revocar el acceso sin perder mis tareas.

Scenario 1: Desconectar cuenta exitosamente
Given: usuario tiene Google Calendar conectado
When: elige desconectar su cuenta de Google desde la configuración
Then: FlowSync revoca el token y deja de sincronizar con Google Calendar
AND todas las tareas existentes en FlowSync permanecen intactas

Scenario 2: Eventos en Google Calendar tras desconexión [asumido]
Given: usuario desconecta su cuenta de Google
When: la desconexión se completa
Then: los eventos previamente creados en Google Calendar permanecen en el calendario del usuario
AND FlowSync no los elimina al desconectar

Scenario 3: Estado de conexión visible en la interfaz [asumido]
Given: usuario accede a la sección de configuración de su cuenta
When: Google Calendar no está conectado
Then: se muestra una opción para conectar Google Calendar
AND cuando sí está conectado, se muestra el estado "conectado" junto con la opción de desconectar
