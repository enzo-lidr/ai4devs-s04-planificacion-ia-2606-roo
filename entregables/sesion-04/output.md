# FlowSync MVP — User Stories

> Generado a partir de `docs/PRD.md`. Agrupado por épica. Excluye explícitamente: funcionalidades fuera de alcance del MVP y la sincronización inversa Google → FlowSync (pendiente de spike técnico).

---

## Épica 1: Autenticación y gestión de cuenta

### Story: Registrar una cuenta
Como visitante,
quiero crear una cuenta con mi email y contraseña,
para poder usar FlowSync con mis propios datos.

#### Criterios de aceptación
- Given soy un visitante y proporciono un email válido y una contraseña de al menos 8 caracteres
  When envío el formulario de registro
  Then mi cuenta se crea y soy dirigido a la pantalla de bienvenida

- Given soy un visitante y proporciono una contraseña de menos de 8 caracteres
  When envío el formulario de registro
  Then veo un mensaje claro indicando que la contraseña debe tener al menos 8 caracteres y la cuenta no se crea

- Given soy un visitante y el email que indico ya está registrado
  When envío el formulario de registro
  Then veo un mensaje indicando que el email ya existe, con la opción de ir al inicio de sesión

- Given soy un visitante y dejo el campo email o contraseña vacío (asumido)
  When envío el formulario de registro
  Then veo un mensaje de validación claro indicando que el campo es obligatorio y la cuenta no se crea

### Story: Ver pantalla de bienvenida tras el registro
Como usuario recién registrado,
quiero ver una pantalla de bienvenida que explique qué hace FlowSync,
para entender el valor de la herramienta y saber cómo empezar.

#### Criterios de aceptación
- Given acabo de completar el registro exitosamente
  When llego a la aplicación por primera vez
  Then veo una pantalla de bienvenida con una frase que explica qué hace FlowSync

- Given estoy en la pantalla de bienvenida
  When la reviso
  Then veo una invitación explícita a crear mi primera tarea

- Given ya tengo una cuenta y he iniciado sesión previamente (asumido)
  When inicio sesión de nuevo
  Then no vuelvo a ver la pantalla de bienvenida de onboarding

### Story: Iniciar sesión
Como usuario registrado,
quiero iniciar sesión con mi email y contraseña,
para acceder a mis tareas.

#### Criterios de aceptación
- Given tengo una cuenta registrada con credenciales válidas
  When envío el formulario de inicio de sesión con mis credenciales correctas
  Then accedo a la aplicación y veo mi listado de tareas

- Given tengo una cuenta registrada
  When envío el formulario de inicio de sesión con una contraseña incorrecta
  Then veo un mensaje claro de error y no se inicia sesión

- Given intento iniciar sesión con un email que no está registrado
  When envío el formulario de inicio de sesión
  Then veo un mensaje claro de error y no se inicia sesión

- Given inicio sesión correctamente
  When la sesión se establece
  Then se emite un token de acceso que mantiene mi sesión activa (asumido: el PRD no detalla duración ni expiración del token)

### Story: Cerrar sesión
Como usuario autenticado,
quiero cerrar sesión,
para dejar de tener acceso activo a mi cuenta desde ese dispositivo.

#### Criterios de aceptación
- Given estoy autenticado y tengo una sesión activa
  When selecciono la opción de cerrar sesión
  Then mi token de acceso deja de ser válido y soy redirigido a la pantalla de inicio de sesión

- Given he cerrado sesión
  When intento acceder a una página que requiere autenticación (por ejemplo, mi listado de tareas)
  Then soy redirigido a la pantalla de inicio de sesión

- Given estoy autenticado
  When cierro sesión
  Then ni mis tareas ni mi conexión con Google se ven afectadas (asumido)

### Story: Aislamiento de datos entre usuarios
Como usuario,
quiero que mis tareas y datos sean visibles únicamente para mí,
para tener la confianza de que mi información personal está protegida.

#### Criterios de aceptación
- Given existen al menos dos usuarios distintos, cada uno con sus propias tareas
  When el usuario A inicia sesión
  Then el usuario A solo ve sus propias tareas, nunca las del usuario B

- Given el usuario A conectó su cuenta de Google
  When el usuario B inicia sesión
  Then el usuario B no puede ver ni usar la conexión de Google del usuario A

- Given un usuario no autenticado (sin sesión)
  When intenta acceder directamente a una URL de tareas o de datos de cuenta
  Then el sistema le deniega el acceso y lo redirige a iniciar sesión (asumido)

---

## Épica 2: Gestión de tareas (CRUD)

### Story: Crear una tarea
Como usuario,
quiero crear una tarea indicando al menos un título,
para registrar un pendiente que necesito hacer.

#### Criterios de aceptación
- Given estoy autenticado y completo el campo título
  When guardo la tarea sin indicar descripción ni fecha límite
  Then la tarea se crea con estado pending y aparece en mi listado

- Given estoy autenticado y completo título, descripción y fecha límite
  When guardo la tarea
  Then la tarea se crea con todos esos datos y estado inicial pending

- Given estoy autenticado
  When intento guardar una tarea sin indicar título
  Then veo un mensaje claro indicando que el título es obligatorio y la tarea no se crea

### Story: Ver listado de tareas
Como usuario,
quiero ver el listado de mis tareas,
para saber qué pendientes tengo.

#### Criterios de aceptación
- Given tengo tareas creadas
  When accedo a mi listado de tareas
  Then veo todas mis tareas con su título, estado y fecha límite (si tiene)

- Given tengo hasta 200 tareas
  When accedo a mi listado de tareas
  Then el listado carga en menos de 1 segundo

- Given no tengo ninguna tarea (cuenta nueva o todas archivadas)
  When accedo a mi listado de tareas
  Then veo un estado vacío con una invitación a crear mi primera tarea

### Story: Editar una tarea
Como usuario,
quiero editar cualquier campo de una tarea existente,
para corregir o actualizar su información.

#### Criterios de aceptación
- Given tengo una tarea existente
  When edito su título, descripción, fecha límite o estado y guardo los cambios
  Then la tarea refleja los nuevos valores en el listado

- Given edito una tarea y dejo el título vacío
  When intento guardar los cambios
  Then veo un mensaje claro indicando que el título es obligatorio y los cambios no se guardan

- Given edito la fecha límite de una tarea que ya está sincronizada con un evento de Google Calendar
  When guardo el cambio
  Then la tarea se actualiza en FlowSync independientemente de si la sincronización con Google se completa de inmediato (asumido, ver épica de sincronización)

### Story: Borrar una tarea
Como usuario,
quiero borrar una tarea,
para eliminar pendientes que ya no necesito.

#### Criterios de aceptación
- Given tengo una tarea existente
  When la borro
  Then la tarea desaparece de mi listado de forma permanente

- Given intento borrar una tarea
  When confirmo la acción (asumido: existe una confirmación previa para evitar borrados accidentales)
  Then la tarea se elimina; si cancelo, se mantiene sin cambios

- Given intento borrar una tarea que ya no existe (caso de carrera, por ejemplo doble clic) (asumido)
  When se procesa la solicitud
  Then veo un mensaje claro indicando que la tarea ya no existe

### Story: Cambiar el estado de una tarea
Como usuario,
quiero cambiar el estado de una tarea,
para reflejar su progreso (por ejemplo, marcarla como completada).

#### Criterios de aceptación
- Given tengo una tarea en estado pending
  When cambio su estado a completed
  Then la tarea pasa a estado completed y se refleja en el listado

- Given tengo una tarea en estado completed
  When cambio su estado a archived
  Then la tarea pasa a estado archived

- Given intento cambiar el estado de una tarea a un valor distinto de pending, completed o archived
  When envío el cambio
  Then veo un mensaje claro indicando que el estado no es válido y el estado de la tarea no cambia

- Given una tarea recién creada
  When la consulto sin haber cambiado su estado
  Then su estado es pending por defecto

---

## Épica 3: Organización y filtrado

### Story: Filtrar tareas por estado
Como usuario,
quiero filtrar mis tareas por estado,
para enfocarme en lo que me falta sin distraerme con lo ya hecho.

#### Criterios de aceptación
- Given tengo tareas en distintos estados
  When filtro por el estado pending
  Then veo solo las tareas en estado pending

- Given tengo tareas en distintos estados
  When filtro por el estado completed
  Then veo solo las tareas en estado completed

- Given tengo tareas en distintos estados
  When filtro por el estado archived
  Then veo solo las tareas en estado archived

- Given no tengo ninguna tarea en el estado seleccionado
  When aplico ese filtro
  Then veo un mensaje indicando que no hay tareas en ese estado (asumido: el texto exacto no está definido en el PRD)

### Story: Ver listado ordenado por relevancia para "hoy"
Como usuario,
quiero que mi listado de tareas se muestre ordenado de forma que las más relevantes para hoy aparezcan primero,
para identificar rápido qué hacer sin tener que ordenar manualmente.

#### Criterios de aceptación
- Given tengo tareas con distintas fechas límite (vencidas, de hoy, futuras o sin fecha)
  When accedo a mi listado sin aplicar ningún filtro
  Then las tareas más relevantes para hoy aparecen antes que las demás (asumido: el PRD indica explícitamente que el criterio exacto de ordenación queda a decisión del equipo durante el refinamiento)

- Given el listado está ordenado por relevancia para hoy
  When aplico un filtro de estado
  Then el orden por relevancia se mantiene dentro del subconjunto filtrado (asumido)

- Given tengo tareas sin fecha límite
  When veo mi listado ordenado por defecto
  Then esas tareas aparecen en una posición consistente respecto a las que sí tienen fecha (asumido, pendiente del refinamiento mencionado en el PRD)

### Story: Ver estado vacío sin tareas
Como usuario,
quiero ver un mensaje y una invitación a crear una tarea cuando no tengo ninguna,
para saber qué hacer a continuación en lugar de ver una pantalla vacía y confusa.

#### Criterios de aceptación
- Given soy un usuario nuevo sin ninguna tarea creada
  When accedo a mi listado de tareas
  Then veo un estado vacío con una invitación a crear mi primera tarea

- Given tengo tareas pero todas están en estado archived
  When accedo a mi listado de tareas (vista por defecto)
  Then veo un estado vacío equivalente, indicando que no tengo tareas activas

- Given tengo al menos una tarea en estado pending o completed
  When accedo a mi listado
  Then no veo el estado vacío, sino el listado de tareas correspondiente

---

## Épica 4: Exportación

### Story: Exportar tareas a CSV
Como usuario,
quiero exportar mis tareas a un archivo CSV,
para conservar o usar mis datos fuera de FlowSync.

#### Criterios de aceptación
- Given tengo tareas creadas
  When solicito la exportación
  Then descargo un archivo CSV que incluye, para cada tarea, al menos título, descripción, estado y fecha límite

- Given tengo tareas en distintos estados (pending, completed, archived)
  When exporto a CSV
  Then el archivo incluye tareas de todos los estados, no solo las visibles bajo el filtro activo en ese momento (asumido)

- Given una tarea no tiene fecha límite o descripción (son campos opcionales)
  When la incluyo en la exportación
  Then el campo correspondiente aparece vacío en el CSV sin romper el formato del archivo

- Given no tengo ninguna tarea
  When solicito la exportación
  Then descargo un CSV válido con solo encabezados, o veo un mensaje indicando que no hay tareas para exportar (asumido)

---

## Épica 5: Sincronización con Google Calendar

### Story: Conectar cuenta de Google
Como usuario,
quiero conectar mi cuenta de Google a FlowSync mediante OAuth,
para que mis tareas con fecha límite puedan reflejarse en mi Google Calendar.

#### Criterios de aceptación
- Given estoy autenticado en FlowSync y no tengo Google conectado
  When inicio el flujo de conexión y autorizo el acceso en la pantalla de consentimiento de Google
  Then mi cuenta de Google queda conectada y FlowSync queda habilitado para leer y escribir en mi calendario

- Given inicio el flujo de conexión
  When cancelo o rechazo la autorización en la pantalla de consentimiento de Google
  Then mi cuenta de Google no queda conectada y veo un mensaje claro indicándolo

- Given ya tengo mi cuenta de Google conectada
  When vuelvo a la pantalla de configuración de conexión
  Then veo que mi cuenta está conectada, sin necesidad de volver a autorizar (asumido)

### Story: Crear evento en Google Calendar a partir de una tarea con fecha límite
Como usuario con Google conectado (asumido: subconjunto de "usuario" que completó la conexión OAuth),
quiero que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar,
para ver en un solo lugar tanto mis reuniones como mis pendientes.

#### Criterios de aceptación
- Given tengo mi cuenta de Google conectada
  When creo una tarea con fecha límite
  Then se crea un evento correspondiente en mi Google Calendar

- Given tengo mi cuenta de Google conectada
  When creo una tarea sin fecha límite
  Then no se crea ningún evento en Google Calendar para esa tarea

- Given no tengo mi cuenta de Google conectada
  When creo una tarea con fecha límite
  Then la tarea se guarda normalmente en FlowSync sin intentar crear ningún evento

- Given tengo mi cuenta de Google conectada y la API de Google no está disponible en ese momento
  When creo una tarea con fecha límite
  Then la tarea se guarda en FlowSync de todas formas y la sincronización del evento se reintenta más tarde

### Story: Actualizar el evento al cambiar la fecha límite de una tarea
Como usuario con Google conectado,
quiero que el evento de Google Calendar se actualice cuando cambio la fecha límite de una tarea,
para no tener que editar el evento manualmente en el calendario.

#### Criterios de aceptación
- Given tengo una tarea con fecha límite ya sincronizada como evento
  When cambio su fecha límite
  Then el evento correspondiente en Google Calendar se actualiza a la nueva fecha

- Given tengo una tarea sin fecha límite (sin evento asociado)
  When le agrego una fecha límite
  Then se crea un nuevo evento en Google Calendar para esa tarea

- Given tengo una tarea con fecha límite y evento asociado
  When le quito la fecha límite
  Then el evento correspondiente se elimina de Google Calendar (asumido, por simetría con la creación; el PRD no lo detalla explícitamente)

- Given cambio la fecha de una tarea sincronizada y la API de Google no está disponible
  When se intenta actualizar el evento
  Then el cambio se guarda en FlowSync y la actualización del evento se reintenta más tarde

### Story: Eliminar o marcar el evento al completar o borrar una tarea
Como usuario con Google conectado,
quiero que el evento de Google Calendar se elimine o se marque adecuadamente cuando completo o borro una tarea,
para que mi calendario refleje con precisión lo que ya no está pendiente.

#### Criterios de aceptación
- Given tengo una tarea con fecha límite sincronizada como evento
  When borro la tarea
  Then el evento correspondiente se elimina de Google Calendar

- Given tengo una tarea con fecha límite sincronizada como evento
  When cambio su estado a completed
  Then el evento correspondiente se elimina o se marca como completado en Google Calendar (asumido: el PRD dice "se elimina o se marca según corresponda" sin precisar cuál de las dos)

- Given cambio el estado de una tarea sincronizada a completed y la API de Google no está disponible
  When se intenta actualizar el evento
  Then el cambio de estado se guarda en FlowSync y la actualización del evento se reintenta más tarde

### Story: Desconectar cuenta de Google
Como usuario con Google conectado,
quiero poder desconectar mi cuenta de Google en cualquier momento,
para dejar de compartir mi calendario con FlowSync cuando ya no lo necesite.

#### Criterios de aceptación
- Given tengo mi cuenta de Google conectada
  When la desconecto
  Then FlowSync deja de sincronizar tareas con Google Calendar

- Given desconecto mi cuenta de Google
  When reviso mis tareas en FlowSync inmediatamente después
  Then todas mis tareas existentes se mantienen intactas, sin borrarse

- Given desconecté mi cuenta de Google
  When creo o edito una tarea con fecha límite
  Then la tarea se guarda en FlowSync sin intentar sincronizar con Google Calendar

### Story: Manejo resiliente de errores de sincronización
Como usuario,
quiero que los fallos de sincronización con Google se gestionen de forma resiliente,
para no perder mis tareas ni tener que enterarme de errores técnicos.

#### Criterios de aceptación
- Given la API de Google Calendar devuelve un error o no está disponible al intentar sincronizar
  When la operación de sincronización falla
  Then la tarea u operación correspondiente se guarda igualmente en FlowSync

- Given una operación de sincronización falló previamente
  When el sistema reintenta más tarde
  Then la sincronización se completa si la API de Google ya está disponible

- Given ocurre cualquier operación de sincronización (exitosa o fallida)
  When se ejecuta
  Then queda registrada en logs para poder diagnosticar fallos

---

### Notas de transparencia
- Todo lo marcado con (asumido) son inferencias razonables ante vacíos del PRD (textos de mensaje exactos, criterio de ordenación "hoy", comportamiento exacto al quitar fecha límite o completar una tarea sincronizada, etc.), no literal del documento.
- No se generaron stories para: equipos/tareas compartidas, sincronización con Outlook/iCal, notificaciones push/email, app móvil nativa, etiquetas/proyectos/subtareas, recordatorios configurables más allá de Google Calendar, ni sincronización inversa Google → FlowSync (pendiente de spike técnico según el PRD).
