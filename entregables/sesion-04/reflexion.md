# Reflexion: Ejercicio de Descomposicion y Poke-Holes

El proceso de descomponer la user story en tasks con Fibonacci me ayudo a identificar dependencias claras entre infraestructura, endpoints y tests. Lo que mas me sorprchio fue cuantos supuestos tecnicos (asumido) habia ocultos en una aparente simple creacion de endpoint. 

El patron poke-holes fue muy valioso: reconocimiento 5 gaps criticos en la infraestructura que no habia notado inicialmente, especialmente el tema de indices de base de datos que impacta directamente el requisito no funcional de performance. 

Falle en no considerar inicialmente la cardinalidad exacta de User-Task, lo cual el analisis poke-holes revelo como un riesgo de privacidad. Esto demuestra que el patron funciono: encontero cosas que yo no habia visto.

El mayor aprendizaje fue que las historias de usuario sencillas esconden complejidad tecnica que solo se revela con analisis estructurado.