# Prompt — Descomposición AI-ready del PRD de FlowSync

## Rol

Eres un Product Owner senior con experiencia en aplicaciones SaaS, planificación de producto para equipos de ingeniería y descomposición de PRDs en backlog AI-ready.

Tu tarea es convertir el PRD de FlowSync MVP en un conjunto inicial de user stories claras, pequeñas, verificables y útiles para un equipo de ingeniería que luego trabajará con agentes de IA.

Debes pensar como PO + analista funcional + tech lead ligero: suficiente detalle para que las stories sean ejecutables, pero sin diseñar arquitectura ni proponer implementación técnica.

---

## Contexto del producto

FlowSync es una aplicación web de gestión de tareas personales que mantiene sincronizadas las tareas del usuario con Google Calendar.

El problema que resuelve es que los usuarios gestionan sus pendientes en una herramienta y su tiempo en otra. FlowSync busca eliminar la doble gestión manual: las tareas con fecha límite deben aparecer como eventos en Google Calendar.

El usuario objetivo es un profesional del conocimiento que usa Google Calendar a diario, tiene entre 5 y 30 tareas activas y valora herramientas simples que no requieran mantenimiento manual.

La hipótesis central del MVP es validar si los usuarios adoptarán un gestor de tareas cuando este elimina la doble gestión entre tareas y calendario.

---

## Alcance permitido

Usa únicamente funcionalidades incluidas en el MVP del PRD.

El MVP incluye:

- Registro y autenticación de usuarios.
- CRUD completo de tareas con estados.
- Filtrado y organización básica de tareas.
- Exportación de tareas.
- Sincronización con Google Calendar, principalmente en dirección FlowSync → Google Calendar.
- Manejo razonable de errores de sincronización con Google.
- Privacidad entre usuarios.
- Interfaz web responsive.
- Errores claros para validaciones.
- Observabilidad básica de operaciones de sincronización.

---

## Non-goals / restricciones

No inventes funcionalidades que no estén en el PRD.

No incluyas:

- Equipos o tareas compartidas.
- Sincronización con Outlook, iCal u otros calendarios.
- Notificaciones push o por email.
- Aplicación móvil nativa.
- Etiquetas, proyectos, jerarquías o subtareas.
- Recordatorios configurables fuera de Google Calendar.
- Recuperación de contraseña.
- Verificación de email por enlace.
- Arquitectura técnica detallada.
- Estimaciones en horas, días, story points o t-shirt sizes.
- Propuesta de base de datos, endpoints concretos, componentes frontend o estructura de carpetas.
- Tasks técnicas de implementación, salvo que sean notas breves de dependencia/riesgo.
- Sincronización inversa completa Google Calendar → FlowSync como feature comprometida. Si aparece, trátala como riesgo o spike candidato, no como user story del MVP.

---

## Instrucciones de transparencia

Diferencia siempre entre lo que está explícitamente indicado en el PRD y lo que estás infiriendo.

Marca con **"(asumido)"** cualquier criterio, decisión, detalle funcional, regla de negocio, caso de uso o dependencia que no esté claramente respaldado por el PRD.

Si una parte del PRD es ambigua, no la completes como si fuera segura. Puedes incluir una sección breve llamada **"Supuestos y puntos para refinamiento"** dentro de la épica correspondiente.

No ocultes riesgos importantes bajo stories genéricas.

---

## Criterios de calidad de las user stories

Cada story debe intentar cumplir INVEST:

- **Independent**: debe poder implementarse sin depender de otra story aún indefinida.
- **Negotiable**: debe dejar claro qué parte puede refinarse.
- **Valuable**: debe aportar valor al usuario o al sistema.
- **Estimable**: debe tener contexto suficiente para que el equipo pueda discutir complejidad.
- **Small**: debe ser lo bastante acotada para una sesión razonable de trabajo con IA.
- **Testable**: debe tener criterios de aceptación observables.

Si una story no cumple bien INVEST, inclúyela igualmente sólo si es necesaria para el MVP, pero marca el problema en una nota breve.

---

## Formato exacto de salida

Devuelve el resultado en Markdown.

Agrupa las stories por módulo, caso de uso o épica, usando la agrupación que tenga más sentido para FlowSync.

Cada story debe tener este formato exacto:

```md
## Épica: [nombre de la épica o módulo]

### Story [número]: [título breve]

**User story**  
Como [rol], quiero [acción], para [beneficio].

**Criterios de aceptación**

Scenario: [nombre del escenario]
Given [contexto inicial observable]
When [acción del usuario o evento del sistema]
Then [resultado esperado verificable]
And [verificación adicional si aplica]

Scenario: [nombre del escenario]
Given ...
When ...
Then ...

**Notas / supuestos**

- [Sólo si aplica. Marcar con "(asumido)" lo que no esté literal en el PRD.]
```

Cada story debe tener entre 3 y 5 criterios de aceptación en formato Given/When/Then.

Los criterios de aceptación deben ser verificables, no genéricos. Evita frases como:

- "Debe funcionar correctamente".
- "El sistema debe ser intuitivo".
- "La sincronización debe andar bien".
- "El usuario puede usar la feature".

Prefiere criterios observables, por ejemplo:

- respuesta esperada;
- estado visible para el usuario;
- cambio persistido;
- error mostrado;
- aislamiento de datos entre usuarios;
- comportamiento cuando Google Calendar falla;
- comportamiento cuando falta un campo obligatorio;
- resultado esperado al filtrar, exportar o cambiar estado.

---

## Ejemplos de formato esperado

Estos ejemplos son sólo para mostrar formato y nivel de detalle. No los trates como la lista completa de stories.

```md
## Épica: Autenticación y cuenta

### Story 1: Registro de usuario

**User story**  
Como visitante, quiero crear una cuenta con email y contraseña, para empezar a usar FlowSync con mis propios datos.

**Criterios de aceptación**

Scenario: Registro exitoso
Given un visitante no autenticado
And proporciona un email válido y una contraseña de al menos 8 caracteres
When envía el formulario de registro
Then se crea una cuenta nueva
And el usuario llega a la pantalla de bienvenida

Scenario: Contraseña demasiado corta
Given un visitante no autenticado
And proporciona una contraseña de menos de 8 caracteres
When envía el formulario de registro
Then el sistema rechaza el registro
And muestra un error claro sobre la longitud mínima de la contraseña

Scenario: Email ya registrado
Given existe una cuenta registrada con el email ingresado
When el visitante intenta registrarse con ese mismo email
Then el sistema no crea una cuenta duplicada
And ofrece ir al inicio de sesión

**Notas / supuestos**

- El PRD no exige verificación por email ni recuperación de contraseña.
```

```md
## Épica: Gestión de tareas

### Story 2: Crear tarea básica

**User story**  
Como usuario autenticado, quiero crear una tarea con título, para registrar un pendiente en FlowSync.

**Criterios de aceptación**

Scenario: Crear tarea con título
Given un usuario autenticado
When crea una tarea indicando un título válido
Then la tarea se guarda correctamente
And la tarea queda en estado "pending"

Scenario: Crear tarea sin título
Given un usuario autenticado
When intenta crear una tarea sin título
Then el sistema rechaza la operación
And muestra un error claro indicando que el título es obligatorio

Scenario: Crear tarea con fecha límite
Given un usuario autenticado
And proporciona título y fecha límite
When crea la tarea
Then la tarea se guarda con esa fecha límite
And queda disponible para sincronización con Google Calendar si la cuenta está conectada

**Notas / supuestos**

- La regla de sincronización al crear una tarea con fecha depende de que el usuario haya conectado Google Calendar.
```

---

## Output esperado

Genera entre 8 y 12 user stories iniciales.

Agrúpalas en épicas o módulos. Una agrupación razonable podría ser:

- Autenticación y cuenta.
- Gestión de tareas.
- Organización y filtrado.
- Exportación.
- Sincronización con Google Calendar.
- Calidad transversal del MVP, sólo si hay stories verificables y no meramente técnicas.

No fuerces una épica si no aporta claridad.

Al final del output agrega una sección breve:

```md
## Supuestos y puntos para refinamiento

- [Lista de ambigüedades, riesgos o decisiones que el equipo debería resolver antes de implementar.]
```

Incluye especialmente:

- criterio exacto de ordenación de tareas relevantes para "hoy";
- manejo de fecha límite y zona horaria;
- comportamiento exacto al completar una tarea sincronizada;
- comportamiento exacto al borrar una tarea sincronizada;
- alcance de sincronización inversa Google Calendar → FlowSync;
- estrategia de reintentos cuando falla Google Calendar;
- cualquier otro punto que el PRD deje abierto.

---

## Tarea

Lee el PRD completo de FlowSync MVP y genera el backlog inicial siguiendo exactamente estas instrucciones.

No expliques tu proceso. No incluyas recomendaciones de arquitectura. No incluyas estimaciones. No agregues funcionalidades fuera del MVP.

PRD de entrada:

docs/PRD.md
