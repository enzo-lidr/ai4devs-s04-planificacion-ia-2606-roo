# Backlog FlowSync MVP — User Stories

## Épica 1 — Autenticación y gestión de cuenta

### US-AUTH-01 — Registro de cuenta
**Como** visitante, **quiero** crear una cuenta con mi email y contraseña, **para** empezar a usar FlowSync con mis datos privados.

- **Dado** que estoy en la pantalla de registro, **Cuando** introduzco un email válido y una contraseña de al menos 8 caracteres, **Entonces** se crea mi cuenta y quedo autenticado.
- **Dado** que introduzco un email ya registrado, **Cuando** envío el formulario, **Entonces** veo un mensaje claro indicándolo y un enlace para ir a iniciar sesión.
- **Dado** que introduzco una contraseña de menos de 8 caracteres, **Cuando** envío el formulario, **Entonces** veo un error de validación comprensible y la cuenta no se crea.
- **Dado** que introduzco un email con formato inválido, **Cuando** envío el formulario, **Entonces** veo un error de validación y la cuenta no se crea. *(asumido: el PRD pide "errores claros" pero no detalla la validación de formato de email)*

### US-AUTH-02 — Onboarding tras el registro
**Como** usuario recién registrado, **quiero** ver una pantalla de bienvenida que explique qué hace FlowSync, **para** entender el producto y crear mi primera tarea.

- **Dado** que acabo de completar el registro con éxito, **Cuando** llego a la app, **Entonces** veo una pantalla de bienvenida con una frase que explica qué hace FlowSync.
- **Dado** que estoy en la pantalla de bienvenida, **Cuando** la leo, **Entonces** veo una invitación/CTA para crear mi primera tarea.
- **Dado** que pulso la invitación a crear una tarea, **Cuando** la acepto, **Entonces** llego al formulario de creación de tarea.

### US-AUTH-03 — Inicio y cierre de sesión
**Como** usuario registrado, **quiero** iniciar y cerrar sesión, **para** acceder a mis tareas de forma segura desde cualquier navegador.

- **Dado** que tengo una cuenta, **Cuando** introduzco mi email y contraseña correctos, **Entonces** quedo autenticado mediante un access token y veo mis tareas.
- **Dado** que introduzco credenciales incorrectas, **Cuando** envío el formulario, **Entonces** veo un mensaje de error comprensible y no se me autentica.
- **Dado** que estoy autenticado, **Cuando** pulso "cerrar sesión", **Entonces** mi sesión se invalida y vuelvo a la pantalla de inicio de sesión.
- **Dado** que no estoy autenticado, **Cuando** intento acceder a una vista de tareas, **Entonces** soy redirigido a iniciar sesión. *(asumido)*

## Épica 2 — Gestión de tareas (CRUD)

### US-TASK-01 — Crear tarea
**Como** usuario autenticado, **quiero** crear una tarea indicando al menos un título, **para** registrar un pendiente.

- **Dado** que estoy en el formulario de nueva tarea, **Cuando** introduzco un título y guardo, **Entonces** la tarea se crea con estado `pending` y aparece en mi listado.
- **Dado** que dejo el título vacío, **Cuando** intento guardar, **Entonces** veo un error de validación y la tarea no se crea.
- **Dado** que estoy creando una tarea, **Cuando** añado descripción y/o fecha límite (ambas opcionales), **Entonces** se guardan junto con la tarea.
- **Dado** que creo una tarea, **Cuando** se guarda, **Entonces** solo es visible para mi cuenta y nunca para otros usuarios.

### US-TASK-02 — Ver listado de tareas
**Como** usuario autenticado, **quiero** ver el listado de mis tareas, **para** saber qué tengo pendiente.

- **Dado** que tengo tareas, **Cuando** abro la vista de listado, **Entonces** veo mis tareas con título, estado y fecha límite si la tienen.
- **Dado** que tengo tareas, **Cuando** se muestra el listado, **Entonces** las más relevantes para "hoy" aparecen primero. *(asumido: el criterio exacto de orden lo deja el PRD al refinamiento)*
- **Dado** que no tengo ninguna tarea (cuenta nueva o todas archivadas), **Cuando** abro el listado, **Entonces** veo un estado vacío que me invita a crear la primera.
- **Dado** que tengo hasta 200 tareas, **Cuando** abro el listado, **Entonces** carga en menos de 1 segundo.

### US-TASK-03 — Editar tarea
**Como** usuario autenticado, **quiero** editar cualquier campo de una tarea, **para** mantenerla al día.

- **Dado** que selecciono una tarea existente, **Cuando** modifico título, descripción o fecha límite y guardo, **Entonces** los cambios se persisten y se reflejan en el listado.
- **Dado** que edito una tarea y vacío el título, **Cuando** intento guardar, **Entonces** veo un error de validación y el cambio no se aplica.
- **Dado** que intento editar una tarea que no es mía, **Cuando** lo intento, **Entonces** la operación es rechazada. *(asumido)*

### US-TASK-04 — Cambiar estado de tarea
**Como** usuario autenticado, **quiero** cambiar el estado de una tarea entre `pending`, `completed` y `archived`, **para** reflejar su situación real.

- **Dado** que tengo una tarea en `pending`, **Cuando** la marco como completada, **Entonces** su estado pasa a `completed`.
- **Dado** que tengo una tarea, **Cuando** la archivo, **Entonces** su estado pasa a `archived` y deja de aparecer en la vista por defecto. *(asumido: que las archivadas se ocultan por defecto)*
- **Dado** que una tarea está `completed` o `archived`, **Cuando** lo decido, **Entonces** puedo devolverla a `pending`. *(asumido)*

### US-TASK-05 — Borrar tarea
**Como** usuario autenticado, **quiero** borrar una tarea, **para** eliminar pendientes que ya no aplican.

- **Dado** que selecciono una tarea, **Cuando** confirmo el borrado, **Entonces** la tarea desaparece de mi listado de forma permanente.
- **Dado** que pulso borrar, **Cuando** se me pide confirmación, **Entonces** puedo cancelar y la tarea se mantiene. *(asumido)*
- **Dado** que borro una tarea que tenía evento sincronizado, **Cuando** se elimina, **Entonces** su evento en Google Calendar también se elimina (ver US-SYNC-03).

## Épica 3 — Organización y filtrado

### US-ORG-01 — Filtrar tareas por estado
**Como** usuario autenticado, **quiero** filtrar mis tareas por estado, **para** centrarme solo en las pendientes o revisar las completadas.

- **Dado** que tengo tareas en varios estados, **Cuando** selecciono el filtro "pendientes", **Entonces** solo veo tareas en `pending`.
- **Dado** que aplico el filtro "completadas", **Cuando** se actualiza el listado, **Entonces** solo veo tareas en `completed`.
- **Dado** que un filtro no devuelve resultados, **Cuando** se aplica, **Entonces** veo un estado vacío acorde al filtro. *(asumido)*
- **Dado** que quito el filtro, **Cuando** vuelvo a la vista por defecto, **Entonces** veo las tareas según el orden por defecto.

## Épica 4 — Exportación

### US-EXP-01 — Exportar tareas a CSV
**Como** usuario autenticado, **quiero** exportar mis tareas a un archivo CSV, **para** llevarme mis datos.

- **Dado** que tengo tareas, **Cuando** pulso "exportar", **Entonces** se descarga un CSV que incluye al menos título, descripción, estado y fecha límite de cada tarea.
- **Dado** que tengo tareas en varios estados, **Cuando** exporto, **Entonces** el CSV incluye todas mis tareas. *(asumido: que la exportación no se limita al filtro activo)*
- **Dado** que no tengo tareas, **Cuando** pulso exportar, **Entonces** obtengo un CSV con solo la fila de cabeceras o un aviso de que no hay datos. *(asumido)*

## Épica 5 — Sincronización con Google Calendar

### US-SYNC-01 — Conectar y desconectar Google Calendar
**Como** usuario autenticado, **quiero** conectar (y desconectar) mi cuenta de Google mediante OAuth, **para** que mis tareas se reflejen en mi calendario.

- **Dado** que no he conectado Google, **Cuando** inicio la conexión, **Entonces** paso por el flujo OAuth 2.0 de Google y, al autorizar, mi cuenta queda conectada.
- **Dado** que mis tokens de Google se guardan, **Cuando** se almacenan, **Entonces** se guardan de forma segura y nunca son accesibles por otros usuarios.
- **Dado** que estoy conectado, **Cuando** pulso "desconectar", **Entonces** FlowSync deja de sincronizar pero **no** borra las tareas ya creadas.
- **Dado** que cancelo o rechazo la autorización en Google, **Cuando** vuelvo a FlowSync, **Entonces** sigo sin conexión y veo un mensaje claro. *(asumido)*

### US-SYNC-02 — Crear evento al tener fecha límite
**Como** usuario con Google conectado, **quiero** que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, **para** ver mis pendientes y mi tiempo en un solo sitio.

- **Dado** que tengo Google conectado, **Cuando** creo o asigno una fecha límite a una tarea, **Entonces** se crea el evento correspondiente en mi Google Calendar.
- **Dado** que creo una tarea **sin** fecha límite, **Cuando** se guarda, **Entonces** no se crea ningún evento.
- **Dado** que la API de Google falla o no está disponible, **Cuando** intento crear el evento, **Entonces** la tarea se guarda igualmente en FlowSync y la sincronización se reintenta más tarde.
- **Dado** que se intenta sincronizar, **Cuando** la operación ocurre, **Entonces** queda registrada en logs para diagnóstico.

### US-SYNC-03 — Actualizar/eliminar evento ante cambios de la tarea
**Como** usuario con Google conectado, **quiero** que al cambiar, completar o borrar una tarea su evento se actualice o elimine, **para** que el calendario refleje siempre el estado real.

- **Dado** que cambio la fecha de una tarea sincronizada, **Cuando** guardo, **Entonces** el evento en Google Calendar se actualiza a la nueva fecha.
- **Dado** que completo una tarea sincronizada, **Cuando** cambia su estado, **Entonces** el evento se elimina o se marca según corresponda.
- **Dado** que borro una tarea sincronizada, **Cuando** se elimina, **Entonces** su evento en Google Calendar se elimina.
- **Dado** que la API de Google falla durante la actualización/borrado, **Cuando** ocurre el error, **Entonces** el cambio se persiste en FlowSync y la sincronización se reintenta más tarde, dejando registro en logs.
