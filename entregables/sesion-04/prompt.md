# Prompt de descomposición — Backlog inicial de FlowSync MVP

> Parte 1 del ejercicio S4. Este es el prompt que diseñé para pedirle a la IA que
> extraiga las user stories del PRD de FlowSync. Patrón usado:
> **rol → contexto → alcance → restricciones → formato → ejemplo → transparencia → instrucción final.**

---

## Rol
Actúa como **Product Owner Senior con experiencia en productos SaaS**. Tu trabajo es
descomponer un PRD en un backlog inicial accionable, escrito en lenguaje natural y
centrado en las necesidades del usuario final (no en detalles técnicos). **No escribes
código ni propones arquitectura.**

Principios de trabajo:
- **Cuestiona cada suposición** del PRD: si algo no está literal, no lo des por hecho
  — márcalo con **(asumido)**.
- **Resuelve el problema real, no solo el declarado**: ancla cada historia al trabajo
  real del usuario (su *job-to-be-done*: "que mi tarea con fecha aparezca en el
  calendario sin copiarla a mano"), no a la feature como simple enunciado técnico.
- **Simplifica sin piedad**: prefiere siempre la rebanada más pequeña que entrega
  valor. Si algo se puede quitar sin perder el beneficio para el usuario, quítalo.

## Contexto del producto
FlowSync es una **app web de gestión de tareas personales que mantiene las tareas
sincronizadas con Google Calendar**. El problema que resuelve: la gente gestiona sus
pendientes en una herramienta y su tiempo en otra, y alinearlas a mano es tedioso.
En FlowSync, las tareas con fecha límite aparecen como eventos en el calendario.

**Usuario objetivo**: knowledge worker de 25–45 años que ya vive en Google Calendar
y hoy gestiona sus pendientes en otra herramienta (Todoist, Notion, una libreta).
Tiene entre 5 y 30 tareas activas. Valora la simplicidad.

La fuente única de verdad es el archivo adjunto **`docs/PRD.md`**. Usa **solo** lo que
dice ese documento.

## Tarea
A partir de `docs/PRD.md`, genera un backlog inicial de **exactamente 18 historias de
usuario**, agrupadas en **módulos**. Documenta todo el resultado en `UserStories.md`.

## Alcance del MVP — qué SÍ entra (basado en la sección 3 del PRD)
- **Auth y cuenta**: registro con email + contraseña (mín. 8 caracteres), aviso si el
  email ya existe, inicio de sesión, cierre de sesión, pantalla de bienvenida
  (onboarding mínimo), sesión por access token.
- **CRUD de tareas**: crear (solo el título es obligatorio; descripción y fecha límite
  opcionales), listar, editar cualquier campo, borrar, estados
  `pending`/`completed`/`archived` (nace en `pending`), cambiar de estado.
- **Organización y filtrado**: filtrar por estado, orden por defecto con lo de "hoy"
  primero, estado vacío cuando no hay tareas.
- **Exportación**: exportar tareas a CSV (mín. título, descripción, estado, fecha límite).
- **Sincronización con Google Calendar**: conectar la cuenta de Google (OAuth); tarea
  con fecha → evento; cambiar la fecha → actualiza el evento; completar/borrar →
  elimina o marca el evento; desconectar Google sin borrar las tareas; manejo razonable
  de fallos de la API (la tarea se guarda aunque la sync falle y se reintenta después).

## Restricciones (non-goals) — NO generes historias sobre esto
- Equipos o tareas compartidas (el MVP es individual).
- Calendarios que no sean Google (Outlook, iCal).
- Notificaciones push o por email.
- App móvil nativa (solo web responsive).
- Etiquetas, proyectos, jerarquías o subtareas.
- Recordatorios más allá de los del propio Google Calendar.
- **Sincronización inversa completa** (editar un evento en Google y que cambie la
  tarea): el PRD la marca como pendiente de spike → trátala como fuera del MVP.

Además:
- **No inventes features** que no estén en el PRD.
- **No propongas arquitectura ni stack** (nada de AdonisJS, React, tablas, endpoints,
  librerías). Las "Tareas" de cada historia deben ser **funcionales** (qué se hace),
  nunca técnicas (cómo se implementa).
- Si infieres algo que **no está literal** en el PRD, márcalo con **(asumido)** en la
  línea correspondiente.
- **Vigila la "falsa completitud"**: un backlog generado por IA puede parecer
  exhaustivo y aun así ignorar reglas de negocio implícitas. En **Notas adicionales**,
  cuando una historia toque una regla que el PRD no detalla del todo (ordenación,
  formato exacto, política de reintentos, etc.), señálalo explícitamente como
  **vacío de información** — no lo rellenes inventando la regla.

## Checklist INVEST (los 6 criterios — autoevaluación antes de entregar cada historia)
Antes de dar por buena una historia, verifícala contra los 6 criterios INVEST. Si
**falla en 2 o más**, no la entregues así: reescríbela o divídela.

- **Independent**: la historia se entiende y se puede trabajar sin que falten
  especificaciones de otras historias que no existen todavía.
- **Negotiable**: describe el qué y el porqué, no una solución cerrada — deja espacio
  para que el equipo decida el detalle de implementación en refinamiento.
- **Valuable**: el beneficio para el usuario está explícito en la historia; si no se
  describe el valor, no lo inventes ni lo des por obvio.
- **Estimable**: hay suficiente claridad (alcance, AC) para poder estimarla con
  confianza — si no puedes estimarla, le falta información, no inventes el número.
- **Small**: cabe en **1–2 días** de trabajo humano — el equivalente a una sola
  sesión de copiloto sin romper el contexto. Pedir algo del tamaño de un módulo
  completo en una sola historia es ineficiente: **si una funcionalidad necesita más
  de 2 días, divídela en varias historias** (por fases del flujo, no por capas
  técnicas). Esto aplica en especial a la sincronización con Google Calendar: separa
  "conectar", "desconectar", "crear evento", "actualizar evento", "eliminar/marcar
  evento" y "manejar fallos" en historias independientes en vez de una sola historia
  gigante.
- **Testable**: tiene Criterios de Aceptación observables y automatizables (por eso
  usamos Given/When/Then) — sin AC verificables, la historia no está lista.

## Estructura de módulos y reparto (para llegar a 18 historias sin inflar)
Codifica los módulos como **MOD-XXX** (secuencial) y las historias como **HUSR-XXX**
(secuencial e incremental). Reparto sugerido:

- **MOD-001 · Autenticación y cuenta** → HUSR-001 a HUSR-004
  (crear cuenta · iniciar sesión · cerrar sesión · onboarding de bienvenida).
- **MOD-002 · Gestión de tareas (CRUD)** → HUSR-005 a HUSR-009
  (crear · listar · editar · borrar · cambiar estado).
- **MOD-003 · Organización y filtrado** → HUSR-010, HUSR-011
  (filtrar por estado · estado vacío + orden por defecto).
- **MOD-004 · Exportación** → HUSR-012 (exportar a CSV).
- **MOD-005 · Sincronización con Google Calendar** → HUSR-013 a HUSR-018, repartidas
  en historias de 1–2 días: conectar cuenta de Google (OAuth) · desconectar sin
  perder tareas · crear evento al asignar fecha · actualizar evento al cambiar fecha ·
  eliminar/marcar evento al completar o borrar · manejar fallos de la API con
  reintento y logs.

## Formato de salida (obligatorio)
El documento usa un **estilo visual propio** (con iconos y badges) que debes respetar
al pie de la letra. Cada módulo abre con su encabezado `## MOD-XXX · <nombre claro y
conciso>`, una línea de objetivo en cursiva y una línea **📊 Métrica del módulo**
(1 indicador cuantificable que resuma el éxito de ese módulo). Debajo van sus historias.

Cada historia con entre **3 y 5 Criterios de Aceptación** como **escenarios** en
**Given/When/Then** (`Dado que / Cuando / Entonces / Y / Pero`), verificables y
concretos (no genéricos), incluyendo al menos un **camino de error o caso límite**
cuando el PRD lo mencione (email duplicado, contraseña corta, API de Google caída,
sin tareas, etc.).

Tanto los módulos como las historias incluyen **Métricas medibles**: indicadores
**cuantificables y verificables** (con número y unidad: %, segundos, tasa de error,
conteo), que permitan saber si cumplen su objetivo. Prioriza métricas derivadas del
PRD: §4 (rendimiento: listado <1 s con hasta 200 tareas; errores de validación
comprensibles) y §6 (éxito del MVP: ≥40% de usuarios conectan Google Calendar; <5% de
operaciones de sincronización fallan de forma no recuperable; el usuario completa el
flujo sin ayuda externa). Si una métrica no se deriva del PRD y la propones tú, márcala
con **(asumido)**. No inventes números que contradigan el PRD.

Usa **exactamente** esta plantilla por historia (respeta los iconos, el código en
`mono`, la línea de badges en blockquote y la historia en cita):

### `HUSR-XXX` · <título descriptivo, claro y sin ambigüedades>

> 🧩 **MOD-XXX** <nombre del módulo>  ·  <🔴 | 🟡 | 🟢> **Prioridad:** <Alta | Media | Baja>  ·  ⏱️ **Estimación:** <0.5 d | 1 d | 1.5 d | 2 d — máximo 2 d por la regla INVEST "Small">

#### 🎯 Historia
> **Como** <rol>, **quiero** <acción>, **para que** <beneficio>.

#### 📝 Descripción
<descripción concisa, en lenguaje natural, de la funcionalidad que el usuario desea y
por qué le importa>

#### ✅ Criterios de Aceptación

**Escenario 1 · <nombre del escenario>**
- **Dado que** <contexto/precondición>
- **Cuando** <acción realizada>
- **Entonces** <resultado esperado>
- **Y** <paso adicional, si aplica>

**Escenario 2 · <nombre>**
- **Dado que** … **Cuando** … **Entonces** … **Pero** …

**Escenario 3 · <nombre — incluye aquí el camino de error cuando exista>**
- **Dado que** … **Cuando** … **Entonces** …

#### 📌 Notas adicionales
<reglas del PRD, NFR aplicables (privacidad, <1 s, responsive, errores claros),
supuestos marcados con *(asumido)*. Sin decisiones de arquitectura.>

#### 🛠️ Tareas funcionales
- [ ] <subtarea funcional 1 — qué debe poder hacerse, no cómo>
- [ ] <subtarea funcional 2>
- [ ] <subtarea funcional 3>

#### 📊 Métricas medibles
- 🎯 <indicador 1 cuantificable, con número y unidad — *(PRD §X)* o *(asumido)*>
- 🎯 <indicador 2, si aplica>

#### 🔗 Relaciones / Dependencias
<HUSR-XXX y por qué> · o "Ninguna".

Estimación: refleja la complejidad relativa según el PRD dentro del tope de 2 días —
las historias de sincronización con Google Calendar y OAuth tienden al máximo (2 d)
por sus riesgos (rate limits, zonas horarias, fallos de API); si alguna lo supera,
es señal de que falta dividirla.

---

## Ejemplo de output esperado (referencia de estilo — NO lo copies tal cual)

## MOD-001 · Autenticación y cuenta
*Objetivo: que cada usuario tenga una cuenta propia y privada para gestionar sus tareas.*
**📊 Métrica del módulo:** ≥95% de los registros iniciados se completan sin error de validación bloqueante *(asumido)*.

### `HUSR-001` · Crear cuenta con email y contraseña

> 🧩 **MOD-001** Autenticación y cuenta  ·  🔴 **Prioridad:** Alta  ·  ⏱️ **Estimación:** 2 d

#### 🎯 Historia
> **Como** visitante, **quiero** crear una cuenta con mi email y contraseña, **para que** pueda guardar mis tareas de forma privada.

#### 📝 Descripción
El visitante necesita una cuenta propia antes de usar FlowSync; los datos de un usuario
nunca son visibles para otro. El registro pide email y una contraseña de al menos 8
caracteres. Si el email ya existe, el sistema lo indica y ofrece ir al inicio de sesión.

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
Privacidad: los datos de un usuario nunca son accesibles por otro (PRD §4). Los mensajes
de error deben ser comprensibles, no técnicos. El método de registro del MVP es solo
email/contraseña; OAuth de Google es solo para conectar el calendario, no para
registrarse *(asumido)*.

#### 🛠️ Tareas funcionales
- [ ] Permitir alta con email y contraseña válida
- [ ] Validar longitud mínima de contraseña (8) y mostrar mensaje claro
- [ ] Detectar email duplicado y ofrecer ir al login
- [ ] Tras el alta, llevar al usuario a la pantalla de bienvenida

#### 📊 Métricas medibles
- 🎯 100% de las contraseñas < 8 caracteres son rechazadas con mensaje comprensible *(PRD §3.1, §4)*
- 🎯 0 cuentas duplicadas creadas con un email ya registrado *(PRD §3.1)*
- 🎯 ≥95% de registros iniciados se completan sin error bloqueante *(asumido)*

#### 🔗 Relaciones / Dependencias
Ninguna — historia raíz del módulo MOD-001.

---

## Instrucción final
Genera ahora las **18 historias (HUSR-001 a HUSR-018)** dentro de sus **5 módulos
(MOD-001 a MOD-005)**, siguiendo todo lo anterior: títulos claros, historias de
**máximo 2 días** (regla "Small"), 3–5 criterios de aceptación en Given/When/Then por
historia (con caminos de error donde el PRD lo permita), Estimación en días
calendario, Notas adicionales (señalando vacíos de información, no rellenándolos),
Tareas funcionales y **Métricas medibles cuantificables** (a nivel de módulo y de
historia), marcando con **(asumido)** cualquier inferencia y respetando estrictamente
los non-goals. Documenta el resultado completo en
`UserStories.md`. No incluyas prosa fuera de las historias ni cierres con resúmenes.
