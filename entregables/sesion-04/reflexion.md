# reflexion.md — Parte 4

Me sorprendió la cobertura del happy path: con un prompt bien estructurado (rol + contexto +
restricciones + ejemplo) salieron 17 stories agrupadas por épica y con AC en Given/When/Then casi
listos. 

Lo que tuve que corregir: Los AC parecen exhaustivos pero ignoran el problema de marcar una hora del calendario y su implicación con las zonas horarias
También tiende a inventar detalles (orden por defecto, formato CSV, hora del evento);
Al forzarla a marcar `(asumido)` y añadir una sección de preguntas abiertas evitó que esas invenciones
se colaran como si fueran requisitos del PRD y son de mucha ayuda a la hora de revisar.

El patrón poke-holes sí me encontró cosas que no había visto: sobre todo la duplicación de eventos
en los reintentos (la interacción entre US-13 y US-17) y el caso del usuario que ya tenía tareas con
fecha al conectar Google, asi como asumir por defecto algo que no esta especificado que es que una tarea es igual a un evento (una tarea no puede generar varios eventos). Eran gaps reales, no ruido, y son justo los que habrían explotado a mitad
de sprint. Conclusión: la IA es excelente expandiendo edge cases sobre un happy path que escribe un
humano, pero peligrosa si le delegas el criterio sin revisarla contra el sistema real.
