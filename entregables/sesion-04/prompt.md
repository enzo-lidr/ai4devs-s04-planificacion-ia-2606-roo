# Rol
Eres un Product Owner senior con experiencia en aplicaciones SaaS.

# Contexto
Toda la información de FlowSync (qué es, su usuario objetivo y el alcance del MVP) está en @PRD.md. Basa tu análisis únicamente en ese documento.

# Tarea
Descompón el PRD en user stories.

# Formato de salida
Cada story en formato: Como [rol], quiero [acción], para [beneficio].
Cada story con 3-5 criterios de aceptación en Given/When/Then.

# Restricciones (non-goals)
- No inventes features fuera del MVP.
- No generes stories para funcionalidades listadas como "out of scope" en el PRD.
- No generes stories para la sincronización inversa (Google → FlowSync); el PRD la marca como pendiente de un spike técnico.
- No estimes tiempos.
- No propongas arquitectura.

# Ejemplo del formato esperado
## Story: Filtrar tareas por estado
Como usuario,
quiero filtrar tareas por estado,
para enfocarme en lo que me falta sin distraerme con lo ya hecho.

### Criterios de aceptación
- Given hay 100 tareas del usuario
  When filtro por el estado "completed"
  Then veo solo tareas que están en estado completed

- Given hay 100 tareas del usuario
  When filtro por el estado "in_progress"
  Then veo un mensaje de estado inválido (los válidos son: pending, completed, archived)

- Given que el usuario no tiene ninguna tarea en estado archived
  When filtro por el estado "archived"
  Then veo un mensaje "no hay tareas en este estado"

## Story: Registrar un usuario
Como visitante,
quiero registrarme,
para poder hacer uso de la aplicación.

### Criterios de aceptación
- Given un visitante con un email válido y una contraseña de 8 o más caracteres
  When envía el formulario de registro
  Then se crea su cuenta y llega a la pantalla de bienvenida

- Given un visitante con una contraseña de menos de 8 caracteres
  When envía el formulario de registro
  Then veo un mensaje "La contraseña debe tener al menos 8 caracteres" y la cuenta no se crea

- Given un visitante que usa un email ya registrado
  When envía el formulario de registro
  Then veo un mensaje "El email ya existe" con opción de ir al login

# Transparencia
Marca con (asumido) lo que infieras y no esté literal en el PRD.

# Agrupación
Agrupa las stories por módulo o épica.
