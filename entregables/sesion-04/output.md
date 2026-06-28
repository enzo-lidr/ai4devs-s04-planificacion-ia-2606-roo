# Backlog de Historias de Usuario — FlowSync MVP

---

## Bloque 1: Registro y Autenticación de Usuarios

---

### HU-01: Registro con email y contraseña
**Historia**: Como usuario de FlowSync, quiero registrarme con mi correo electrónico y una contraseña para crear mi cuenta y empezar a gestionar mis tareas.

**Criterios de aceptación**:
```gherkin
Given que soy un usuario nuevo en la pantalla de registro
When introduzco un email con formato inválido (ej. "usuario@")
Then el sistema muestra un mensaje de error indicando que el email no es válido
  y el formulario no se envía

Given que soy un usuario nuevo en la pantalla de registro
When introduzco una contraseña de menos de 8 caracteres
Then el sistema muestra un mensaje de error con los requisitos mínimos de contraseña
  y no permite continuar el registro

Given que soy un usuario nuevo en la pantalla de registro
When introduzco un email que ya existe en el sistema
Then el sistema muestra un mensaje indicando que ese email ya está registrado
  y no crea una segunda cuenta duplicada

Given que soy un usuario nuevo en la pantalla de registro
When dejo algún campo obligatorio vacío y pulso "Registrarse"
Then los campos vacíos se marcan visualmente como errores
  y el formulario no se envía hasta que todos estén completos

Given que completo el registro correctamente
When el servidor experimenta un error al crear la cuenta
Then el sistema muestra un mensaje de error genérico indicando que inténtelo de nuevo
  y no redirige al usuario a ninguna pantalla autenticada
```

**DoD**: Documentación de endpoint actualizada; tests unitarios e integración pasados; PR revisado y aprobado por al menos un reviewer; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Se asume que el MVP usa email/contraseña como método de registro (no SSO corporativo ni magic link). [asumido]

---

### HU-02: Inicio de sesión con email y contraseña
**Historia**: Como usuario de FlowSync, quiero iniciar sesión con mi correo y contraseña para acceder a mis tareas de forma segura.

**Criterios de aceptación**:
```gherkin
Given que estoy en la pantalla de login
When introduzco una contraseña incorrecta para un email existente
Then el sistema muestra un mensaje genérico de "credenciales incorrectas"
  y no revela si el email existe o no en el sistema

Given que estoy en la pantalla de login
When fallo el inicio de sesión 5 veces consecutivas con el mismo email
Then el sistema bloquea temporalmente los intentos para ese email
  y muestra un mensaje indicando el tiempo de espera [asumido: threshold de 5 intentos]

Given que inicio sesión correctamente
When mi sesión expira por inactividad
Then el sistema me redirige a la pantalla de login
  y al volver a autenticarme accedo exactamente al estado anterior de mi lista de tareas [asumido: tiempo de expiración de sesión configurable en back]

Given que estoy autenticado
When accedo a una URL protegida sin sesión activa (ej. pegando la URL en otro navegador)
Then el sistema me redirige a la pantalla de login
  y no expone ningún dato de tareas sin autenticación
```

**DoD**: Documentación de endpoint actualizada; tests unitarios e integración pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Política de bloqueo por intentos fallidos [asumido]. Tiempo de expiración de sesión [asumido].

---

### HU-03: Cierre de sesión
**Historia**: Como usuario de FlowSync, quiero cerrar mi sesión de forma explícita para asegurarme de que nadie más puede acceder a mis tareas desde el mismo dispositivo.

**Criterios de aceptación**:
```gherkin
Given que estoy autenticado y pulso "Cerrar sesión"
When el sistema procesa el logout
Then mi token/sesión queda invalidado en servidor
  y cualquier petición posterior con ese token recibe un 401

Given que he cerrado sesión
When pulso el botón "Atrás" del navegador
Then el sistema me redirige a la pantalla de login
  y no muestra ninguna pantalla o dato de mis tareas en caché

Given que tengo FlowSync abierto en dos pestañas y cierro sesión en una
When intento interactuar con la segunda pestaña
Then la segunda pestaña detecta la sesión inválida y redirige al login [asumido: detección vía interceptor de petición 401]
```

**DoD**: Documentación de endpoint actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Invalidación de token en servidor (no solo borrado de cookie en cliente) [asumido].

---

## Bloque 2: CRUD Completo de Tareas con Estados

---

### HU-04: Crear una tarea
**Historia**: Como usuario de FlowSync, quiero crear una nueva tarea con título, descripción y fecha límite para registrar mis pendientes en un solo lugar.

**Criterios de aceptación**:
```gherkin
Given que estoy en el formulario de nueva tarea
When dejo el campo "título" vacío y pulso guardar
Then el sistema muestra un error de validación en el campo título
  y la tarea no se crea

Given que estoy creando una tarea
When introduzco una fecha límite anterior a la fecha actual
Then el sistema muestra una advertencia indicando que la fecha está en el pasado
  y requiere confirmación explícita antes de guardar [asumido: advertencia, no bloqueo]

Given que el servidor no está disponible cuando pulso guardar
When el sistema intenta persistir la tarea
Then se muestra un mensaje de error al usuario
  y el formulario conserva los datos introducidos para no perderlos

Given que creo una tarea correctamente
When la tarea se guarda
Then aparece inmediatamente en mi lista de tareas con estado "Pendiente" por defecto
  y sin necesidad de recargar la página [asumido: actualización optimista o reactiva]
```

**DoD**: Documentación de endpoint actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Los estados posibles son al menos "Pendiente", "En progreso", "Completada" [asumido]. Advertencia (no bloqueo) en fecha pasada [asumido].

---

### HU-05: Editar una tarea existente
**Historia**: Como usuario de FlowSync, quiero editar el título, descripción o fecha límite de una tarea ya creada para mantener mis pendientes actualizados.

**Criterios de aceptación**:
```gherkin
Given que edito una tarea y borro el título dejándolo vacío
When intento guardar los cambios
Then el sistema muestra un error de validación
  y no persiste los cambios

Given que edito una tarea y la fecha límite cambia
When la tarea ya estaba sincronizada con Google Calendar
Then el evento en Google Calendar se actualiza automáticamente con la nueva fecha [asumido: la sincronización es reactiva a ediciones]

Given que dos sesiones del mismo usuario intentan editar la misma tarea simultáneamente
When la segunda edición llega al servidor
Then el sistema detecta el conflicto y notifica al usuario de la segunda sesión
  sin sobrescribir silenciosamente los cambios previos [asumido: control de concurrencia optimista]

Given que edito una tarea y el servidor devuelve error al guardar
When el sistema falla
Then el formulario mantiene los valores editados (no los originales)
  y muestra un mensaje de error accionable
```

**DoD**: Documentación de endpoint actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Medio

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ⚠️ Small (la sincronización reactiva con Calendar añade complejidad, considerar separar) · ✅ Testeable

**Asumidos**: Sincronización reactiva en edición [asumido]. Control de concurrencia optimista [asumido].

---

### HU-06: Eliminar una tarea
**Historia**: Como usuario de FlowSync, quiero eliminar una tarea que ya no es relevante para mantener mi lista limpia y sin ruido.

**Criterios de aceptación**:
```gherkin
Given que pulso "Eliminar" en una tarea
When aparece la confirmación
Then si cancelo, la tarea sigue existiendo sin cambios

Given que confirmo la eliminación de una tarea con evento en Google Calendar
When la tarea se borra en FlowSync
Then el evento asociado en Google Calendar también se elimina [asumido: borrado en cascada al calendario]

Given que confirmo la eliminación y el servidor devuelve error
When el sistema falla al borrar
Then la tarea permanece en la lista
  y se muestra un mensaje de error al usuario

Given que elimino una tarea que estaba siendo mostrada en la vista filtrada
When la tarea se borra correctamente
Then la vista se actualiza y la tarea desaparece sin recargar toda la página
```

**DoD**: Documentación de endpoint actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Borrado en cascada del evento en Google Calendar [asumido]. Confirmación previa al borrado [asumido].

---

### HU-07: Cambiar el estado de una tarea
**Historia**: Como usuario de FlowSync, quiero cambiar el estado de una tarea (ej. de "Pendiente" a "Completada") para reflejar mi progreso sin tener que editar el resto del contenido.

**Criterios de aceptación**:
```gherkin
Given que tengo una tarea en estado "Pendiente"
When intento cambiarla directamente a un estado que no existe en el flujo definido
Then el sistema rechaza el cambio y muestra los estados válidos disponibles [asumido: flujo de estados definido y validado en back]

Given que marco una tarea como "Completada"
When el cambio se guarda
Then la tarea sigue siendo visible en la lista (no se oculta automáticamente)
  hasta que el usuario aplique un filtro que la excluya [asumido: no se borran las completadas]

Given que cambio el estado de una tarea y el servidor no responde
When el sistema aplica el cambio de forma optimista en la UI
Then si la petición falla, el estado se revierte al valor anterior
  y se muestra un mensaje de error

Given que cambio el estado de una tarea con fecha a "Completada"
When el evento existe en Google Calendar
Then el evento en Calendar se marca visualmente como completado o se elimina [asumido: comportamiento a confirmar con PO]
```

**DoD**: Documentación de endpoint actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Estados: Pendiente, En progreso, Completada [asumido]. Comportamiento del evento Calendar al completar [asumido — requiere decisión de PO].

---

## Bloque 3: Filtrado y Organización Básica de Tareas

---

### HU-08: Filtrar tareas por estado
**Historia**: Como usuario de FlowSync, quiero filtrar mis tareas por estado para enfocarme en lo que está pendiente o revisar lo que ya completé sin ver todo mezclado.

**Criterios de aceptación**:
```gherkin
Given que tengo tareas en varios estados y aplico el filtro "Completadas"
When la lista se actualiza
Then solo se muestran las tareas con ese estado
  y el número de resultados refleja exactamente las tareas en ese estado

Given que aplico un filtro y no hay tareas que lo cumplan
When la lista se renderiza
Then se muestra un estado vacío informativo (ej. "No tienes tareas completadas")
  y no una lista en blanco sin contexto

Given que tengo un filtro activo y creo una nueva tarea con un estado diferente al filtrado
When la tarea se guarda
Then la tarea nueva no aparece en la lista filtrada actual
  y se puede ver al quitar el filtro

Given que aplico filtros combinados (ej. estado + fecha) [asumido: filtros combinables]
When un filtro no tiene resultados pero el otro sí
Then el sistema muestra los resultados de la intersección (cero resultados)
  y permite limpiar los filtros desde la misma vista
```

**DoD**: Documentación actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Filtros combinables por estado y fecha [asumido]. Estado vacío con mensaje informativo [asumido].

---

### HU-09: Ordenar tareas por fecha límite
**Historia**: Como usuario de FlowSync, quiero ordenar mis tareas por fecha límite para identificar rápidamente qué vence antes y priorizar mi día.

**Criterios de aceptación**:
```gherkin
Given que tengo tareas con y sin fecha límite y ordeno por fecha
When se aplica el orden ascendente
Then las tareas sin fecha aparecen al final de la lista
  y no mezcladas con las que tienen fecha

Given que ordeno mis tareas por fecha descendente
When hay varias tareas con exactamente la misma fecha límite
Then esas tareas se ordenan entre sí por fecha de creación como criterio secundario [asumido]

Given que tengo un orden aplicado y creo una nueva tarea con fecha
When la tarea se guarda
Then se inserta en la posición correcta según el orden activo
  sin requerir que el usuario re-ordene manualmente

Given que cambio el criterio de ordenación mientras tengo un filtro activo
When se aplica el nuevo orden
Then el filtro permanece activo y solo se reordena el conjunto ya filtrado
```

**DoD**: Documentación actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Criterio secundario de orden por fecha de creación en empates [asumido]. Ordenación disponible también descendente [asumido].

---

## Bloque 4: Exportación de Tareas

---

### HU-10: Exportar tareas a CSV
**Historia**: Como usuario de FlowSync, quiero exportar mis tareas a un archivo CSV para tener una copia local o importarlas en otras herramientas.

**Criterios de aceptación**:
```gherkin
Given que tengo 0 tareas y pulso "Exportar"
When el sistema intenta generar el archivo
Then se muestra un mensaje informando que no hay tareas para exportar
  y no se descarga ningún archivo vacío o corrupto

Given que tengo un filtro activo (ej. solo "Pendientes") y pulso "Exportar"
When se genera el archivo
Then el CSV contiene únicamente las tareas del filtro activo
  y no la totalidad de las tareas del usuario [asumido: la exportación respeta el filtro activo]

Given que tengo tareas con caracteres especiales en el título o descripción (comas, comillas, saltos de línea)
When se genera el CSV
Then los campos están correctamente escapados según el estándar RFC 4180
  y el archivo se abre correctamente en Excel/Google Sheets

Given que el servidor falla durante la generación del CSV
When el sistema no puede completar la exportación
Then se muestra un mensaje de error al usuario
  y no se descarga un archivo parcial o corrupto
```

**DoD**: Documentación actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Formato de exportación CSV [asumido — el PRD no especifica el formato]. La exportación respeta el filtro activo [asumido]. Compatibilidad con Excel/Sheets [asumido].

---

### HU-11: Seleccionar columnas o campos a exportar
**Historia**: Como product owner de FlowSync, quiero que la exportación incluya al menos título, descripción, estado y fecha límite para que los datos exportados sean útiles para análisis de adopción y churn en el MVP.

**Criterios de aceptación**:
```gherkin
Given que se genera un CSV de exportación
When el archivo se descarga
Then contiene como mínimo las columnas: título, descripción, estado, fecha límite y fecha de creación
  en ese orden y con cabeceras en inglés o español consistente [asumido: idioma a definir]

Given que una tarea no tiene fecha límite
When aparece en el CSV
Then el campo fecha límite aparece vacío (celda vacía, no "null" ni "undefined")

Given que una tarea tiene una descripción con más de 500 caracteres [asumido: límite de campo]
When aparece en el CSV
Then el contenido completo se incluye sin truncar
```

**DoD**: Documentación actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Bajo

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ✅ Small · ✅ Testeable

**Asumidos**: Campos mínimos del export [asumido]. Idioma de cabeceras [asumido]. Límite de caracteres en descripción [asumido].

---

## Bloque 5: Sincronización con Google Calendar (Lectura y Escritura)

---

### HU-12: Conectar cuenta de Google Calendar
**Historia**: Como usuario de FlowSync, quiero conectar mi cuenta de Google Calendar mediante OAuth para autorizar a FlowSync a leer y escribir eventos en mi nombre.

**Criterios de aceptación**:
```gherkin
Given que inicio el flujo OAuth con Google
When deniego los permisos en la pantalla de Google
Then FlowSync me redirige a la app con un mensaje claro indicando que la sincronización no está activa
  y la app sigue funcionando sin sincronización

Given que he conectado mi Google Calendar y revo el acceso desde la consola de Google
When FlowSync intenta sincronizar
Then el sistema detecta el token inválido y muestra al usuario una notificación para reconectar
  sin mostrar errores técnicos o stacktraces

Given que el flujo OAuth de Google devuelve un error (ej. timeout, error de red)
When el sistema recibe el callback con error
Then se muestra un mensaje de error amigable
  y el usuario puede reintentar la conexión sin necesidad de recargar la app

Given que ya tengo una cuenta de Google conectada e intento conectar la misma cuenta de nuevo
When el sistema detecta que el token ya existe
Then informa al usuario que la cuenta ya está conectada
  y no duplica los tokens ni genera eventos duplicados
```

**DoD**: Documentación de endpoint y flujo OAuth actualizada; tests de integración pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Alto

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ⚠️ Small (el flujo OAuth con manejo de errores y revocación es considerable — valorar separar la conexión del manejo de reconexión) · ✅ Testeable

**Asumidos**: OAuth 2.0 con Google como mecanismo de autorización [asumido — estándar para Google Calendar API]. Token almacenado y refrescado en back [asumido].

---

### HU-13: Crear evento en Google Calendar al crear una tarea con fecha
**Historia**: Como usuario de FlowSync, quiero que al crear una tarea con fecha límite se cree automáticamente un evento en mi Google Calendar para no tener que añadirlo manualmente.

**Criterios de aceptación**:
```gherkin
Given que creo una tarea con fecha límite y tengo Google Calendar conectado
When la tarea se guarda en FlowSync
Then el evento se crea en Google Calendar en un plazo máximo de 5 segundos [asumido: SLA de sincronización]
  y el título del evento corresponde al título de la tarea

Given que creo una tarea sin fecha límite y tengo Google Calendar conectado
When la tarea se guarda
Then no se crea ningún evento en Google Calendar
  y la tarea aparece normalmente en FlowSync

Given que creo una tarea con fecha pero el token de Google está caducado
When FlowSync intenta crear el evento
Then el evento se encola para reintento [asumido: mecanismo de reintentos]
  y el usuario ve la tarea en FlowSync aunque con un indicador de "pendiente de sincronizar"

Given que Google Calendar API devuelve error al crear el evento
When el sistema recibe el error
Then la tarea se guarda igualmente en FlowSync
  y el usuario recibe un aviso de que la sincronización con Calendar falló para esa tarea
```

**DoD**: Documentación de integración actualizada; tests de integración con mock de Google Calendar API pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Alto

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ⚠️ Small (incluye manejo de reintentos, tokens caducados y estados de sincronización — es la HU más densa del MVP) · ✅ Testeable

**Asumidos**: SLA de sincronización de 5 segundos [asumido]. Mecanismo de cola/reintento en back [asumido]. Indicador visual de estado de sincronización [asumido].

---

### HU-14: Actualizar evento en Google Calendar al editar una tarea
**Historia**: Como usuario de FlowSync, quiero que al editar la fecha o el título de una tarea el evento correspondiente en Google Calendar se actualice automáticamente para mantener ambos sistemas alineados.

**Criterios de aceptación**:
```gherkin
Given que edito el título de una tarea que ya tiene evento en Calendar
When guardo los cambios
Then el evento en Google Calendar refleja el nuevo título
  y no se crea un evento duplicado

Given que edito una tarea y elimino su fecha límite
When los cambios se guardan
Then el evento en Google Calendar se elimina
  y la tarea permanece en FlowSync sin fecha

Given que edito una tarea pero Google Calendar API está caída
When FlowSync intenta actualizar el evento
Then la tarea se actualiza en FlowSync
  y se muestra al usuario que la sincronización con Calendar está pendiente

Given que edito una tarea y el evento fue borrado manualmente por el usuario en Google Calendar
When FlowSync intenta actualizar ese evento inexistente
Then el sistema recrea el evento en Calendar con los datos actuales
  y no lanza un error 404 sin tratar al usuario [asumido: gestión de evento huérfano]
```

**DoD**: Documentación de integración actualizada; tests pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Alto

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ⚠️ Small (depende de HU-13 para el mapeo evento↔tarea) · ✅ Testeable

**Asumidos**: El sistema almacena el ID del evento de Google Calendar por tarea para actualizarlo [asumido]. Gestión de evento huérfano (recreación) [asumido].

---

### HU-15: Leer eventos de Google Calendar y reflejarlos en FlowSync
**Historia**: Como usuario de FlowSync, quiero que los eventos que ya tengo en Google Calendar aparezcan en mi vista de tareas de FlowSync para tener una visión unificada de mis compromisos.

**Criterios de aceptación**:
```gherkin
Given que tengo Google Calendar conectado con 50 eventos futuros
When accedo a la vista de tareas de FlowSync
Then solo se importan los eventos dentro de un rango de fechas relevante (ej. próximos 30 días) [asumido: rango a definir]
  y no se importan eventos del pasado sin acción explícita

Given que un evento importado de Google Calendar es modificado en Calendar
When FlowSync sincroniza
Then el estado del evento en FlowSync refleja los cambios
  y no mantiene datos obsoletos

Given que tengo un evento en Google Calendar que es una tarea ya creada en FlowSync
When FlowSync sincroniza
Then el sistema detecta la correspondencia y no crea un duplicado
  manteniendo una única entrada vinculada [asumido: deduplicación por ID de evento]

Given que el usuario borra un evento directamente en Google Calendar
When FlowSync sincroniza en el siguiente ciclo
Then la tarea correspondiente en FlowSync se marca o notifica como huérfana
  y el usuario decide qué hacer con ella [asumido: no se borra automáticamente en FlowSync]
```

**DoD**: Documentación de integración actualizada; tests de integración con mock pasados; PR revisado y aprobado; OpenAPI spec archivado tras merge.

**Dificultad**: Alto

**INVEST**: ✅ Independiente · ✅ Negociable · ✅ Valiosa · ✅ Estimable · ⚠️ Small (la lectura bidireccional con deduplicación y gestión de huérfanos es compleja — esta HU puede ser la candidata a dividirse en "importación inicial" vs "sincronización continua") · ✅ Testeable

**Asumidos**: Rango de importación de 30 días futuros [asumido]. Deduplicación por ID de evento de Google [asumido]. Tarea huérfana no se borra automáticamente [asumido].

---

## Resumen del Backlog por Dificultad

| ID | Historia | Dificultad | INVEST |
|----|----------|------------|--------|
| HU-01 | Registro con email y contraseña | Bajo | ✅ |
| HU-02 | Inicio de sesión | Bajo | ✅ |
| HU-03 | Cierre de sesión | Bajo | ✅ |
| HU-04 | Crear tarea | Bajo | ✅ |
| HU-06 | Eliminar tarea | Bajo | ✅ |
| HU-07 | Cambiar estado de tarea | Bajo | ✅ |
| HU-08 | Filtrar tareas por estado | Bajo | ✅ |
| HU-09 | Ordenar por fecha límite | Bajo | ✅ |
| HU-10 | Exportar tareas a CSV | Bajo | ✅ |
| HU-11 | Campos del export | Bajo | ✅ |
| HU-05 | Editar tarea | Medio | ⚠️ Small |
| HU-12 | Conectar Google Calendar (OAuth) | Alto | ⚠️ Small |
| HU-13 | Crear evento en Calendar | Alto | ⚠️ Small |
| HU-14 | Actualizar evento en Calendar | Alto | ⚠️ Small |
| HU-15 | Leer Calendar → FlowSync | Alto | ⚠️ Small |

> Las HU-12 a HU-15 cumplen INVEST pero el campo **Small** está bajo vigilancia: la complejidad de la integración con Google Calendar API (manejo de tokens, reintentos, deduplicación, eventos huérfanos) puede superar la capacidad de un sprint de 2 semanas para 1 persona si se abordan todas juntas. **Recomendación de PO: priorizar HU-12 y HU-13 en el primer sprint de sincronización, y HU-14 y HU-15 en el siguiente.**

---

## Razonamiento y Transparencia

**Sobre la descomposición:** El PRD define 5 bloques de MVP. Los he descompuesto en 15 historias priorizando que cada una entregue valor de forma independiente y sea verificable en aislamiento. Las historias de autenticación (HU-01 a HU-03) se separan porque tienen ciclos de vida distintos: registro, login y logout tienen criterios de seguridad diferentes y pueden ser testeados y desplegados independientemente.

**Sobre los criterios de aceptación (no happy path):** Me centré en: errores de validación de entrada, fallos de red/servidor, estados vacíos, comportamiento con sesiones expiradas, concurrencia, y datos edge-case (caracteres especiales, campos vacíos). Estos escenarios son los que más frecuentemente causan bugs en producción y los que QA necesita cubrir explícitamente. Evité los happy path que el equipo de ingeniería construirá por defecto.

**Sobre los [asumidos]:** El PRD no especifica el modelo de estados de tareas, el SLA de sincronización, el formato de exportación, el comportamiento ante eventos huérfanos ni el rango de importación de Calendar. Los marqué explícitamente para que el PO los valide antes de que el equipo de desarrollo los implemente — son decisiones de producto, no de ingeniería.

**Sobre INVEST y el campo Small:** Las historias de sincronización con Google Calendar son las que presentan mayor riesgo de incumplir Small. La integración OAuth + manejo de tokens + lógica de deduplicación + gestión de errores de API de terceros es inherentemente más voluminosa que el CRUD local. Lo he marcado como advertencia (⚠️) en lugar de descomponerlas más porque el PRD define la sincronización como una feature atómica desde el punto de vista de valor al usuario — partir "conectar Calendar" de "crear evento" rompería la narrativa de valor.

**Sobre el rol "Product Owner":** HU-11 usa el rol de PO porque la decisión de qué campos incluir en el export responde a una necesidad de métrica de adopción del propio equipo de producto, no a una necesidad expresada por el usuario final en el PRD.
