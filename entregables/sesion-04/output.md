# FlowSync — User stories del MVP

**Fuente única:** [PRD — FlowSync MVP, versión 1.0](../docs/PRD.md).

Las historias se agrupan por módulos y usan los estados literales del PRD: `pending`, `completed` y `archived`. Cada historia incluye entre 3 y 5 criterios de aceptación (AC) en formato Given/When/Then.

**Convención de inferencias:** los beneficios redactados después de «para» son una síntesis editorial **(asumido)** del propósito de los requisitos, no funcionalidades adicionales. La agrupación y los identificadores también son **(asumido)**. Los puntos que el PRD deja abiertos se identifican al final y no se resuelven mediante supuestos funcionales.

## 1. Cuenta y acceso

### US-01 — Crear una cuenta

Como visitante, quiero crear una cuenta con email y contraseña, para disponer de una cuenta propia en FlowSync **(asumido)**.

**Fuente:** §3.1; §4, errores claros.

- **AC-01 — Registro:** **Given** un visitante con un email válido no registrado y una contraseña de al menos 8 caracteres, **When** solicita crear su cuenta, **Then** el registro se completa correctamente.
- **AC-02 — Contraseña corta:** **Given** un visitante que indica una contraseña de menos de 8 caracteres, **When** solicita registrarse, **Then** se muestra un error de validación comprensible que indica el mínimo de 8 caracteres.
- **AC-03 — Email inválido:** **Given** un visitante que introduce un email inválido, **When** solicita registrarse, **Then** se muestra un error de validación comprensible, sin presentar un error técnico.
- **AC-04 — Email registrado:** **Given** un visitante cuyo email ya está registrado, **When** intenta crear una cuenta con ese email, **Then** el sistema le indica que ya está registrado y le ofrece ir al inicio de sesión.

### US-02 — Iniciar y cerrar sesión

Como usuario registrado, quiero iniciar y cerrar sesión, para gestionar mi acceso a FlowSync **(asumido)**.

**Fuente:** §3.1.

- **AC-01 — Inicio de sesión:** **Given** un usuario registrado, **When** inicia sesión con el email y la contraseña de su cuenta, **Then** accede como usuario autenticado.
- **AC-02 — Mantenimiento de sesión:** **Given** un usuario que ha iniciado sesión, **When** utiliza FlowSync durante esa sesión, **Then** la sesión se mantiene mediante tokens de acceso.
- **AC-03 — Cierre de sesión:** **Given** un usuario autenticado, **When** solicita cerrar sesión, **Then** su sesión se cierra.

### US-03 — Recibir la bienvenida

Como usuario que acaba de registrarse, quiero ver una bienvenida que explique FlowSync y me invite a crear mi primera tarea, para conocer cómo empezar **(asumido)**.

**Fuente:** §3.1.

- **AC-01 — Destino tras el registro:** **Given** un visitante que completa correctamente su registro, **When** finaliza el registro, **Then** llega a la pantalla de bienvenida.
- **AC-02 — Explicación breve:** **Given** un usuario en la pantalla de bienvenida, **When** consulta su contenido, **Then** encuentra una frase que explica qué hace FlowSync.
- **AC-03 — Primera tarea:** **Given** un usuario en la pantalla de bienvenida, **When** consulta su contenido, **Then** encuentra una invitación a crear su primera tarea.

## 2. Gestión personal de tareas

### US-04 — Crear una tarea

Como usuario, quiero crear una tarea con título y con descripción y fecha límite opcionales, para registrar mis pendientes **(asumido)**.

**Fuente:** §3.2; §4, errores claros.

- **AC-01 — Datos mínimos:** **Given** un usuario que indica únicamente un título, **When** crea la tarea, **Then** la tarea se crea sin exigir descripción ni fecha límite.
- **AC-02 — Descripción opcional:** **Given** un usuario que indica un título y una descripción, **When** crea la tarea, **Then** la tarea conserva ambos valores.
- **AC-03 — Fecha límite opcional:** **Given** un usuario que indica un título y una fecha límite, **When** crea la tarea, **Then** la tarea conserva la fecha límite indicada.
- **AC-04 — Estado inicial:** **Given** un usuario que crea una tarea correctamente, **When** finaliza la creación, **Then** la tarea tiene el estado `pending`.
- **AC-05 — Título obligatorio:** **Given** un usuario que no indica el título obligatorio, **When** intenta crear una tarea, **Then** se muestra un error de validación comprensible sobre el campo obligatorio, sin presentar un error técnico.

### US-05 — Consultar mis tareas

Como usuario, quiero ver el listado de mis tareas, para consultar mis pendientes en FlowSync **(asumido)**.

**Fuente:** §3.1; §3.2; §4, privacidad y rendimiento.

- **AC-01 — Listado personal:** **Given** un usuario que tiene tareas registradas, **When** abre el listado, **Then** puede consultar sus tareas.
- **AC-02 — Aislamiento:** **Given** dos usuarios con tareas propias, **When** uno de ellos consulta las tareas desde FlowSync, **Then** las tareas del otro usuario no son visibles ni accesibles para él.
- **AC-03 — Tiempo de carga:** **Given** un usuario con hasta 200 tareas, **When** abre el listado de tareas, **Then** el listado carga en menos de 1 segundo.

### US-06 — Editar y borrar una tarea

Como usuario, quiero editar los campos o borrar una tarea existente, para mantener actualizados mis pendientes **(asumido)**.

**Fuente:** §3.2. Para las consecuencias en Google Calendar, véase US-11.

- **AC-01 — Título:** **Given** una tarea existente del usuario, **When** cambia su título y guarda la edición, **Then** la tarea conserva el título actualizado.
- **AC-02 — Descripción:** **Given** una tarea existente del usuario, **When** edita su descripción y guarda la edición, **Then** la tarea conserva la descripción actualizada.
- **AC-03 — Fecha límite:** **Given** una tarea existente del usuario, **When** cambia su fecha límite y guarda la edición, **Then** la tarea conserva la fecha límite actualizada.
- **AC-04 — Estado:** **Given** una tarea existente del usuario, **When** cambia su estado a uno de los estados definidos (`pending`, `completed` o `archived`), **Then** la tarea conserva el estado seleccionado.
- **AC-05 — Borrado:** **Given** una tarea existente del usuario, **When** la borra, **Then** la tarea deja de existir en su listado.

## 3. Organización y filtrado

### US-07 — Filtrar por estado

Como usuario, quiero filtrar mis tareas por estado, para consultar las que se encuentran en la situación que me interesa **(asumido)**.

**Fuente:** §3.2 y §3.3.

- **AC-01 — Pendientes:** **Given** un usuario con tareas en los tres estados, **When** filtra por `pending`, **Then** el listado muestra únicamente sus tareas pendientes.
- **AC-02 — Completadas:** **Given** un usuario con tareas en los tres estados, **When** filtra por `completed`, **Then** el listado muestra únicamente sus tareas completadas.
- **AC-03 — Archivadas:** **Given** un usuario con tareas en los tres estados, **When** filtra por `archived`, **Then** el listado muestra únicamente sus tareas archivadas.

### US-08 — Ver tareas relevantes para hoy y el estado vacío

Como usuario, quiero que el listado priorice lo relevante para hoy y me invite a crear una tarea cuando esté vacío, para orientar mi siguiente acción **(asumido)**.

**Fuente:** §3.3.

- **AC-01 — Orden predeterminado:** **Given** un usuario con tareas y un criterio de relevancia para hoy acordado durante el refinamiento, **When** abre el listado con su orden predeterminado, **Then** las tareas más relevantes para hoy según ese criterio aparecen primero.
- **AC-02 — Cuenta sin tareas:** **Given** un usuario que no tiene ninguna tarea, **When** abre el listado, **Then** ve un estado vacío con una invitación a crear la primera tarea.
- **AC-03 — Todas archivadas:** **Given** un usuario cuyas tareas están todas archivadas, **When** abre el listado predeterminado, **Then** ve un estado vacío con una invitación a crear la primera tarea.

**Pendiente de refinamiento:** el AC-01 requiere concretar el criterio de ordenación antes de poder ejecutarse como prueba de aceptación. El contexto «listado predeterminado» del AC-03 es **(asumido)** para distinguirlo del filtro explícito de archivadas; el PRD no resuelve esa interacción.

## 4. Portabilidad de datos

### US-09 — Exportar tareas a CSV

Como usuario, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos **(asumido)**.

**Fuente:** §3.4; §4, privacidad.

- **AC-01 — Archivo CSV:** **Given** un usuario con tareas, **When** solicita exportarlas, **Then** obtiene un archivo en formato CSV.
- **AC-02 — Contenido mínimo:** **Given** tareas con título, descripción, estado y fecha límite, **When** el usuario las exporta, **Then** el CSV incluye, como mínimo, esos cuatro campos para cada tarea exportada.
- **AC-03 — Datos personales:** **Given** dos usuarios con tareas propias, **When** uno de ellos exporta sus tareas, **Then** el archivo no contiene tareas del otro usuario.

## 5. Conexión y sincronización con Google Calendar

### US-10 — Conectar mi cuenta de Google

Como usuario, quiero conectar mi cuenta de Google a FlowSync, para habilitar la sincronización con mi Google Calendar **(asumido)**.

**Fuente:** §3.5; §4, privacidad; §8, conexión de Google.

- **AC-01 — Autorización:** **Given** un usuario que quiere conectar su cuenta de Google, **When** realiza la conexión, **Then** la autorización se realiza mediante OAuth con Google.
- **AC-02 — Lectura y escritura:** **Given** un usuario que completa la autorización de Google, **When** su cuenta queda conectada, **Then** la conexión permite a FlowSync leer y escribir en el calendario del usuario.
- **AC-03 — Almacenamiento seguro:** **Given** una conexión autorizada que proporciona tokens de Google, **When** FlowSync almacena esos tokens, **Then** los almacena de forma segura.
- **AC-04 — Aislamiento de tokens:** **Given** dos usuarios de FlowSync, uno de ellos con Google conectado, **When** el otro usuario utiliza FlowSync, **Then** no puede acceder a los tokens de Google del primero.

**Límite de definición:** el PRD exige almacenamiento seguro, pero no concreta el mecanismo ni la evidencia de seguridad; deberán definirse en las especificaciones técnicas para verificar AC-03.

### US-11 — Reflejar mis tareas en Google Calendar

Como usuario con Google conectado, quiero que mis tareas con fecha límite y sus cambios se reflejen en mi calendario, para evitar mantener tareas y eventos manualmente **(asumido)**.

**Fuente:** §3.5; §4, observabilidad.

- **AC-01 — Evento de tarea:** **Given** un usuario con Google conectado y una tarea con fecha límite, **When** FlowSync sincroniza la tarea, **Then** la tarea aparece como un evento en el Google Calendar del usuario.
- **AC-02 — Cambio de fecha:** **Given** una tarea que tiene un evento correspondiente en Google Calendar, **When** el usuario cambia la fecha de la tarea en FlowSync y se ejecuta la sincronización, **Then** se actualiza el evento correspondiente en Google Calendar.
- **AC-03 — Tarea completada:** **Given** una tarea que tiene un evento correspondiente en Google Calendar, **When** el usuario completa la tarea en FlowSync y se ejecuta la sincronización, **Then** el evento se elimina o se marca según la regla que se defina en el refinamiento.
- **AC-04 — Tarea borrada:** **Given** una tarea que tiene un evento correspondiente en Google Calendar, **When** el usuario borra la tarea en FlowSync y se ejecuta la sincronización, **Then** el evento se elimina o se marca según la regla que se defina en el refinamiento.
- **AC-05 — Registro de operaciones:** **Given** una operación de sincronización con Google Calendar, **When** FlowSync la ejecuta, **Then** la operación queda registrada en logs para poder diagnosticar fallos.

**Pendiente de refinamiento:** AC-01 y AC-02 requieren definir la relación entre fecha límite, hora del evento y zona horaria. AC-03 y AC-04 conservan la alternativa literal del PRD; no son criterios cerrados hasta decidir qué significa «se elimina o se marca según corresponda» en cada caso.

### US-12 — Conservar tareas y reintentar cuando falle Google

Como usuario con Google conectado, quiero que mi tarea se guarde aunque falle la sincronización y que esta se reintente más tarde, para conservar mis datos ante fallos de Google **(asumido)**.

**Fuente:** §3.5.

- **AC-01 — API no disponible:** **Given** un usuario que guarda una tarea que requiere sincronización y la API de Google no está disponible, **When** falla la sincronización por esa indisponibilidad, **Then** la tarea se guarda en FlowSync.
- **AC-02 — Error de la API:** **Given** un usuario que guarda una tarea que requiere sincronización y la API de Google devuelve un error, **When** falla la sincronización por ese error, **Then** la tarea se guarda en FlowSync.
- **AC-03 — Reintento posterior:** **Given** una sincronización fallida por indisponibilidad o error de la API de Google, **When** FlowSync vuelve a procesar la sincronización pendiente más tarde, **Then** se realiza un nuevo intento de sincronización.

**Límite de definición:** el PRD no establece intervalos, número máximo de reintentos ni plazo de recuperación; estos AC no fijan ninguno.

### US-13 — Desconectar mi cuenta de Google

Como usuario con Google conectado, quiero desconectar mi cuenta de Google en cualquier momento, para dejar de sincronizar y conservar mis tareas en FlowSync **(asumido)**.

**Fuente:** §3.5.

- **AC-01 — Desconexión:** **Given** un usuario cuya cuenta de Google está conectada, **When** solicita desconectarla, **Then** puede completar la desconexión en ese momento.
- **AC-02 — Cese de sincronización:** **Given** un usuario que ha desconectado su cuenta de Google, **When** modifica una tarea en FlowSync, **Then** FlowSync no sincroniza ese cambio con Google Calendar.
- **AC-03 — Conservación de tareas:** **Given** un usuario con tareas creadas y Google conectado, **When** desconecta su cuenta de Google, **Then** sus tareas ya creadas permanecen en FlowSync.

## 6. Condiciones transversales del MVP

Estas condiciones provienen del §4 y complementan los AC anteriores; no añaden funcionalidades:

- **Web responsive:** **Given** un usuario en un navegador de escritorio o móvil, **When** utiliza las funcionalidades del MVP, **Then** la interfaz funciona y es usable en ese navegador. Aplica a US-01 a US-13. El PRD no define navegadores, resoluciones ni métricas de usabilidad.
- **Errores claros:** **Given** una entrada con email inválido, contraseña corta o campos obligatorios ausentes, **When** FlowSync valida la entrada, **Then** muestra el error de forma comprensible y no como un error técnico. Aplica a las historias con esas entradas.
- **Privacidad:** **Given** usuarios distintos con datos propios, **When** uno utiliza FlowSync, **Then** los datos del otro nunca resultan visibles ni accesibles. Aplica a todas las historias que acceden a datos personales; los tokens de Google se almacenan de forma segura.

## 7. Decisiones abiertas y límites de alcance

| Punto | Lo que establece el PRD | Tratamiento en este backlog |
| --- | --- | --- |
| Sincronización inversa | §1 habla de cambios en ambas direcciones; la nota específica de §3.5 condiciona la sincronización inversa completa a un spike técnico. | Se toma la nota específica como delimitación del compromiso **(asumido)**. No se incluye una historia comprometida para editar eventos en Google y actualizar tareas hasta validar su alcance. |
| Ordenación | §3.3 deja el criterio exacto a decisión del equipo durante el refinamiento. | US-08 no inventa prioridades ni reglas de desempate. |
| Archivadas y estado vacío | §3.3 permite filtrar por estado y define estado vacío cuando todas las tareas están archivadas. | Confirmar cómo interactúa el estado vacío con el filtro de archivadas. La distinción propuesta en US-08 está marcada **(asumido)**. |
| Completar o borrar tareas sincronizadas | §3.5 indica que el evento «se elimina o se marca según corresponda». | US-11 mantiene abierta la elección; no presupone eliminación en ambos casos ni un tipo de marca. |
| Fecha, hora y zona horaria | §7 exige definir con cuidado la relación entre fecha límite y hora del evento. | No se inventan duración, hora, zona horaria ni uso de eventos de todo el día. |
| Otros cambios en eventos | El PRD exige actualizar la fecha y tratar la finalización y el borrado. | No se añaden reglas sobre edición de título o descripción del evento, archivado, retirada de fecha ni eliminación de eventos al desconectar. |
| Exportación | §3.4 exige CSV con cuatro campos mínimos. | No se fija exportación según filtros, codificación, delimitador ni representación de valores opcionales ausentes. |
| Fallos y seguridad | §3.5 y §4 exigen reintentos, logs y almacenamiento seguro de tokens. | No se prescribe arquitectura, política de reintentos, contenido de logs ni mecanismo de almacenamiento. |

**Fuera del MVP según §1 y §3.1:** equipos y tareas compartidas; calendarios distintos de Google; notificaciones push o por email; app móvil nativa; etiquetas, proyectos y subtareas; recordatorios configurables adicionales a los de Google Calendar. Tampoco se requieren recuperación de contraseña ni verificación de email por enlace.

**Validación del producto, no historias adicionales (§6):** tras 4 semanas con usuarios reales, el flujo registro → creación de tareas → conexión de Google → visualización de eventos debe completarse sin ayuda externa; al menos el 40 % de los registrados debe conectar Google Calendar; menos del 5 % de las operaciones de sincronización debe fallar de forma no recuperable. Estas metas no implican añadir funcionalidades de analítica que el PRD no especifica.
