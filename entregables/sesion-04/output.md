## Output crudo de la IA

### Épica: Autenticación y gestión de cuenta

**US: Crear cuenta con email y contraseña**
Como usuario no registrado, quiero crear una cuenta con email y contraseña, para empezar a usar FlowSync de forma segura.
- **Scenario:** Registro básico
  Given que el visitante está en la pantalla de registro  
  When ingresa un email válido y una contraseña de 8 caracteres o más y confirma  
  Then el sistema crea una cuenta nueva  
  And el usuario recibe acceso a la aplicación  
  And se muestra un mensaje de bienvenida.

**US: Iniciar sesión**
Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas personales.
- **Scenario:** Login exitoso
  Given que el usuario ha creado una cuenta previamente  
  When ingresa el email y la contraseña correctos y envía el formulario  
  Then el sistema valida las credenciales  
  And permite el acceso a la vista de tareas  
  And crea una sesión válida con token de acceso.

**US: Cerrar sesión**
Como usuario autenticado, quiero cerrar sesión, para proteger mis datos cuando dejo de usar la aplicación.
- **Scenario:** Logout
  Given que el usuario está autenticado  
  When pulsa el botón de cerrar sesión  
  Then el sistema invalida la sesión actual  
  And redirige al usuario a la pantalla de inicio  
  And no permite acceder a la vista de tareas sin nuevo login.

**US: Ver onboarding tras registro**
Como nuevo usuario, quiero ver una pantalla de bienvenida después del registro, para entender qué hace FlowSync y crear mi primera tarea.
- **Scenario:** Onboarding mínimo
  Given que el usuario acaba de registrarse con éxito  
  When la cuenta es creada  
  Then se muestra una pantalla de bienvenida con una frase descriptiva  
  And se ofrece un CTA para crear la primera tarea  
  And no se requiere verificación de email.

---

### Épica: Gestión de tareas (CRUD)

**US: Crear tarea básica**
Como usuario, quiero crear una tarea con al menos un título, para capturar ideas y pendientes rápidamente.
- **Scenario:** Título obligatorio
  Given que el usuario está en el formulario de creación de tarea  
  When ingresa un título y guarda la tarea  
  Then la tarea se crea con estado `pending`  
  And la tarea aparece en el listado del usuario  
  And no es necesario completar descripción ni fecha límite.

**US: Ver listado de tareas**
Como usuario, quiero ver el listado de mis tareas, para revisar y gestionar mis pendientes personales.
- **Scenario:** Listado personal
  Given que el usuario está autenticado  
  When accede a la vista de tareas  
  Then el sistema muestra solo sus tareas  
  And las tareas se ordenan para que las más relevantes para hoy sean visibles primero (asumido)  
  And si no hay tareas, se muestra un estado vacío con invitación a crear la primera.

**US: Editar tarea**
Como usuario, quiero editar cualquier campo de una tarea existente, para actualizar el contenido o la fecha límite según cambien mis prioridades.
- **Scenario:** Modificar título y fecha
  Given que el usuario selecciona una tarea existente  
  When cambia el título, la descripción o la fecha límite y guarda  
  Then la tarea se actualiza con los nuevos valores  
  And los cambios se reflejan en el listado.

**US: Eliminar tarea**
Como usuario, quiero borrar una tarea, para mantener mi lista actualizada y sin pendientes irrelevantes.
- **Scenario:** Borrar tarea
  Given que el usuario está viendo una tarea existente  
  When selecciona eliminar y confirma  
  Then la tarea se elimina del sistema  
  And ya no aparece en el listado de tareas.

**US: Cambiar estado de tarea**
Como usuario, quiero cambiar el estado de una tarea a `completed` o `archived`, para indicar progreso y separar tareas activas de las cerradas.
- **Scenario:** Marcar completada
  Given que una tarea está en estado `pending`  
  When el usuario marca la tarea como `completed`  
  Then el estado de la tarea cambia a `completed`  
  And se actualiza en el listado de tareas.

---

### Épica: Organización y filtrado

**US: Filtrar tareas por estado**
Como usuario, quiero filtrar mis tareas por estado, para ver solo las pendientes, completadas o archivadas según lo necesite.
- **Scenario:** Filtro por pendientes
  Given que el usuario está en la vista de tareas  
  When selecciona el filtro `pending`  
  Then el listado muestra solo tareas con estado `pending`  
  And no se muestran tareas con estado `completed` o `archived`.

**US: Ver estado vacío**
Como usuario nuevo o sin tareas activas, quiero ver una pantalla vacía con invitación a crear una tarea, para saber qué hacer a continuación.
- **Scenario:** Listado vacío
  Given que el usuario no tiene tareas activas  
  When abre la vista de tareas  
  Then se muestra un mensaje claro de estado vacío  
  And se ofrece un botón o enlace para crear una nueva tarea  
  And no se muestran tareas de otros usuarios.

---

### Épica: Exportación

**US: Exportar tareas a CSV**
Como usuario, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos fuera de FlowSync.
- **Scenario:** Exportar datos
  Given que el usuario está en la vista de tareas  
  When solicita exportar a CSV  
  Then el sistema genera un archivo CSV con título, descripción, estado y fecha límite de cada tarea  
  And el archivo se descarga en el navegador  
  And solo se incluyen las tareas del usuario autenticado.

---

### Épica: Sincronización con Google Calendar

**US: Conectar Google Calendar**
Como usuario, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para permitir que mis tareas con fecha aparezcan en mi calendario.
- **Scenario:** OAuth Google
  Given que el usuario está autenticado  
  When inicia el flujo de conexión con Google  
  Then se redirige a Google para autorizar el acceso al calendario  
  And al aceptar, FlowSync guarda el token de autorización seguro  
  And la aplicación muestra que la cuenta está conectada.

**US: Crear eventos desde tareas con fecha**
Como usuario, quiero que las tareas con fecha límite aparezcan como eventos en Google Calendar, para no tener que copiar mi tarea manualmente al calendario.
- **Scenario:** Crear evento
  Given que el usuario tiene la cuenta de Google conectada  
  When crea o actualiza una tarea con fecha límite  
  Then FlowSync crea un evento correspondiente en Google Calendar  
  And la tarea permanece en `pending` en FlowSync  
  And el evento refleja el título y la fecha límite de la tarea.

**US: Actualizar evento al cambiar fecha de tarea**
Como usuario, quiero que si cambio la fecha límite de una tarea, el evento de Google Calendar se actualice, para mantener ambos lados sincronizados.
- **Scenario:** Actualización de fecha
  Given que una tarea ya está sincronizada con Google Calendar  
  When el usuario cambia la fecha límite de la tarea y guarda  
  Then FlowSync actualiza el evento existente en Google Calendar  
  And el evento refleja la nueva fecha límite.

**US: Eliminar/marcar evento al completar o borrar tarea**
Como usuario, quiero que completar o borrar una tarea actualice el evento en Google Calendar, para no mantener eventos obsoletos en mi calendario.
- **Scenario:** Tarea completada
  Given que una tarea sincronizada existe en Google Calendar  
  When el usuario marca la tarea como `completed`  
  Then FlowSync elimina o marca el evento correspondiente en Google Calendar  
  And la tarea sigue registrada en FlowSync con estado `completed`.
- **Scenario:** Tarea borrada
  Given que una tarea sincronizada existe en Google Calendar  
  When el usuario borra la tarea  
  Then FlowSync elimina el evento asociado en Google Calendar  
  And la tarea desaparece de FlowSync.

**US: Desconectar Google**
Como usuario, quiero desconectar mi cuenta de Google, para detener la sincronización sin perder mis tareas ya creadas.
- **Scenario:** Desconectar cuenta
  Given que el usuario tiene Google Calendar conectado  
  When pulsa desconectar  
  Then FlowSync elimina la conexión activa con Google  
  And deja de intentar sincronizar nuevas tareas  
  And preserva las tareas existentes en FlowSync.

**US: Manejar errores de Google Calendar**
Como usuario, quiero que FlowSync maneje fallos de la API de Google de forma razonable, para que mis tareas no se pierdan aunque la sincronización falle temporalmente.
- **Scenario:** Falla de API
  Given que la API de Google devuelve un error o está inaccesible  
  When el usuario crea o actualiza una tarea con fecha límite  
  Then la tarea se guarda en FlowSync aunque la sincronización falle  
  And el sistema registra el fallo para diagnóstico  
  And el intento de sincronización se reprograma o reintenta más tarde (asumido).

---

## Notas de calidad para agentes IA
- No se añadieron funcionalidades fuera del MVP: Outlook, etiquetas, subtareas, equipos, notificaciones push/email, app nativa.
- La sincronización inversa completa desde Google a FlowSync se trata como deseo fuera del alcance comprometido.
- Los criterios de aceptación usan `Given/When/Then` y marcan explícitamente las asunciones cuando el PRD no las especifica de forma literal.
