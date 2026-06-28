- Contexto : quiero descomponer el MVP de un PRD en historias de Usuario; el PRD esta en el archivo adjunto “PRD — FlowSync MVP.txt”, la aplicación se llama FlowSync.
- Role: Eres un experto product owner con altos conocimientos en SAAS, además de un perfil técnico y experiencia en agilidad y métricas
- Formato de la historia de usuario: “Como [rol], quiero [acción] para [beneficio];solo hay 2 tipos de usuario en el PRD, por eso dependiendo de la historia los roles pueden ser : el usuario que usara la aplicación o el propio producto owner
Ejemplo de Historia  de Usuario para “Registro y autenticación de usuarios” del PRD.
Como Usuario de FlowSync quiero logarme con mi correo personal para autenticarme en la aplicación y poder ver mis tareas
- Criterios de Aceptación: Define entre 3 y 5 criterios que no Sean happy path y Sean verificables; deben ir en formato gherkin (Given/when / then)
Ejemplos de Criterio de Aceptación (te paso 2 happy path para referencia tu haz no happy path):
	Given un text box para poner el correo y un text box para escribir la contraseña when los rellenos con los datos correctos y pulso entrar then aparezco logado en la aplicación.
	Given un text box para poner el correo y un text box para escribir la contraseña when los rellenos con los datos incorrectos y pulso entrar then aparece un mensaje de error.
- DOD: documentación; test pasados, pullrequest validado, openspec archivado tras merge.
- Tamaño: deben caber en un sprint de 2 semanas para 1 persona.
- Alcance: Solo define los puntos marcados dentro del MVP
- Aplica el Patron INVEST a las historias y marca aquellas que no lo cumplen 100%
- Non Goals: no propongas arquitectura; no inventes features limitate al MVP
- Agrupa las historias de usuario en cada punto del MVP y marca con una etiqueta de dificultad (bajo , medio, alto) cada historia: orden deben ir las de dificultad bajo.
- Marca con una etiqueta de "asumido" lo que infieras y no este literal al final de la Historia de Usuario
- “Al final de tu respuesta, añade una sección titulada 'Razonamiento y Transparencia'. Explica brevemente qué consideraste para crear las historias de usuario y los criterios de aceptación"
- Output de salida completo:
### HU-[N]: [Título]
**Historia**: Como...
**Criterios de aceptación**: (Gherkin)
**DoD**: ...
**Dificultad**: Bajo/Medio/Alto
**INVEST**: ✅ / ⚠️ [campo que falla]
**Asumidos**: ...
**Razonamiento y transparencia**

