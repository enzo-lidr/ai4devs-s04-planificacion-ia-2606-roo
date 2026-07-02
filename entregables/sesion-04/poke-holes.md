# Poke-holes — Story 10: Crear y actualizar eventos desde tareas con fecha límite

## Story analizada

**User story**  
Como usuario autenticado con Google Calendar conectado, quiero que mis tareas con fecha límite aparezcan y se actualicen como eventos, para evitar duplicar manualmente mi planificación.

## Hallazgos genuinos

### 1. La fecha límite no está suficientemente definida como evento de calendario

La story asume que una “fecha límite” puede convertirse directamente en un evento de Google Calendar, pero el PRD no define si la fecha límite representa solo un día, una fecha con hora, un evento de todo el día, una duración por defecto, la zona horaria del usuario o la zona horaria del navegador.

Impacto: dos implementaciones distintas podrían cumplir la story pero producir calendarios muy diferentes o incorrectos.

---

### 2. Falta el caso de quitar una fecha límite existente

Los AC cubren crear una tarea con fecha, crear una sin fecha, cambiar una fecha y agregar fecha a una tarea existente. Pero no cubren qué ocurre cuando una tarea sincronizada ya tenía fecha límite y el usuario la elimina.

Impacto: podría quedar un evento huérfano en Google Calendar aunque la tarea ya no tenga fecha límite en FlowSync.

---

### 3. Falta definir una relación persistente tarea ↔ evento para evitar duplicados

La story habla de actualizar “el evento correspondiente”, pero no explicita cómo FlowSync identifica ese evento ni cómo evita duplicados si el usuario guarda varias veces, si cambia la fecha varias veces o si una operación anterior quedó en estado incierto.

Impacto: una misma tarea podría terminar creando múltiples eventos en Google Calendar.

---

### 4. Falta cubrir fallos, permisos y reintentos de Google Calendar

La story asume que Google Calendar está conectado y disponible, pero no cubre tokens vencidos, permisos revocados, errores de la API, rate limits o una conexión guardada en FlowSync pero inválida en Google.

Impacto: la tarea debería guardarse igual, pero falta definir si la sincronización queda pendiente, si se reintenta, si se pide reconectar Google y cómo se comunica el estado al usuario.

---

### 5. Falta definir qué datos se sincronizan y en qué calendario

La story se enfoca en la fecha límite, pero no aclara si el evento debe incluir título, descripción, estado u otros datos visibles de la tarea. Tampoco define en qué calendario de Google se crea el evento si el usuario tiene más de un calendario disponible.

Impacto: el evento podría ser técnicamente creado, pero no ser útil para el usuario o aparecer en un calendario inesperado.
