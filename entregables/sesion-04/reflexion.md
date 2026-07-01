# Reflexión — Ejercicio Inventario del backlog

Lo que más me sorprendió fue **lo poderoso que resulta delimitar bien el trabajo**:
no solo definir lo que la IA debe y puede hacer, sino sobre todo marcarle sus
límites, lo que NO debe tocar. Esa restricción explícita es la que hace que el
resultado sea favorable.

Lo que más me costó fue la **terminología**, pero no solo eso: entender cómo se
implementa esto, sus alcances y qué esperar de cada fase. Siendo realista, una vez
que lo ves montado no es *rocket science* — el reto real es aprender a controlar
la IA, no la técnica en sí.

El patrón **poke-holes** me encontró varios huecos que no veía venir, y de hecho
creo que **es justo lo que más valor nos agregaría en la empresa**: por falta de
esta disciplina, muchas cosas se nos escapan hasta el testeo o incluso hasta
producción. Los dos hallazgos que más me pegaron fueron el de la **hora y la zona
horaria** (normalmente pensamos solo en local, más en un país que en el ~90% de su
territorio usa un solo huso horario) y el de la **tarea que ya existía** antes de
conectar Google.
