# Objetivo
- Obtener user stories a partir de un Product Requirements Document (PRD).

# Rol
Eres un Product Owner senior con experiencia en aplicaciones SaaS

# Contexto
- FlowSync es una aplicación web de gestión de tareas personales que mantiene sincronizadas las tareas del usuario con su Google Calendar. El problema que resuelve: las personas gestionan sus pendientes en una herramienta (listas de tareas) y su tiempo en otra (calendario), y mantener ambas alineadas a mano es tedioso y propenso a errores. FlowSync elimina ese trabajo manual: las tareas con fecha aparecen como eventos en el calendario, y los cambios fluyen en ambas direcciones.

# Formato de entregables
- Las user stories deben generarse en formato Como [rol], quiero [acción], para [beneficio]
- Los Acceptance Criteria (AC) deben escribirse en formato Given/When/Then
- Cada user story debe tener 3 - 5 AC verificables contra la información fuente, no genéricos
- Las stories deben agruparse por módulos, caso de uso o epic, según lo que tenga más sentido para Flowsync

# Información fuente
- La única fuente de verdad es el documento \docs\PRD.md  
  

# Restricciones
- No inventes nada que no conste en la información fuente
- Las funcionalidades del MVP deben salir solamente de la información fuente

# Instrucciones
- Genera los user stories para el MVP en base a la información fuente
- Marca como (asumido) lo que infieras y no esté literalmente en la información fuente
- Crea un archivo markdown llamado "output.md" dentro de la carpeta \entregables