# User Stories — FlowSync MVP

> **Versión**: 1.0 · Basado en PRD v1.0 Q3 2026
> **Rol BA/PO**: historias generadas estrictamente desde el PRD adjunto.

---

## ⚠️ Aviso previo: discrepancia detectada en el alcance

El prompt solicita historias para **"Exportación a CSV y Markdown"**, pero el PRD (sección 3.4) únicamente menciona **CSV**. La exportación a Markdown **no está en el PRD** y generarla violaría la restricción explícita de no inventar funcionalidades no descritas.

**Decisión**: se genera únicamente la historia de exportación a CSV. Si el equipo de producto quiere añadir Markdown, debe actualizarse el PRD antes de descomponerse en backlog.

---

## Módulo 1: Autenticación y Gestión de Cuenta

### US01: Registro de cuenta con email y contraseña

**Historia:** Como visitante de FlowSync, quiero crear una cuenta con mi email y contraseña, para acceder a la aplicación y comenzar a gestionar mis tareas.

**Criterios de Aceptación:**

- **CA1:** Dado que soy un visitante en la página de registro / Cuando introduzco un email con formato válido y una contraseña de al menos 8 caracteres y confirmo el envío / Entonces el sistema crea mi cuenta correctamente y me redirige a la pantalla de bienvenida.

- **CA2:** Dado que soy un visitante en la página de registro / Cuando introduzco una contraseña con menos de 8 caracteres / Entonces el sistema muestra un mensaje de error comprensible indicando que la contraseña debe tener mínimo 8 caracteres, e impide el envío del formulario.

- **CA3:** Dado que soy un visitante en la página de registro / Cuando introduzco un email que ya está registrado en el sistema / Entonces el sistema lo indica con un mensaje claro y ofrece una opción para ir al inicio de sesión.

- **CA4:** Dado que soy un visitante en la página de registro / Cuando introduzco un email con formato inválido (p.ej. sin "@" o sin dominio) / Entonces el sistema muestra un error de validación comprensible e impide el envío. [Asumido: la validación de formato de email se realiza antes de enviar al servidor, bien en cliente o en el primer response del servidor.]

- **CA5:** Dado que el registro se completa con éxito / Cuando el sistema crea la cuenta / Entonces no se envía ningún email de verificación ni se requiere ningún paso adicional para activar la cuenta.

---

### US02: Inicio de sesión

**Historia:** Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder de forma autenticada a mis tareas.

**Criterios de Aceptación:**

- **CA1:** Dado que soy un usuario registrado en la página de inicio de sesión / Cuando introduzco mi email y contraseña correctos / Entonces el sistema me autentica, genera un token de acceso y me redirige al listado de mis tareas.

- **CA2:** Dado que soy un usuario en la página de inicio de sesión / Cuando introduzco una contraseña incorrecta o un email no registrado / Entonces el sistema muestra un mensaje de error comprensible sin revelar cuál de los dos datos es incorrecto. [Asumido: por seguridad estándar, el mensaje de error es genérico para no confirmar si el email existe.]

- **CA3:** Dado que he iniciado sesión correctamente / Cuando cierro la pestaña del navegador y la vuelvo a abrir / Entonces mi sesión se mantiene activa mediante el token de acceso y no se me pide que inicie sesión de nuevo. [Asumido: el token de acceso se persiste en el cliente (p.ej. localStorage o cookie) con una duración razonable; el PRD no especifica TTL.]

- **CA4:** Dado que soy un usuario autenticado que intenta acceder a una ruta protegida / Cuando mi token de acceso ha expirado o es inválido / Entonces el sistema me redirige al inicio de sesión. [Asumido: las rutas de la aplicación requieren token válido para ser accedidas.]

---

### US03: Cierre de sesión

**Historia:** Como usuario autenticado, quiero cerrar sesión, para proteger mi cuenta cuando dejo de usar la aplicación.

**Criterios de Aceptación:**

- **CA1:** Dado que estoy autenticado en FlowSync / Cuando ejecuto la acción de cerrar sesión / Entonces el sistema invalida mi token de acceso en el servidor y me redirige a la página de inicio de sesión.

- **CA2:** Dado que he cerrado sesión / Cuando intento acceder directamente a una URL protegida de la aplicación (p.ej. el listado de tareas) / Entonces el sistema me redirige al inicio de sesión sin mostrarme ningún dato.

- **CA3:** Dado que he cerrado sesión / Cuando pulso el botón "atrás" del navegador / Entonces no puedo acceder a páginas protegidas sin volver a autenticarme. [Asumido: el cliente limpia el token almacenado al cerrar sesión para evitar acceso por caché del navegador.]

---

### US04: Pantalla de bienvenida post-registro (onboarding mínimo)

**Historia:** Como usuario recién registrado, quiero ver una pantalla de bienvenida que explique en una frase qué hace FlowSync, para entender el valor del producto y saber cómo empezar sin necesitar ayuda externa.

**Criterios de Aceptación:**

- **CA1:** Dado que acabo de registrarme exitosamente / Cuando el sistema completa el registro / Entonces soy redirigido automáticamente a una pantalla de bienvenida que contiene: (a) una frase explicativa de qué hace FlowSync y (b) una llamada a la acción clara para crear mi primera tarea.

- **CA2:** Dado que estoy en la pantalla de bienvenida / Cuando pulso la acción de crear mi primera tarea / Entonces el sistema me lleva al flujo de creación de tareas. [Asumido: el CTA de bienvenida redirige directamente al formulario de nueva tarea.]

- **CA3:** Dado que soy un usuario ya existente que inicia sesión / Cuando me autentico / Entonces no se me muestra la pantalla de bienvenida; accedo directamente al listado de tareas. [Asumido: la pantalla de onboarding solo se muestra en el primer acceso post-registro.]

---

## Módulo 2: CRUD de Tareas con Estados

### US05: Crear una tarea

**Historia:** Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar un pendiente en FlowSync.

**Criterios de Aceptación:**

- **CA1:** Dado que estoy autenticado / Cuando creo una tarea proporcionando únicamente el título / Entonces la tarea se guarda con estado `pending`, sin descripción ni fecha límite, y aparece visible en mi listado.

- **CA2:** Dado que estoy autenticado / Cuando creo una tarea con título, descripción y fecha límite / Entonces los tres campos se persisten correctamente y la tarea aparece en mi listado con todos sus datos.

- **CA3:** Dado que estoy en el formulario de creación de tarea / Cuando intento guardar la tarea con el campo título vacío / Entonces el sistema muestra un error de validación comprensible e impide el guardado.

- **CA4:** Dado que se crea una tarea correctamente / Cuando reviso su estado en el listado / Entonces su estado inicial es siempre `pending`, independientemente de si tiene o no fecha límite.

---

### US06: Ver el listado de tareas

**Historia:** Como usuario autenticado, quiero ver el listado de todas mis tareas, para tener una visión general de mis pendientes y su estado.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo tareas creadas / Cuando accedo al listado / Entonces veo cada tarea con, al menos, su título, su estado y su fecha límite (si la tiene asignada).

- **CA2:** Dado que no tengo ninguna tarea (cuenta nueva o todas archivadas) / Cuando accedo al listado / Entonces veo un estado vacío con una invitación clara a crear la primera tarea.

- **CA3:** Dado que tengo tareas creadas / Cuando accedo al listado / Entonces las tareas están ordenadas de forma que las más relevantes para el día actual aparecen primero. [Asumido: el criterio exacto de ordenación se define durante el refinamiento de sprint, según indica el propio PRD; este CA valida que existe un orden por defecto con criterio de relevancia diaria, no por orden de creación arbitrario.]

- **CA4:** Dado que tengo hasta 200 tareas / Cuando accedo al listado / Entonces la carga completa del listado se produce en menos de 1 segundo.

- **CA5:** Dado que soy usuario de otro usuario / Cuando accedo a la aplicación / Entonces nunca veo las tareas de otro usuario; solo las propias.

---

### US07: Editar una tarea existente

**Historia:** Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para mantener mis datos actualizados cuando cambian las circunstancias.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo una tarea creada / Cuando edito su título y guardo / Entonces el nuevo título queda persistido y se refleja inmediatamente en el listado.

- **CA2:** Dado que tengo una tarea con fecha límite / Cuando modifico su fecha límite y guardo / Entonces la nueva fecha queda persistida correctamente.

- **CA3:** Dado que tengo una tarea sin fecha límite / Cuando le añado una fecha límite y guardo / Entonces el campo queda persistido, y si el usuario tiene Google Calendar conectado, se genera el evento correspondiente. [Asumido: añadir una fecha límite a una tarea previamente sin ella dispara la creación del evento en Google Calendar, coherente con la sección 3.5 del PRD.]

- **CA4:** Dado que estoy editando una tarea / Cuando intento guardar los cambios con el campo título vacío / Entonces el sistema muestra un error de validación e impide el guardado.

- **CA5:** Dado que edito una tarea / Cuando guardo los cambios / Entonces solo se modifican los datos de esa tarea; el resto del listado permanece inalterado.

---

### US08: Borrar una tarea

**Historia:** Como usuario autenticado, quiero borrar una tarea, para eliminar permanentemente pendientes que ya no son relevantes.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo una tarea en mi listado / Cuando la borro y confirmo la acción / Entonces la tarea se elimina permanentemente y desaparece del listado.

- **CA2:** Dado que la tarea borrada tenía un evento asociado en Google Calendar y el usuario tiene Google Calendar conectado / Cuando se completa el borrado / Entonces el evento correspondiente se elimina del Google Calendar del usuario.

- **CA3:** Dado que borro una tarea / Cuando la API de Google no está disponible en ese momento / Entonces la tarea se elimina igualmente en FlowSync y el sistema reintenta la eliminación del evento de Google Calendar más tarde.

- **CA4:** Dado que ejecuto la acción de borrar una tarea / Cuando pulso el botón de borrar / Entonces el sistema solicita confirmación explícita antes de proceder con la eliminación permanente. [Asumido: por UX estándar para operaciones destructivas, se incluye un paso de confirmación; el PRD no lo especifica pero es un comportamiento crítico para evitar pérdidas accidentales de datos.]

---

### US09: Cambiar el estado de una tarea

**Historia:** Como usuario autenticado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar con precisión el progreso real de mis pendientes.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo una tarea en estado `pending` / Cuando la marco como `completed` / Entonces su estado cambia a `completed` y el cambio se refleja visualmente en el listado.

- **CA2:** Dado que tengo una tarea en estado `completed` / Cuando la marco como `archived` / Entonces su estado cambia a `archived`. [Asumido: la transición entre los tres estados (`pending`, `completed`, `archived`) es libre en cualquier dirección; el PRD define los tres estados pero no restricciones de transición explícitas.]

- **CA3:** Dado que tengo una tarea con evento en Google Calendar y el usuario tiene Google Calendar conectado / Cuando su estado cambia a `completed` o `archived` / Entonces el evento correspondiente en Google Calendar se elimina o se actualiza según la lógica definida por el equipo. [Asumido: la semántica exacta del cambio en el evento (borrar vs. marcar) se define durante el refinamiento; el PRD indica "se elimina o se marca según corresponda".]

- **CA4:** Dado que cualquier tarea es recién creada / Cuando aparece en el listado / Entonces su estado inicial es siempre `pending`, sin excepción.

---

## Módulo 3: Filtrado y Organización de Tareas

### US10: Filtrar tareas por estado

**Historia:** Como usuario autenticado, quiero filtrar mi listado de tareas por su estado, para ver únicamente las tareas relevantes en cada momento.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo tareas en distintos estados / Cuando aplico el filtro `pending` / Entonces el listado muestra únicamente las tareas con estado `pending`.

- **CA2:** Dado que tengo tareas en distintos estados / Cuando aplico el filtro `completed` / Entonces el listado muestra únicamente las tareas con estado `completed`.

- **CA3:** Dado que tengo tareas en distintos estados / Cuando aplico el filtro `archived` / Entonces el listado muestra únicamente las tareas con estado `archived`.

- **CA4:** Dado que tengo un filtro de estado activo / Cuando elimino el filtro o selecciono una opción del tipo "todas" / Entonces el listado vuelve a mostrar las tareas de todos los estados. [Asumido: existe una opción explícita para ver el listado completo sin filtro activo.]

- **CA5:** Dado que tengo un filtro activo que no devuelve resultados (p.ej. filtro `completed` pero ninguna tarea está completada) / Cuando se aplica el filtro / Entonces se muestra el estado vacío apropiado al contexto del filtro, no un error. [Asumido: el mensaje del estado vacío es coherente con el filtro aplicado, p.ej. "No tienes tareas completadas".]

---

### US11: Estado vacío del listado

**Historia:** Como usuario autenticado, quiero ver un mensaje claro cuando no hay tareas en mi listado, para saber que la vista está vacía y poder actuar de inmediato.

**Criterios de Aceptación:**

- **CA1:** Dado que soy un usuario nuevo sin ninguna tarea creada / Cuando accedo al listado de tareas / Entonces veo un estado vacío con una invitación explícita a crear mi primera tarea.

- **CA2:** Dado que tengo tareas pero todas están archivadas y el filtro activo es `pending` / Cuando accedo al listado filtrado / Entonces veo un estado vacío que refleja el contexto del filtro activo. [Asumido: el texto del estado vacío varía según si el listado está vacío globalmente o por efecto de un filtro.]

---

## Módulo 4: Exportación de Tareas

### US12: Exportar tareas a CSV

**Historia:** Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos y analizarlos o usarlos fuera de FlowSync.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo tareas creadas / Cuando solicito la exportación a CSV / Entonces el sistema genera y descarga un archivo `.csv` que incluye como mínimo las columnas: título, descripción, estado y fecha límite de cada tarea.

- **CA2:** Dado que solicito la exportación / Cuando el archivo se genera / Entonces incluye tareas de todos los estados (`pending`, `completed`, `archived`), no solo las visibles bajo el filtro activo en ese momento. [Asumido: la exportación no se ve afectada por el filtro de estado activo en el listado; exporta el conjunto completo de tareas del usuario.]

- **CA3:** Dado que no tengo ninguna tarea / Cuando solicito la exportación / Entonces el sistema genera un archivo `.csv` válido que contiene únicamente la fila de cabeceras de columna. [Asumido: el CSV vacío incluye al menos las cabeceras para mantener el formato válido.]

- **CA4:** Dado que el archivo CSV se genera / Cuando lo abro en una herramienta de hojas de cálculo estándar / Entonces los datos son legibles y están correctamente delimitados. [Asumido: el delimitador es coma (`,`) y los campos con comas o saltos de línea internos se encierran en comillas dobles, siguiendo el estándar RFC 4180.]

---

## Módulo 5: Sincronización con Google Calendar

### US13: Conectar la cuenta de Google (OAuth)

**Historia:** Como usuario autenticado sin Google Calendar conectado, quiero autorizar a FlowSync para acceder a mi Google Calendar mediante OAuth, para que mis tareas con fecha límite puedan sincronizarse automáticamente.

**Criterios de Aceptación:**

- **CA1:** Dado que soy un usuario autenticado en FlowSync sin Google Calendar conectado / Cuando inicio el flujo de conexión de Google / Entonces el sistema me redirige a la pantalla de autorización OAuth de Google solicitando exclusivamente los permisos necesarios para leer y escribir en mi calendario.

- **CA2:** Dado que completo la autorización en Google / Cuando el proceso OAuth finaliza con éxito / Entonces FlowSync almacena mis tokens de Google de forma segura, asociados únicamente a mi cuenta, y muestra un indicador de que Google Calendar está conectado. [Asumido: existe un indicador visual del estado de conexión en la interfaz, p.ej. en los ajustes de la cuenta.]

- **CA3:** Dado que cancelo el proceso de autorización OAuth en Google / Cuando se retorna a FlowSync / Entonces mi cuenta de FlowSync permanece sin cambios; no se almacena ningún token ni se muestra una conexión activa.

- **CA4:** Dado que ya tengo tareas con fecha límite cuando conecto Google Calendar / Cuando la conexión se establece con éxito / Entonces las tareas existentes con fecha límite se sincronizan como eventos en Google Calendar. [Asumido: la sincronización inicial incluye el backlog de tareas con fecha ya existentes; el PRD no lo especifica explícitamente pero es coherente con el flujo.]

- **CA5:** Dado que la conexión está activa / Cuando reviso la configuración de mi cuenta / Entonces mis tokens de Google no son visibles en texto plano en ninguna pantalla de la interfaz.

---

### US14: Sincronización de tareas con fecha límite hacia Google Calendar

**Historia:** Como usuario autenticado con Google Calendar conectado, quiero que las tareas con fecha límite se reflejen automáticamente como eventos en mi Google Calendar y se actualicen cuando cambien, para no tener que gestionar mi calendario manualmente.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo Google Calendar conectado / Cuando creo una tarea con fecha límite / Entonces aparece un evento correspondiente en mi Google Calendar dentro de un tiempo razonable tras guardar la tarea. [Asumido: la sincronización es prácticamente inmediata en condiciones normales de disponibilidad de la API de Google; el PRD no define un SLA exacto para este flujo.]

- **CA2:** Dado que tengo Google Calendar conectado y una tarea sincronizada con un evento / Cuando modifico la fecha límite de esa tarea en FlowSync / Entonces el evento correspondiente en Google Calendar se actualiza con la nueva fecha.

- **CA3:** Dado que tengo Google Calendar conectado / Cuando creo o edito una tarea sin asignarle fecha límite / Entonces no se crea ni modifica ningún evento en Google Calendar para esa tarea.

- **CA4:** Dado que tengo Google Calendar conectado y una tarea sincronizada / Cuando completo o borro esa tarea en FlowSync / Entonces el evento correspondiente en Google Calendar se elimina o se actualiza según corresponda.

- **CA5:** Dado que intento sincronizar una tarea con fecha límite / Cuando la API de Google Calendar no está disponible o devuelve un error / Entonces la tarea se guarda correctamente en FlowSync, el fallo de sincronización queda registrado en los logs del sistema, y el sistema reintenta la sincronización más tarde sin bloquear al usuario.

---

### US15: Desconectar la cuenta de Google

**Historia:** Como usuario autenticado con Google Calendar conectado, quiero poder desconectar mi cuenta de Google de FlowSync en cualquier momento, para dejar de sincronizar mi calendario sin perder ninguna de mis tareas.

**Criterios de Aceptación:**

- **CA1:** Dado que tengo Google Calendar conectado / Cuando ejecuto la acción de desconectar mi cuenta de Google / Entonces FlowSync revoca y elimina mis tokens de Google almacenados, y deja de sincronizar cualquier cambio futuro en mis tareas hacia Google Calendar.

- **CA2:** Dado que he desconectado mi cuenta de Google / Cuando reviso mi listado de tareas en FlowSync / Entonces todas mis tareas siguen existiendo en FlowSync sin ser borradas ni modificadas.

- **CA3:** Dado que he desconectado mi cuenta de Google / Cuando creo o edito una tarea con fecha límite / Entonces no se realiza ningún intento de sincronización con Google Calendar.

- **CA4:** Dado que he desconectado mi cuenta de Google / Cuando reviso la configuración de mi cuenta / Entonces el sistema refleja que no hay ninguna cuenta de Google conectada y ofrece la opción de volver a conectarla. [Asumido: la UI de ajustes de cuenta permite tanto conectar como desconectar Google Calendar desde el mismo punto de acceso.]

---

## Resumen del backlog generado

| ID | Título | Módulo | Notas |
|---|---|---|---|
| US01 | Registro con email y contraseña | Autenticación | — |
| US02 | Inicio de sesión | Autenticación | — |
| US03 | Cierre de sesión | Autenticación | — |
| US04 | Pantalla de bienvenida (onboarding) | Autenticación | — |
| US05 | Crear tarea | CRUD Tareas | — |
| US06 | Ver listado de tareas | CRUD Tareas | NFR de rendimiento incluido en CA4 |
| US07 | Editar tarea | CRUD Tareas | — |
| US08 | Borrar tarea | CRUD Tareas | — |
| US09 | Cambiar estado de tarea | CRUD Tareas | Transiciones no definidas en PRD → [Asumido] |
| US10 | Filtrar tareas por estado | Filtrado | — |
| US11 | Estado vacío del listado | Filtrado | — |
| US12 | Exportar tareas a CSV | Exportación | Markdown excluido: no está en el PRD |
| US13 | Conectar Google Calendar (OAuth) | Sincronización GCal | — |
| US14 | Sincronización tarea → Google Calendar | Sincronización GCal | Dirección primaria según PRD |
| US15 | Desconectar Google Calendar | Sincronización GCal | — |

> **Pendiente de spike técnico** antes de comprometer en sprint: US14 en su totalidad (manejo de zonas horarias, rate limits de la API de Google, hora de creación del evento a partir de una fecha sin hora explícita). El PRD lo señala explícitamente como el mayor riesgo técnico del MVP.


