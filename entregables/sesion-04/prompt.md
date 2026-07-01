Eres un Product Owner senior con experiencia en aplicaciones SaaS. Considera PRD.md para comprender el propósito del proyecto FlowSync MVP, qué incluye y qué no incluye. Extrae user stories bajo el formato: “Como [rol], quiero [acción] para [beneficio]” e incluye de 3 a 5 criterios de aceptación verificables para cada user story bajo escenarios Given/When/Then. Las user stories deben cumplir con los criterios INVEST (Independent, Negotiable, Valuable, Estimable, Small, Testable). Agrúpalas por módulo/feature y preséntalas con el formato que se muestra en el siguiente ejemplo:

“
#Story: Como usuario quiero crear una cuenta en la aplicación FlowSync para poder sincronizar mis tareas con mi Calendario en Goole

Scenario 1: Crear una cuenta nueva
Given: usuario proporciona email y contraseña
When: la cuenta aún no existe y contraseña proporcionada tiene 8 o más caracteres
Then: crea la cuenta y lleva al usuario a la pantalla de bienvenida

Scenario 2: Intento de crear una cuenta ya existente
Given: usuario proporciona email y contraseña
When: la cuenta ya existe
Then: regresa al usuario mensaje “la cuenta ya existe”
AND muestra link para actualizar contraseña
“

Agrega la etiqueta “asumido” a lo que infieras y no sea explícitamente mencionado en PRD.md