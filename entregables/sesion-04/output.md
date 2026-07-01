# 🗂️ Backlog inicial — FlowSync MVP

> Generado a partir de [`docs/PRD.md`](../../docs/PRD.md) con el prompt de
> [`prompt.md`](prompt.md). 18 historias de usuario organizadas en 5 módulos.

## 📌 Nota de desviaciones vs. la consigna del ejercicio

Este output **no es 100% "crudo sin pulir"**, ni cae en el rango de 8–12 historias,
ni el patrón poke-holes se quedó en 3–5 hallazgos como pide la consigna. Tampoco la
reflexión final se quedó en 4–6 líneas. Son 5 decisiones deliberadas, no descuidos:

| Consigna pide | Esta entrega | Por qué |
|---|---|---|
| 8–12 historias | **18** | Aplicamos el criterio INVEST "Small": ninguna historia debe superar 1–2 días de trabajo (una sesión de copiloto sin romper contexto). Dos historias iniciales de sincronización con Google Calendar medían ~5 días cada una — se dividieron en 6 historias más chicas y trazables en vez de mantenerlas grandes para encajar en el rango. El enunciado completo (`README.md` raíz, "Criterio de completitud") pide **"al menos** 8-12 user stories" — no es un techo cerrado, así que 18 no incumple la letra del criterio, solo excede la expectativa de tamaño típico. |
| Output crudo, sin pulir | Formato visual propio (badges, iconos, escenarios con nombre) | Decisión de estilo para que el backlog sea legible en refinamiento real; el contenido (AC, alcance, non-goals, métricas) es el que generó el prompt, no se reescribió a mano. |
| No estimar tiempos | Incluye estimación en días por historia | Como PO, la estimación es información que necesitaría para priorizar un sprint; se agregó a propósito sobre la consigna base. |
| 3–5 hallazgos en poke-holes | **6** hallazgos | El primer pase de poke-holes (Parte 3) se corrió **antes** de dividir MOD-005, sobre la historia original `HUSR-014` ("Sincronizar tareas con fecha como eventos del calendario", 5 días) — ese ID ya no existe con ese alcance en este backlog: aquí `HUSR-014` es "Desconectar cuenta de Google". `poke-holes.md` documenta esto y trae al final un mapeo de sus 6 hallazgos a las historias finales (`HUSR-013`–`HUSR-018`); no se recortó a 5 para no perder ninguno de los genuinos. |
| Reflexión de 4–6 líneas | **5 párrafos** (`reflexion.md`) | Pedido explícito del usuario en esta sesión; prioriza cubrir con detalle qué sorprendió, qué falló y el hallazgo de poke-holes que se había pasado por alto, en vez de recortar a 4–6 líneas literales. |

El razonamiento completo de cada decisión queda en `prompt.md`. Tras dos auditorías
(inicial y "ojos frescos"), quedaron resueltos con AC concretos: el umbral de
reintentos de `HUSR-018`, el vínculo tarea↔evento (`eventId`) entre `HUSR-015` y
`HUSR-016`, y el efecto de archivar sobre el evento sincronizado en `HUSR-009`. Sigue
abierto a propósito, como vacío de información: qué pasa con el **estado de
conexión** de la cuenta cuando `HUSR-018` detecta en background un token revocado
(señalado en las Notas de `HUSR-014`) y las race conditions entre operaciones
concurrentes (`HUSR-016`/`HUSR-017`, ver poke-holes #4).

---

## 📋 Resumen ejecutivo

**Producto:** FlowSync — app web de gestión de tareas personales sincronizada con Google Calendar.
**Objetivo del backlog:** descomponer el MVP del PRD en historias de usuario accionables, con criterios de aceptación verificables, para empezar el refinamiento con el equipo.

**Metodología**
- Formato de historia: **Como [rol], quiero [acción], para que [beneficio]**.
- Criterios de aceptación en **Gherkin** (Dado que / Cuando / Entonces / Y / Pero).
- Agrupación por **módulos** (`MOD-XXX`); historias con folio incremental (`HUSR-XXX`).
- Métricas **cuantificables** por módulo e historia, ancladas al PRD.
- Lo inferido y no literal en el PRD se marca como *(asumido)*.

**Leyenda**
- Prioridad: 🔴 Alta · 🟡 Media · 🟢 Baja
- Iconos: 🧩 módulo · ⏱️ estimación · 🎯 historia/métrica · 📝 descripción · ✅ criterios · 📌 notas · 🛠️ tareas · 📊 métricas · 🔗 dependencias

---

## 🗺️ Roadmap de historias

| ID | Título | Módulo | Prioridad | Estimación | Depende de |
|----|--------|--------|:---------:|:----------:|------------|
| HUSR-001 | Crear cuenta con email y contraseña | MOD-001 | 🔴 | 2 d | — |
| HUSR-002 | Iniciar sesión | MOD-001 | 🔴 | 1 d | HUSR-001 |
| HUSR-003 | Cerrar sesión | MOD-001 | 🟡 | 0.5 d | HUSR-002 |
| HUSR-004 | Pantalla de bienvenida (onboarding) | MOD-001 | 🟡 | 1 d | HUSR-001 |
| HUSR-005 | Crear una tarea | MOD-002 | 🔴 | 2 d | HUSR-002 |
| HUSR-006 | Ver el listado de mis tareas | MOD-002 | 🔴 | 2 d | HUSR-005 |
| HUSR-007 | Editar una tarea | MOD-002 | 🔴 | 2 d | HUSR-005 |
| HUSR-008 | Borrar una tarea | MOD-002 | 🟡 | 1 d | HUSR-005 |
| HUSR-009 | Cambiar el estado de una tarea | MOD-002 | 🔴 | 1 d | HUSR-005 |
| HUSR-010 | Filtrar tareas por estado | MOD-003 | 🟡 | 1 d | HUSR-006, HUSR-009 |
| HUSR-011 | Estado vacío y orden por defecto | MOD-003 | 🟡 | 1 d | HUSR-006 |
| HUSR-012 | Exportar tareas a CSV | MOD-004 | 🟢 | 1 d | HUSR-005, HUSR-006 |
| HUSR-013 | Conectar cuenta de Google (OAuth) | MOD-005 | 🔴 | 1.5 d | HUSR-002 |
| HUSR-014 | Desconectar cuenta de Google sin perder tareas | MOD-005 | 🟡 | 1 d | HUSR-013 |
| HUSR-015 | Crear evento al asignar fecha límite a una tarea | MOD-005 | 🔴 | 1.5 d | HUSR-013, HUSR-005 |
| HUSR-016 | Actualizar evento al cambiar la fecha de una tarea | MOD-005 | 🔴 | 1 d | HUSR-015, HUSR-007, HUSR-018 |
| HUSR-017 | Eliminar o marcar evento al completar/borrar tarea | MOD-005 | 🔴 | 1 d | HUSR-015, HUSR-008, HUSR-009 |
| HUSR-018 | Manejo de fallos de sincronización: reintento y logs | MOD-005 | 🔴 | 2 d | HUSR-015, HUSR-013 |

### ⏱️ Esfuerzo estimado por módulo

| Módulo | Historias | Esfuerzo |
|--------|:---------:|:--------:|
| MOD-001 · Autenticación y cuenta | 4 | 4.5 d |
| MOD-002 · Gestión de tareas (CRUD) | 5 | 8 d |
| MOD-003 · Organización y filtrado | 2 | 2 d |
| MOD-004 · Exportación | 1 | 1 d |
| MOD-005 · Sincronización Google Calendar | 6 | 8 d |
| **Total MVP** | **18** | **23.5 d** |

> ⚠️ MOD-005 concentra el 34% del esfuerzo y todo el riesgo técnico (OAuth, rate limits, zonas horarias). Se dividió en 6 historias de máximo 2 días (regla INVEST "Small") en vez de 2 historias grandes; el PRD §7 recomienda además un spike antes de comprometer su decomposición fina.

---

## MOD-001 · Autenticación y cuenta
*Objetivo: que cada usuario tenga una cuenta propia y privada antes de gestionar tareas.*
**📊 Métrica del módulo:** ≥95% de los registros iniciados se completan sin error bloqueante *(asumido)*.

### `HUSR-001` · Crear cuenta con email y contraseña

> 🧩 **MOD-001** Autenticación y cuenta  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** visitante, **quiero** crear una cuenta con mi email y contraseña, **para que** pueda guardar mis tareas de forma privada.

#### 📝 Descripción
El visitante necesita una cuenta propia antes de usar FlowSync; los datos de un usuario nunca son visibles para otro. El registro pide email y una contraseña de al menos 8 caracteres. Si el email ya existe, el sistema lo indica y ofrece ir al inicio de sesión.

#### ✅ Criterios de Aceptación

**Escenario 1 · Registro exitoso**
- **Dado que** soy un visitante sin cuenta
- **Cuando** envío un email válido y una contraseña de 8 caracteres o más
- **Entonces** el sistema crea mi cuenta
- **Y** me lleva a la pantalla de bienvenida

**Escenario 2 · Contraseña demasiado corta**
- **Dado que** estoy en el formulario de registro
- **Cuando** envío una contraseña de menos de 8 caracteres
- **Entonces** el sistema no crea la cuenta
- **Y** muestra un mensaje claro de validación (no un error técnico)

**Escenario 3 · Email ya registrado**
- **Dado que** ya existe una cuenta con ese email
- **Cuando** intento registrarme con el mismo email
- **Entonces** el sistema me avisa de que el email ya está en uso
- **Y** me ofrece ir al inicio de sesión

#### 📌 Notas adicionales
Privacidad estricta (PRD §4). Mensajes de error comprensibles. El registro del MVP es solo email/contraseña; OAuth de Google sirve para conectar el calendario, no para registrarse *(asumido)*. No hay verificación de email por enlace ni "recordar contraseña" en el MVP.

#### 🛠️ Tareas funcionales
- [ ] Permitir alta con email y contraseña válida
- [ ] Validar longitud mínima de contraseña (8) con mensaje claro
- [ ] Detectar email duplicado y ofrecer ir al login
- [ ] Tras el alta, llevar al usuario a la pantalla de bienvenida

#### 📊 Métricas medibles
- 🎯 100% de las contraseñas < 8 caracteres son rechazadas con mensaje comprensible *(PRD §3.1, §4)*
- 🎯 0 cuentas duplicadas creadas con un email ya registrado *(PRD §3.1)*
- 🎯 ≥95% de registros iniciados se completan sin error bloqueante *(asumido)*

#### 🔗 Relaciones / Dependencias
Ninguna — historia raíz del módulo MOD-001.

---

### `HUSR-002` · Iniciar sesión

> 🧩 **MOD-001** Autenticación y cuenta  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario registrado, **quiero** iniciar sesión con mi email y contraseña, **para que** pueda acceder a mis tareas privadas.

#### 📝 Descripción
El usuario que ya tiene cuenta introduce sus credenciales y obtiene acceso a su espacio de tareas. La sesión se mantiene mediante un token de acceso. Si las credenciales son incorrectas, recibe un mensaje claro sin pistas que comprometan la seguridad.

#### ✅ Criterios de Aceptación

**Escenario 1 · Inicio de sesión exitoso**
- **Dado que** tengo una cuenta registrada
- **Cuando** introduzco mi email y contraseña correctos
- **Entonces** el sistema me autentica y me lleva a mi listado de tareas
- **Y** mantiene mi sesión mediante un token de acceso

**Escenario 2 · Credenciales incorrectas**
- **Dado que** estoy en la pantalla de inicio de sesión
- **Cuando** introduzco un email o contraseña incorrectos
- **Entonces** el sistema no me autentica
- **Y** muestra un mensaje de error comprensible sin revelar qué dato falló

**Escenario 3 · Sesión persistente dentro de la vigencia del token**
- **Dado que** ya inicié sesión correctamente hace menos de 7 días *(vigencia asumida — el PRD no la define)*
- **Cuando** vuelvo a la aplicación
- **Entonces** accedo directamente a mis tareas sin volver a autenticarme
- **Pero** si pasaron 7 días o más, el sistema me pide iniciar sesión de nuevo

#### 📌 Notas adicionales
Sesión por access token (PRD §3.1). Mensajes de error claros (PRD §4). El PRD no define la vigencia exacta del token; se asume 7 días como valor de trabajo — debe confirmarse en refinamiento, no es un dato del PRD.

#### 🛠️ Tareas funcionales
- [ ] Autenticar con email + contraseña correctos y abrir la sesión
- [ ] Rechazar credenciales inválidas con mensaje genérico claro
- [ ] Mantener la sesión activa mientras el token sea válido

#### 📊 Métricas medibles
- 🎯 100% de los intentos con credenciales inválidas son rechazados *(PRD §3.1)*
- 🎯 El login con credenciales válidas resuelve en < 2 s *(asumido)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-001 (debe existir la cuenta para iniciar sesión).

---

### `HUSR-003` · Cerrar sesión

> 🧩 **MOD-001** Autenticación y cuenta  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 0.5 d

#### 🎯 Historia
> **Como** usuario autenticado, **quiero** cerrar sesión, **para que** nadie más pueda acceder a mis tareas desde mi dispositivo.

#### 📝 Descripción
El usuario puede terminar su sesión de forma explícita. Tras cerrar sesión, el acceso a las tareas requiere volver a autenticarse.

#### ✅ Criterios de Aceptación

**Escenario 1 · Cierre de sesión exitoso**
- **Dado que** estoy autenticado en FlowSync
- **Cuando** selecciono "cerrar sesión"
- **Entonces** el sistema termina mi sesión
- **Y** me lleva a la pantalla de inicio de sesión

**Escenario 2 · Acceso bloqueado tras cerrar sesión**
- **Dado que** acabo de cerrar sesión
- **Cuando** intento acceder a la vista de tareas
- **Entonces** el sistema me exige iniciar sesión de nuevo

**Escenario 3 · Token invalidado**
- **Dado que** cerré sesión
- **Cuando** se intenta reutilizar el token anterior
- **Entonces** el token ya no concede acceso *(asumido)*

#### 📌 Notas adicionales
Refuerza la privacidad de los datos (PRD §4). El detalle de invalidación del token queda a refinamiento *(asumido)*.

#### 🛠️ Tareas funcionales
- [ ] Ofrecer una acción visible de cerrar sesión
- [ ] Terminar la sesión y redirigir al login
- [ ] Impedir el acceso a tareas sin sesión activa

#### 📊 Métricas medibles
- 🎯 100% de los accesos a tareas tras cerrar sesión son bloqueados *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-002 (requiere una sesión activa para cerrarla).

---

### `HUSR-004` · Pantalla de bienvenida (onboarding mínimo)

> 🧩 **MOD-001** Autenticación y cuenta  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario recién registrado, **quiero** una pantalla de bienvenida que me explique qué hace FlowSync, **para que** entienda el valor del producto y cree mi primera tarea.

#### 📝 Descripción
Tras un registro exitoso, el usuario llega a un onboarding mínimo: una frase que explica qué hace FlowSync y una invitación a crear su primera tarea. Es la primera impresión y conecta con la propuesta de valor.

#### ✅ Criterios de Aceptación

**Escenario 1 · Llegada al onboarding**
- **Dado que** acabo de completar mi registro
- **Cuando** se crea mi cuenta
- **Entonces** veo una pantalla de bienvenida que explica en una frase qué hace FlowSync
- **Y** una invitación clara a crear mi primera tarea

**Escenario 2 · Crear la primera tarea desde el onboarding**
- **Dado que** estoy en la pantalla de bienvenida
- **Cuando** acepto la invitación a crear mi primera tarea
- **Entonces** el sistema me lleva al flujo de creación de tarea

**Escenario 3 · Continuar sin crear tarea**
- **Dado que** estoy en la pantalla de bienvenida
- **Cuando** decido no crear una tarea todavía
- **Entonces** accedo a mi listado de tareas, que muestra el estado vacío

#### 📌 Notas adicionales
Conecta con el criterio de éxito de que el usuario complete el flujo sin ayuda externa (PRD §6). El copy exacto de la frase queda a definición de producto *(asumido)*.

#### 🛠️ Tareas funcionales
- [ ] Mostrar la bienvenida tras el primer registro
- [ ] Explicar en una frase el valor de FlowSync
- [ ] Ofrecer acceso directo a crear la primera tarea
- [ ] Permitir continuar al listado (estado vacío) sin crear tarea

#### 📊 Métricas medibles
- 🎯 ≥70% de usuarios nuevos crean su primera tarea en la primera sesión *(asumido)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-001 · se enlaza con HUSR-005 (crear tarea) y HUSR-011 (estado vacío).

---

## MOD-002 · Gestión de tareas (CRUD)
*Objetivo: que el usuario pueda crear y administrar sus tareas con estados, el núcleo de FlowSync.*
**📊 Métrica del módulo:** el usuario puede crear, editar, cambiar de estado y borrar una tarea sin error en el 100% de los casos válidos *(PRD §3.2)*.

### `HUSR-005` · Crear una tarea

> 🧩 **MOD-002** Gestión de tareas (CRUD)  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** usuario, **quiero** crear una tarea indicando al menos un título, **para que** pueda registrar lo que tengo que hacer.

#### 📝 Descripción
El usuario crea una tarea con un título obligatorio; la descripción y la fecha límite son opcionales. Toda tarea nace en estado `pending`.

#### ✅ Criterios de Aceptación

**Escenario 1 · Crear tarea solo con título**
- **Dado que** estoy autenticado
- **Cuando** creo una tarea indicando solo el título
- **Entonces** la tarea se guarda con estado `pending`
- **Y** aparece en mi listado de tareas

**Escenario 2 · Crear tarea con todos los campos**
- **Dado que** estoy creando una tarea
- **Cuando** indico título, descripción y fecha límite
- **Entonces** la tarea se guarda con todos esos datos en estado `pending`

**Escenario 3 · Título vacío**
- **Dado que** estoy en el formulario de creación
- **Cuando** intento guardar sin título
- **Entonces** el sistema no crea la tarea
- **Y** muestra un mensaje claro indicando que el título es obligatorio

#### 📌 Notas adicionales
Estado inicial siempre `pending` (PRD §3.2). Descripción y fecha límite opcionales. La fecha límite es la que habilita la sincronización con el calendario (ver MOD-005).

#### 🛠️ Tareas funcionales
- [ ] Crear tarea con solo título
- [ ] Aceptar descripción y fecha límite opcionales
- [ ] Asignar estado `pending` al crear
- [ ] Rechazar creación sin título con mensaje claro

#### 📊 Métricas medibles
- 🎯 100% de las tareas creadas nacen en estado `pending` *(PRD §3.2)*
- 🎯 100% de los intentos sin título se rechazan con mensaje comprensible *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-002 · habilita HUSR-006 y HUSR-015 (si tiene fecha límite).

---

### `HUSR-006` · Ver el listado de mis tareas

> 🧩 **MOD-002** Gestión de tareas (CRUD)  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** usuario, **quiero** ver el listado de mis tareas, **para que** pueda saber de un vistazo qué tengo pendiente.

#### 📝 Descripción
El usuario ve sus tareas en una lista que carga rápido y muestra primero lo más relevante para "hoy". Solo ve sus propias tareas.

#### ✅ Criterios de Aceptación

**Escenario 1 · Ver tareas con y sin campos opcionales**
- **Dado que** tengo tareas creadas, algunas solo con título y otras con descripción y fecha límite
- **Cuando** abro la vista de tareas
- **Entonces** veo el listado completo, cada tarea con su título y estado siempre visibles
- **Y** la descripción y la fecha límite se muestran solo en las tareas que las tienen, sin espacios rotos ni errores en las que no

**Escenario 2 · Rendimiento del listado**
- **Dado que** tengo hasta 200 tareas
- **Cuando** cargo el listado
- **Entonces** se muestra en menos de 1 segundo

**Escenario 3 · Aislamiento por usuario**
- **Dado que** estoy autenticado
- **Cuando** veo mi listado
- **Entonces** solo aparecen mis tareas y nunca las de otro usuario

#### 📌 Notas adicionales
Rendimiento: <1 s con hasta 200 tareas (PRD §4). Orden por defecto con lo de "hoy" primero; el criterio exacto de orden queda a refinamiento (PRD §3.3). Privacidad estricta (PRD §4).

#### 🛠️ Tareas funcionales
- [ ] Listar las tareas del usuario autenticado
- [ ] Ordenar por defecto priorizando lo relevante para "hoy"
- [ ] Garantizar que un usuario nunca vea tareas de otro

#### 📊 Métricas medibles
- 🎯 El listado carga en < 1 s con hasta 200 tareas *(PRD §4)*
- 🎯 0 incidencias de fuga de tareas entre usuarios *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-005 · se relaciona con HUSR-010 (filtro) y HUSR-011 (estado vacío).

---

### `HUSR-007` · Editar una tarea

> 🧩 **MOD-002** Gestión de tareas (CRUD)  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** usuario, **quiero** editar cualquier campo de una tarea existente, **para que** pueda mantener mi información actualizada.

#### 📝 Descripción
El usuario puede modificar título, descripción y fecha límite de una tarea ya creada. Si la tarea está sincronizada y cambia su fecha, el evento del calendario se actualiza (ver HUSR-016).

#### ✅ Criterios de Aceptación

**Escenario 1 · Editar campos de una tarea**
- **Dado que** tengo una tarea creada
- **Cuando** modifico su título, descripción o fecha límite y guardo
- **Entonces** la tarea queda actualizada con los nuevos valores

**Escenario 2 · Quitar el título al editar**
- **Dado que** estoy editando una tarea
- **Cuando** intento guardar dejando el título vacío
- **Entonces** el sistema no guarda el cambio
- **Y** muestra un mensaje claro de que el título es obligatorio

**Escenario 3 · Cambio de fecha en tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento en Google Calendar
- **Cuando** cambio su fecha límite
- **Entonces** se actualiza el evento correspondiente en el calendario (ver HUSR-016)

#### 📌 Notas adicionales
La validación del título obligatorio se mantiene también al editar *(asumido, por coherencia con HUSR-005)*. El efecto sobre el calendario se detalla en MOD-005 (HUSR-016).

#### 🛠️ Tareas funcionales
- [ ] Permitir editar título, descripción y fecha límite
- [ ] Mantener la validación de título obligatorio
- [ ] Propagar el cambio de fecha al evento sincronizado

#### 📊 Métricas medibles
- 🎯 100% de las ediciones válidas persisten correctamente *(PRD §3.2)*
- 🎯 100% de los cambios de fecha en tareas sincronizadas actualizan su evento *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-005 · se relaciona con HUSR-016 (sincronización).

---

### `HUSR-008` · Borrar una tarea

> 🧩 **MOD-002** Gestión de tareas (CRUD)  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario, **quiero** borrar una tarea, **para que** mi lista solo contenga lo que de verdad me importa.

#### 📝 Descripción
El usuario puede eliminar una tarea. Si la tarea estaba reflejada como evento en Google Calendar, el evento se elimina (ver HUSR-017).

#### ✅ Criterios de Aceptación

**Escenario 1 · Borrar una tarea**
- **Dado que** tengo una tarea creada
- **Cuando** la borro
- **Entonces** la tarea desaparece de mi listado

**Escenario 2 · Borrado de tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento en Google Calendar
- **Cuando** la borro en FlowSync
- **Entonces** el evento correspondiente se elimina del calendario (ver HUSR-017)

**Escenario 3 · Confirmación antes de borrar**
- **Dado que** voy a borrar una tarea
- **Cuando** confirmo la acción de borrado
- **Entonces** la tarea se elimina de forma definitiva *(asumido)*

#### 📌 Notas adicionales
Borrar es distinto de archivar (estado `archived`, ver HUSR-009): archivar conserva la tarea, borrar la elimina. El paso de confirmación se asume para evitar borrados accidentales *(asumido)*.

#### 🛠️ Tareas funcionales
- [ ] Eliminar la tarea del listado del usuario
- [ ] Eliminar el evento asociado en el calendario si existía
- [ ] Pedir confirmación antes de borrar

#### 📊 Métricas medibles
- 🎯 100% de las tareas borradas que tenían evento eliminan también su evento *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-005 · se relaciona con HUSR-009 (archivar) y HUSR-017.

---

### `HUSR-009` · Cambiar el estado de una tarea

> 🧩 **MOD-002** Gestión de tareas (CRUD)  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario, **quiero** cambiar el estado de una tarea entre pendiente, completada y archivada, **para que** pueda reflejar mi progreso real.

#### 📝 Descripción
Cada tarea tiene un estado: `pending`, `completed` o `archived`. El usuario puede cambiarlo (por ejemplo, marcar como completada). Completar una tarea sincronizada afecta a su evento en el calendario (ver HUSR-017).

#### ✅ Criterios de Aceptación

**Escenario 1 · Marcar como completada**
- **Dado que** tengo una tarea en estado `pending`
- **Cuando** la marco como completada
- **Entonces** su estado pasa a `completed`

**Escenario 2 · Archivar una tarea**
- **Dado que** tengo una tarea
- **Cuando** la archivo
- **Entonces** su estado pasa a `archived` y deja de aparecer entre las activas *(asumido)*

**Escenario 3 · Completar tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento en Google Calendar
- **Cuando** la marco como completada
- **Entonces** su evento en el calendario se elimina o se marca según corresponda (ver HUSR-017)

#### 📌 Notas adicionales
Estados definidos en PRD §3.2 y glosario §8. Que las archivadas salgan de la vista activa es coherente con el estado vacío de HUSR-011 *(asumido)*.

#### 🛠️ Tareas funcionales
- [ ] Permitir transición entre `pending`, `completed` y `archived`
- [ ] Reflejar el nuevo estado en el listado
- [ ] Propagar "completada" al evento sincronizado (ver HUSR-017)
- [ ] Propagar "archivada" al evento sincronizado — eliminar el evento (ver HUSR-017)

#### 📊 Métricas medibles
- 🎯 100% de los cambios de estado se reflejan correctamente en el listado *(PRD §3.2)*
- 🎯 100% de las tareas completadas o archivadas que tenían evento actualizan/eliminan su evento *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-005 · se relaciona con HUSR-010 (filtro por estado) y HUSR-017.

---

## MOD-003 · Organización y filtrado
*Objetivo: que el usuario encuentre sus tareas fácilmente y sepa qué hacer cuando no tiene ninguna.*
**📊 Métrica del módulo:** el usuario localiza las tareas de un estado concreto en ≤1 acción de filtrado *(asumido)*.

### `HUSR-010` · Filtrar tareas por estado

> 🧩 **MOD-003** Organización y filtrado  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario, **quiero** filtrar mis tareas por estado, **para que** pueda concentrarme solo en las que me interesan en cada momento.

#### 📝 Descripción
El usuario puede ver solo las pendientes, solo las completadas o solo las archivadas, en lugar de toda la lista mezclada.

#### ✅ Criterios de Aceptación

**Escenario 1 · Filtrar por pendientes**
- **Dado que** tengo tareas en distintos estados
- **Cuando** filtro por estado `pending`
- **Entonces** el listado muestra únicamente mis tareas pendientes

**Escenario 2 · Cambiar de filtro**
- **Dado que** estoy viendo las tareas pendientes
- **Cuando** cambio el filtro a `completed`
- **Entonces** el listado muestra únicamente las completadas

**Escenario 3 · Filtro sin resultados**
- **Dado que** no tengo tareas en el estado seleccionado
- **Cuando** aplico ese filtro
- **Entonces** veo un mensaje de que no hay tareas en ese estado

#### 📌 Notas adicionales
Estados según PRD §3.2/§3.3. El filtro sin resultados se relaciona con el estado vacío de HUSR-011.

#### 🛠️ Tareas funcionales
- [ ] Filtrar el listado por `pending`, `completed` y `archived`
- [ ] Permitir cambiar de filtro sin recargar el flujo
- [ ] Mostrar mensaje claro cuando un filtro no tiene resultados

#### 📊 Métricas medibles
- 🎯 100% de los filtros muestran solo tareas del estado seleccionado *(PRD §3.3)*
- 🎯 El filtrado se aplica en < 1 s con hasta 200 tareas *(PRD §4, asumido para el filtro)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-006 y HUSR-009 · se relaciona con HUSR-011.

---

### `HUSR-011` · Estado vacío y orden por defecto del listado

> 🧩 **MOD-003** Organización y filtrado  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario sin tareas visibles, **quiero** ver una invitación a crear la primera y, cuando sí tengo tareas, verlas ordenadas con lo de hoy primero, **para que** siempre sepa cuál es mi siguiente paso.

#### 📝 Descripción
Cuando el usuario no tiene tareas (cuenta nueva o todas archivadas) ve un estado vacío con una invitación a crear la primera. Cuando sí tiene, el listado se ordena por defecto mostrando primero lo más relevante para "hoy".

#### ✅ Criterios de Aceptación

**Escenario 1 · Estado vacío en cuenta nueva**
- **Dado que** soy un usuario sin ninguna tarea
- **Cuando** abro el listado
- **Entonces** veo un estado vacío con una invitación clara a crear mi primera tarea

**Escenario 2 · Estado vacío por todas archivadas**
- **Dado que** todas mis tareas están archivadas
- **Cuando** abro la vista de tareas activas
- **Entonces** veo el estado vacío con la invitación a crear una tarea

**Escenario 3 · Orden por defecto**
- **Dado que** tengo varias tareas con distintas fechas
- **Cuando** abro el listado sin aplicar filtros
- **Entonces** las más relevantes para "hoy" aparecen primero

#### 📌 Notas adicionales
PRD §3.3. El criterio exacto de ordenación queda a decisión del equipo en refinamiento (declarado en el PRD). Se enlaza con el onboarding (HUSR-004).

#### 🛠️ Tareas funcionales
- [ ] Mostrar estado vacío con invitación cuando no hay tareas activas
- [ ] Cubrir tanto cuenta nueva como "todas archivadas"
- [ ] Ordenar por defecto priorizando lo relevante para "hoy"

#### 📊 Métricas medibles
- 🎯 100% de los casos sin tareas activas muestran el estado vacío con invitación *(PRD §3.3)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-006 · se relaciona con HUSR-004 y HUSR-009.

---

## MOD-004 · Exportación
*Objetivo: que el usuario pueda llevarse sus datos fuera de FlowSync.*
**📊 Métrica del módulo:** el archivo exportado incluye el 100% de las tareas del usuario con los campos requeridos *(PRD §3.4)*.

### `HUSR-012` · Exportar tareas a CSV

> 🧩 **MOD-004** Exportación  ·  🟢 **Prioridad:** Baja  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario, **quiero** exportar mis tareas a un archivo CSV, **para que** pueda llevarme mis datos y usarlos en otra herramienta.

#### 📝 Descripción
El usuario genera un archivo CSV con sus tareas. El archivo incluye, como mínimo, título, descripción, estado y fecha límite de cada tarea.

#### ✅ Criterios de Aceptación

**Escenario 1 · Exportación exitosa**
- **Dado que** tengo tareas creadas
- **Cuando** solicito exportar a CSV
- **Entonces** obtengo un archivo CSV con título, descripción, estado y fecha límite de cada tarea

**Escenario 2 · Exportar sin tareas**
- **Dado que** no tengo ninguna tarea
- **Cuando** solicito exportar a CSV
- **Entonces** obtengo un archivo solo con la cabecera de columnas *(asumido)*
- **Pero** el sistema no muestra ningún error

**Escenario 3 · Solo mis tareas**
- **Dado que** estoy autenticado
- **Cuando** exporto
- **Entonces** el archivo contiene únicamente mis tareas y ninguna de otro usuario

#### 📌 Notas adicionales
Campos mínimos definidos en PRD §3.4. El formato exacto del CSV (separador, codificación) queda a refinamiento *(asumido)*. Privacidad estricta (PRD §4).

#### 🛠️ Tareas funcionales
- [ ] Generar CSV con título, descripción, estado y fecha límite
- [ ] Incluir todas las tareas del usuario y solo las suyas
- [ ] Manejar el caso de exportar sin tareas sin error

#### 📊 Métricas medibles
- 🎯 100% de las tareas del usuario aparecen en el CSV con los 4 campos mínimos *(PRD §3.4)*
- 🎯 0 tareas de otros usuarios presentes en el archivo *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-005 y HUSR-006.

---

## MOD-005 · Sincronización con Google Calendar
*Objetivo: la funcionalidad diferenciadora — que las tareas con fecha aparezcan y se mantengan como eventos en Google Calendar. Es el módulo de mayor riesgo del MVP (PRD §7). Dividido en 6 historias de máximo 2 días (regla INVEST "Small") en vez de 2 historias grandes.*
**📊 Métrica del módulo:** <5% de las operaciones de sincronización fallan de forma no recuperable; ≥40% de los usuarios registrados conectan su Google Calendar *(PRD §6)*.

### `HUSR-013` · Conectar cuenta de Google (OAuth)

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1.5 d

#### 🎯 Historia
> **Como** usuario, **quiero** conectar mi cuenta de Google, **para que** FlowSync pueda reflejar mis tareas en mi calendario.

#### 📝 Descripción
El usuario autoriza a FlowSync el acceso a su Google Calendar mediante OAuth. Los tokens obtenidos se almacenan de forma segura. Si el usuario cancela o deniega el permiso, FlowSync no queda conectado pero conserva su sesión y sus tareas intactas.

#### ✅ Criterios de Aceptación

**Escenario 1 · Conexión exitosa**
- **Dado que** estoy autenticado y no he conectado Google
- **Cuando** autorizo el acceso vía OAuth de Google
- **Entonces** FlowSync queda conectado a mi Google Calendar
- **Y** queda habilitada la sincronización de tareas con fecha

**Escenario 2 · El usuario cancela la autorización**
- **Dado que** inicio la conexión con Google
- **Cuando** cancelo o deniego el permiso en la pantalla de Google
- **Entonces** FlowSync no queda conectado
- **Y** me lo indica sin perder mis tareas ni mi sesión

**Escenario 3 · Almacenamiento seguro del token**
- **Dado que** la conexión con Google fue exitosa
- **Cuando** un usuario B autenticado intenta leer o acceder al token de acceso del usuario A
- **Entonces** la petición se rechaza (403/404)
- **Y** el token nunca aparece en texto plano en logs ni en respuestas de ninguna API

#### 📌 Notas adicionales
OAuth 2.0 con Google (PRD §3.5, §5). Tokens almacenados de forma segura (PRD §4). El setup de Google Cloud Console es un riesgo de tiempo conocido (PRD §7) — **vacío de información**: el PRD no detalla el mecanismo exacto de almacenamiento seguro del token; queda a refinamiento técnico, no se inventa aquí.

#### 🛠️ Tareas funcionales
- [ ] Permitir conectar la cuenta de Google vía OAuth
- [ ] Manejar la cancelación/denegación de permisos sin romper la sesión
- [ ] Almacenar los tokens de Google de forma segura

#### 📊 Métricas medibles
- 🎯 ≥40% de los usuarios registrados conectan su Google Calendar *(PRD §6)*
- 🎯 0 tokens de Google almacenados de forma no segura *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-002 · es prerrequisito de HUSR-014 y HUSR-015.

---

### `HUSR-014` · Desconectar cuenta de Google sin perder tareas

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🟡 **Prioridad:** Media  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario con Google conectado, **quiero** poder desconectar mi cuenta en cualquier momento, **para que** controle cuándo FlowSync sincroniza sin perder mi información.

#### 📝 Descripción
El usuario puede desconectar su cuenta de Google. Al hacerlo, FlowSync deja de sincronizar pero no borra ninguna tarea ya creada.

#### ✅ Criterios de Aceptación

**Escenario 1 · Desconexión sin perder tareas**
- **Dado que** tengo mi cuenta de Google conectada
- **Cuando** desconecto Google
- **Entonces** FlowSync deja de sincronizar
- **Pero** mis tareas ya creadas se conservan intactas

**Escenario 2 · Tareas existentes quedan sin evento activo**
- **Dado que** desconecté mi cuenta de Google
- **Cuando** reviso una tarea que antes tenía evento
- **Entonces** la tarea sigue existiendo en FlowSync sin sincronizarse hasta reconectar

**Escenario 3 · Confirmación antes de desconectar**
- **Dado que** voy a desconectar mi cuenta de Google
- **Cuando** confirmo la acción
- **Entonces** la desconexión se aplica de forma inmediata *(asumido)*

#### 📌 Notas adicionales
La desconexión no borra tareas (PRD §3.5). **Vacío de información 1**: el PRD no define qué pasa con los eventos ya creados en Google al desconectar (¿se eliminan o quedan huérfanos?); no se asume, se deja como pregunta abierta para refinamiento. **Vacío de información 2**: esta historia solo cubre la desconexión **explícita** iniciada por el usuario. Si `HUSR-018` detecta en background que el token fue revocado (Escenario 4 de esa historia), **ninguna historia define** si la cuenta debe marcarse aquí como desconectada automáticamente o si queda "conectada" en la UI mientras el job falla en silencio — es una decisión de producto pendiente, no técnica, y no se decide aquí.

#### 🛠️ Tareas funcionales
- [ ] Permitir desconectar Google en cualquier momento
- [ ] Conservar las tareas tras desconectar
- [ ] Detener la sincronización activa al desconectar

#### 📊 Métricas medibles
- 🎯 0 tareas perdidas al desconectar la cuenta de Google *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-013.

---

### `HUSR-015` · Crear evento al asignar fecha límite a una tarea

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1.5 d

#### 🎯 Historia
> **Como** usuario con Google conectado, **quiero** que mi tarea con fecha límite aparezca como evento en mi calendario, **para que** no tenga que copiarla a mano.

#### 📝 Descripción
Con la cuenta de Google conectada, toda tarea con fecha límite (al crearse o al asignarle fecha después) genera un evento correspondiente en el Google Calendar del usuario. La dirección del MVP es FlowSync → Google Calendar.

#### ✅ Criterios de Aceptación

**Escenario 1 · Tarea con fecha se refleja como evento**
- **Dado que** tengo Google conectado
- **Cuando** creo una tarea con fecha límite
- **Entonces** aparece un evento correspondiente en mi Google Calendar
- **Y** FlowSync guarda el identificador del evento (`eventId`) devuelto por Google, asociado a esa tarea

**Escenario 2 · Asignar fecha a una tarea existente sin fecha**
- **Dado que** tengo una tarea sin fecha límite
- **Cuando** le asigno una fecha límite
- **Entonces** se crea el evento correspondiente en el calendario y se guarda su `eventId` asociado a la tarea

**Escenario 3 · Tarea sin Google conectado**
- **Dado que** no tengo mi cuenta de Google conectada
- **Cuando** creo una tarea con fecha límite
- **Entonces** la tarea se guarda normalmente en FlowSync sin generar ningún evento

#### 📌 Notas adicionales
PRD §3.5. **Vacío de información**: el PRD no define la regla exacta de conversión entre la fecha límite (sin hora) y el evento de calendario (zona horaria, hora del día o evento de "todo el día"); el PRD §7 lo marca como riesgo abierto — no se inventa la regla aquí, se deja para spike técnico. El vínculo tarea↔evento vive en el `eventId` persistido junto a la tarea — sin ese dato, `HUSR-016` y `HUSR-017` no pueden identificar qué evento actualizar o eliminar.

#### 🛠️ Tareas funcionales
- [ ] Crear evento al crear una tarea con fecha límite
- [ ] Crear evento al asignar fecha límite a una tarea existente
- [ ] Persistir el `eventId` de Google Calendar devuelto, asociado a la tarea
- [ ] No generar evento si el usuario no tiene Google conectado

#### 📊 Métricas medibles
- 🎯 100% de las tareas con fecha generan su evento cuando la API responde y Google está conectado *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-013 y HUSR-005 · es prerrequisito de HUSR-016, HUSR-017 y HUSR-018.

---

### `HUSR-016` · Actualizar evento al cambiar la fecha de una tarea

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario con una tarea sincronizada, **quiero** que su evento se actualice cuando cambio la fecha límite, **para que** mi calendario siempre refleje la fecha correcta.

#### 📝 Descripción
Si una tarea con fecha ya está reflejada como evento, cambiar su fecha límite en FlowSync actualiza ese evento en Google Calendar en vez de crear uno nuevo.

#### ✅ Criterios de Aceptación

**Escenario 1 · Cambio de fecha actualiza el evento**
- **Dado que** una tarea ya está reflejada como evento (con su `eventId` guardado, ver `HUSR-015`)
- **Cuando** cambio su fecha límite en FlowSync
- **Entonces** se actualiza ese mismo evento (identificado por su `eventId`) en el calendario, sin crear uno nuevo

**Escenario 2 · Quitar la fecha límite de una tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento
- **Cuando** le quito la fecha límite
- **Entonces** el evento correspondiente se elimina del calendario *(asumido)*

**Escenario 3 · Falla la actualización del evento**
- **Dado que** cambio la fecha de una tarea sincronizada
- **Cuando** la API de Google no responde
- **Entonces** el cambio de fecha queda guardado en FlowSync de inmediato, aunque el evento en Google todavía muestre la fecha anterior
- **Y** la actualización del evento queda encolada para reintento (política de reintentos en HUSR-018)

#### 📌 Notas adicionales
PRD §3.5. La actualización usa el `eventId` persistido en `HUSR-015` — sin ese dato no hay forma de saber qué evento actualizar. El Escenario 3 es observable y testeable por sí solo (el dato en FlowSync manda, el evento queda temporalmente desincronizado); pero el Escenario 3 **no se puede dar por completado en un sprint** sin que la cola de reintentos de `HUSR-018` ya esté implementada en código, no solo documentada — es una dependencia dura, no solo declarativa.

#### 🛠️ Tareas funcionales
- [ ] Actualizar el evento existente al cambiar la fecha de la tarea
- [ ] Eliminar el evento si la tarea pierde su fecha límite
- [ ] Encolar el cambio para reintento si la actualización falla, sin bloquear el guardado en FlowSync

#### 📊 Métricas medibles
- 🎯 100% de los cambios de fecha en tareas sincronizadas actualizan su evento sin duplicarlo *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-015 y HUSR-007 (editar tarea) · depende de HUSR-018 para la política de reintento cuando la API falla.

---

### `HUSR-017` · Eliminar o marcar el evento al completar o borrar una tarea

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 1 d

#### 🎯 Historia
> **Como** usuario con una tarea sincronizada, **quiero** que su evento se elimine o se marque al completarla o borrarla, **para que** mi calendario no muestre pendientes que ya resolví.

#### 📝 Descripción
Cuando una tarea con evento se completa o se borra en FlowSync, el evento correspondiente en Google Calendar se elimina o se marca según corresponda.

#### ✅ Criterios de Aceptación

**Escenario 1 · Completar tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento en Google Calendar
- **Cuando** la marco como completada
- **Entonces** su evento en el calendario se elimina o se marca como completado

**Escenario 2 · Borrar tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento en Google Calendar
- **Cuando** la borro en FlowSync
- **Entonces** el evento correspondiente se elimina del calendario

**Escenario 3 · Archivar tarea sincronizada**
- **Dado que** una tarea con fecha está reflejada como evento
- **Cuando** la archivo (sin borrarla)
- **Entonces** su evento se elimina del calendario, aunque la tarea se conserve en FlowSync *(asumido)*

#### 📌 Notas adicionales
PRD §3.5. **Vacío de información — solo en el Escenario 1 (completar)**: el PRD dice "se elimina **o** se marca según corresponda" sin definir cuál de las dos aplica al completar una tarea. Los Escenarios 2 (borrar) y 3 (archivar) **no** comparten esta ambigüedad — ambos tienen un único resultado ("se elimina") y no dependen de esta decisión pendiente. Queda señalado para refinamiento; no se inventa la regla aquí.

#### 🛠️ Tareas funcionales
- [ ] Eliminar o marcar el evento al completar la tarea
- [ ] Eliminar el evento al borrar la tarea
- [ ] Eliminar el evento al archivar la tarea

#### 📊 Métricas medibles
- 🎯 100% de las tareas completadas, borradas o archivadas que tenían evento lo eliminan o marcan *(PRD §3.5)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-015, HUSR-008 (borrar) y HUSR-009 (cambiar estado).

---

### `HUSR-018` · Manejo de fallos de sincronización: reintento y registro en logs

> 🧩 **MOD-005** Sincronización con Google Calendar  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** usuario, **quiero** que mis tareas se guarden aunque la sincronización con Google falle, **para que** nunca pierda información por una falla externa.

#### 📝 Descripción
Si la API de Google no está disponible o devuelve error al crear, actualizar o eliminar un evento, la tarea se guarda igualmente en FlowSync, la sincronización se reintenta más tarde, y el fallo queda registrado en logs para diagnóstico.

#### ✅ Criterios de Aceptación

**Escenario 1 · La API de Google falla al sincronizar**
- **Dado que** intento sincronizar una tarea (crear, actualizar o eliminar su evento)
- **Cuando** la API de Google no está disponible o devuelve error
- **Entonces** la tarea se guarda igualmente en FlowSync
- **Y** el fallo queda registrado en logs

**Escenario 2 · Reintento posterior exitoso**
- **Dado que** una sincronización falló y quedó pendiente de reintento
- **Cuando** la API de Google vuelve a estar disponible
- **Entonces** la sincronización se completa en el siguiente reintento

**Escenario 3 · Fallo no recuperable por indisponibilidad**
- **Dado que** una sincronización lleva **3 reintentos fallidos** con backoff de 5, 15 y 60 minutos *(asumido — política concreta para que el AC sea testeable; validar en refinamiento)*
- **Cuando** el 3er reintento también falla
- **Entonces** se registra como fallo no recuperable en logs

**Escenario 4 · Fallo por autorización revocada (no se reintenta)** *(asumido — el PRD no distingue tipos de error; se infiere para evitar reintentos inútiles)*
- **Dado que** intento sincronizar una tarea
- **Cuando** la API de Google responde con un error de autorización (token revocado o expirado, no de disponibilidad)
- **Entonces** se marca como fallo no recuperable de inmediato, sin agotar los 3 reintentos, **para esa operación de sincronización puntual**
- **Y** queda registrado en logs que la causa fue de autorización, no de disponibilidad

#### 📌 Notas adicionales
Observabilidad: las operaciones de sync se registran en logs (PRD §4). El PRD no especifica la política de reintentos; se fija aquí un valor concreto *(asumido)* — 3 intentos con backoff 5/15/60 min — precisamente para que el Escenario 3 sea testeable y la métrica del módulo (<5%) sea medible; debe validarse en spike/refinamiento, no es un número del PRD. El Escenario 4 distingue el error de autorización del error de disponibilidad para evitar reintentos inútiles, pero **solo a nivel de esa operación de sync**: esta historia no decide qué pasa con el **estado de conexión de la cuenta** cuando se detecta la revocación en background — ese vacío queda señalado en las Notas de `HUSR-014` (Vacío de información 2), no se resuelve aquí.

#### 🛠️ Tareas funcionales
- [ ] Guardar la tarea en FlowSync aunque la sincronización falle
- [ ] Reintentar la sincronización pendiente hasta 3 veces con backoff 5/15/60 min
- [ ] Distinguir error de disponibilidad (reintentable) de error de autorización (no reintentable)
- [ ] Registrar en logs cada operación de sincronización, exitosa o fallida
- [ ] Marcar como fallo no recuperable al agotar los reintentos o ante error de autorización

#### 📊 Métricas medibles
- 🎯 <5% de las operaciones de sincronización fallan de forma no recuperable tras sus 3 reintentos *(PRD §6, umbral asumido)*
- 🎯 100% de los fallos de sincronización quedan registrados en logs, con causa (disponibilidad/autorización) *(PRD §4)*

#### 🔗 Relaciones / Dependencias
Depende de HUSR-015 y HUSR-013 (para distinguir error de autorización) · es la red de seguridad de HUSR-016 y HUSR-017 cuando la API falla.
