# Backlog de FlowSync MVP — Épicas, Historias de Usuario y Criterios de Aceptación

> **Rol:** Product Owner Senior · **Insumo:** `docs/PRD.md` v1.0 (Q3 2026)
> **Convención:** lo marcado con `(asumido)` es inferencia del PO y **no** aparece
> literal en el PRD; debe validarse en refinamiento.

---

## 1. Aterrizaje de negocio y alcance del MVP

### 1.1 Hipótesis y propuesta de valor

FlowSync valida una hipótesis: **¿la gente con sobrecarga de herramientas adoptará
un gestor de tareas si elimina la doble gestión tarea/calendario?** El valor
diferenciador es que **las tareas con fecha viven también en Google Calendar sin
copiar a mano**, en dirección primaria FlowSync → Google.

### 1.2 Las 3 funcionalidades más críticas (donde concentramos el esfuerzo)

Priorizadas por valor real al usuario y por ser el camino mínimo para validar la
hipótesis:

| # | Funcionalidad | Épica | Por qué es crítica |
| --- | --- | --- | --- |
| 1 | **Sincronización con Google Calendar** | EP-005 | Es el diferenciador. Sin esto FlowSync es "otro to-do más". Es además el mayor riesgo técnico (rate limits, zonas horarias). |
| 2 | **Gestión de tareas (CRUD + estados)** | EP-002 | Es el núcleo. Sin tareas no hay nada que sincronizar. |
| 3 | **Autenticación y aislamiento de datos** | EP-001 | Habilitador obligatorio: cada usuario con su cuenta, sus tareas y sus tokens de Google aislados. |

El resto (filtrado EP-003, exportación EP-004) son de soporte: aportan, pero no
son la prueba de la hipótesis.

### 1.3 Decisiones de arquitectura más importantes (tal como ya las fija el PRD)

> No se propone arquitectura nueva. Se resumen las decisiones **ya tomadas** en el
> PRD que condicionan el backlog:

- **Stack fijado:** Backend AdonisJS 7 + TypeScript + Lucid + SQLite
  (`better-sqlite3`) + VineJS + `@adonisjs/auth` (access tokens). Frontend React 19
  + TypeScript + Vite + React Router + Tailwind v4 + shadcn/ui. Integración: Google
  Calendar API (OAuth 2.0). *(PRD §5)*
- **Dirección de sync:** primariamente **FlowSync → Google Calendar**. La sync
  inversa completa (editar el evento en Google y que vuelva a la tarea) queda
  **fuera del compromiso del MVP** hasta un **spike técnico**. *(PRD §3.5, §7)*
- **Resiliencia de sync:** la tarea **se guarda aunque la sync falle**, con
  reintento posterior. La sync nunca bloquea el CRUD. *(PRD §3.5)*
- **Seguridad de tokens:** los tokens de Google se almacenan de forma segura y
  jamás son accesibles por otros usuarios. *(PRD §4)*
- **Sin app nativa:** solo web responsive (desktop + móvil). *(PRD §1, §4)*

### 1.4 Métricas de éxito del MVP *(PRD §6)*

- **Activación end-to-end:** el usuario completa registro → crear tareas →
  conectar Google → ver tareas como eventos, **sin ayuda externa**.
- **Adopción del diferenciador:** **≥ 40%** de los usuarios registrados conectan
  su Google Calendar.
- **Fiabilidad de sync:** **< 5%** de operaciones de sincronización fallan de
  forma no recuperable.
- **Rendimiento:** el listado carga en **< 1 s** con hasta 200 tareas. *(PRD §4)*

### 1.5 Fuera de alcance del MVP *(PRD §1 — no se generan HDU para esto)*

Equipos / tareas compartidas · calendarios no-Google · push/email · app nativa ·
etiquetas/proyectos/subtareas · recordatorios propios más allá de los de Google ·
sync inversa completa Google → FlowSync.

---

## 2. Mapa de épicas

| Épica | Nombre | HDU | Criticidad |
| --- | --- | --- | --- |
| EP-001 | Autenticación y Gestión de Cuenta | HDU-001 … HDU-003 | 🔴 Crítica |
| EP-002 | Gestión de Tareas (CRUD y Estados) | HDU-004 … HDU-008 | 🔴 Crítica |
| EP-003 | Organización y Filtrado | HDU-009 | 🟡 Soporte |
| EP-004 | Exportación de Datos | HDU-010 | 🟡 Soporte |
| EP-005 | Sincronización con Google Calendar | HDU-011 … HDU-012 | 🔴 Crítica |

Total: **12 historias de usuario** en **5 épicas**.

---

# EP-001 — Autenticación y Gestión de Cuenta

> Cada usuario tiene su cuenta propia; los datos de un usuario nunca son visibles
> para otro. Habilitador de todo lo demás.

## Título de la Historia de Usuario: HDU-001 - Registro de cuenta de usuario

*Como* visitante sin cuenta *quiero* registrarme con mi email y una contraseña
*para* tener un espacio propio y privado donde gestionar mis tareas.

**Escenario 1: Registro exitoso**
- **Dado que** soy un visitante en la pantalla de registro
- **Cuando** introduzco un email no registrado y una contraseña de al menos 8 caracteres
- **Y** confirmo el registro
- **Entonces** el sistema crea mi cuenta
- **Y** me lleva a una pantalla de bienvenida (onboarding mínimo) que explica en una frase qué hace FlowSync
- **Y** me invita a crear mi primera tarea.

**Escenario 2: Email ya registrado**
- **Dado que** estoy en la pantalla de registro
- **Cuando** introduzco un email que ya existe en el sistema
- **Entonces** se me indica claramente que el email ya está registrado
- **Y** se me ofrece un acceso directo para ir a iniciar sesión.

**Escenario 3: Contraseña demasiado corta**
- **Dado que** estoy en la pantalla de registro
- **Cuando** introduzco una contraseña de menos de 8 caracteres
- **Entonces** se muestra un mensaje de validación comprensible (no un error técnico)
- **Y** no se crea la cuenta.

**Escenario 4: Email con formato inválido (asumido)**
- **Dado que** estoy en la pantalla de registro
- **Cuando** introduzco un email con formato inválido
- **Entonces** se muestra un mensaje de validación claro
- **Y** no se crea la cuenta.

**✅ Criterios de Aceptación**
- El registro requiere email y contraseña; la contraseña tiene mínimo 8 caracteres.
- Un email duplicado se rechaza con mensaje claro y enlace a inicio de sesión.
- Tras registro exitoso se muestra el onboarding de una frase con invitación a crear la primera tarea.
- Los mensajes de validación son comprensibles para el usuario, no errores técnicos.
- No se exige verificación de email por enlace ni "recordar contraseña" en el MVP.

**🎯 Definición de Terminado (DoD)**
- [ ] Formulario de registro implementado y funcional
- [ ] Validaciones (email, longitud de contraseña, duplicado) con mensajes claros
- [ ] Pantalla de onboarding tras registro
- [ ] Responsive design para desktop y móvil
- [ ] Contraseña almacenada de forma segura (hash) — nunca en texto plano
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El onboarding es "mínimo": una frase + CTA a crear tarea (PRD §3.1). El copy exacto queda a definir (asumido).
- Sin verificación de email ni recuperación de contraseña en el MVP (PRD §3.1).
- El hash de contraseña es práctica obligada de seguridad (asumido: no literal en PRD, pero §4 exige datos protegidos).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-002: Inicio de sesión (enlace ofrecido ante email duplicado).
- HDU-004: Creación de tarea (CTA del onboarding).

---

## Título de la Historia de Usuario: HDU-002 - Inicio de sesión

*Como* usuario registrado *quiero* iniciar sesión con mi email y contraseña
*para* acceder a mis tareas desde cualquier navegador.

**Escenario 1: Inicio de sesión exitoso**
- **Dado que** tengo una cuenta registrada
- **Cuando** introduzco mi email y contraseña correctos
- **Entonces** el sistema valida mis credenciales y emite un token de acceso
- **Y** me da acceso a mi listado de tareas.

**Escenario 2: Credenciales incorrectas**
- **Dado que** estoy en la pantalla de inicio de sesión
- **Cuando** introduzco un email o contraseña incorrectos
- **Entonces** se muestra un mensaje claro de credenciales inválidas
- **Y** no se concede acceso
- **Y** el mensaje no revela si el fallo fue por el email o por la contraseña (asumido, buena práctica de seguridad).

**Escenario 3: Sesión persistente por token**
- **Dado que** inicié sesión correctamente
- **Cuando** recargo la aplicación o vuelvo dentro de la vigencia del token
- **Entonces** sigo autenticado sin volver a introducir credenciales.

**✅ Criterios de Aceptación**
- El inicio de sesión valida email + contraseña contra la cuenta existente.
- La sesión se mantiene mediante token de acceso (`@adonisjs/auth`).
- Las credenciales inválidas producen un mensaje claro y comprensible.
- Un usuario solo accede a sus propios datos, nunca a los de otro.

**🎯 Definición de Terminado (DoD)**
- [ ] Formulario de inicio de sesión implementado y funcional
- [ ] Emisión y validación de token de acceso
- [ ] Mensajes de error claros para credenciales inválidas
- [ ] Aislamiento de datos por usuario verificado
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- La vigencia/expiración del token queda a definir en refinamiento (asumido).
- Mensaje de error genérico ("credenciales inválidas") por seguridad (asumido).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-001: Registro de cuenta.
- HDU-003: Cierre de sesión.

---

## Título de la Historia de Usuario: HDU-003 - Cierre de sesión

*Como* usuario autenticado *quiero* cerrar sesión *para* proteger mi cuenta
cuando uso un dispositivo compartido.

**Escenario 1: Cierre de sesión exitoso**
- **Dado que** estoy autenticado en FlowSync
- **Cuando** hago clic en "Cerrar sesión"
- **Entonces** mi token de acceso se invalida
- **Y** soy redirigido a la pantalla de inicio de sesión
- **Y** no puedo volver a mis tareas sin autenticarme de nuevo.

**Escenario 2: Acceso bloqueado tras cierre (asumido)**
- **Dado que** cerré sesión
- **Cuando** intento acceder a una URL interna de la app
- **Entonces** soy redirigido a iniciar sesión.

**✅ Criterios de Aceptación**
- El cierre de sesión invalida el token de acceso vigente.
- Tras cerrar sesión, las rutas protegidas no son accesibles sin reautenticación.

**🎯 Definición de Terminado (DoD)**
- [ ] Acción de cierre de sesión implementada y funcional
- [ ] Invalidación del token verificada
- [ ] Protección de rutas tras logout
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El cierre de sesión **no** afecta a la conexión de Google ya guardada (asumido: la desconexión de Google es HDU-011, acción independiente).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-002: Inicio de sesión.

---

# EP-002 — Gestión de Tareas (CRUD y Estados)

> El núcleo de FlowSync. Una tarea tiene como mínimo: título, descripción
> opcional, estado y fecha límite opcional.

## Título de la Historia de Usuario: HDU-004 - Creación de tarea

*Como* usuario autenticado *quiero* crear una tarea indicando al menos un título
*para* registrar rápidamente algo que tengo que hacer.

**Escenario 1: Creación con solo título**
- **Dado que** estoy autenticado y en mi listado de tareas
- **Cuando** abro el formulario de nueva tarea e introduzco solo un título
- **Y** guardo
- **Entonces** la tarea se crea con estado `pending`
- **Y** aparece en mi listado.

**Escenario 2: Creación con descripción y fecha límite**
- **Dado que** estoy creando una tarea
- **Cuando** introduzco título, descripción y fecha límite
- **Y** guardo
- **Entonces** la tarea se crea con todos sus datos en estado `pending`.

**Escenario 3: Título vacío**
- **Dado que** estoy creando una tarea
- **Cuando** intento guardar sin título
- **Entonces** se muestra un mensaje de validación claro
- **Y** la tarea no se crea.

**Escenario 4: Tarea con fecha desencadena sincronización**
- **Dado que** tengo mi cuenta de Google conectada
- **Cuando** creo una tarea con fecha límite
- **Entonces** se solicita la creación del evento correspondiente en Google Calendar (ver HDU-012).

**✅ Criterios de Aceptación**
- Una tarea se puede crear con solo el título; descripción y fecha límite son opcionales.
- Toda tarea nueva nace en estado `pending`.
- Sin título, la creación se rechaza con mensaje claro.
- La tarea queda asociada exclusivamente al usuario que la crea.

**🎯 Definición de Terminado (DoD)**
- [ ] Formulario de creación implementado y funcional
- [ ] Validación de título obligatorio con mensaje claro
- [ ] Estado inicial `pending` garantizado
- [ ] Disparo de sincronización si hay fecha y Google conectado
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El campo fecha límite es lo que detona la sincronización; sin fecha no hay evento (PRD §3.5).
- El manejo de zona horaria de la fecha límite → hora del evento se define en HDU-012 (PRD §7).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-005: Visualización del listado de tareas.
- HDU-012: Sincronización de tareas como eventos de calendario.

---

## Título de la Historia de Usuario: HDU-005 - Visualización del listado de tareas

*Como* usuario autenticado *quiero* ver el listado de mis tareas *para* saber de
un vistazo qué tengo pendiente.

**Escenario 1: Listado con tareas**
- **Dado que** tengo tareas creadas
- **Cuando** entro a mi listado
- **Entonces** veo mis tareas con su título, estado y fecha límite (si la tienen)
- **Y** las más relevantes para "hoy" aparecen primero.

**Escenario 2: Estado vacío (cuenta nueva o todo archivado)**
- **Dado que** no tengo ninguna tarea visible
- **Cuando** entro a mi listado
- **Entonces** veo un estado vacío con una invitación clara a crear mi primera tarea.

**Escenario 3: Rendimiento con muchas tareas**
- **Dado que** tengo hasta 200 tareas
- **Cuando** cargo el listado
- **Entonces** la carga se completa en menos de 1 segundo.

**Escenario 4: Aislamiento de datos**
- **Dado que** existen tareas de otros usuarios en el sistema
- **Cuando** veo mi listado
- **Entonces** solo veo mis propias tareas, nunca las de otros.

**✅ Criterios de Aceptación**
- El listado muestra título, estado y fecha límite de cada tarea.
- El orden por defecto prioriza lo relevante para "hoy".
- Con cero tareas se muestra el estado vacío con CTA de creación.
- El listado carga en < 1 s con hasta 200 tareas.
- Un usuario solo ve sus propias tareas.

**🎯 Definición de Terminado (DoD)**
- [ ] Listado implementado y funcional
- [ ] Estado vacío con CTA implementado
- [ ] Orden por defecto acordado e implementado
- [ ] Verificado < 1 s con 200 tareas
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El **criterio exacto de ordenación** queda a decisión del equipo en refinamiento (PRD §3.3). Propuesta de partida (asumido): por fecha límite ascendente, vencidas primero, sin fecha al final.
- Por defecto el listado muestra activas; las `archived` se ven al filtrar (asumido, ver HDU-009).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-004: Creación de tarea.
- HDU-009: Filtrado de tareas por estado.

---

## Título de la Historia de Usuario: HDU-006 - Edición de tarea

*Como* usuario autenticado *quiero* editar cualquier campo de una tarea existente
*para* mantenerla actualizada cuando cambian las circunstancias.

**Escenario 1: Edición de campos**
- **Dado que** tengo una tarea creada
- **Cuando** edito su título, descripción o fecha límite y guardo
- **Entonces** los cambios se reflejan en el listado.

**Escenario 2: Cambio de fecha límite con Google conectado**
- **Dado que** tengo Google conectado y una tarea con evento ya creado
- **Cuando** cambio la fecha límite de la tarea
- **Entonces** se solicita la actualización del evento correspondiente en Google Calendar (ver HDU-012).

**Escenario 3: Añadir fecha a una tarea sin fecha**
- **Dado que** tengo Google conectado y una tarea sin fecha límite (sin evento)
- **Cuando** le añado una fecha límite
- **Entonces** se solicita la creación de un nuevo evento en Google Calendar.

**Escenario 4: Quitar la fecha límite (asumido)**
- **Dado que** tengo Google conectado y una tarea con evento
- **Cuando** elimino su fecha límite
- **Entonces** se solicita la eliminación del evento asociado en Google Calendar.

**Escenario 5: Validación al editar**
- **Dado que** estoy editando una tarea
- **Cuando** dejo el título vacío e intento guardar
- **Entonces** se muestra un mensaje de validación claro y no se guarda.

**✅ Criterios de Aceptación**
- Se puede editar cualquier campo: título, descripción y fecha límite.
- El título sigue siendo obligatorio al editar.
- Los cambios de fecha límite propagan la acción correcta de sync (crear/actualizar/eliminar evento) si Google está conectado.

**🎯 Definición de Terminado (DoD)**
- [ ] Edición implementada y funcional
- [ ] Validación de título obligatorio al editar
- [ ] Propagación correcta a sync según el cambio de fecha
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- Los escenarios 3 y 4 (añadir/quitar fecha) son inferencia lógica de la regla "tareas con fecha límite aparecen como eventos" (PRD §3.5) — marcados (asumido) en su detalle de borrado.

#### **🔗 Historias de Usuario Relacionadas**
- HDU-004: Creación de tarea.
- HDU-008: Cambio de estado de tarea.
- HDU-012: Sincronización de tareas como eventos de calendario.

---

## Título de la Historia de Usuario: HDU-007 - Eliminación de tarea

*Como* usuario autenticado *quiero* borrar una tarea *para* quitar de en medio lo
que ya no necesito.

**Escenario 1: Borrado de tarea**
- **Dado que** tengo una tarea creada
- **Cuando** la borro
- **Y** confirmo la acción
- **Entonces** la tarea desaparece de mi listado.

**Escenario 2: Confirmación previa (asumido)**
- **Dado que** voy a borrar una tarea
- **Cuando** pulso borrar
- **Entonces** se me pide confirmación antes de eliminarla definitivamente.

**Escenario 3: Borrado con evento asociado**
- **Dado que** tengo Google conectado y la tarea tiene un evento en el calendario
- **Cuando** borro la tarea
- **Entonces** se solicita la eliminación del evento correspondiente en Google Calendar (ver HDU-012).

**✅ Criterios de Aceptación**
- El borrado elimina la tarea del listado del usuario.
- Si la tarea tenía evento y Google está conectado, se solicita eliminar ese evento.
- El usuario solo puede borrar sus propias tareas.

**🎯 Definición de Terminado (DoD)**
- [ ] Borrado implementado y funcional
- [ ] Confirmación previa implementada
- [ ] Propagación de eliminación de evento a sync
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- La confirmación previa al borrado es buena práctica de UX (asumido: no literal en PRD).
- Distinguir **borrar** (HDU-007) de **archivar** (estado `archived`, HDU-008): borrar elimina; archivar conserva (PRD §3.2).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-008: Cambio de estado de tarea (archivar como alternativa a borrar).
- HDU-012: Sincronización de tareas como eventos de calendario.

---

## Título de la Historia de Usuario: HDU-008 - Cambio de estado de tarea

*Como* usuario autenticado *quiero* cambiar el estado de una tarea entre
`pending`, `completed` y `archived` *para* reflejar mi progreso real.

**Escenario 1: Marcar como completada**
- **Dado que** tengo una tarea en estado `pending`
- **Cuando** la marco como `completed`
- **Entonces** su estado cambia a `completed` en el listado.

**Escenario 2: Completar tarea con evento asociado**
- **Dado que** tengo Google conectado y la tarea tiene un evento
- **Cuando** marco la tarea como `completed`
- **Entonces** se solicita eliminar o marcar el evento correspondiente en Google Calendar según corresponda (ver HDU-012).

**Escenario 3: Archivar una tarea**
- **Dado que** tengo una tarea
- **Cuando** la marco como `archived`
- **Entonces** sale del listado de activas pero no se borra
- **Y** sigue siendo recuperable mediante filtro (HDU-009).

**Escenario 4: Reabrir una tarea (asumido)**
- **Dado que** tengo una tarea `completed` o `archived`
- **Cuando** la vuelvo a `pending`
- **Entonces** reaparece como activa.

**✅ Criterios de Aceptación**
- Los estados válidos son exactamente `pending`, `completed` y `archived`.
- Completar o borrar una tarea con evento desencadena la acción de sync correspondiente.
- Archivar conserva la tarea (no la borra).

**🎯 Definición de Terminado (DoD)**
- [ ] Cambio de estado implementado y funcional
- [ ] Transiciones entre los tres estados soportadas
- [ ] Propagación a sync al completar (eliminar/marcar evento)
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El PRD dice "se elimina o se marca según corresponda" para el evento al completar (PRD §3.5); la decisión exacta (eliminar vs. marcar) se cierra en HDU-012 (asumido).
- La reapertura de tareas (escenario 4) es inferencia razonable (asumido).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-006: Edición de tarea.
- HDU-009: Filtrado de tareas por estado.
- HDU-012: Sincronización de tareas como eventos de calendario.

---

# EP-003 — Organización y Filtrado

> Con varias tareas, el usuario necesita encontrarlas y agruparlas.

## Título de la Historia de Usuario: HDU-009 - Filtrado de tareas por estado

*Como* usuario autenticado *quiero* filtrar mis tareas por estado *para*
concentrarme solo en lo que me interesa en cada momento.

**Escenario 1: Ver solo pendientes**
- **Dado que** tengo tareas en distintos estados
- **Cuando** aplico el filtro `pending`
- **Entonces** el listado muestra solo mis tareas pendientes.

**Escenario 2: Ver solo completadas**
- **Dado que** tengo tareas completadas
- **Cuando** aplico el filtro `completed`
- **Entonces** el listado muestra solo mis tareas completadas.

**Escenario 3: Ver archivadas**
- **Dado que** tengo tareas archivadas
- **Cuando** aplico el filtro `archived`
- **Entonces** el listado muestra solo mis tareas archivadas.

**Escenario 4: Filtro sin resultados**
- **Dado que** aplico un filtro de estado
- **Cuando** no hay tareas en ese estado
- **Entonces** veo un estado vacío acorde al filtro aplicado.

**✅ Criterios de Aceptación**
- Se puede filtrar el listado por cada uno de los estados (`pending`, `completed`, `archived`).
- El filtrado respeta el aislamiento de datos del usuario.
- Un filtro sin coincidencias muestra un estado vacío comprensible.

**🎯 Definición de Terminado (DoD)**
- [ ] Filtros por estado implementados y funcionales
- [ ] Estado vacío por filtro implementado
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- El PRD pide filtrar "por estado" como mínimo (PRD §3.3). No se incluyen otros filtros (etiquetas/proyectos están fuera de alcance).
- La combinación de varios filtros a la vez queda fuera del MVP salvo decisión de refinamiento (asumido).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-005: Visualización del listado de tareas.
- HDU-008: Cambio de estado de tarea.

---

# EP-004 — Exportación de Datos

> Los usuarios quieren poder llevarse sus datos.

## Título de la Historia de Usuario: HDU-010 - Exportación de tareas a CSV

*Como* usuario autenticado *quiero* exportar mis tareas a un archivo CSV *para*
tener una copia de mis datos y poder usarlos fuera de FlowSync.

**Escenario 1: Exportación exitosa**
- **Dado que** tengo tareas creadas
- **Cuando** solicito exportar a CSV
- **Entonces** se descarga un archivo CSV que incluye, como mínimo, título, descripción, estado y fecha límite de cada tarea.

**Escenario 2: Exportación incluye solo mis tareas**
- **Dado que** existen tareas de otros usuarios
- **Cuando** exporto
- **Entonces** el CSV contiene únicamente mis tareas.

**Escenario 3: Exportación sin tareas (asumido)**
- **Dado que** no tengo ninguna tarea
- **Cuando** solicito exportar
- **Entonces** se descarga un CSV solo con la fila de encabezados (o se me informa de que no hay datos).

**✅ Criterios de Aceptación**
- El CSV incluye al menos: título, descripción, estado y fecha límite.
- La exportación contiene exclusivamente las tareas del usuario que la solicita.

**🎯 Definición de Terminado (DoD)**
- [ ] Exportación a CSV implementada y funcional
- [ ] Columnas mínimas requeridas presentes
- [ ] Aislamiento de datos verificado en la exportación
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- Detalles de formato (separador, codificación UTF-8, formato de fecha, manejo de comas/saltos en la descripción) quedan a definir en refinamiento (asumido).
- Solo exportación; **no** se pide importación (fuera de alcance).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-005: Visualización del listado de tareas.

---

# EP-005 — Sincronización con Google Calendar

> La funcionalidad diferenciadora **y la más compleja y arriesgada** del MVP.
> Dirección primaria: FlowSync → Google. La sync funciona de forma resiliente:
> nunca bloquea el CRUD y se reintenta ante fallos.

## Título de la Historia de Usuario: HDU-011 - Conexión y desconexión de la cuenta de Google

*Como* usuario autenticado *quiero* conectar (y poder desconectar) mi cuenta de
Google a FlowSync *para* autorizar que mis tareas se reflejen en mi calendario.

**Escenario 1: Conexión OAuth exitosa**
- **Dado que** estoy autenticado y no tengo Google conectado
- **Cuando** inicio la conexión y autorizo los permisos en el flujo OAuth de Google
- **Entonces** FlowSync guarda de forma segura los tokens de Google
- **Y** mi cuenta queda marcada como conectada.

**Escenario 2: Usuario cancela la autorización (asumido)**
- **Dado que** estoy en el flujo OAuth de Google
- **Cuando** rechazo o cancelo la autorización
- **Entonces** vuelvo a FlowSync sin conexión establecida
- **Y** se me informa de que no se conectó.

**Escenario 3: Desconexión**
- **Dado que** tengo Google conectado
- **Cuando** desconecto mi cuenta de Google
- **Entonces** FlowSync deja de sincronizar
- **Y** mis tareas ya creadas **no** se borran
- **Y** los tokens almacenados se eliminan/invalidan (asumido).

**Escenario 4: Privacidad de tokens**
- **Dado que** hay tokens de Google almacenados
- **Cuando** otro usuario opera en el sistema
- **Entonces** nunca puede acceder a mis tokens ni a mi calendario.

**✅ Criterios de Aceptación**
- La conexión se realiza mediante OAuth 2.0 con Google.
- Los tokens se almacenan de forma segura y son privados por usuario.
- La desconexión detiene la sincronización sin borrar las tareas existentes.

**🎯 Definición de Terminado (DoD)**
- [ ] Flujo OAuth de conexión implementado y funcional
- [ ] Almacenamiento seguro de tokens
- [ ] Desconexión implementada (cesa sync, conserva tareas)
- [ ] Manejo de cancelación del usuario en el flujo OAuth
- [ ] Privacidad de tokens verificada entre usuarios
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- Requiere configuración de proyecto en Google Cloud Console y revisión de permisos; el setup puede llevar más tiempo del esperado (PRD §7) — considerar **spike previo**.
- Esta historia impacta directamente la métrica de éxito "≥ 40% conectan Google" (PRD §6).
- El destino y comportamiento de los tokens al desconectar (borrar vs. invalidar) se confirma en refinamiento (asumido).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-012: Sincronización de tareas como eventos de calendario.
- HDU-002: Inicio de sesión.

---

## Título de la Historia de Usuario: HDU-012 - Sincronización de tareas como eventos de calendario

*Como* usuario con Google conectado *quiero* que mis tareas con fecha límite se
reflejen automáticamente como eventos en Google Calendar y se mantengan al día
*para* no tener que copiar nada a mano ni mirar dos sitios distintos.

**Escenario 1: Crear evento al crear tarea con fecha**
- **Dado que** tengo Google conectado
- **Cuando** creo una tarea con fecha límite
- **Entonces** se crea un evento correspondiente en mi Google Calendar.

**Escenario 2: Actualizar evento al cambiar la fecha**
- **Dado que** una tarea ya tiene su evento en el calendario
- **Cuando** cambio su fecha límite en FlowSync
- **Entonces** el evento correspondiente se actualiza en Google Calendar.

**Escenario 3: Eliminar/marcar evento al completar o borrar**
- **Dado que** una tarea con evento existe
- **Cuando** la completo o la borro en FlowSync
- **Entonces** el evento correspondiente se elimina o se marca en Google Calendar según corresponda.

**Escenario 4: Fallo de la API de Google — la tarea no se pierde**
- **Dado que** la API de Google no está disponible o devuelve error
- **Cuando** realizo una operación de tarea que debe sincronizar
- **Entonces** la tarea se guarda igualmente en FlowSync
- **Y** la operación de sincronización se reintenta más tarde
- **Y** el fallo queda registrado en logs para diagnóstico.

**Escenario 5: Tarea sin fecha límite no genera evento**
- **Dado que** tengo Google conectado
- **Cuando** creo una tarea sin fecha límite
- **Entonces** no se crea ningún evento en el calendario.

**Escenario 6: Sin Google conectado no hay sincronización**
- **Dado que** no tengo Google conectado
- **Cuando** creo o edito tareas con fecha
- **Entonces** las tareas se gestionan con normalidad pero no se crean eventos.

**✅ Criterios de Aceptación**
- Solo las tareas **con fecha límite** generan eventos.
- Crear/cambiar fecha/completar/borrar propagan la acción correcta sobre el evento.
- Si la sync falla, la tarea **se guarda igual** y la operación se reintenta; el fallo se registra en logs.
- La sincronización nunca bloquea las operaciones de CRUD.
- Objetivo de fiabilidad: < 5% de operaciones de sync fallan de forma no recuperable.

**🎯 Definición de Terminado (DoD)**
- [ ] Creación, actualización y eliminación/marcado de eventos implementadas
- [ ] Mecanismo de reintento ante fallos implementado
- [ ] Logs de operaciones de sincronización implementados
- [ ] Regla de zona horaria fecha límite → hora de evento definida e implementada
- [ ] Sync desacoplada del CRUD (no bloqueante) verificada
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

**📝 Notas Adicionales**
- **Mayor riesgo técnico del MVP** (PRD §7): rate limits, zonas horarias, eventos recurrentes / de todo el día pueden inflar el alcance. **Se recomienda spike técnico antes de comprometer la decomposición fina.**
- **Zonas horarias:** la relación fecha límite ↔ hora del evento debe definirse con cuidado (PRD §7). Propuesta de partida (asumido): crear el evento como "todo el día" en la fecha límite, para evitar horas erróneas — a confirmar en el spike.
- "Eliminar o marcar" el evento al completar: la decisión exacta se cierra en refinamiento (PRD §3.5).
- **Fuera de alcance:** sync inversa completa Google → FlowSync (requiere spike y gestión de conflictos/webhooks — PRD §3.5, §7).
- Impacta la métrica "< 5% de fallos no recuperables" (PRD §6).

#### **🔗 Historias de Usuario Relacionadas**
- HDU-011: Conexión y desconexión de la cuenta de Google.
- HDU-004: Creación de tarea.
- HDU-006: Edición de tarea.
- HDU-007: Eliminación de tarea.
- HDU-008: Cambio de estado de tarea.

---

## 3. Resumen de trazabilidad PRD → backlog

| Requisito PRD | Cubierto por |
| --- | --- |
| §3.1 Autenticación y cuenta | HDU-001, HDU-002, HDU-003 |
| §3.2 CRUD de tareas y estados | HDU-004, HDU-005, HDU-006, HDU-007, HDU-008 |
| §3.3 Organización y filtrado | HDU-009 (+ estado vacío en HDU-005) |
| §3.4 Exportación | HDU-010 |
| §3.5 Sincronización Google Calendar | HDU-011, HDU-012 |
| §4 No funcionales (privacidad, < 1 s, responsive, errores, logs) | Transversal: HDU-002/005/011/012 + DoD de todas |
| §6 Métricas de éxito | HDU-011 (≥40% conexión), HDU-012 (<5% fallos), HDU-005 (<1 s) |

**Cobertura:** los 5 bloques funcionales del PRD quedan cubiertos por 12 HDU en
5 épicas, con el esfuerzo concentrado en EP-001, EP-002 y EP-005 (las 3
funcionalidades críticas).
