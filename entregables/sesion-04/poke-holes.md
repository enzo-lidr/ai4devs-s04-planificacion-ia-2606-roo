# Poke-holes: "Crear evento en Google Calendar a partir de una tarea con fecha límite"

## 1. Zonas horarias / ambigüedad fecha vs. fecha+hora
El PRD señala explícitamente en la sección de Riesgos que las zonas horarias
son "el mayor riesgo técnico del MVP", pero ningún criterio de aceptación de
esta story lo aborda. Además, el glosario define "fecha límite" sin aclarar
si incluye hora — esto determina si el evento se crea como "todo el día" o
con horario específico, y afecta directamente cómo se resuelve el riesgo de
zona horaria.

## 2. Rate limits de la API de Google no contemplados
El PRD menciona los rate limits de la API de Google Calendar como riesgo
conocido (sección 7), pero los AC solo cubren el caso genérico de
"la API no está disponible" (errores 5xx). No hay criterio para el caso
específico de un usuario creando muchas tareas con fecha límite en poco
tiempo y recibiendo respuestas 429.

## 3. Riesgo de eventos duplicados por reintento
Si la creación del evento en Google tiene éxito pero la respuesta se pierde
(timeout), el sistema podría interpretarlo como fallo y reintentar, creando
un segundo evento duplicado para la misma tarea. El PRD dice "se reintenta
más tarde" pero no contempla la idempotencia de esos reintentos.

## 4. Revocación de acceso OAuth fuera de FlowSync
El PRD solo distingue el caso de "API no disponible" (error de
disponibilidad), pero no el caso de que el usuario revoque el permiso
OAuth directamente desde su cuenta de Google. Esto produciría un error de
autorización (401/403) distinto, que probablemente debería desencadenar
una acción distinta (marcar la cuenta como desconectada) en vez de un
simple reintento.

## 5. Backfill de tareas existentes al conectar Google por primera vez
Ninguna story cubre qué pasa con las tareas que el usuario ya tenía creadas
(con fecha límite) ANTES de conectar su cuenta de Google. ¿Se generan
eventos retroactivos para ellas al conectar, o la sincronización solo
aplica hacia adelante? Es una decisión de producto real que afecta la
primera experiencia del usuario al conectar.
