# Prompt de descomposición — FlowSync

```
Eres un Product Owner senior con experiencia en aplicaciones SaaS B2C.

CONTEXTO
FlowSync es una app web de gestión de tareas personales que sincroniza
las tareas del usuario con su Google Calendar en ambas direcciones (con
foco principal FlowSync → Calendar). Resuelve el problema de mantener
manualmente alineadas una lista de tareas y un calendario. Usuario
objetivo: knowledge worker de 25-45 años que ya usa Google Calendar a
diario y hoy gestiona sus pendientes en otra herramienta aparte.

Te paso el PRD completo del MVP a continuación. Tu tarea es descomponerlo
en user stories, usando SOLO la sección "3. Requisitos funcionales" como
alcance (secciones 1, 2, 4-8 son contexto de apoyo, no fuente de stories).

[PEGAR AQUÍ EL CONTENIDO COMPLETO DE docs/PRD.md]

FORMATO EXACTO
Cada story en el formato:
  Como [rol], quiero [acción], para [beneficio]
seguida de 3 a 5 criterios de aceptación en Given/When/Then, específicos
y verificables (nada de "el sistema debe funcionar correctamente").

ALCANCE
- Solo funcionalidades listadas en "Qué incluye el MVP" / sección 3.
- No inventes features que no estén en el documento.
- Si algo del PRD es ambiguo y tienes que asumir un detalle para escribir
  un AC concreto, escribe la asunción y márcala al final del criterio
  con "(asumido)".

AGRUPACIÓN
Agrupa las stories por módulo/épica según la estructura del propio PRD
(ej. Autenticación, Gestión de tareas, Organización y filtrado,
Exportación, Sincronización con Google Calendar). Usa un encabezado por
grupo.

NO HAGAS
- No estimes tiempos ni story points.
- No propongas arquitectura ni stack técnico.
- No agregues stories de post-MVP (equipos compartidos, otros
  calendarios, notificaciones push, app nativa, etiquetas/subtareas).

EJEMPLO DE FORMATO ESPERADO (una story ya escrita a mano, como referencia
de nivel de detalle — no la repitas, es solo ejemplo):

## Autenticación

### Story: Registro de cuenta
Como visitante, quiero crear una cuenta con email y contraseña,
para poder empezar a usar FlowSync con mis propios datos.

- Given que no tengo cuenta, When ingreso un email no registrado y una
  contraseña de al menos 8 caracteres, Then se crea mi cuenta y quedo
  autenticado.
- Given que ingreso un email ya registrado, When intento crear la
  cuenta, Then el sistema me lo indica y me ofrece ir a inicio de
  sesión.
- Given que ingreso una contraseña de menos de 8 caracteres, When
  intento crear la cuenta, Then veo un mensaje de validación claro y
  la cuenta no se crea.

Entrega el resultado completo, agrupado, en Markdown.
```
