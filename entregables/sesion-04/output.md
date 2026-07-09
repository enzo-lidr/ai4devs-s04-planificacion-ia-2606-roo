# Output crudo de la IA

> Generado ejecutando `prompt.md` contra `docs/PRD.md` con Claude Code. **Sin
> retocar** (tal cual lo devolvió la IA). El análisis crítico va aparte, en
> `poke-holes.md`.

---

# Autenticación y gestión de cuenta

## 1. Registro de cuenta con email y contraseña
**Historia:** Como visitante, quiero crear una cuenta con mi email y una contraseña, para tener un espacio propio y privado donde gestionar mis tareas.

**Criterios de aceptación:**
- Given un visitante en la pantalla de registro, When introduce un email válido y una contraseña de al menos 8 caracteres y confirma, Then se crea su cuenta y accede a la pantalla de bienvenida.
- Given un visitante en el registro, When introduce una contraseña de menos de 8 caracteres, Then el sistema rechaza el registro y muestra un mensaje comprensible indicando que la contraseña debe tener al menos 8 caracteres.
- Given un email que ya está registrado, When el visitante intenta registrarse con ese email, Then el sistema lo indica y ofrece un enlace para ir al inicio de sesión.
- Given un email con formato inválido, When el visitante intenta registrarse, Then se muestra un error de validación claro y no se crea la cuenta. (asumido)

## 2. Inicio y cierre de sesión
**Historia:** Como usuario registrado, quiero iniciar y cerrar sesión con mis credenciales, para acceder a mis tareas de forma segura y salir cuando termine.

**Criterios de aceptación:**
- Given un usuario registrado en la pantalla de inicio de sesión, When introduce su email y contraseña correctos, Then accede a su cuenta y ve su listado de tareas.
- Given un usuario en el inicio de sesión, When introduce credenciales incorrectas, Then se muestra un mensaje de error comprensible y no accede. (asumido)
- Given una sesión iniciada, When el usuario navega por la aplicación, Then la sesión se mantiene mediante tokens de acceso sin volver a pedir credenciales.
- Given un usuario autenticado, When pulsa cerrar sesión, Then su sesión finaliza y se le redirige a la pantalla de inicio de sesión. (asumido)

## 3. Pantalla de bienvenida tras el registro (onboarding mínimo)
**Historia:** Como usuario recién registrado, quiero ver una pantalla de bienvenida que explique qué hace FlowSync, para entender el producto y crear mi primera tarea.

**Criterios de aceptación:**
- Given un registro exitoso, When el usuario llega a la aplicación por primera vez, Then ve una pantalla de bienvenida que explica en una frase qué hace FlowSync.
- Given la pantalla de bienvenida, When se muestra, Then incluye una invitación o acción explícita para crear la primera tarea.
- Given un usuario que ya completó el onboarding, When vuelve a iniciar sesión, Then no se le muestra de nuevo la pantalla de bienvenida. (asumido)

---

# Gestión de tareas (CRUD)

## 4. Crear una tarea
**Historia:** Como usuario autenticado, quiero crear una tarea indicando al menos un título, para registrar algo que tengo que hacer.

**Criterios de aceptación:**
- Given un usuario autenticado, When crea una tarea con un título, Then la tarea se guarda y aparece en su listado.
- Given el formulario de creación, When el usuario añade descripción y/o fecha límite (ambos opcionales), Then se guardan junto a la tarea.
- Given una tarea recién creada, When se guarda, Then su estado inicial es `pending`.
- Given el formulario de creación, When el usuario intenta crear una tarea sin título, Then se muestra un error de validación indicando que el título es obligatorio y la tarea no se crea.

## 5. Editar una tarea
**Historia:** Como usuario autenticado, quiero editar cualquier campo de una tarea existente, para mantenerla actualizada.

**Criterios de aceptación:**
- Given una tarea existente, When el usuario modifica su título, descripción o fecha límite y guarda, Then los cambios se reflejan en el listado.
- Given una tarea sin fecha límite, When el usuario le añade una fecha límite y guarda, Then la tarea queda con esa fecha límite.
- Given una tarea en edición, When el usuario borra el título y guarda, Then se muestra el error de título obligatorio y no se guardan los cambios. (asumido)

## 6. Cambiar el estado de una tarea
**Historia:** Como usuario autenticado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar en qué punto está.

**Criterios de aceptación:**
- Given una tarea en `pending`, When el usuario la marca como completada, Then su estado pasa a `completed`.
- Given una tarea, When el usuario la archiva, Then su estado pasa a `archived`.
- Given un cambio de estado, When se aplica, Then solo se admiten los valores `pending`, `completed` o `archived`.
- Given una tarea en `completed`, When el usuario la reabre, Then su estado vuelve a `pending`. (asumido)

## 7. Borrar una tarea
**Historia:** Como usuario autenticado, quiero borrar una tarea, para eliminar lo que ya no necesito.

**Criterios de aceptación:**
- Given una tarea existente, When el usuario la borra, Then desaparece de su listado.
- Given una acción de borrado, When el usuario la solicita, Then se le pide confirmación antes de eliminarla definitivamente. (asumido)
- Given una tarea borrada, When se completa el borrado, Then deja de aparecer en los filtros y en las exportaciones.

---

# Organización y filtrado

## 8. Ver, ordenar y filtrar el listado de tareas
**Historia:** Como usuario autenticado, quiero ver mi listado de tareas ordenado y poder filtrarlo por estado, para encontrar rápidamente lo relevante para hoy.

**Criterios de aceptación:**
- Given un usuario con varias tareas, When abre su listado, Then ve sus tareas ordenadas de forma que las más relevantes para "hoy" aparecen primero.
- Given el listado, When el usuario filtra por un estado (p. ej. solo `pending` o solo `completed`), Then solo se muestran las tareas de ese estado.
- Given un usuario sin ninguna tarea (cuenta nueva o todas archivadas), When abre el listado, Then ve un estado vacío con una invitación a crear la primera tarea.
- Given un usuario con hasta 200 tareas, When abre el listado, Then este carga en menos de 1 segundo.

---

# Exportación

## 9. Exportar tareas a CSV
**Historia:** Como usuario autenticado, quiero exportar mis tareas a un archivo CSV, para poder llevarme mis datos.

**Criterios de aceptación:**
- Given un usuario con tareas, When solicita la exportación, Then se descarga un archivo CSV con sus tareas.
- Given el CSV generado, When se abre, Then incluye como mínimo título, descripción, estado y fecha límite de cada tarea.
- Given un usuario sin ninguna tarea, When solicita la exportación, Then se genera un CSV con solo la cabecera o se le indica que no hay tareas que exportar. (asumido)

---

# Sincronización con Google Calendar

## 10. Conectar la cuenta de Google (OAuth)
**Historia:** Como usuario autenticado, quiero conectar mi cuenta de Google a FlowSync mediante OAuth, para que mis tareas se sincronicen con Google Calendar.

**Criterios de aceptación:**
- Given un usuario autenticado sin Google conectado, When inicia la conexión y autoriza los permisos en Google, Then FlowSync queda conectado y puede leer y escribir en su calendario.
- Given el flujo de OAuth, When el usuario deniega o cancela la autorización, Then FlowSync no queda conectado y muestra un mensaje claro. (asumido)
- Given los tokens obtenidos de Google, When se almacenan, Then se guardan de forma segura y no son accesibles por otros usuarios.
- Given una conexión establecida, When se completa, Then el usuario ve reflejado que su Google Calendar está conectado. (asumido)

## 11. Reflejar y actualizar tareas con fecha como eventos de Google Calendar
**Historia:** Como usuario con Google conectado, quiero que mis tareas con fecha límite se reflejen y actualicen como eventos en Google Calendar, para no tener que copiarlas a mano y mirar un solo sitio.

**Criterios de aceptación:**
- Given un usuario con Google conectado, When tiene o crea una tarea con fecha límite, Then aparece el evento correspondiente en su Google Calendar.
- Given una tarea ya sincronizada, When el usuario cambia su fecha límite en FlowSync, Then el evento correspondiente en Google Calendar se actualiza a la nueva fecha.
- Given una tarea sincronizada, When el usuario la completa o la borra en FlowSync, Then el evento correspondiente en Google Calendar se elimina o se marca según corresponda.
- Given una tarea sin fecha límite, When se crea o edita, Then no se genera ningún evento en el calendario. (asumido)

## 12. Desconectar Google y resiliencia ante fallos de sincronización
**Historia:** Como usuario con Google conectado, quiero poder desconectar Google y que la sincronización tolere fallos de su API, para mantener el control y no perder tareas si Google no responde.

**Criterios de aceptación:**
- Given un usuario con Google conectado, When desconecta su cuenta de Google, Then FlowSync deja de sincronizar pero conserva las tareas ya creadas.
- Given la API de Google no disponible o devolviendo errores, When el usuario guarda una tarea, Then la tarea se guarda en FlowSync aunque la sincronización falle.
- Given una operación de sincronización fallida, When se produce el fallo, Then la sincronización se reintenta más tarde.
- Given las operaciones de sincronización con Google, When se ejecutan, Then quedan registradas en logs para poder diagnosticar fallos.
