# BACKLOG — FlowSync MVP

> Generado a partir de `docs/PRD.md` v1.0. Cada tarea es atómica: cubre un único requisito del MVP.
> Las épicas siguen el orden de la sección 3 del PRD; la épica 6 agrupa trabajo de habilitación técnica derivado de §3.5 y §7.

---

## Épica 1 — Autenticación y gestión de cuenta

### TASK-1.1 — Registro con email y contraseña
**Story:** Como visitante, quiero crear una cuenta con email y contraseña, para poder usar FlowSync con mis propios datos.

**Acceptance Criteria:**
```
GIVEN un visitante en la pantalla de registro
WHEN introduce un email válido y una contraseña de al menos 8 caracteres y envía el formulario
THEN se crea su cuenta y queda autenticado en la aplicación
```
```
GIVEN un visitante en la pantalla de registro
WHEN introduce una contraseña de menos de 8 caracteres
THEN el sistema muestra un mensaje de error comprensible y no crea la cuenta
```
```
GIVEN un visitante en la pantalla de registro
WHEN introduce un email con formato inválido
THEN el sistema muestra un error de validación y no crea la cuenta
```

---

### TASK-1.2 — Detección de email ya registrado
**Story:** Como visitante que intenta registrarse con un email ya existente, quiero ser avisado y poder ir a iniciar sesión, para no quedarme bloqueado.

**Acceptance Criteria:**
```
GIVEN un email que ya está registrado en FlowSync
WHEN un visitante intenta registrarse con ese mismo email
THEN el sistema indica que el email ya existe y no crea una segunda cuenta
```
```
GIVEN el mensaje de email ya registrado
WHEN el visitante lo ve
THEN se le ofrece un enlace o acción para ir a la pantalla de inicio de sesión
```
```
GIVEN un email ya registrado
WHEN se intenta crear de nuevo la cuenta
THEN los datos de la cuenta original no se modifican
```

---

### TASK-1.3 — Inicio de sesión
**Story:** Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas.

**Acceptance Criteria:**
```
GIVEN un usuario registrado con credenciales válidas
WHEN introduce su email y contraseña correctos
THEN queda autenticado y accede a su listado de tareas
```
```
GIVEN un usuario que introduce una contraseña incorrecta
WHEN envía el formulario de login
THEN el sistema muestra un mensaje de credenciales inválidas sin revelar si el email existe
```
```
GIVEN un usuario autenticado
WHEN realiza peticiones a la aplicación
THEN su sesión se mantiene mediante un token de acceso emitido en el login
```

---

### TASK-1.4 — Cierre de sesión
**Story:** Como usuario autenticado, quiero cerrar sesión, para que nadie pueda usar mi cuenta desde este dispositivo.

**Acceptance Criteria:**
```
GIVEN un usuario autenticado
WHEN pulsa la opción de cerrar sesión
THEN su token de acceso se invalida y se le redirige a la pantalla de inicio de sesión
```
```
GIVEN un usuario que ha cerrado sesión
WHEN intenta acceder a una ruta protegida
THEN el sistema le pide autenticarse de nuevo
```
```
GIVEN un token invalidado por logout
WHEN se reutiliza para una petición a la API
THEN la petición es rechazada con un error de autenticación
```

---

### TASK-1.5 — Pantalla de bienvenida tras el registro
**Story:** Como usuario recién registrado, quiero ver una pantalla de bienvenida que me explique qué es FlowSync, para entender el producto y crear mi primera tarea.

**Acceptance Criteria:**
```
GIVEN un visitante que acaba de completar el registro con éxito
WHEN entra por primera vez a la aplicación
THEN ve una pantalla de bienvenida con una frase que explica qué hace FlowSync
```
```
GIVEN un usuario en la pantalla de bienvenida
WHEN la mira
THEN encuentra una llamada a la acción para crear su primera tarea
```
```
GIVEN un usuario que ya ha pasado por la bienvenida
WHEN inicia sesión de nuevo en sesiones posteriores
THEN no se le vuelve a mostrar la pantalla de bienvenida
```

---

## Épica 2 — Gestión de tareas (CRUD)

### TASK-2.1 — Crear una tarea
**Story:** Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar algo que tengo que hacer.

**Acceptance Criteria:**
```
GIVEN un usuario autenticado
WHEN crea una tarea introduciendo solo un título
THEN la tarea se guarda y aparece en su listado con estado `pending`
```
```
GIVEN un usuario que crea una tarea
WHEN introduce también descripción y/o fecha límite
THEN ambos campos quedan guardados junto con la tarea
```
```
GIVEN un usuario que intenta crear una tarea sin título
WHEN envía el formulario
THEN el sistema muestra un error de validación y no crea la tarea
```

---

### TASK-2.2 — Listar tareas propias
**Story:** Como usuario autenticado, quiero ver el listado de mis tareas, para saber qué tengo pendiente.

**Acceptance Criteria:**
```
GIVEN un usuario autenticado con tareas guardadas
WHEN abre la pantalla de listado
THEN ve únicamente las tareas creadas por él
```
```
GIVEN dos usuarios distintos cada uno con sus propias tareas
WHEN un usuario consulta su listado
THEN no aparece ninguna tarea del otro usuario
```
```
GIVEN un usuario con hasta 200 tareas
WHEN abre el listado
THEN la pantalla carga en menos de 1 segundo
```

---

### TASK-2.3 — Editar una tarea
**Story:** Como usuario autenticado, quiero editar cualquier campo de una tarea, para corregir o actualizar su información.

**Acceptance Criteria:**
```
GIVEN un usuario con una tarea existente
WHEN modifica su título, descripción o fecha límite y guarda
THEN los nuevos valores quedan persistidos y se muestran en el listado
```
```
GIVEN una edición que deja el título vacío
WHEN el usuario intenta guardar
THEN el sistema muestra un error de validación y conserva los valores anteriores
```
```
GIVEN un usuario A que intenta editar una tarea del usuario B
WHEN envía la edición
THEN la operación es rechazada y la tarea no se modifica
```

---

### TASK-2.4 — Borrar una tarea
**Story:** Como usuario autenticado, quiero borrar una tarea, para eliminar lo que ya no necesito tener registrado.

**Acceptance Criteria:**
```
GIVEN un usuario con una tarea existente
WHEN ejecuta la acción de borrar esa tarea
THEN la tarea desaparece de su listado y deja de estar accesible
```
```
GIVEN un usuario que borra una tarea
WHEN la operación termina
THEN no es posible recuperarla desde la interfaz (operación final dentro del MVP)
```
```
GIVEN un usuario A que intenta borrar una tarea del usuario B
WHEN envía la petición
THEN la operación es rechazada y la tarea sigue existiendo
```

---

### TASK-2.5 — Cambiar el estado de una tarea
**Story:** Como usuario autenticado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar su situación real.

**Acceptance Criteria:**
```
GIVEN una tarea en estado `pending`
WHEN el usuario la marca como completada
THEN el estado pasa a `completed` y se refleja en el listado
```
```
GIVEN una tarea en estado `completed`
WHEN el usuario la archiva
THEN el estado pasa a `archived`
```
```
GIVEN una tarea recién creada
WHEN se consulta su estado inicial
THEN su estado es `pending`
```
```
GIVEN un cambio de estado a un valor distinto de `pending`, `completed` o `archived`
WHEN se intenta aplicar
THEN el sistema lo rechaza
```

---

## Épica 3 — Organización y filtrado

### TASK-3.1 — Filtrar tareas por estado
**Story:** Como usuario con varias tareas, quiero filtrar mi listado por estado, para enfocarme solo en pendientes, completadas o archivadas.

**Acceptance Criteria:**
```
GIVEN un usuario con tareas en distintos estados
WHEN aplica el filtro "pendientes"
THEN solo ve tareas con estado `pending`
```
```
GIVEN un usuario con tareas en distintos estados
WHEN aplica el filtro "completadas"
THEN solo ve tareas con estado `completed`
```
```
GIVEN un filtro aplicado
WHEN el usuario lo desactiva o cambia
THEN el listado refleja inmediatamente la nueva selección
```

---

### TASK-3.2 — Ordenación por defecto del listado
**Story:** Como usuario, quiero que el listado se ordene por defecto mostrando primero lo más relevante para "hoy", para identificar rápidamente lo importante.

**Acceptance Criteria:**
```
GIVEN un usuario con tareas con distintas fechas límite (algunas para hoy, otras futuras y otras sin fecha)
WHEN abre el listado sin aplicar ninguna ordenación manual
THEN las tareas más relevantes para hoy aparecen en la parte superior
```
```
GIVEN un usuario sin tareas con fecha para hoy
WHEN abre el listado
THEN el orden por defecto sigue siendo consistente y no muestra error
```
```
GIVEN dos tareas con la misma relevancia para hoy
WHEN se muestran en el listado
THEN su orden relativo es estable entre cargas consecutivas
```

> **Nota:** el criterio exacto de "relevancia para hoy" se define durante el refinamiento (§3.3 PRD).

---

### TASK-3.3 — Estado vacío del listado
**Story:** Como usuario nuevo o sin tareas activas, quiero ver un estado vacío con una invitación a crear la primera, para saber cómo empezar.

**Acceptance Criteria:**
```
GIVEN un usuario sin ninguna tarea
WHEN abre el listado
THEN ve un mensaje de estado vacío con una llamada a la acción para crear la primera tarea
```
```
GIVEN un usuario con todas sus tareas en estado `archived` y el filtro mostrando solo activas
WHEN abre el listado
THEN ve el mismo estado vacío
```
```
GIVEN el estado vacío visible
WHEN el usuario pulsa la llamada a la acción
THEN se abre el flujo de creación de una nueva tarea
```

---

## Épica 4 — Exportación

### TASK-4.1 — Exportar tareas a CSV
**Story:** Como usuario, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos fuera de FlowSync.

**Acceptance Criteria:**
```
GIVEN un usuario autenticado con tareas
WHEN ejecuta la acción de exportar a CSV
THEN obtiene un archivo descargable con una fila por tarea
```
```
GIVEN un CSV exportado
WHEN se inspeccionan sus columnas
THEN incluyen al menos título, descripción, estado y fecha límite
```
```
GIVEN un usuario sin tareas
WHEN ejecuta la exportación
THEN obtiene un CSV válido (solo con la cabecera) sin error
```
```
GIVEN un usuario A que exporta
WHEN se examina el contenido del CSV
THEN no aparece ninguna tarea de otro usuario
```

---

## Épica 5 — Sincronización con Google Calendar

### TASK-5.1 — Conectar cuenta de Google vía OAuth
**Story:** Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para autorizar la sincronización con mi calendario.

**Acceptance Criteria:**
```
GIVEN un usuario autenticado sin conexión previa a Google
WHEN inicia el flujo OAuth y concede los permisos solicitados
THEN su cuenta queda marcada como conectada a Google en FlowSync
```
```
GIVEN un usuario que cancela la pantalla de consentimiento de Google
WHEN regresa a FlowSync
THEN su cuenta sigue sin estar conectada y se le informa del resultado
```
```
GIVEN un usuario que completa la conexión
WHEN se almacenan sus credenciales de Google
THEN los tokens se persisten de forma segura y no son accesibles por otros usuarios
```

---

### TASK-5.2 — Crear evento en Google Calendar al crear tarea con fecha
**Story:** Como usuario con Google conectado, quiero que las tareas con fecha límite aparezcan como eventos en mi Google Calendar, para no tener que copiarlas a mano.

**Acceptance Criteria:**
```
GIVEN un usuario con Google conectado
WHEN crea una tarea con fecha límite
THEN se genera un evento correspondiente en su Google Calendar
```
```
GIVEN un usuario con Google conectado
WHEN crea una tarea sin fecha límite
THEN no se genera ningún evento en Google Calendar
```
```
GIVEN un usuario sin Google conectado
WHEN crea una tarea con fecha límite
THEN la tarea se guarda pero no se intenta crear ningún evento en Google Calendar
```

---

### TASK-5.3 — Actualizar evento al cambiar la fecha de una tarea
**Story:** Como usuario con Google conectado, quiero que al cambiar la fecha de una tarea su evento se actualice, para que el calendario siempre refleje la fecha real.

**Acceptance Criteria:**
```
GIVEN una tarea con evento ya creado en Google Calendar
WHEN el usuario cambia la fecha límite de esa tarea
THEN el evento correspondiente se actualiza con la nueva fecha
```
```
GIVEN una tarea sin fecha límite cuyo usuario le añade una
WHEN guarda el cambio
THEN se crea un nuevo evento en Google Calendar con esa fecha
```
```
GIVEN una tarea con fecha que el usuario deja sin fecha
WHEN guarda el cambio
THEN el evento previamente creado se elimina del Google Calendar
```

---

### TASK-5.4 — Eliminar evento al completar o borrar la tarea
**Story:** Como usuario con Google conectado, quiero que al completar o borrar una tarea su evento desaparezca o se marque, para no ver en el calendario cosas que ya no aplican.

**Acceptance Criteria:**
```
GIVEN una tarea con evento en Google Calendar
WHEN el usuario marca la tarea como `completed`
THEN el evento se elimina o se marca según el criterio definido para "completada"
```
```
GIVEN una tarea con evento en Google Calendar
WHEN el usuario borra la tarea
THEN el evento correspondiente se elimina del Google Calendar
```
```
GIVEN una tarea sin fecha (por tanto sin evento)
WHEN el usuario la completa o la borra
THEN no se realiza ninguna llamada a Google Calendar
```

---

### TASK-5.5 — Desconectar la cuenta de Google
**Story:** Como usuario con Google conectado, quiero desconectar mi cuenta en cualquier momento, para dejar de sincronizar sin perder mis tareas.

**Acceptance Criteria:**
```
GIVEN un usuario con Google conectado
WHEN ejecuta la acción de desconectar
THEN su cuenta queda marcada como desconectada y las credenciales de Google se eliminan o invalidan
```
```
GIVEN un usuario que acaba de desconectar Google
WHEN consulta su listado de tareas
THEN todas sus tareas siguen existiendo intactas en FlowSync
```
```
GIVEN un usuario desconectado de Google
WHEN crea, edita o borra una tarea
THEN FlowSync no realiza ninguna llamada a la API de Google
```

---

### TASK-5.6 — Tolerancia a errores y reintentos de sincronización
**Story:** Como usuario, quiero que un fallo de la API de Google no me impida usar FlowSync, para que mis tareas se guarden siempre y la sincronización se reintente más tarde.

**Acceptance Criteria:**
```
GIVEN una llamada a la API de Google que falla por indisponibilidad
WHEN el usuario crea o modifica una tarea
THEN la tarea se guarda en FlowSync y el usuario no ve un error técnico bloqueante
```
```
GIVEN una operación de sincronización fallida
WHEN pasa el tiempo y la API vuelve a estar disponible
THEN el sistema reintenta la operación pendiente hasta sincronizarla
```
```
GIVEN una operación de sincronización (éxito o fallo)
WHEN se ejecuta
THEN queda registrada en logs con información suficiente para diagnosticar el caso
```

---

## Épica 6 — Habilitación técnica (spikes)

> Tareas de investigación derivadas de §3.5 y §7 del PRD. No son features de usuario final: producen decisiones y/o configuración necesarias antes de comprometer alcance.

### TASK-6.1 — Spike: sincronización inversa Google Calendar → FlowSync
**Story:** Como equipo de producto, quiero investigar el coste y alcance viable de la sincronización inversa (eventos editados en Google que actualizan tareas), para decidir qué parte se incluye en el MVP.

**Acceptance Criteria:**
```
GIVEN el spike completado
WHEN se revisa su entregable
THEN existe una recomendación documentada sobre incluir o no la sincronización inversa en el MVP
```
```
GIVEN el spike completado
WHEN se revisan los hallazgos
THEN están identificados los mecanismos posibles (polling vs webhooks) con sus costes y limitaciones
```
```
GIVEN el spike completado
WHEN se revisa la estrategia de conflictos
THEN está descrita la política de resolución cuando una tarea y su evento cambian a la vez
```

---

### TASK-6.2 — Spike: configuración de OAuth en Google Cloud Console
**Story:** Como equipo de ingeniería, quiero dejar configurado el proyecto de Google Cloud con los permisos OAuth necesarios, para desbloquear las tareas de integración con Google Calendar.

**Acceptance Criteria:**
```
GIVEN el proyecto en Google Cloud Console
WHEN finaliza la configuración
THEN existe un client ID y client secret válidos para FlowSync con los scopes mínimos de Google Calendar
```
```
GIVEN la configuración completada
WHEN otro desarrollador del equipo necesita reproducir el setup
THEN dispone de instrucciones documentadas para hacerlo
```
```
GIVEN el estado de revisión de la app en Google
WHEN se consulta antes del lanzamiento del MVP
THEN está claro qué pasos de verificación de Google son obligatorios y cuáles quedan pendientes
```

---

### TASK-6.3 — Spike: estrategia de zonas horarias entre tareas y eventos
**Story:** Como equipo, quiero definir cómo se traduce la fecha límite de una tarea a la hora de un evento en Google Calendar, para evitar eventos a horas erróneas según la zona horaria del usuario.

**Acceptance Criteria:**
```
GIVEN el spike completado
WHEN se revisa el entregable
THEN existe una regla definida para convertir la fecha límite de una tarea en la hora y duración de su evento
```
```
GIVEN la regla definida
WHEN se aplica a un usuario en una zona horaria distinta a la del servidor
THEN el evento aparece en su Google Calendar a la hora local esperada
```
```
GIVEN el spike completado
WHEN se revisan los casos límite
THEN está descrito cómo se tratan los eventos de día completo y los cambios de horario (DST)
```
