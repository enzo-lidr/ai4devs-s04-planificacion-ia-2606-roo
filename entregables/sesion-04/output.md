# User Stories — FlowSync MVP

> **Fuente**: PRD FlowSync MVP v1.0 · Q3 2026
> **Fecha de generación**: 2026-06-29
> **Metodología**: descomposición por épicas funcionales según secciones 3.1–3.5 del PRD

---

# Épica 1 — Autenticación y gestión de cuenta

## User Story 1 — Registro de cuenta

**Como visitante, quiero crear una cuenta con email y contraseña, para acceder a FlowSync y gestionar mis tareas personales.**

### Criterios de aceptación

* Given que el visitante está en la pantalla de registro,
  When ingresa un email válido, una contraseña de al menos 8 caracteres y confirma el formulario,
  Then el sistema crea la cuenta y muestra la pantalla de bienvenida con una frase explicando qué hace FlowSync e invitando a crear la primera tarea.

* Given que el visitante ingresa un email ya registrado,
  When confirma el formulario de registro,
  Then el sistema muestra un mensaje indicando que el email ya existe y ofrece un enlace para ir al inicio de sesión.

* Given que el visitante ingresa una contraseña con menos de 8 caracteres,
  When confirma el formulario,
  Then el sistema muestra un mensaje de error indicando que la contraseña debe tener al menos 8 caracteres.

* Given que el visitante deja el campo email vacío o ingresa un formato inválido,
  When confirma el formulario,
  Then el sistema muestra un mensaje de error indicando que el email es inválido.

---

## User Story 2 — Inicio de sesión

**Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.**

### Criterios de aceptación

* Given que el usuario está en la pantalla de inicio de sesión,
  When ingresa email y contraseña correctos y confirma,
  Then accede al listado de sus tareas.

* Given que el usuario ingresa credenciales incorrectas,
  When confirma el formulario de inicio de sesión,
  Then el sistema muestra un mensaje de error sin revelar cuál campo es incorrecto. **(asumido: no exponer si el error es de email o contraseña, para evitar enumeración de cuentas.)**

* Given que el usuario ya inició sesión con un token de acceso activo,
  When navega a una página protegida,
  Then accede sin necesidad de autenticarse nuevamente.

---

## User Story 3 — Cierre de sesión

**Como usuario autenticado, quiero cerrar sesión, para proteger mi cuenta cuando termino de usar FlowSync.**

### Criterios de aceptación

* Given que el usuario está autenticado,
  When selecciona la opción de cerrar sesión,
  Then la sesión se invalida y es redirigido a la pantalla de inicio de sesión.

* Given que el usuario cerró sesión,
  When intenta acceder a una URL protegida directamente,
  Then el sistema lo redirige al inicio de sesión.

---

# Épica 2 — Gestión de tareas (CRUD)

## User Story 4 — Crear tarea

**Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar mis pendientes con toda la información relevante.**

### Criterios de aceptación

* Given que el usuario está autenticado,
  When ingresa un título y guarda la tarea,
  Then la tarea aparece en el listado con estado `pending` y los campos ingresados.

* Given que el usuario intenta crear una tarea sin título,
  When confirma el formulario,
  Then el sistema muestra un mensaje indicando que el título es obligatorio y no crea la tarea.

* Given que el usuario crea una tarea sin fecha límite ni descripción,
  When guarda la tarea,
  Then la tarea se crea correctamente con solo el título, mostrando esos campos como vacíos.

* Given que el usuario crea una tarea con fecha límite,
  When guarda la tarea,
  Then la tarea muestra la fecha límite en el listado.

---

## User Story 5 — Ver listado de tareas

**Como usuario autenticado, quiero ver el listado de todas mis tareas, para conocer mis pendientes actuales de un vistazo.**

### Criterios de aceptación

* Given que el usuario tiene tareas creadas,
  When accede al listado de tareas,
  Then ve todas sus tareas ordenadas priorizando las más relevantes para el día actual. **(asumido: el criterio exacto de ordenación queda pendiente de definir en el refinamiento, según indica el PRD.)**

* Given que el usuario no tiene tareas creadas o todas están archivadas,
  When accede al listado,
  Then ve un estado vacío con una invitación a crear su primera tarea.

* Given que el usuario tiene hasta 200 tareas,
  When accede al listado,
  Then el listado carga en menos de 1 segundo.

---

## User Story 6 — Editar tarea

**Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para mantener mis pendientes actualizados cuando cambian.**

### Criterios de aceptación

* Given que el usuario accede a una tarea existente,
  When modifica el título y guarda,
  Then el listado muestra el título actualizado.

* Given que el usuario edita la fecha límite de una tarea,
  When guarda el cambio,
  Then la tarea muestra la nueva fecha límite.

* Given que el usuario intenta dejar el título vacío al editar,
  When confirma el formulario,
  Then el sistema muestra un mensaje indicando que el título es obligatorio y no guarda el cambio.

* Given que el usuario elimina la fecha límite de una tarea al editar,
  When guarda el cambio,
  Then la tarea queda sin fecha límite.

---

## User Story 7 — Borrar tarea

**Como usuario autenticado, quiero borrar una tarea, para eliminar pendientes que ya no son relevantes.**

### Criterios de aceptación

* Given que el usuario selecciona la acción de borrar sobre una tarea,
  When confirma la operación, **(asumido: el sistema solicita confirmación antes de eliminar de forma irreversible.)**
  Then la tarea desaparece del listado permanentemente.

* Given que el usuario cancela la acción de borrado,
  When descarta la confirmación,
  Then la tarea permanece en el listado sin cambios.

---

## User Story 8 — Cambiar estado de tarea

**Como usuario autenticado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar el progreso real de mis pendientes.**

### Criterios de aceptación

* Given que una tarea está en estado `pending`,
  When el usuario la marca como completada,
  Then la tarea pasa a estado `completed`.

* Given que una tarea está en cualquier estado,
  When el usuario la archiva,
  Then la tarea pasa a estado `archived` y deja de aparecer en la vista principal del listado.

* Given que una tarea recién se crea,
  When aparece en el listado,
  Then su estado es `pending`.

* Given que el usuario cambia el estado de una tarea,
  When el cambio se guarda,
  Then el nuevo estado queda reflejado visualmente en el listado sin necesidad de recargar la página. **(asumido: comportamiento esperado en una interfaz web moderna.)**

---

# Épica 3 — Organización y filtrado

## User Story 9 — Filtrar tareas por estado

**Como usuario autenticado, quiero filtrar mi listado de tareas por estado, para ver únicamente las que me interesan en cada momento.**

### Criterios de aceptación

* Given que el usuario tiene tareas en distintos estados,
  When selecciona el filtro `pending`,
  Then el listado muestra únicamente las tareas con estado `pending`.

* Given que el usuario tiene tareas en distintos estados,
  When selecciona el filtro `completed`,
  Then el listado muestra únicamente las tareas con estado `completed`.

* Given que el usuario tiene tareas en distintos estados,
  When selecciona el filtro `archived`,
  Then el listado muestra únicamente las tareas con estado `archived`.

* Given que el usuario aplica un filtro y no existen tareas con ese estado,
  When el filtro se aplica,
  Then el listado muestra un estado vacío.

---

# Épica 4 — Exportación

## User Story 10 — Exportar tareas a CSV

**Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos fuera de FlowSync.**

### Criterios de aceptación

* Given que el usuario tiene tareas creadas,
  When solicita la exportación CSV,
  Then descarga un archivo que incluye al menos las columnas: título, descripción, estado y fecha límite de cada tarea.

* Given que el usuario tiene tareas en múltiples estados,
  When exporta,
  Then el CSV incluye tareas de todos los estados sin excepción.

* Given que el usuario no tiene tareas,
  When solicita la exportación, **(asumido: la exportación de cuenta vacía sigue siendo válida.)**
  Then descarga un archivo CSV con solo las cabeceras de columna y sin filas de datos.

---

# Épica 5 — Sincronización con Google Calendar

## User Story 11 — Conectar cuenta de Google

**Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para habilitar la sincronización de mis tareas con Google Calendar.**

### Criterios de aceptación

* Given que el usuario no tiene una cuenta de Google conectada,
  When inicia el proceso de conexión,
  Then el sistema lo redirige al flujo de autorización OAuth de Google.

* Given que el usuario completa la autorización en Google,
  When regresa a FlowSync,
  Then su cuenta aparece como conectada y la sincronización queda activa.

* Given que el usuario rechaza o cancela la autorización en Google,
  When regresa a FlowSync,
  Then su cuenta permanece desconectada y el sistema muestra un mensaje indicando que la conexión no se completó.

---

## User Story 12 — Sincronizar tareas con fecha límite como eventos en Google Calendar

**Como usuario autenticado con Google Calendar conectado, quiero que mis tareas con fecha límite aparezcan automáticamente como eventos en Google Calendar, para ver todos mis compromisos en un solo lugar sin doble gestión.**

### Criterios de aceptación

* Given que el usuario tiene Google Calendar conectado y crea una tarea con fecha límite,
  When la tarea se guarda en FlowSync,
  Then aparece un evento correspondiente en Google Calendar del usuario.

* Given que el usuario crea una tarea sin fecha límite,
  When la tarea se guarda,
  Then no se crea ningún evento en Google Calendar.

* Given que la API de Google no está disponible en el momento de crear la tarea,
  When la sincronización falla,
  Then la tarea se guarda correctamente en FlowSync y el sistema reintenta la sincronización más tarde.

---

## User Story 13 — Actualizar evento en Google Calendar al cambiar fecha de tarea

**Como usuario autenticado con Google Calendar conectado, quiero que al cambiar la fecha límite de una tarea el evento en Google Calendar se actualice automáticamente, para mantener ambos sistemas sincronizados.**

### Criterios de aceptación

* Given que una tarea tiene un evento en Google Calendar,
  When el usuario cambia la fecha límite de la tarea en FlowSync,
  Then el evento en Google Calendar se actualiza con la nueva fecha.

* Given que el usuario cambia la fecha límite y la API de Google no está disponible,
  When la sincronización falla,
  Then el cambio se guarda en FlowSync y el sistema reintenta la sincronización más tarde.

* Given que el usuario agrega una fecha límite a una tarea que antes no la tenía,
  When guarda el cambio,
  Then se crea un evento nuevo en Google Calendar para esa tarea.

---

## User Story 14 — Eliminar evento en Google Calendar al completar o borrar una tarea

**Como usuario autenticado con Google Calendar conectado, quiero que al completar o borrar una tarea su evento en Google Calendar se elimine, para que mi calendario no muestre compromisos ya resueltos.**

### Criterios de aceptación

* Given que una tarea tiene un evento en Google Calendar,
  When el usuario borra la tarea en FlowSync,
  Then el evento correspondiente se elimina de Google Calendar.

* Given que una tarea con evento en Google Calendar pasa a estado `completed`,
  When el usuario la marca como completada,
  Then el evento se elimina o actualiza en Google Calendar según corresponda. **(asumido: el comportamiento exacto —eliminar vs. marcar— queda sujeto a refinamiento, el PRD indica "se elimina o se marca según corresponda".)**

* Given que la API de Google no está disponible al borrar o completar una tarea,
  When la sincronización falla,
  Then la operación se aplica en FlowSync y el sistema reintenta la eliminación del evento más tarde.

---

## User Story 15 — Desconectar cuenta de Google

**Como usuario autenticado con Google Calendar conectado, quiero desconectar mi cuenta de Google en cualquier momento, para revocar el acceso de FlowSync a mi calendario.**

### Criterios de aceptación

* Given que el usuario tiene Google Calendar conectado,
  When solicita la desconexión,
  Then FlowSync revoca el acceso y deja de sincronizar con Google Calendar.

* Given que el usuario desconecta su cuenta de Google,
  When la desconexión se completa,
  Then todas las tareas existentes en FlowSync se mantienen intactas.

* Given que el usuario está desconectado de Google,
  When crea o modifica una tarea,
  Then no se realiza ningún intento de sincronización con Google Calendar.

---

**Resultado: 15 user stories · 5 épicas · todas dentro del alcance MVP · sin funcionalidades inventadas · supuestos marcados.**
