## Autenticación y gestión de cuenta

### Story: Registro de cuenta
Como visitante, quiero crear una cuenta con email y contraseña, para poder empezar a usar FlowSync con mis propios datos.

- Given que no tengo cuenta, When ingreso un email no registrado y una contraseña de al menos 8 caracteres, Then se crea mi cuenta, quedo autenticado y llego a la pantalla de bienvenida.
- Given que ingreso un email ya registrado, When intento crear la cuenta, Then el sistema me lo indica y me ofrece ir a inicio de sesión.
- Given que ingreso una contraseña de menos de 8 caracteres, When intento crear la cuenta, Then veo un mensaje de validación claro y la cuenta no se crea.
- Given que acabo de registrarme con éxito, When llego a la pantalla de bienvenida, Then veo una frase que explica qué hace FlowSync y una invitación a crear mi primera tarea (asumido: el copy exacto de esa frase no está en el PRD).

### Story: Inicio de sesión
Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.

- Given que tengo una cuenta, When ingreso email y contraseña correctos, Then quedo autenticado y veo mi listado de tareas.
- Given que ingreso credenciales incorrectas, When intento iniciar sesión, Then veo un mensaje de error claro sin detalles técnicos.
- Given que ya tengo una sesión activa, When cierro y reabro la app, Then sigo autenticado sin tener que volver a iniciar sesión (asumido: el token de acceso persiste entre sesiones del navegador).

### Story: Cierre de sesión
Como usuario autenticado, quiero cerrar sesión, para dejar de tener acceso activo desde ese dispositivo/navegador.

- Given que estoy autenticado, When elijo cerrar sesión, Then mi token de acceso deja de ser válido y vuelvo a la pantalla de inicio de sesión.
- Given que cerré sesión, When intento volver a una página de tareas sin autenticarme, Then se me redirige a inicio de sesión.

---

## Gestión de tareas

### Story: Crear tarea
Como usuario autenticado, quiero crear una tarea con al menos un título, para registrar un pendiente.

- Given que estoy en mi listado de tareas, When creo una tarea indicando solo el título, Then la tarea se guarda con estado `pending` y aparece en mi listado.
- Given que quiero agregar detalle, When incluyo descripción y/o fecha límite al crear la tarea, Then esos datos se guardan junto con la tarea.
- Given que no ingreso ningún título, When intento crear la tarea, Then veo un mensaje de validación claro y la tarea no se crea.

### Story: Ver listado de tareas
Como usuario autenticado, quiero ver el listado de mis tareas, para saber qué tengo pendiente.

- Given que tengo tareas creadas, When abro mi listado, Then veo todas mis tareas con su título, estado y fecha límite si tiene.
- Given que tengo varias tareas con distintas fechas, When veo el listado sin aplicar ningún filtro, Then las más relevantes para "hoy" aparecen primero (asumido: el criterio exacto de "relevante para hoy" prioriza tareas `pending` con fecha límite más próxima, ya que el PRD deja el criterio exacto a definir en refinamiento).
- Given que soy un usuario distinto a otro usuario de FlowSync, When veo mi listado, Then nunca veo tareas de otra cuenta.

### Story: Editar tarea
Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para corregir o actualizar su información.

- Given que tengo una tarea creada, When edito su título, descripción o fecha límite, Then los cambios se reflejan de inmediato en el listado.
- Given que edito una tarea, When dejo el título vacío, Then el sistema no permite guardar el cambio y muestra un mensaje de validación.

### Story: Borrar tarea
Como usuario autenticado, quiero borrar una tarea, para eliminar un pendiente que ya no aplica.

- Given que tengo una tarea creada, When la borro, Then desaparece de mi listado de forma permanente.
- Given que borro una tarea que tenía fecha límite y estaba sincronizada con Google Calendar, When se completa el borrado, Then el evento correspondiente en Calendar también se elimina.

### Story: Cambiar estado de una tarea
Como usuario autenticado, quiero cambiar el estado de una tarea, para reflejar su progreso real.

- Given que tengo una tarea `pending`, When la marco como completada, Then su estado cambia a `completed` y deja de contar como pendiente en los listados filtrados por "pendientes".
- Given que tengo una tarea `completed`, When decido archivarla, Then su estado cambia a `archived`.
- Given que una tarea recién creada, When no aplico ningún cambio de estado, Then permanece en `pending` por defecto.

---

## Organización y filtrado

### Story: Filtrar tareas por estado
Como usuario autenticado, quiero filtrar mis tareas por estado, para enfocarme en las que me interesan en ese momento.

- Given que tengo tareas en varios estados, When filtro por "pendientes", Then solo veo las tareas en estado `pending`.
- Given que aplico un filtro por "completadas" o "archivadas", When veo el listado filtrado, Then solo aparecen tareas de ese estado exacto.
- Given que quito el filtro aplicado, When vuelvo al listado general, Then veo tareas de todos los estados otra vez.

### Story: Ver estado vacío
Como usuario autenticado sin tareas visibles, quiero ver una invitación clara a crear mi primera tarea, para saber qué hacer a continuación.

- Given que soy un usuario nuevo sin tareas, When entro a mi listado, Then veo un estado vacío con una invitación a crear mi primera tarea.
- Given que tengo tareas pero todas están archivadas, When aplico el filtro por defecto, Then también veo el estado vacío con la invitación (asumido: el PRD no aclara si "todas archivadas" cuenta como "sin tareas" para este propósito, pero el enunciado de la sección 3.3 lo agrupa junto al caso de cuenta nueva).

---

## Exportación

### Story: Exportar tareas a CSV
Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para tener mis datos fuera de FlowSync.

- Given que tengo tareas creadas, When exporto, Then descargo un archivo CSV que incluye título, descripción, estado y fecha límite de cada tarea.
- Given que no tengo ninguna tarea, When intento exportar, Then obtengo un CSV válido aunque esté vacío de filas de datos (asumido: comportamiento no especificado en el PRD para el caso sin tareas).
- Given que exporto mis tareas, When reviso el archivo, Then los datos de otro usuario nunca aparecen ahí.

---

## Sincronización con Google Calendar

### Story: Conectar cuenta de Google
Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync, para que mis tareas con fecha se reflejen en mi calendario.

- Given que no tengo Google conectado, When inicio el flujo de conexión, Then se me redirige a la autorización OAuth de Google y, al aceptar, FlowSync queda conectado.
- Given que cancelo o rechazo la autorización de Google, When vuelvo a FlowSync, Then mi cuenta permanece sin conectar y puedo intentarlo de nuevo más tarde.
- Given que ya tengo Google conectado, When entro a mi cuenta, Then veo indicado que la conexión está activa.

### Story: Sincronizar tarea con fecha como evento
Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi calendario, para ver todos mis pendientes en un solo lugar.

- Given que tengo Google conectado, When creo una tarea con fecha límite, Then se crea un evento correspondiente en mi Google Calendar.
- Given que tengo una tarea con fecha límite ya sincronizada, When cambio su fecha en FlowSync, Then el evento en Google Calendar se actualiza a la nueva fecha.
- Given que la API de Google no está disponible al momento de sincronizar, When guardo o edito la tarea, Then la tarea igual se guarda en FlowSync y la sincronización se reintenta más tarde.

### Story: Eliminar evento al completar o borrar una tarea
Como usuario con Google conectado, quiero que el evento del calendario se elimine o marque cuando ya no necesito la tarea, para que mi calendario no quede desactualizado.

- Given que tengo una tarea sincronizada, When la completo, Then el evento correspondiente en Google Calendar se elimina o se marca según corresponda (asumido: "marca según corresponda" se interpreta como renombrar/etiquetar el evento como completado, ya que el PRD no especifica cuál de las dos opciones aplica).
- Given que tengo una tarea sincronizada, When la borro en FlowSync, Then el evento correspondiente en Google Calendar se elimina.

### Story: Desconectar cuenta de Google
Como usuario con Google conectado, quiero poder desconectar mi cuenta de Google en cualquier momento, para dejar de compartir mi calendario si ya no quiero usar la sincronización.

- Given que tengo Google conectado, When elijo desconectar, Then FlowSync deja de sincronizar nuevos cambios con Google Calendar.
- Given que acabo de desconectar Google, When reviso mis tareas en FlowSync, Then ninguna tarea existente se borra por la desconexión.
- Given que desconecté Google y luego vuelvo a conectar la misma cuenta, When se completa la nueva conexión, Then la sincronización se reanuda para cambios futuros (asumido: el PRD no dice explícitamente qué pasa con una reconexión, solo qué pasa al desconectar).
