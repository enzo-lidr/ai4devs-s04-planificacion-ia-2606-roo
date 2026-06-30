# Reflexión

### ¿Qué te sorprendió del output de la IA?

Lo que más me sorprendió fue la rapidez con la que la IA transformó un PRD relativamente extenso en un backlog bien estructurado y agrupado por épicas. Como punto de partida, el resultado fue bastante sólido y ahorra mucho tiempo en una actividad que normalmente requiere varias horas de análisis.

Sin embargo, también noté que la IA tiende a completar vacíos con supuestos sin dejar siempre claro que los está infiriendo. En un entorno laboral esto puede ser riesgoso, porque un backlog construido sobre supuestos no validados puede generar desarrollos que no respondan realmente a la necesidad del negocio. Por eso fue necesario reforzar el prompt para limitar el alcance y exigir que cualquier inferencia quedara marcada como "(asumido)".

### ¿Qué falló o tuviste que corregir?

Inicialmente elaboré mi propio prompt, pero el resultado no fue el esperado. Las historias de usuario se generaron con un formato incompleto, algunos criterios de aceptación eran demasiado genéricos y varias historias carecían del nivel de detalle necesario para ser implementadas. A partir de esa primera iteración decidí apoyarme en la IA para mejorar el prompt, agregando restricciones, ejemplos y un formato de salida mucho más específico. Ese ajuste mejoró considerablemente la calidad y consistencia del backlog generado.

### ¿El patrón *poke-holes* te encontró algo que tú no habías visto?

La parte de AI as poke-holes fue la que más valor me aportó. Aunque la historia seleccionada parecía completa a primera vista, el análisis permitió identificar varios supuestos y escenarios que no estaban explícitos en el PRD y que podrían generar comportamientos inesperados durante el desarrollo. Por ejemplo, inicialmente asumí que, una vez conectada la cuenta de Google, la sincronización siempre funcionaría correctamente. Sin embargo, el análisis hizo evidente que el token de acceso puede dejar de ser válido con el tiempo, ya sea porque el usuario revocó los permisos desde Google o porque la autorización expiró. Si ese escenario no se considera desde el refinamiento, el sistema podría seguir mostrando la cuenta como “conectada”, cuando en realidad la sincronización ya no es posible.
También identifiqué otros supuestos importantes, como el caso en que el usuario concede solo algunos permisos (OAuth scopes) durante la autorización o qué debería ocurrir con las tareas que ya existían antes de conectar Google Calendar. Ninguno de estos escenarios estaba definido en la historia, pero todos afectan directamente la experiencia del usuario y el comportamiento esperado del sistema. Esto me hizo comprender que una historia puede parecer completa porque cubre el flujo principal (happy path), pero seguir dejando fuera casos límite, reglas de negocio y dependencias que normalmente aparecen durante el refinamiento funcional. En un entorno laboral, detectar estos vacíos antes de iniciar el desarrollo reduce retrabajos, evita interpretaciones distintas entre desarrollo y QA y ayuda a construir un backlog mucho más sólido.

Como aprendizaje, me llevo que la IA es una excelente herramienta para acelerar la descomposición de requisitos, pero no reemplaza el criterio del Product Owner ni del equipo de desarrollo. La calidad del backlog depende tanto de un buen prompt como de una revisión crítica posterior que permita descubrir ambigüedades, supuestos y riesgos antes de que lleguen a la etapa de implementación.
