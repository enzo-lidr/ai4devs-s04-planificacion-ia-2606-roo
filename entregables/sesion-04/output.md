# output.md — Backlog inicial de FlowSync MVP (output crudo de la IA)

> Salida sin retocar generada al ejecutar `prompt.md` contra el PRD de FlowSync MVP.
> Stories en formato `Como/quiero/para`, agrupadas por épica, con AC en Given/When/Then.
> Lo inferido y no literal en el PRD está marcado con `(asumido)`.

---

## ÉPICA A — Autenticación y gestión de cuenta

### US-01 — Crear cuenta con email y contraseña
**Como** visitante, **quiero** registrarme con mi email y una contraseña, **para** tener una cuenta propia donde gestionar mis tareas.  [PRD 3.1]

**Criterios de aceptación**
- **Escenario: registro válido**
  - Given un visitante en la pantalla de registro
  - When envía un email no registrado y una contraseña de 8+ caracteres
  - Then la cuenta se crea y queda autenticado
  - And es redirigido a la pantalla de bienvenida (onboarding)
- **Escenario: contraseña demasiado corta**
  - Given un visitante en la pantalla de registro
  - When envía una contraseña de menos de 8 caracteres
  - Then el registro se rechaza con un mensaje de validación comprensible
  - And la cuenta no se crea
- **Escenario: email ya registrado**
  - Given un email que ya existe en el sistema
  - When el visitante intenta registrarse con ese email
  - Then el sistema indica que el email ya está registrado
  - And ofrece un enlace para ir al inicio de sesión
- **Escenario: email con formato inválido (asumido)**
  - Given un visitante en la pantalla de registro
  - When envía un email sin formato válido (p. ej. "juan@")
  - Then el registro se rechaza con un error de validación claro

### US-02 — Iniciar sesión
**Como** usuario registrado, **quiero** iniciar sesión con mi email y contraseña, **para** acceder a mis tareas.  [PRD 3.1]

**Criterios de aceptación**
- **Escenario: credenciales correctas**
  - Given un usuario registrado
  - When introduce su email y contraseña correctos
  - Then se autentica y recibe un token de acceso
  - And accede a su listado de tareas
- **Escenario: credenciales incorrectas**
  - Given un usuario registrado
  - When introduce una contraseña incorrecta
  - Then la respuesta es 401 y se muestra un mensaje genérico de credenciales inválidas
  - And no se revela si el fallo fue el email o la contraseña (asumido)
- **Escenario: sesión persistente por token**
  - Given un usuario que ya inició sesión
  - When vuelve a la aplicación con un token de acceso válido
  - Then accede sin tener que volver a autenticarse
  - And si el token ha expirado (asumido), se le pide iniciar sesión de nuevo

### US-03 — Cerrar sesión
**Como** usuario autenticado, **quiero** cerrar sesión, **para** proteger mi cuenta en dispositivos compartidos.  [PRD 3.1]

**Criterios de aceptación**
- **Escenario: cierre de sesión**
  - Given un usuario autenticado
  - When pulsa "cerrar sesión"
  - Then su token de acceso se invalida
  - And es redirigido a la pantalla de inicio de sesión
- **Escenario: acceso tras cerrar sesión**
  - Given un usuario que ha cerrado sesión
  - When intenta acceder a un recurso protegido con el token anterior
  - Then la respuesta es 401 No autorizado

### US-04 — Onboarding mínimo tras el registro
**Como** usuario recién registrado, **quiero** una pantalla de bienvenida que explique qué hace FlowSync, **para** entender el producto y crear mi primera tarea.  [PRD 3.1]

**Criterios de aceptación**
- **Escenario: bienvenida tras registro**
  - Given un usuario que acaba de completar el registro
  - When llega a la pantalla de bienvenida
  - Then ve una frase que explica qué hace FlowSync
  - And ve una llamada a la acción para crear su primera tarea
- **Escenario: una sola vez (asumido)**
  - Given un usuario que ya pasó por el onboarding
  - When vuelve a iniciar sesión más adelante
  - Then va directo a su listado de tareas, no al onboarding

---

## ÉPICA B — Gestión de tareas (CRUD)

### US-05 — Crear una tarea
**Como** usuario, **quiero** crear una tarea indicando al menos un título, **para** registrar algo que tengo que hacer.  [PRD 3.2]

**Criterios de aceptación**
- **Escenario: crear con solo título**
  - Given un usuario autenticado
  - When crea una tarea con título y sin descripción ni fecha límite
  - Then la tarea se guarda con estado "pending"
  - And aparece en su listado de tareas
- **Escenario: crear con todos los campos**
  - Given un usuario autenticado
  - When crea una tarea con título, descripción y fecha límite
  - Then la tarea se guarda con esos tres campos y estado "pending"
- **Escenario: título vacío**
  - Given un usuario autenticado
  - When intenta crear una tarea sin título
  - Then la respuesta es 400 Bad Request con un mensaje de validación claro
  - And la tarea no se crea

### US-06 — Ver el listado de tareas
**Como** usuario, **quiero** ver el listado de mis tareas, **para** saber qué tengo pendiente.  [PRD 3.2, 3.3]

**Criterios de aceptación**
- **Escenario: listado solo del propio usuario**
  - Given dos usuarios distintos con tareas propias
  - When el usuario A pide su listado
  - Then solo ve sus tareas, nunca las del usuario B
- **Escenario: orden por relevancia para "hoy"**
  - Given un usuario con varias tareas con distintas fechas límite
  - When carga su listado
  - Then las tareas más relevantes para hoy aparecen primero (criterio exacto a definir en refinamiento) (asumido)
- **Escenario: estado vacío**
  - Given un usuario sin ninguna tarea (cuenta nueva o todas archivadas)
  - When abre su listado
  - Then ve un estado vacío con una invitación a crear la primera tarea
- **Escenario: rendimiento**
  - Given un usuario con hasta 200 tareas
  - When solicita el listado
  - Then la respuesta se entrega en menos de 1 segundo

### US-07 — Editar una tarea
**Como** usuario, **quiero** editar cualquier campo de una tarea existente, **para** mantenerla actualizada.  [PRD 3.2]

**Criterios de aceptación**
- **Escenario: edición válida**
  - Given una tarea existente del usuario
  - When edita su título, descripción o fecha límite con valores válidos
  - Then los cambios se guardan y se reflejan en el listado
- **Escenario: editar tarea ajena**
  - Given una tarea que pertenece a otro usuario
  - When el usuario intenta editarla
  - Then la respuesta es 403 o 404 (asumido) y la tarea no se modifica
- **Escenario: quitar la fecha límite**
  - Given una tarea con fecha límite
  - When el usuario la edita dejando la fecha límite vacía
  - Then la tarea se guarda sin fecha límite

### US-08 — Borrar una tarea
**Como** usuario, **quiero** borrar una tarea, **para** quitar lo que ya no necesito.  [PRD 3.2]

**Criterios de aceptación**
- **Escenario: borrado**
  - Given una tarea existente del usuario
  - When la borra
  - Then deja de aparecer en su listado
- **Escenario: confirmación previa (asumido)**
  - Given un usuario que pulsa borrar
  - When la acción es destructiva
  - Then se le pide confirmación antes de eliminar definitivamente
- **Escenario: borrar tarea ya inexistente**
  - Given una tarea que ya fue borrada
  - When el usuario intenta borrarla de nuevo
  - Then la respuesta es 404 Not Found

### US-09 — Cambiar el estado de una tarea
**Como** usuario, **quiero** cambiar el estado de una tarea (pending / completed / archived), **para** reflejar su progreso.  [PRD 3.2]

**Criterios de aceptación**
- **Escenario: marcar como completada**
  - Given una tarea en estado "pending"
  - When el usuario la marca como completada
  - Then su estado pasa a "completed"
- **Escenario: archivar**
  - Given una tarea en estado "pending" o "completed"
  - When el usuario la archiva
  - Then su estado pasa a "archived"
  - And deja de aparecer en el listado por defecto (asumido)
- **Escenario: estado inválido**
  - Given una petición de cambio de estado
  - When el valor no es "pending" | "completed" | "archived"
  - Then la respuesta es 400 Bad Request con los valores válidos

---

## ÉPICA C — Organización y filtrado

### US-10 — Filtrar tareas por estado
**Como** usuario con varias tareas, **quiero** filtrar por estado, **para** centrarme en lo pendiente.  [PRD 3.3]

**Criterios de aceptación**
- **Escenario: filtro por pendientes**
  - Given un usuario con 12 tareas (5 pending, 7 completed)
  - When filtra por status="pending"
  - Then la respuesta es 200 OK y contiene exactamente 5 tareas, todas "pending"
- **Escenario: filtro por completadas**
  - Given el mismo usuario
  - When filtra por status="completed"
  - Then contiene exactamente 7 tareas, todas "completed"
- **Escenario: valor de filtro inválido**
  - Given el usuario filtra por status="banana"
  - When la petición llega al backend
  - Then responde 400 Bad Request
  - And el mensaje incluye los valores válidos: "pending" | "completed" | "archived"

---

## ÉPICA D — Exportación

### US-11 — Exportar tareas a CSV
**Como** usuario, **quiero** exportar mis tareas a un archivo CSV, **para** poder llevarme mis datos.  [PRD 3.4]

**Criterios de aceptación**
- **Escenario: exportación con tareas**
  - Given un usuario con varias tareas
  - When solicita la exportación a CSV
  - Then descarga un archivo CSV que incluye al menos título, descripción, estado y fecha límite de cada tarea
  - And el CSV contiene una fila por tarea más la cabecera (asumido)
- **Escenario: exportación sin tareas**
  - Given un usuario sin ninguna tarea
  - When solicita la exportación
  - Then descarga un CSV con solo la fila de cabecera (asumido)
- **Escenario: solo datos propios**
  - Given dos usuarios con tareas
  - When el usuario A exporta
  - Then el CSV contiene únicamente sus tareas

---

## ÉPICA E — Sincronización con Google Calendar

### US-12 — Conectar la cuenta de Google (OAuth)
**Como** usuario, **quiero** conectar mi cuenta de Google a FlowSync, **para** que mis tareas se reflejen en mi calendario.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: autorización exitosa**
  - Given un usuario autenticado sin Google conectado
  - When completa el flujo OAuth 2.0 y concede permisos de lectura y escritura de calendario
  - Then FlowSync almacena de forma segura sus tokens de Google
  - And la cuenta queda marcada como "conectada a Google"
- **Escenario: el usuario cancela la autorización**
  - Given un usuario en el flujo OAuth
  - When deniega el permiso o cancela
  - Then FlowSync no guarda token y la cuenta sigue "no conectada"
  - And se muestra un mensaje claro de que la conexión no se completó
- **Escenario: permisos insuficientes (asumido)**
  - Given un usuario que concede solo lectura
  - When FlowSync detecta que falta el permiso de escritura
  - Then informa de que la sincronización no podrá crear eventos

### US-13 — Crear evento de calendario a partir de una tarea con fecha
**Como** usuario con Google conectado, **quiero** que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, **para** no copiarlas a mano.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: tarea con fecha crea evento**
  - Given un usuario con Google conectado
  - When crea (o asigna fecha límite a) una tarea
  - Then se crea un evento en su Google Calendar asociado a esa tarea
  - And el evento queda vinculado a la tarea para futuras actualizaciones
- **Escenario: tarea sin fecha no crea evento**
  - Given un usuario con Google conectado
  - When crea una tarea sin fecha límite
  - Then no se crea ningún evento de calendario
- **Escenario: hora del evento (asumido)**
  - Given una fecha límite sin hora concreta
  - When se crea el evento
  - Then el evento se crea como evento de día completo o a una hora por defecto a definir con Producto (asumido)

### US-14 — Actualizar el evento al cambiar la fecha de la tarea
**Como** usuario con Google conectado, **quiero** que al cambiar la fecha de una tarea se actualice su evento, **para** que calendario y tareas no se desincronicen.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: cambio de fecha**
  - Given una tarea con evento ya creado en Google Calendar
  - When el usuario cambia su fecha límite
  - Then el evento correspondiente se actualiza a la nueva fecha
- **Escenario: añadir fecha a tarea sin evento**
  - Given una tarea sin fecha límite y sin evento
  - When el usuario le asigna una fecha límite
  - Then se crea un nuevo evento (no se actualiza ninguno)
- **Escenario: quitar la fecha**
  - Given una tarea con evento
  - When el usuario elimina su fecha límite
  - Then el evento asociado se elimina del calendario (asumido)

### US-15 — Eliminar/ajustar el evento al completar o borrar la tarea
**Como** usuario con Google conectado, **quiero** que al completar o borrar una tarea su evento desaparezca, **para** que el calendario refleje solo lo vigente.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: borrar tarea**
  - Given una tarea con evento en Google Calendar
  - When el usuario borra la tarea
  - Then el evento correspondiente se elimina del calendario
- **Escenario: completar tarea**
  - Given una tarea con evento en Google Calendar
  - When el usuario la marca como completada
  - Then el evento se elimina o se marca según corresponda (criterio exacto a definir) (asumido)

### US-16 — Desconectar la cuenta de Google
**Como** usuario, **quiero** desconectar mi cuenta de Google cuando quiera, **para** controlar mi privacidad sin perder mis tareas.  [PRD 3.5]

**Criterios de aceptación**
- **Escenario: desconexión**
  - Given un usuario con Google conectado
  - When pulsa "desconectar Google"
  - Then FlowSync deja de sincronizar con su calendario
  - And conserva todas las tareas ya creadas
- **Escenario: tokens tras desconectar (asumido)**
  - Given un usuario que desconecta Google
  - When se completa la desconexión
  - Then los tokens de Google almacenados se revocan/eliminan de FlowSync
- **Escenario: reconectar**
  - Given un usuario que se había desconectado
  - When vuelve a conectar Google
  - Then la sincronización se reanuda para nuevas tareas con fecha

### US-17 — Sincronización resiliente ante fallos de la API de Google
**Como** usuario, **quiero** que mis tareas se guarden aunque la sincronización con Google falle, **para** no perder datos cuando la API no esté disponible.  [PRD 3.5, 4]

**Criterios de aceptación**
- **Escenario: API de Google no disponible**
  - Given un usuario con Google conectado
  - When crea una tarea con fecha y la API de Google devuelve error o no responde
  - Then la tarea se guarda igualmente en FlowSync
  - And la sincronización se marca como pendiente y se reintenta más tarde
- **Escenario: registro de la operación**
  - Given una operación de sincronización
  - When se ejecuta (con éxito o fallo)
  - Then queda registrada en logs para poder diagnosticar fallos
- **Escenario: reintento exitoso**
  - Given una sincronización fallida marcada como pendiente
  - When un reintento posterior tiene éxito
  - Then el evento se crea/actualiza y la operación deja de estar pendiente

---

## Supuestos y preguntas abiertas

Marcados como `(asumido)` arriba; recomiendo validar con Producto antes de entrar a sprint:

- **Expiración / refresco de tokens** de acceso (auth de FlowSync) y de OAuth de Google: el PRD no fija duración ni estrategia de refresh.
- **Orden por defecto del listado**: el PRD delega el criterio exacto al refinamiento.
- **Hora del evento de calendario**: la fecha límite es una *fecha*; el PRD no define si el evento es de día completo o a qué hora. Riesgo de zonas horarias (sección 7).
- **Qué significa exactamente "marcar" el evento al completar** una tarea (¿borrar, mover, renombrar?).
- **Formato CSV**: separador, codificación, fila de cabecera y formato de fecha no especificados.
- **Visibilidad de archivadas** en el listado por defecto.
- **Comportamiento ante desconexión**: ¿se borran los eventos ya creados en Google o se quedan? El PRD solo dice que no se borran las *tareas*.
- **Sincronización inversa** (editar evento en Google → tarea): el PRD la deja fuera del alcance comprometido del MVP (pendiente de spike). No se generó story.
