# Reflexión

Me sorprendió lo bien que la IA respetó el alcance cuando el prompt fue explícito con los non-goals: no inventó equipos, etiquetas ni sincronización inversa, justo lo que el PRD dejaba fuera. El patrón estructura → restricciones → ejemplo → transparencia se nota: el ejemplo de formato evitó que se desviara del Given/When/Then.

Lo que falló: los criterios de aceptación de la primera pasada eran correctos pero "cómodos" — cubrían el camino feliz y poco más. La marca `(asumido)` ayudó a ver dónde la IA rellenaba huecos del PRD (orden por defecto, ocultar archivadas, alcance del CSV) que en realidad son decisiones de producto pendientes.

El poke-holes encontró cosas que no había visto: sobre todo el caso de **quitar la fecha límite a una tarea ya sincronizada** (cae entre dos stories) y la falta de **política de reintento medible**, que además choca con el criterio de éxito de "<5% de fallos no recuperables" del PRD. La lección: la IA genera un backlog plausible rápido, pero el valor real aparece al pedirle que ataque su propio output.
