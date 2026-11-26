### Épica 1 — Autenticación y cuenta
*Objetivo: que cada persona tenga una cuenta propia y privada, y pueda entrar y salir de ella con seguridad.*

#### US-01 · Registro con email y contraseña
**Como** visitante, **quiero** crear una cuenta con mi email y una contraseña, **para** tener un espacio propio donde guardar mis tareas.
- **Fuente:** §3.1
- **Criterios de aceptación:**
  1. **Dado** que estoy en la pantalla de registro, **cuando** introduzco un email válido no registrado y una contraseña de 8 o más caracteres y envío el formulario, **entonces** se crea mi cuenta y quedo autenticado.
  2. **Dado** que estoy en la pantalla de registro, **cuando** introduzco una contraseña de 7 caracteres o menos, **entonces** no se crea la cuenta y se muestra un mensaje comprensible junto al campo indicando que la contraseña debe tener al menos 8 caracteres, sin mensajes técnicos (§4 Errores claros).
  3. **Dado** que estoy en la pantalla de registro, **cuando** introduzco un email con formato inválido (p. ej. «ana@») o dejo un campo obligatorio vacío, **entonces** no se crea la cuenta y se muestra un mensaje de validación junto al campo afectado.
  4. **Dado** que ya existe una cuenta con mi email, **cuando** intento registrarme con ese mismo email, **entonces** se indica que el email ya está registrado y se ofrece un enlace a «Iniciar sesión».

#### US-02 · Pantalla de bienvenida tras el registro
**Como** usuario recién registrado, **quiero** ver una bienvenida que me explique qué hace FlowSync, **para** saber cómo empezar.
- **Fuente:** §3.1
- **Criterios de aceptación:**
  1. **Dado** que acabo de completar el registro con éxito, **cuando** termina el proceso, **entonces** llego a una pantalla de bienvenida (no al listado).
  2. **Dado** que estoy en la pantalla de bienvenida, **cuando** la leo, **entonces** veo una única frase que explica qué hace FlowSync (el texto exacto es **(asumido)**) y una invitación a crear mi primera tarea.
  3. **Dado** que estoy en la pantalla de bienvenida, **cuando** pulso la invitación a crear mi primera tarea, **entonces** accedo al formulario de creación de tarea.
  4. **Dado** que ya inicié sesión en una cuenta existente, **cuando** entro en la aplicación, **entonces** no se me muestra la pantalla de bienvenida de registro **(asumido)**.

#### US-03 · Inicio de sesión
**Como** usuario registrado, **quiero** iniciar sesión con mi email y contraseña, **para** acceder a mis tareas.
- **Fuente:** §3.1
- **Criterios de aceptación:**
  1. **Dado** que tengo una cuenta, **cuando** introduzco mi email y contraseña correctos, **entonces** quedo autenticado mediante un token de acceso y veo el listado de mis tareas.
  2. **Dado** que estoy en la pantalla de inicio de sesión, **cuando** introduzco una contraseña incorrecta o un email no registrado, **entonces** no accedo y se muestra un mensaje genérico de credenciales incorrectas que no revela cuál de los dos datos falló **(asumido)**.
  3. **Dado** que dejo vacío el email o la contraseña, **cuando** envío el formulario, **entonces** se muestra un mensaje de campo obligatorio junto al campo vacío.
  4. **Dado** que inicio sesión, **cuando** consulto el listado, **entonces** solo veo tareas de mi cuenta y ninguna de otros usuarios (§4 Privacidad).

#### US-04 · Cerrar sesión
**Como** usuario autenticado, **quiero** cerrar mi sesión, **para** que nadie con acceso a mi dispositivo vea mis tareas.
- **Fuente:** §3.1
- **Criterios de aceptación:**
  1. **Dado** que estoy autenticado, **cuando** pulso «Cerrar sesión», **entonces** mi token de acceso deja de ser válido y se me redirige a la pantalla de inicio de sesión.
  2. **Dado** que he cerrado sesión, **cuando** intento abrir la URL del listado de tareas, **entonces** se me redirige al inicio de sesión sin mostrar ninguna tarea.
  3. **Dado** que he cerrado sesión, **cuando** vuelvo a iniciar sesión con mis credenciales, **entonces** veo mis tareas anteriores intactas.

---

### Épica 2 — Gestión de tareas
*Objetivo: que el usuario cree, consulte, modifique, cambie de estado y elimine sus tareas.*

#### US-05 · Crear una tarea
**Como** usuario registrado, **quiero** crear una tarea con título y, opcionalmente, descripción y fecha límite, **para** registrar lo que tengo pendiente.
- **Fuente:** §3.2
- **Criterios de aceptación:**
  1. **Dado** que estoy en el formulario de creación, **cuando** introduzco solo un título y guardo, **entonces** la tarea se crea con estado `pending`, sin descripción ni fecha límite, y aparece en mi listado.
  2. **Dado** que estoy en el formulario de creación, **cuando** introduzco título, descripción y fecha límite y guardo, **entonces** la tarea se crea con los tres valores y estado `pending`.
  3. **Dado** que estoy en el formulario de creación, **cuando** intento guardar con el título vacío, **entonces** no se crea la tarea y se muestra un mensaje de campo obligatorio junto al título (§4 Errores claros).
  4. **Dado** que he creado una tarea, **cuando** otro usuario consulta su listado, **entonces** mi tarea no aparece (§4 Privacidad).

#### US-06 · Ver el listado de tareas
**Como** usuario registrado, **quiero** ver el listado de mis tareas, **para** saber qué tengo pendiente de un vistazo.
- **Fuente:** §3.2, §4
- **Criterios de aceptación:**
  1. **Dado** que tengo tareas, **cuando** abro el listado, **entonces** cada tarea muestra al menos su título, su estado y su fecha límite si la tiene.
  2. **Dado** que tengo hasta 200 tareas, **cuando** abro el listado, **entonces** este termina de cargar en menos de 1 segundo (§4 Rendimiento).
  3. **Dado** que abro el listado en un navegador móvil, **cuando** se muestra, **entonces** todas las tareas son legibles y operables sin desplazamiento horizontal **(asumido)** (§4 Responsive).
  4. **Dado** que falla la carga del listado, **cuando** ocurre el error, **entonces** veo un mensaje comprensible, no un error técnico (§4 Errores claros).

#### US-07 · Editar una tarea
**Como** usuario registrado, **quiero** editar cualquier campo de una tarea existente, **para** mantenerla actualizada.
- **Fuente:** §3.2
- **Criterios de aceptación:**
  1. **Dado** que tengo una tarea, **cuando** cambio su título, descripción o fecha límite y guardo, **entonces** el listado muestra los nuevos valores.
  2. **Dado** que edito una tarea, **cuando** borro la fecha límite y guardo, **entonces** la tarea queda sin fecha límite.
  3. **Dado** que edito una tarea, **cuando** dejo el título vacío y guardo, **entonces** no se guardan los cambios y se muestra un mensaje de campo obligatorio.
  4. **Dado** que intento editar una tarea de otro usuario (p. ej. mediante su URL), **cuando** envío la petición, **entonces** se rechaza y no se modifica nada (§4 Privacidad).

#### US-08 · Cambiar el estado de una tarea
**Como** usuario registrado, **quiero** cambiar el estado de una tarea entre `pending`, `completed` y `archived`, **para** reflejar su progreso.
- **Fuente:** §3.2
- **Criterios de aceptación:**
  1. **Dado** que tengo una tarea `pending`, **cuando** la marco como completada, **entonces** su estado pasa a `completed`.
  2. **Dado** que tengo una tarea `completed`, **cuando** la archivo, **entonces** su estado pasa a `archived`.
  3. **Dado** que tengo una tarea `completed` o `archived`, **cuando** la devuelvo a pendiente, **entonces** su estado pasa a `pending` **(asumido: el PRD no define qué transiciones están permitidas)**.
  4. **Dado** que cambio el estado de una tarea, **cuando** recargo la página, **entonces** el nuevo estado persiste.

#### US-09 · Borrar una tarea
**Como** usuario registrado, **quiero** borrar una tarea, **para** eliminar lo que ya no necesito.
- **Fuente:** §3.2
- **Criterios de aceptación:**
  1. **Dado** que tengo una tarea, **cuando** pido borrarla y confirmo, **entonces** desaparece del listado y no reaparece al recargar.
  2. **Dado** que pido borrar una tarea, **cuando** cancelo en la confirmación, **entonces** la tarea permanece sin cambios **(asumido: el PRD no exige confirmación)**.
  3. **Dado** que intento borrar una tarea de otro usuario, **cuando** envío la petición, **entonces** se rechaza y la tarea no se elimina (§4 Privacidad).

---

### Épica 3 — Organización y filtrado
*Objetivo: que el usuario encuentre rápido lo relevante cuando acumula tareas.*

#### US-10 · Filtrar tareas por estado
**Como** usuario registrado, **quiero** filtrar mi listado por estado, **para** ver solo las tareas que me interesan.
- **Fuente:** §3.3
- **Criterios de aceptación:**
  1. **Dado** que tengo tareas en varios estados, **cuando** selecciono el filtro «Pendientes», **entonces** el listado muestra únicamente tareas `pending`.
  2. **Dado** que tengo tareas en varios estados, **cuando** selecciono «Completadas» o «Archivadas», **entonces** el listado muestra únicamente tareas `completed` o `archived` respectivamente.
  3. **Dado** que hay un filtro activo, **cuando** lo quito, **entonces** se muestran de nuevo las tareas de todos los estados **(asumido)**.
  4. **Dado** que selecciono un filtro sin tareas coincidentes, **cuando** se aplica, **entonces** se muestra un mensaje indicando que no hay tareas con ese estado **(asumido)**.

#### US-11 · Orden por defecto orientado a «hoy»
**Como** usuario registrado, **quiero** que mi listado priorice lo relevante para hoy, **para** ver primero lo que debo hacer ya.
- **Fuente:** §3.3
- **Criterios de aceptación:**
  1. **Dado** que tengo tareas pendientes con distintas fechas límite, **cuando** abro el listado sin filtros, **entonces** las tareas con fecha límite más cercana (incluidas las vencidas) aparecen antes que las de fecha más lejana **(asumido: el PRD deja el criterio al refinamiento)**.
  2. **Dado** que tengo tareas con y sin fecha límite, **cuando** abro el listado, **entonces** las tareas sin fecha límite aparecen después de las que sí la tienen **(asumido)**.
  3. **Dado** que cambio la fecha límite de una tarea, **cuando** vuelvo al listado, **entonces** la tarea ocupa la posición que le corresponde según el criterio anterior.

#### US-12 · Estado vacío del listado
**Como** usuario sin tareas, **quiero** ver una invitación clara a crear mi primera tarea, **para** saber qué hacer a continuación.
- **Fuente:** §3.3
- **Criterios de aceptación:**
  1. **Dado** que mi cuenta es nueva y no tiene tareas, **cuando** abro el listado, **entonces** veo un estado vacío con una invitación a crear la primera tarea y no una tabla vacía.
  2. **Dado** que todas mis tareas están archivadas y el listado no muestra tareas, **cuando** lo abro, **entonces** veo el mismo estado vacío con la invitación **(asumido: el PRD no aclara si el listado por defecto incluye archivadas)**.
  3. **Dado** que veo el estado vacío, **cuando** pulso la invitación, **entonces** accedo al formulario de creación de tarea.

---

### Épica 4 — Exportación
*Objetivo: que el usuario pueda llevarse sus datos.*

#### US-13 · Exportar tareas a CSV
**Como** usuario registrado, **quiero** exportar mis tareas a un archivo CSV, **para** llevarme mis datos.
- **Fuente:** §3.4
- **Criterios de aceptación:**
  1. **Dado** que tengo tareas, **cuando** pulso «Exportar CSV», **entonces** se descarga un archivo `.csv` con una fila por tarea y las columnas título, descripción, estado y fecha límite.
  2. **Dado** que una tarea tiene comas, comillas o saltos de línea en título o descripción, **cuando** exporto, **entonces** el CSV las representa correctamente escapadas y se abre sin desplazar columnas.
  3. **Dado** que una tarea no tiene descripción o fecha límite, **cuando** exporto, **entonces** esas celdas quedan vacías.
  4. **Dado** que no tengo tareas, **cuando** pulso «Exportar CSV», **entonces** se descarga un CSV con solo la fila de cabecera **(asumido)**.
  5. **Dado** que exporto, **cuando** reviso el archivo, **entonces** contiene solo mis tareas (§4 Privacidad).

---

### Épica 5 — Sincronización con Google Calendar
*Objetivo: que las tareas con fecha límite se reflejen como eventos en Google Calendar sin trabajo manual.*

#### US-14 · Conectar la cuenta de Google
**Como** usuario registrado, **quiero** conectar mi cuenta de Google mediante autorización OAuth, **para** que mis tareas puedan reflejarse en mi calendario.
- **Fuente:** §3.5, §4
- **Criterios de aceptación:**
  1. **Dado** que no tengo Google conectado, **cuando** pulso «Conectar Google Calendar» y acepto los permisos en Google, **entonces** vuelvo a FlowSync y se muestra mi cuenta como conectada.
  2. **Dado** que estoy en la pantalla de autorización de Google, **cuando** deniego los permisos o cancelo, **entonces** vuelvo a FlowSync con un mensaje comprensible y sigo sin conexión.
  3. **Dado** que la conexión se completa, **cuando** se guardan las credenciales de Google, **entonces** no son accesibles por otros usuarios ni visibles en la interfaz (§4 Privacidad).
  4. **Dado** que ya tenía tareas con fecha límite antes de conectar, **cuando** la conexión se completa, **entonces** esas tareas pendientes con fecha límite se sincronizan como eventos **(asumido: el PRD no lo define)**.

#### US-15 · Crear eventos desde tareas con fecha límite
**Como** usuario con Google conectado, **quiero** que mis tareas con fecha límite aparezcan como eventos en Google Calendar, **para** no copiarlas a mano.
- **Fuente:** §3.5, §4, §7
- **Criterios de aceptación:**
  1. **Dado** que tengo Google conectado, **cuando** creo una tarea con fecha límite, **entonces** aparece un evento en mi Google Calendar en esa fecha con el título de la tarea **(asumido: el mapeo exacto de título y hora no está definido)**.
  2. **Dado** que tengo Google conectado, **cuando** creo una tarea sin fecha límite, **entonces** no se crea ningún evento.
  3. **Dado** que la API de Google no está disponible, **cuando** creo una tarea con fecha límite, **entonces** la tarea se guarda en FlowSync, la sincronización queda pendiente de reintento y se me confirma el guardado.
  4. **Dado** que ocurre una operación de sincronización, **cuando** termina con éxito o error, **entonces** queda un registro (log) con la operación, la tarea afectada y el resultado (§4 Observabilidad).

#### US-16 · Actualizar el evento al cambiar la fecha de la tarea
**Como** usuario con Google conectado, **quiero** que el evento se actualice cuando cambio la fecha de la tarea, **para** que mi calendario no quede desfasado.
- **Fuente:** §3.5, §4
- **Criterios de aceptación:**
  1. **Dado** que una tarea tiene un evento, **cuando** cambio su fecha límite en FlowSync, **entonces** el evento se mueve a la nueva fecha en Google Calendar sin crear un duplicado.
  2. **Dado** que una tarea sin fecha límite se edita para añadirle una, **cuando** guardo, **entonces** se crea el evento correspondiente.
  3. **Dado** que una tarea con evento se edita para quitarle la fecha límite, **cuando** guardo, **entonces** el evento se elimina del calendario **(asumido)**.
  4. **Dado** que la API de Google devuelve un error durante la actualización, **cuando** guardo el cambio, **entonces** la tarea se guarda con la nueva fecha, el fallo se registra en logs y se reintenta más tarde.

#### US-17 · Retirar el evento al completar o borrar la tarea
**Como** usuario con Google conectado, **quiero** que el evento se retire cuando completo o borro la tarea, **para** que mi calendario solo muestre lo pendiente.
- **Fuente:** §3.5, §4
- **Criterios de aceptación:**
  1. **Dado** que una tarea tiene un evento, **cuando** la borro en FlowSync, **entonces** el evento desaparece de Google Calendar.
  2. **Dado** que una tarea tiene un evento, **cuando** la marco como `completed`, **entonces** el evento se elimina del calendario **(asumido: el PRD dice «se elimina o se marca según corresponda»)**.
  3. **Dado** que la API de Google no está disponible al borrar una tarea, **cuando** confirmo el borrado, **entonces** la tarea se borra en FlowSync, el fallo queda registrado en logs y la eliminación del evento se reintenta más tarde.

#### US-18 · Desconectar la cuenta de Google
**Como** usuario con Google conectado, **quiero** desconectar mi cuenta de Google en cualquier momento, **para** dejar de sincronizar sin perder mis tareas.
- **Fuente:** §3.5, §4
- **Criterios de aceptación:**
  1. **Dado** que tengo Google conectado, **cuando** pulso «Desconectar» y confirmo, **entonces** la cuenta aparece como no conectada.
  2. **Dado** que he desconectado, **cuando** reviso mi listado, **entonces** todas mis tareas siguen presentes con sus datos intactos.
  3. **Dado** que he desconectado, **cuando** creo o edito una tarea con fecha límite, **entonces** no se crea ni modifica ningún evento en Google Calendar.
  4. **Dado** que he desconectado, **cuando** se completa la desconexión, **entonces** FlowSync elimina mis tokens de Google almacenados **(asumido)**.

---

## Supuestos y preguntas abiertas

**Preguntas abiertas / ambigüedades del PRD**
1. **Sincronización inversa** (editar un evento en Google y reflejarlo en la tarea): no se genera ninguna story. El PRD exige un spike técnico previo; queda pendiente decidir si entra en el MVP. Nótese la contradicción en §1 («los cambios fluyen en ambas direcciones», «lectura y escritura») frente a la Nota de §3.5 («primariamente FlowSync → Google»).
2. **Criterio de ordenación** del listado (US-11): el PRD lo deja al refinamiento. Se asumió fecha límite ascendente, con las tareas sin fecha al final.
3. **Completar una tarea** (US-17): el PRD dice «se elimina o se marca según corresponda». Se asumió eliminación; falta decidir si debe marcarse de otra forma.
4. **Zona horaria y hora del evento** (US-15, §7): el PRD no define cómo se traduce la fecha límite a un evento (todo el día o a una hora concreta, zona horaria usada).
5. **Transiciones de estado** (US-08): no se define si todas son válidas, ni si se puede volver de `archived` o `completed` a `pending`.
6. **Visibilidad de tareas archivadas**: no está claro si el listado por defecto las incluye (afecta a US-10 y US-12).
7. **Reintentos de sincronización** (US-15, US-16, US-17): no se define la política (número, intervalo ni qué ocurre tras agotarlos). §6 menciona «fallos no recuperables» sin definirlos.
8. **Tareas existentes al conectar Google** (US-14): no se indica si se sincronizan retroactivamente.
9. **Tareas completadas o archivadas con fecha límite**: no se define si deben tener evento al conectar Google o al volver a `pending`.
10. **Eventos recurrentes y de todo el día** (§7): mencionados como casos límite sin decidir si entran en el MVP.
11. **Token de acceso**: no se define caducidad ni renovación; solo se afirma que no hay «recordar contraseña» ni verificación de email.
12. **Desconexión**: no se aclara si los eventos ya creados permanecen en Google Calendar o se eliminan.

**Elementos marcados como (asumido)**
- Texto exacto de la frase de bienvenida (US-02) y que no se muestra a usuarios que inician sesión en una cuenta existente.
- Mensaje genérico de credenciales incorrectas sin revelar qué dato falló (US-03).
- Legibilidad móvil sin desplazamiento horizontal (US-06).
- Transición de vuelta a `pending` desde `completed` o `archived` (US-08).
- Confirmación previa al borrado de una tarea y opción de cancelar (US-09).
- Quitar el filtro devuelve todas las tareas y mensaje cuando no hay coincidencias (US-10).
- Ordenación por fecha límite ascendente con tareas sin fecha al final (US-11).
- Estado vacío cuando todas las tareas están archivadas (US-12).
- CSV con solo cabecera si no hay tareas (US-13).
- Sincronización retroactiva al conectar Google (US-14).
- Mapeo título/fecha → evento (US-15).
- Eliminación del evento al quitar la fecha límite (US-16) o al completar la tarea (US-17).
- Eliminación de tokens de Google al desconectar (US-18).