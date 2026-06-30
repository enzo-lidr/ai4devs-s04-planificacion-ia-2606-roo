# Backlog de User Stories — FlowSync MVP

## Épica 1: Autenticación y gestión de cuenta

**US-1.1 — Registro con email y contraseña**

> Como visitante, quiero crear una cuenta con mi email y una contraseña, para tener un espacio privado donde gestionar mis tareas.

**Criterios de aceptación:**
- **Dado** que introduzco un email no registrado y una contraseña de al menos 8 caracteres, **Cuando** envío el formulario de registro, **Entonces** la cuenta se crea y llego a la pantalla de bienvenida (onboarding mínimo) que explica en una frase qué hace FlowSync y me invita a crear mi primera tarea.
- **Dado** que el email ya está registrado, **Cuando** intento crear la cuenta, **Entonces** el sistema lo indica y me ofrece ir al inicio de sesión.
- **Dado** que introduzco una contraseña de menos de 8 caracteres, **Cuando** envío el formulario, **Entonces** el sistema muestra un error de validación comprensible y no crea la cuenta.
- **Dado** que introduzco un email con formato inválido (asumido), **Cuando** envío el formulario, **Entonces** el sistema muestra un error de validación comprensible y no crea la cuenta.

**US-1.2 — Inicio de sesión**

> Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas de forma segura.

**Criterios de aceptación:**
- **Dado** que introduzco el email y la contraseña correctos de una cuenta existente, **Cuando** envío el formulario de inicio de sesión, **Entonces** se crea una sesión mediante un token de acceso y accedo a mi listado de tareas.
- **Dado** que introduzco credenciales incorrectas, **Cuando** intento iniciar sesión, **Entonces** el sistema muestra un error comprensible y no inicia la sesión.
- **Dado** que estoy autenticado, **Cuando** vuelvo a la aplicación con un token de acceso válido, **Entonces** mantengo mi sesión sin tener que volver a introducir credenciales.

**US-1.3 — Cierre de sesión**

> Como usuario autenticado, quiero cerrar sesión, para proteger el acceso a mis tareas cuando dejo de usar la aplicación.

**Criterios de aceptación:**
- **Dado** que tengo una sesión activa, **Cuando** selecciono cerrar sesión, **Entonces** mi token de acceso deja de ser válido y soy redirigido a la pantalla de inicio de sesión (asumido).
- **Dado** que he cerrado sesión, **Cuando** intento acceder a una vista que requiere autenticación, **Entonces** el sistema me solicita iniciar sesión de nuevo.
- **Dado** que los datos de mi cuenta son privados, **Cuando** otro usuario inicia sesión en el mismo navegador, **Entonces** no puede ver mis tareas.

**US-1.4 — Privacidad de los datos entre usuarios**

> Como usuario autenticado, quiero que mis tareas y datos solo sean accesibles para mí, para confiar en que mi información personal está protegida.

**Criterios de aceptación:**
- **Dado** que existo como usuario con tareas creadas, **Cuando** otro usuario autenticado consulta su listado, **Entonces** nunca ve mis tareas.
- **Dado** que estoy autenticado, **Cuando** consulto mi listado de tareas, **Entonces** solo veo las tareas asociadas a mi cuenta.
- **Dado** que mi cuenta tiene una conexión de Google con tokens almacenados, **Cuando** otro usuario accede al sistema, **Entonces** no puede acceder a mis tokens de Google.

---

## Épica 2: Gestión de tareas (CRUD)

**US-2.1 — Crear una tarea**

> Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar un pendiente rápidamente sin campos obligatorios adicionales.

**Criterios de aceptación:**
- **Dado** que estoy autenticado y en la pantalla de tareas, **Cuando** introduzco un título y confirmo, **Entonces** la tarea se crea con estado `pending` y aparece en mi listado.
- **Dado** que dejo el título vacío, **Cuando** intento guardar la tarea, **Entonces** el sistema muestra un error de validación comprensible y no crea la tarea.
- **Dado** que añado una descripción y una fecha límite opcionales, **Cuando** guardo la tarea, **Entonces** ambos valores quedan persistidos en la tarea.
- **Dado** que solo indico el título, **Cuando** guardo la tarea, **Entonces** la tarea se crea correctamente con descripción y fecha límite vacías.

**US-2.2 — Ver el listado de tareas**

> Como usuario autenticado, quiero ver el listado de mis tareas, para saber de un vistazo qué pendientes tengo.

**Criterios de aceptación:**
- **Dado** que tengo tareas creadas, **Cuando** accedo a la pantalla de tareas, **Entonces** veo el listado con al menos el título, el estado y la fecha límite de cada tarea (asumido).
- **Dado** que tengo varias tareas, **Cuando** se muestra el listado por defecto, **Entonces** las más relevantes para "hoy" aparecen primero.
- **Dado** que no tengo ninguna tarea (cuenta nueva o todas archivadas), **Cuando** accedo a la pantalla de tareas, **Entonces** veo un estado vacío con una invitación a crear la primera tarea.

**US-2.3 — Editar una tarea**

> Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para mantener su información actualizada.

**Criterios de aceptación:**
- **Dado** que tengo una tarea existente, **Cuando** modifico su título, descripción o fecha límite y guardo, **Entonces** los cambios quedan persistidos y se reflejan en el listado.
- **Dado** que edito una tarea, **Cuando** dejo el título vacío, **Entonces** el sistema muestra un error de validación y no guarda el cambio.
- **Dado** que una tarea tiene fecha límite, **Cuando** elimino su fecha límite y guardo, **Entonces** la tarea queda sin fecha límite.

**US-2.4 — Borrar una tarea**

> Como usuario autenticado, quiero borrar una tarea, para eliminar pendientes que ya no necesito.

**Criterios de aceptación:**
- **Dado** que tengo una tarea existente, **Cuando** confirmo su borrado, **Entonces** la tarea desaparece de mi listado.
- **Dado** que inicio el borrado de una tarea, **Cuando** se me solicita confirmación (asumido), **Entonces** la tarea no se borra hasta que confirmo.
- **Dado** que borro una tarea, **Cuando** vuelvo a consultar mi listado, **Entonces** la tarea ya no aparece.

**US-2.5 — Cambiar el estado de una tarea**

> Como usuario autenticado, quiero cambiar el estado de una tarea entre pending, completed y archived, para reflejar su progreso real.

**Criterios de aceptación:**
- **Dado** que tengo una tarea en estado `pending`, **Cuando** la marco como completada, **Entonces** su estado pasa a `completed`.
- **Dado** que tengo una tarea, **Cuando** la archivo, **Entonces** su estado pasa a `archived` y deja de mostrarse entre las pendientes (asumido).
- **Dado** que una tarea recién creada nace en `pending`, **Cuando** cambio su estado a `completed` o `archived`, **Entonces** el nuevo estado queda persistido.
- **Dado** que una tarea está `completed`, **Cuando** la reabro a `pending` (asumido), **Entonces** su estado vuelve a `pending`.

---

## Épica 3: Organización y filtrado

**US-3.1 — Filtrar tareas por estado**

> Como usuario autenticado, quiero filtrar mis tareas por estado, para concentrarme solo en las pendientes, las completadas o las archivadas según lo que necesite.

**Criterios de aceptación:**
- **Dado** que tengo tareas en distintos estados, **Cuando** selecciono el filtro "pendientes", **Entonces** el listado muestra solo las tareas en estado `pending`.
- **Dado** que aplico un filtro por estado, **Cuando** selecciono "completadas", **Entonces** el listado muestra solo las tareas en estado `completed`.
- **Dado** que un filtro por estado no devuelve resultados, **Cuando** se muestra el listado filtrado, **Entonces** veo un estado vacío acorde al filtro aplicado (asumido).

---

## Épica 4: Exportación

**US-4.1 — Exportar tareas a CSV**

> Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos fuera de FlowSync.

**Criterios de aceptación:**
- **Dado** que tengo tareas creadas, **Cuando** solicito exportar a CSV, **Entonces** se genera un archivo CSV que incluye, como mínimo, título, descripción, estado y fecha límite de cada tarea.
- **Dado** que solicito la exportación, **Cuando** se genera el archivo, **Entonces** el CSV contiene todas mis tareas (asumido).
- **Dado** que no tengo ninguna tarea, **Cuando** solicito exportar a CSV, **Entonces** obtengo un archivo con solo la fila de cabecera o una indicación de que no hay datos (asumido).

---

## Épica 5: Sincronización con Google Calendar

**US-5.1 — Conectar la cuenta de Google**

> Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para que mis tareas con fecha límite se reflejen en mi Google Calendar.

**Criterios de aceptación:**
- **Dado** que estoy autenticado y sin Google conectado, **Cuando** inicio la conexión de Google, **Entonces** soy llevado al flujo de autorización OAuth de Google.
- **Dado** que completo la autorización OAuth correctamente, **Cuando** vuelvo a FlowSync, **Entonces** mi cuenta queda marcada como conectada y los tokens de Google se almacenan de forma segura.
- **Dado** que cancelo o rechazo la autorización en Google, **Cuando** vuelvo a FlowSync, **Entonces** mi cuenta permanece sin conectar y se me informa de forma comprensible.

**US-5.2 — Reflejar tareas con fecha límite como eventos en Google Calendar**

> Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, para no tener que copiarlas a mano y ver todo en un solo sitio.

**Criterios de aceptación:**
- **Dado** que tengo Google conectado, **Cuando** creo una tarea con fecha límite, **Entonces** se crea un evento correspondiente en mi Google Calendar.
- **Dado** que tengo Google conectado, **Cuando** creo una tarea sin fecha límite, **Entonces** no se crea ningún evento en mi Google Calendar.
- **Dado** que cambio la fecha límite de una tarea, **Cuando** guardo el cambio, **Entonces** el evento correspondiente en Google Calendar se actualiza con la nueva fecha.
- **Dado** que la sincronización es principalmente FlowSync → Google Calendar, **Cuando** se genera el evento, **Entonces** este refleja al menos el título y la fecha límite de la tarea (asumido).

**US-5.3 — Actualizar Google Calendar al completar o borrar una tarea**

> Como usuario con Google conectado, quiero que al completar o borrar una tarea su evento en Google Calendar se ajuste o elimine, para que mi calendario no muestre pendientes que ya no aplican.

**Criterios de aceptación:**
- **Dado** que una tarea con fecha límite tiene un evento en Google Calendar, **Cuando** borro esa tarea, **Entonces** el evento correspondiente se elimina de Google Calendar.
- **Dado** que una tarea con fecha límite tiene un evento en Google Calendar, **Cuando** la marco como completada, **Entonces** el evento se elimina o se marca según corresponda.
- **Dado** que una tarea no tiene fecha límite ni evento asociado, **Cuando** la completo o la borro, **Entonces** no se realiza ninguna operación sobre Google Calendar.

**US-5.4 — Desconectar la cuenta de Google**

> Como usuario con Google conectado, quiero desconectar mi cuenta de Google en cualquier momento, para dejar de sincronizar conservando mis tareas ya creadas.

**Criterios de aceptación:**
- **Dado** que tengo Google conectado, **Cuando** desconecto mi cuenta de Google, **Entonces** FlowSync deja de sincronizar tareas con Google Calendar.
- **Dado** que desconecto Google, **Cuando** consulto mi listado, **Entonces** mis tareas ya creadas siguen existiendo en FlowSync sin borrarse.
- **Dado** que he desconectado Google, **Cuando** creo o modifico una tarea con fecha límite, **Entonces** no se crea ni actualiza ningún evento en Google Calendar.

**US-5.5 — Manejo razonable de errores de la API de Google**

> Como usuario con Google conectado, quiero que mis tareas se guarden aunque la sincronización con Google falle, para no perder información cuando la API de Google no está disponible.

**Criterios de aceptación:**
- **Dado** que la API de Google no está disponible o devuelve un error, **Cuando** creo o modifico una tarea con fecha límite, **Entonces** la tarea se guarda en FlowSync aunque la sincronización falle.
- **Dado** que una operación de sincronización ha fallado, **Cuando** el sistema lo detecta, **Entonces** la sincronización se reintenta más tarde.
- **Dado** que ocurre un fallo de sincronización, **Cuando** se procesa la operación, **Entonces** queda registrada en los logs para poder diagnosticar el problema.
