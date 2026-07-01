# Backlog inicial — FlowSync MVP

> Output generado a partir del PRD de FlowSync MVP (v1.0) aplicando el prompt de
> descomposición. Salida cruda, sin edición posterior.

---

## Épica 1: Autenticación y gestión de cuenta

**Historia A1 — Como visitante, quiero crear una cuenta con email y contraseña, para tener un espacio propio donde gestionar mis tareas.**

- **Given** un visitante en la pantalla de registro, **When** introduce un email válido y una contraseña de al menos 8 caracteres, **Then** el sistema crea la cuenta y lo lleva a la pantalla de bienvenida.
- **Given** un visitante que introduce un email ya registrado, **When** intenta registrarse, **Then** el sistema le indica que el email ya existe y le ofrece ir al inicio de sesión.
- **Given** un visitante que introduce una contraseña de menos de 8 caracteres, **When** intenta registrarse, **Then** el sistema muestra un error de validación comprensible y no crea la cuenta.
- **Given** un visitante que introduce un email con formato inválido, **When** intenta registrarse, **Then** el sistema muestra un error de validación claro. (asumido)

**Historia A2 — Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.**

- **Given** un usuario registrado en la pantalla de inicio de sesión, **When** introduce sus credenciales correctas, **Then** el sistema lo autentica y le muestra su listado de tareas.
- **Given** un usuario que introduce credenciales incorrectas, **When** intenta iniciar sesión, **Then** el sistema muestra un mensaje de error sin revelar si el fallo fue el email o la contraseña. (asumido)
- **Given** un usuario autenticado, **When** su sesión sigue activa (token válido), **Then** puede volver a la aplicación sin volver a introducir credenciales.

**Historia A3 — Como usuario autenticado, quiero cerrar sesión, para proteger mi cuenta en un dispositivo compartido.**

- **Given** un usuario con sesión activa, **When** pulsa "cerrar sesión", **Then** el sistema invalida la sesión y lo lleva a la pantalla de inicio de sesión.
- **Given** un usuario que ha cerrado sesión, **When** intenta acceder a una pantalla interna, **Then** el sistema lo redirige al inicio de sesión. (asumido)

**Historia A4 — Como usuario recién registrado, quiero una pantalla de bienvenida que me explique qué hace FlowSync, para entender el producto y crear mi primera tarea.**

- **Given** un usuario que acaba de registrarse con éxito, **When** llega a la pantalla de bienvenida, **Then** ve una frase que explica qué hace FlowSync y una invitación a crear su primera tarea.
- **Given** un usuario en la pantalla de bienvenida, **When** pulsa la invitación a crear su primera tarea, **Then** el sistema lo lleva al formulario de creación de tarea. (asumido)

---

## Épica 2: Gestión de tareas (CRUD)

**Historia T1 — Como usuario, quiero crear una tarea indicando al menos un título, para registrar algo que tengo que hacer.**

- **Given** un usuario autenticado en el formulario de creación, **When** introduce un título y guarda, **Then** el sistema crea la tarea con estado `pending` y la muestra en el listado.
- **Given** un usuario que deja el título vacío, **When** intenta guardar, **Then** el sistema muestra un error de validación y no crea la tarea.
- **Given** un usuario que crea una tarea, **When** añade opcionalmente descripción y fecha límite, **Then** el sistema las guarda junto con la tarea.

**Historia T2 — Como usuario, quiero ver el listado de mis tareas, para saber qué tengo pendiente.**

- **Given** un usuario autenticado con tareas creadas, **When** abre el listado, **Then** ve sus tareas con título, estado y fecha límite (si tiene).
- **Given** un usuario sin ninguna tarea, **When** abre el listado, **Then** ve un estado vacío con una invitación a crear la primera tarea.
- **Given** un usuario con hasta 200 tareas, **When** abre el listado, **Then** este carga en menos de 1 segundo.

**Historia T3 — Como usuario, quiero editar cualquier campo de una tarea existente, para corregirla o actualizarla.**

- **Given** un usuario con una tarea existente, **When** edita el título, la descripción o la fecha límite y guarda, **Then** el sistema actualiza la tarea con los nuevos valores.
- **Given** un usuario que edita una tarea y borra el título, **When** intenta guardar, **Then** el sistema muestra un error de validación y no guarda el cambio. (asumido)

**Historia T4 — Como usuario, quiero cambiar el estado de una tarea, para marcarla como completada o archivarla.**

- **Given** una tarea en estado `pending`, **When** el usuario la marca como completada, **Then** su estado pasa a `completed`.
- **Given** una tarea en cualquier estado, **When** el usuario la archiva, **Then** su estado pasa a `archived`.
- **Given** una tarea `completed`, **When** el usuario la reabre, **Then** su estado vuelve a `pending`. (asumido)

**Historia T5 — Como usuario, quiero borrar una tarea, para eliminar algo que ya no necesito.**

- **Given** un usuario con una tarea existente, **When** pulsa borrar, **Then** el sistema elimina la tarea del listado.
- **Given** un usuario que va a borrar una tarea, **When** se le pide confirmación, **Then** la tarea solo se borra si confirma. (asumido)

---

## Épica 3: Organización y filtrado

**Historia O1 — Como usuario, quiero filtrar mis tareas por estado, para concentrarme solo en las pendientes o revisar las completadas.**

- **Given** un usuario con tareas en varios estados, **When** aplica el filtro "pendientes", **Then** el listado muestra solo las tareas en estado `pending`.
- **Given** un usuario con el filtro "completadas" aplicado, **When** revisa el listado, **Then** ve solo las tareas en estado `completed`.
- **Given** un usuario con un filtro aplicado que no arroja resultados, **When** revisa el listado, **Then** ve un estado vacío indicando que no hay tareas para ese filtro. (asumido)

**Historia O2 — Como usuario, quiero que mis tareas se muestren por defecto con las más relevantes para "hoy" primero, para ver de un vistazo qué tengo que hacer.**

- **Given** un usuario con tareas con distintas fechas límite, **When** abre el listado sin aplicar filtros, **Then** las tareas más relevantes para "hoy" aparecen primero.
- **Given** un usuario con tareas sin fecha límite, **When** abre el listado, **Then** esas tareas se muestran de forma consistente respecto a las que sí tienen fecha. (asumido)

---

## Épica 4: Exportación

**Historia E1 — Como usuario, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos.**

- **Given** un usuario con tareas creadas, **When** solicita exportar, **Then** el sistema genera un archivo CSV con título, descripción, estado y fecha límite de cada tarea.
- **Given** un usuario sin ninguna tarea, **When** solicita exportar, **Then** el sistema genera un CSV solo con la fila de cabeceras. (asumido)

---

## Épica 5: Sincronización con Google Calendar

**Historia G1 — Como usuario, quiero conectar mi cuenta de Google a FlowSync, para que mis tareas se reflejen en mi calendario.**

- **Given** un usuario autenticado sin Google conectado, **When** inicia la conexión y autoriza vía OAuth, **Then** el sistema queda vinculado a su cuenta de Google.
- **Given** un usuario que cancela el flujo de autorización de Google, **When** vuelve a FlowSync, **Then** el sistema indica que la conexión no se completó y no queda vinculado. (asumido)

**Historia G2 — Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi calendario, para no copiarlas a mano.**

- **Given** un usuario con Google conectado, **When** crea o ya tiene una tarea con fecha límite, **Then** aparece un evento correspondiente en su Google Calendar.
- **Given** una tarea sin fecha límite, **When** el usuario la crea, **Then** no se genera ningún evento en el calendario.

**Historia G3 — Como usuario, quiero que al cambiar la fecha de una tarea el evento del calendario se actualice, para mantener ambos alineados.**

- **Given** una tarea ya sincronizada como evento, **When** el usuario cambia su fecha límite, **Then** el evento correspondiente en Google Calendar se actualiza a la nueva fecha.
- **Given** una tarea sincronizada, **When** el usuario la completa o la borra, **Then** el evento correspondiente se elimina o se marca según corresponda.

**Historia G4 — Como usuario, quiero desconectar mi cuenta de Google cuando quiera, para dejar de sincronizar sin perder mis tareas.**

- **Given** un usuario con Google conectado, **When** pulsa desconectar, **Then** FlowSync deja de sincronizar pero conserva todas las tareas ya creadas.

**Historia G5 — Como usuario, quiero que mis tareas se guarden aunque falle la sincronización con Google, para no perder trabajo cuando el servicio no está disponible.**

- **Given** un usuario con Google conectado, **When** crea una tarea y la API de Google no está disponible, **Then** la tarea se guarda igualmente en FlowSync y la sincronización se reintenta más tarde.
- **Given** una operación de sincronización fallida, **When** el sistema la registra, **Then** queda un log que permite diagnosticar el fallo. (asumido)
