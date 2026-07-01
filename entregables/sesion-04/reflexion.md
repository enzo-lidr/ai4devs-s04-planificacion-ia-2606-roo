# 🪞 Reflexión — Sesión 4

Lo que más me sorprendió fue lo mucho que cambió la calidad del output según lo afinado que estaba el prompt: la primera versión narrativa daba historias planas, y al pasar a la estructura rol → contexto → alcance → restricciones → formato → ejemplo, las historias salieron casi listas para refinar.

Lo que mejor funcionó fueron las **restricciones explícitas** (non-goals) y el marcado de *(asumido)*: sin ellas, la IA tendía a inventar features y a colarse en decisiones de arquitectura que el PRD dejaba fuera. Acotar el alcance y exigir transparencia sobre lo inferido fue lo que mantuvo el backlog honesto.

Lo que falló: la IA fue **demasiado optimista con los caminos felices** y floja con los límites. En sincronización con Google escribía "se reintenta más tarde" o "se marca según corresponda" como si fueran criterios cerrados, cuando en realidad son agujeros. También tuve que corregir a mano el conflicto entre mi plantilla (estimaciones) y la rúbrica, algo que la IA no iba a detectar sola.

El patrón **poke-holes fue lo más valioso del ejercicio**, y sí descubrió cosas que yo había pasado por alto: sobre todo la regla de zona horaria (¿a qué hora cae una tarea que solo tiene fecha?) y la revocación de permisos desde Google, que rompía la lógica de reintentos. Yo había leído el PRD y aun así no los había visto con esa claridad.

Mi conclusión es que la IA es un acelerador brutal para la primera versión del backlog, pero el criterio sigue siendo mío: decidir qué entra, qué se asume y qué hay que mandar a spike antes de comprometer.
