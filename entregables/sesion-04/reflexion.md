# Reflexión — Ejercicio S4: Inventario del backlog

Lo que más me sorprendió del output de la IA fue el nivel de honestidad ante
la ambigüedad: cuando el PRD dice que un evento "se elimina o se marca según
corresponda" sin precisar cuál de las dos, la IA no inventó una respuesta —
lo marcó explícitamente como ambigüedad sin resolver. No solo señaló lo que
asumió, sino que reconoció cuando ni siquiera ella podía decidir.

Lo que más tuve que corregir fue mío, no de la IA: al diseñar el ejemplo
para el prompt, propuse stories como "buscar tareas vencidas" y "buscar
usuarios conectados con su cuenta de Google" — ninguna estaba en el PRD, y
la segunda incluso violaba el non-goal de privacidad del MVP (un usuario
viendo datos de otro). Tuve que revisarlas contra el documento y
descartarlas. Fue una forma directa de vivir la "false completeness" de la
que habla la sesión: cosas que suenan plausibles pero no están escritas.

El patrón poke-holes sí me encontró cosas que no había considerado: que el
usuario puede revocar el acceso OAuth directamente desde su cuenta de
Google (no solo desde FlowSync), y que ninguna story cubre qué pasa con las
tareas que ya existían antes de conectar Google por primera vez (backfill).
Los hallazgos más fuertes, en cualquier caso, fueron los que conectaban con
riesgos que el propio PRD ya señalaba (zonas horarias, rate limits) pero
que no llegaron a los criterios de aceptación.
