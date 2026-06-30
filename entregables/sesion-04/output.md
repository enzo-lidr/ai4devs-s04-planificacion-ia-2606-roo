# Backlog inicial — FlowSync MVP (User Stories)

> **Fuente**: `docs/PRD.md` (FlowSync MVP v1.0).
> **Alcance**: solo funcionalidades del MVP descritas en la sección 3 del PRD. No se inventan features fuera del documento.
> **Supuesto marcado**: el *rol* de las historias es **profesional del conocimiento** (knowledge worker, sección 2 del PRD). Lo abrevio como **"usuario"** salvo cuando aún no tiene cuenta, donde uso **"visitante"**. _(asumido)_

---

## Mini-guía de Scrum para leer este backlog

Como esto va dirigido a alguien con experiencia básica en agilidad, dejo el vocabulario claro antes del backlog:

- **Historia de usuario (user story)**: la unidad más pequeña de valor que se puede entregar y demostrar. No es una especificación técnica, sino una promesa de conversación: describe *quién* quiere *qué* y *para qué*. Formato canónico: `Como [rol], quiero [acción], para [beneficio]`. El "para" es lo más importante: justifica por qué la historia merece estar en el backlog.
- **Épica**: una historia grande que agrupa varias historias relacionadas. No se implementa de una sola vez; se "rompe" en historias más pequeñas. Aquí uso las épicas para agrupar por módulo funcional del PRD.
- **Criterios de aceptación (Acceptance Criteria, AC)**: las condiciones concretas y verificables que deben cumplirse para considerar la historia "terminada". Sirven para tres cosas: alinear expectativas con Producto, guiar al desarrollo y dar a QA la base de los casos de prueba. Los escribo en formato **Given/When/Then** (Gherkin), donde *Given* es el contexto previo, *When* la acción del usuario y *Then* el resultado observable. La clave es que sean **verificables**, no genéricos: "el sistema funciona bien" no es un AC; "se muestra el mensaje X" sí lo es.
- **INVEST**: heurística para una buena historia — **I**ndependiente, **N**egociable, **V**aliosa, **E**stimable, **S**mall (pequeña), **T**esteable. He intentado que cada historia sea testeable e independiente; donde hay dependencias técnicas reales (p. ej. la sincronización depende de la conexión OAuth), lo indico explícitamente.
- **Definition of Ready / Done**: una historia está *Ready* cuando el equipo entiende qué se pide y puede estimarla (estos AC ayudan a eso); está *Done* cuando todos sus AC se cumplen y pasa las pruebas. El detalle fino (criterio de ordenación, formato exacto de fechas, etc.) se cierra en el **refinamiento** del backlog, no en este documento.

> **Nota de alcance**: el PRD marca como *fuera del MVP* equipos/tareas compartidas, calendarios no-Google, notificaciones push/email, app nativa, etiquetas/proyectos/subtareas y recordatorios propios. Ninguna historia de este backlog cubre eso. La **sincronización inversa** (editar el evento en Google y que cambie la tarea) se deja fuera porque el PRD la condiciona a un *spike* técnico previo (secciones 3.5 y 7); aquí solo se modela la dirección **FlowSync → Google Calendar**.

---

## Épica 1 — Autenticación y gestión de cuenta

*Cubre la sección 3.1 del PRD. Es la puerta de entrada: sin cuenta no hay tareas, y el PRD exige aislamiento estricto de datos entre usuarios.*

### US-01 · Registro con email y contraseña

**Historia de usuario**
Como visitante, quiero crear una cuenta con mi email y contraseña, para empezar a gestionar mis tareas en FlowSync.

**Criterios de aceptación (Given/When/Then)**
- Given que estoy en la pantalla de registro con un email no registrado, When introduzco un email válido y una contraseña de 8 o más caracteres y confirmo, Then se crea mi cuenta y quedo autenticado.
- Given que introduzco una contraseña de menos de 8 caracteres, When intento registrarme, Then el registro se rechaza y veo un mensaje claro que indica el mínimo de 8 caracteres (no un error técnico).
- Given que el email que introduzco ya está registrado, When intento registrarme, Then el sistema me lo indica y me ofrece ir a la pantalla de inicio de sesión.
- Given que introduzco un email con formato inválido, When intento registrarme, Then veo un mensaje de validación comprensible y no se crea ninguna cuenta.
- Given que mi registro se completa con éxito, When entro por primera vez, Then llego a una pantalla de bienvenida (onboarding mínimo) que explica en una frase qué hace FlowSync y me invita a crear mi primera tarea.

### US-02 · Inicio de sesión

**Historia de usuario**
Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas de forma privada.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo una cuenta, When introduzco mi email y contraseña correctos, Then quedo autenticado mediante un token de acceso y veo mi listado de tareas.
- Given que introduzco una contraseña incorrecta o un email inexistente, When intento iniciar sesión, Then veo un mensaje de credenciales inválidas sin revelar cuál de los dos campos falla.
- Given que estoy autenticado, When recargo la aplicación dentro de la validez de mi token, Then sigo autenticado sin tener que volver a introducir mis credenciales.
- Given que no estoy autenticado, When intento acceder directamente a una vista de tareas, Then se me redirige a la pantalla de inicio de sesión.

### US-03 · Cierre de sesión

**Historia de usuario**
Como usuario autenticado, quiero cerrar sesión, para proteger mis datos cuando uso un dispositivo compartido.

**Criterios de aceptación (Given/When/Then)**
- Given que estoy autenticado, When pulso "cerrar sesión", Then mi token de acceso se invalida y vuelvo a la pantalla de inicio de sesión.
- Given que he cerrado sesión, When intento acceder a una vista de tareas usando el navegador (botón atrás o URL directa), Then se me exige volver a iniciar sesión y no veo datos previos.
- Given que cerré sesión en este dispositivo, When otro usuario inicia sesión en el mismo navegador, Then solo ve sus propios datos y nunca los míos.

---

## Épica 2 — Gestión de tareas (CRUD)

*Cubre la sección 3.2 del PRD. Es el núcleo del producto. Una tarea tiene como mínimo: título, descripción opcional, estado y fecha límite opcional.*

### US-04 · Crear una tarea

**Historia de usuario**
Como usuario, quiero crear una tarea indicando al menos un título, para registrar un pendiente sin fricción.

**Criterios de aceptación (Given/When/Then)**
- Given que estoy en mi listado de tareas, When creo una tarea con solo un título, Then la tarea se guarda y aparece en el listado con estado `pending`.
- Given que estoy creando una tarea, When dejo el título vacío, Then no se guarda y veo un mensaje que indica que el título es obligatorio.
- Given que estoy creando una tarea, When añado además descripción y/o fecha límite (ambas opcionales), Then esos datos se guardan y se muestran en la tarea.
- Given que acabo de crear una tarea con éxito, When se confirma el guardado, Then la veo reflejada en el listado sin necesidad de recargar la página.

### US-05 · Editar una tarea

**Historia de usuario**
Como usuario, quiero editar cualquier campo de una tarea existente, para mantenerla al día cuando cambian los detalles.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo una tarea, When modifico su título, descripción o fecha límite y guardo, Then los cambios persisten y se reflejan en el listado.
- Given que estoy editando una tarea, When borro el título y guardo, Then la edición se rechaza con un mensaje de campo obligatorio y la tarea conserva su valor anterior.
- Given que edito una tarea, When cancelo antes de guardar, Then la tarea mantiene sus valores originales sin cambios.
- Given que una tarea tiene fecha límite y está conectada a Google Calendar, When cambio esa fecha, Then se dispara la actualización del evento asociado (ver US-11).

### US-06 · Cambiar el estado de una tarea

**Historia de usuario**
Como usuario, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar en qué punto está mi trabajo.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo una tarea en `pending`, When la marco como completada, Then su estado pasa a `completed` y el cambio se refleja inmediatamente en el listado.
- Given que tengo una tarea en cualquier estado, When la archivo, Then su estado pasa a `archived` y deja de aparecer en la vista por defecto de tareas activas.
- Given que tengo una tarea `completed`, When la reabro, Then vuelve a `pending`.
- Given que una tarea con fecha límite está sincronizada con Google Calendar, When la marco como `completed` o `archived`, Then se dispara la actualización/eliminación del evento asociado (ver US-11).

### US-07 · Borrar una tarea

**Historia de usuario**
Como usuario, quiero borrar una tarea, para eliminar definitivamente algo que ya no necesito registrar.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo una tarea, When elijo borrarla y confirmo, Then desaparece de mi listado de forma permanente.
- Given que pulso borrar, When aún no he confirmado, Then la tarea no se elimina (se exige confirmación explícita para evitar borrados accidentales).
- Given que borro una tarea que tenía un evento en Google Calendar, When se completa el borrado, Then se dispara la eliminación del evento asociado (ver US-11).

---

## Épica 3 — Organización y filtrado

*Cubre la sección 3.3 del PRD. Con varias tareas, el usuario necesita encontrarlas y centrarse en lo de "hoy".*

### US-08 · Ver, ordenar y filtrar el listado de tareas

**Historia de usuario**
Como usuario, quiero ver mis tareas ordenadas por relevancia y poder filtrarlas por estado, para centrarme en lo que importa hoy sin perderme entre pendientes.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo varias tareas, When abro mi listado, Then veo por defecto las tareas más relevantes para "hoy" primero. _(El criterio exacto de ordenación se cierra en refinamiento, según indica el PRD.)_
- Given que estoy en el listado, When aplico el filtro "pendientes", Then solo veo tareas en estado `pending`; lo mismo aplica para `completed` y `archived`.
- Given que tengo un filtro aplicado, When lo quito, Then vuelvo a ver el conjunto por defecto de tareas.
- Given que no tengo ninguna tarea (cuenta nueva o todas archivadas), When abro el listado, Then veo un estado vacío con una invitación clara a crear mi primera tarea.
- Given que tengo hasta 200 tareas, When abro el listado, Then carga en menos de 1 segundo (requisito no funcional, sección 4 del PRD).

---

## Épica 4 — Exportación

*Cubre la sección 3.4 del PRD. Los usuarios quieren poder llevarse sus datos (portabilidad).*

### US-09 · Exportar tareas a CSV

**Historia de usuario**
Como usuario, quiero exportar mis tareas a un archivo CSV, para tener una copia de mis datos fuera de FlowSync.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo tareas, When solicito la exportación, Then se descarga un archivo CSV que incluye, como mínimo, título, descripción, estado y fecha límite de cada tarea.
- Given que el CSV se genera, When lo abro en una hoja de cálculo, Then cada tarea es una fila y los campos están correctamente separados (sin romperse por comas, saltos de línea o tildes en el contenido).
- Given que no tengo ninguna tarea, When solicito la exportación, Then obtengo un CSV solo con la fila de cabeceras (o un mensaje claro de que no hay nada que exportar).
- Given que tengo tareas en cualquier estado, When exporto, Then el CSV solo contiene mis propias tareas y ninguna de otro usuario.

---

## Épica 5 — Sincronización con Google Calendar

*Cubre la sección 3.5 del PRD. Es la funcionalidad diferenciadora y, según el propio PRD (secciones 3.5 y 7), la más compleja y arriesgada: rate limits, zonas horarias y casos límite. La dirección modelada en el MVP es **FlowSync → Google Calendar**. Estas historias dependen funcionalmente de US-13 (la conexión OAuth) y conviene refinarlas tras el spike técnico que recomienda el PRD.*

### US-10 · Conectar la cuenta de Google y reflejar tareas como eventos

**Historia de usuario**
Como usuario, quiero conectar mi cuenta de Google a FlowSync, para que mis tareas con fecha límite aparezcan automáticamente como eventos en mi Google Calendar.

**Criterios de aceptación (Given/When/Then)**
- Given que estoy autenticado y sin Google conectado, When inicio la conexión, Then se me lleva al flujo de autorización OAuth de Google y, al aceptar, FlowSync queda autorizado para leer y escribir en mi calendario.
- Given que acabo de conectar Google, When la conexión se confirma, Then mis tareas existentes con fecha límite se crean como eventos en mi Google Calendar.
- Given que tengo Google conectado, When creo o asigno una fecha límite a una tarea, Then aparece el evento correspondiente en mi calendario.
- Given que una tarea no tiene fecha límite, When existe en FlowSync, Then no se crea ningún evento en el calendario para ella.
- Given que la autorización OAuth se concede, When FlowSync almacena mis tokens de Google, Then se guardan de forma segura y nunca son accesibles por otro usuario (requisito de privacidad, sección 4).

### US-11 · Propagar cambios de la tarea al evento de calendario (con resiliencia)

**Historia de usuario**
Como usuario con Google conectado, quiero que los cambios en mis tareas se reflejen en sus eventos de calendario, para no tener que mantener ambas herramientas a mano.

**Criterios de aceptación (Given/When/Then)**
- Given que una tarea con fecha límite tiene un evento en el calendario, When cambio su fecha en FlowSync, Then el evento correspondiente se actualiza a la nueva fecha.
- Given que una tarea con evento está en el calendario, When la marco como `completed`/`archived` o la borro, Then el evento asociado se elimina o se marca según corresponda.
- Given que la API de Google no está disponible o devuelve un error, When intento un cambio que debería sincronizarse, Then la tarea se guarda igualmente en FlowSync y la sincronización se reintenta más tarde.
- Given que una operación de sincronización falla, When ocurre el error, Then queda registrado en logs para poder diagnosticarlo (observabilidad, sección 4).
- Given que reintento una sincronización previamente fallida, When la API vuelve a estar disponible, Then el evento queda finalmente consistente con la tarea (sin crear eventos duplicados).

### US-12 · Desconectar la cuenta de Google

**Historia de usuario**
Como usuario, quiero desconectar mi cuenta de Google cuando quiera, para dejar de sincronizar sin perder mis tareas.

**Criterios de aceptación (Given/When/Then)**
- Given que tengo Google conectado, When elijo desconectar, Then FlowSync deja de sincronizar y se invalida/elimina su autorización sobre mi calendario.
- Given que acabo de desconectar Google, When reviso FlowSync, Then todas mis tareas siguen existiendo intactas (la desconexión no borra tareas).
- Given que estoy desconectado, When creo o edito una tarea con fecha límite, Then no se crea ni modifica ningún evento en Google Calendar.
- Given que vuelvo a conectar Google más tarde, When la conexión se restablece, Then mis tareas con fecha límite vuelven a poder reflejarse como eventos.

---

## Resumen y trazabilidad

| Épica | Historias | Sección del PRD |
| --- | --- | --- |
| 1 · Autenticación y cuenta | US-01, US-02, US-03 | 3.1 |
| 2 · Gestión de tareas (CRUD) | US-04, US-05, US-06, US-07 | 3.2 |
| 3 · Organización y filtrado | US-08 | 3.3 |
| 4 · Exportación | US-09 | 3.4 |
| 5 · Sincronización Google Calendar | US-10, US-11, US-12 | 3.5 |

**Total: 12 historias de usuario.**

**Pendientes para refinamiento** (decisiones que el PRD deja abiertas, no se resuelven en el backlog inicial):
- Criterio exacto de ordenación "por relevancia para hoy" (US-08).
- Tratamiento de zonas horarias entre fecha límite de tarea y hora del evento (riesgo de sección 7) → afecta a US-10 y US-11.
- Alcance de reintentos y backoff ante rate limits de Google (US-11) → conviene cerrarlo tras el spike técnico recomendado en el PRD.
- Sincronización inversa (Google → FlowSync): **fuera del MVP** hasta validar con spike.
