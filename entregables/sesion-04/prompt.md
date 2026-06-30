# Prompt — Descomposición del PRD de FlowSync en user stories

## Rol
Eres un Product Owner senior con experiencia en aplicaciones SaaS web.
Tu trabajo es descomponer PRDs en backlogs de user stories listas para
refinamiento técnico.

## Tarea
Lee el PRD de FlowSync en @docs/PRD.md y extrae todas las user stories
del MVP. Genera entre 8 y 12 stories en total.

## Usuario objetivo
El usuario primario es un knowledge worker de 25-45 años que ya usa
Google Calendar a diario. Usa ese perfil al redactar el `Como [rol]`
de cada story.

## Formato de cada story
Como [rol de usuario], quiero [acción], para [beneficio].

Cada story debe cumplir los criterios INVEST, en especial ser **testable**
y entregar **valor observable por sí sola**. No fragmentes en micro-acciones
que no tienen valor independiente (ej: "ver el estado vacío" no es una story
aparte), ni agrupes en una sola story acciones que sí lo tienen (crear,
editar y borrar son stories distintas).

El campo `para [beneficio]` debe expresar el valor real para el usuario,
no repetir la acción. Incorrecto: "para que se cree la cuenta". Correcto:
"para no tener que mirar dos herramientas distintas para saber qué tengo
que hacer hoy".

## Criterios de aceptación
Cada story debe incluir entre 3 y 5 criterios de aceptación verificables
en formato Given/When/Then. Los criterios deben ser específicos para
FlowSync, no genéricos. Cuando el PRD provea valores concretos (mínimo
8 caracteres, menos de 1 segundo, columnas exactas del CSV), úsalos
literalmente en el AC — no los parafrasees. Cada story debe cubrir al
menos: el camino feliz (happy path), un error de validación, y un edge
case o escenario de fallo cuando el PRD lo mencione.

Un AC verificable describe un resultado observable, no un estado vago:
- Débil (evítalo): `Then la tarea se guarda correctamente`
- Fuerte (úsalo): `Then la tarea aparece en el listado con estado pending`

## Agrupación y numeración
Agrupa las stories por módulo funcional y numéralas secuencialmente
(US-01, US-02…) para facilitar su referencia. El número final corresponde
al total de stories generadas: si generas 10 stories, la última es US-10.
- Autenticación y gestión de cuenta
- Gestión de tareas (CRUD)
- Organización y filtrado
- Exportación
- Sincronización con Google Calendar

El número de stories por módulo debe reflejar su complejidad real en el
PRD: los módulos con más requisitos funcionales (Gestión de tareas,
Sincronización) tendrán varias stories; un módulo simple como Exportación
puede tener una sola. No fuerces stories adicionales para rellenar.

Indica las dependencias entre stories. Cuando una story requiera que otra
se haya completado antes (ej: conectar Google Calendar antes de sincronizar
eventos, o registrarse antes de crear tareas), añade una línea
`Depende de: US-0X` justo debajo del título. Si no tiene dependencias,
escribe `Depende de: ninguna`.

## Restricciones
- Las stories deben derivarse exclusivamente de las secciones 3.1 a 3.5
  del PRD (requisitos funcionales). Las secciones 1, 2, 4, 5, 6, 7 y 8
  son contexto — no generes stories directamente de ellas.
- No inventes features que no estén en el PRD.
- No incluyas funcionalidades marcadas como out of scope.
- No estimes tiempos, esfuerzo ni story points.
- No propongas arquitectura técnica.
- La sincronización del MVP es primariamente FlowSync → Google Calendar.
  No generes stories para la dirección inversa (Google Calendar → FlowSync)
  ya que el PRD la marca como pendiente de spike técnico.
- Los requisitos no funcionales (sección 4 del PRD) no son stories
  independientes. Incorpóralos como criterios de aceptación dentro de
  la story funcional que corresponda.

## Formato de output
Devuelve únicamente el backlog en Markdown. Sin introducción, sin resumen
al final, sin comentarios adicionales. Redacta todo el output en español.

## Ejemplo de formato esperado

### Autenticación y gestión de cuenta

**US-01 — Registro de usuario**
Depende de: ninguna
Como knowledge worker que quiere centralizar sus pendientes, quiero crear
una cuenta con email y contraseña, para acceder a mis tareas de forma
personal y privada.

Criterios de aceptación:
- Given que soy un visitante en la pantalla de registro,
  When ingreso un email válido y una contraseña de al menos 8 caracteres
  y hago clic en "Registrarse",
  Then se crea mi cuenta y soy redirigido a la pantalla de bienvenida.
- Given que ingreso un email ya registrado,
  When envío el formulario,
  Then el sistema muestra un error y me ofrece ir al inicio de sesión.
- Given que ingreso una contraseña de menos de 8 caracteres,
  When envío el formulario,
  Then el sistema muestra un mensaje de validación comprensible,
  no un error técnico.

### Sincronización con Google Calendar

_(los números a continuación son ilustrativos; en tu backlog dependerán
de cuántas stories haya en los módulos anteriores)_

**US-09 — Crear evento en Google Calendar al asignar fecha límite**
Depende de: US-08 (conectar cuenta de Google)
Como knowledge worker que gestiona tareas y calendario en herramientas
separadas, quiero que al guardar una tarea con fecha límite aparezca
automáticamente como evento en mi Google Calendar, para no tener que
copiarla a mano ni revisar dos lugares distintos para saber qué tengo
pendiente hoy.

Criterios de aceptación:
- Given que mi cuenta de Google está conectada y creo una tarea
  con fecha límite,
  When guardo la tarea,
  Then aparece un evento en mi Google Calendar en la fecha indicada
  con el título de la tarea como nombre del evento.
  (asumido: el PRD no especifica el contenido del evento más allá
  de la fecha)
- Given que mi cuenta de Google está conectada y creo una tarea
  sin fecha límite,
  When guardo la tarea,
  Then no se crea ningún evento en Google Calendar.
- Given que mi cuenta de Google está conectada y la API de Google
  no está disponible al guardar una tarea con fecha límite,
  When guardo la tarea,
  Then la tarea se guarda correctamente en FlowSync, la sincronización
  falla sin interrumpir al usuario, el sistema reintenta la sync más
  tarde, y la operación queda registrada en los logs para diagnóstico.
  (asumido: el PRD no especifica el intervalo de reintento ni si se
  notifica al usuario cuando la sync falla)
- Given que mi cuenta de Google no está conectada y creo una tarea
  con fecha límite,
  When guardo la tarea,
  Then la tarea se guarda en FlowSync sin crear ningún evento en
  Google Calendar.

---

## Transparencia
Si infiere algo que no está escrito literalmente en el PRD, márcalo con
(asumido) al final del criterio correspondiente. Si el PRD deja un
detalle como TBD o a decisión del equipo (ej: criterio de ordenación
del listado), propón una interpretación razonable y márcala con
(asumido).

> El PRD completo está en `docs/PRD.md` dentro de este repositorio.
