ANÁLISIS CRÍTICO DE LA HISTORIA 2.1 — "Crear tarea con al menos un título"

No reescribo la historia; a continuación listo lo que falta, lo que se asume y lo que introduce riesgo.

---

1. EDGE CASES (casos límite no cubiertos por los criterios actuales)

Sobre el campo título:
- Título con longitud máxima: no hay límite superior definido. ¿Qué pasa con un título de 10.000 caracteres? Riesgo de UI rota o error de base de datos.
- Título con caracteres especiales, emojis o RTL: no se especifica normalización ni sanitización.


Sobre la fecha límite:
- Fecha límite en el pasado: ¿se permite crear una tarea con fecha ya vencida? No está definido y tiene implicación directa en Google Calendar (crearía un evento en el pasado).
- Formato y zona horaria de la fecha: crítico. El PRD (§7 Riesgos) señala que la relación fecha límite ↔ hora del evento debe definirse con cuidado. La historia no dice si la fecha límite es solo fecha (día) o fecha+hora, ni en qué zona horaria se interpreta.

Sobre la descripción:
- Longitud máxima de la descripción: no definida.

---

2. SUPUESTOS IMPLÍCITOS (asumidos y no escritos literalmente)

- Se asume que la tarea creada se asocia automáticamente al usuario autenticado y a nadie más (aislamiento por usuario del PRD §3.1 / §4).

---

3. ESCENARIOS FALTANTES (deberían ser criterios de aceptación adicionales)

- Error de validación con mensaje comprensible (no técnico), según requisito no funcional del PRD §4 "Errores claros". Los criterios actuales no exigen que el mensaje sea comprensible.
- Fallo de guardado en el servidor (error 500 / red caída): ¿se informa al usuario y se preserva lo tecleado? No hay criterio de comportamiento ante fallo de persistencia.

---

4. DEPENDENCIAS Y RIESGOS NO MENCIONADOS

Dependencias:
- Depende de Autenticación (Epígrafe 1): sin usuario autenticado no hay creación posible.
- Acopla con Sincronización (Historia 5.2/5.3): crear una tarea con fecha límite dispara la creación de un evento. La historia 2.1 no menciona este efecto secundario, lo que puede llevar a implementarla sin prever el punto de integración.

Riesgos:
- Zonas horarias (PRD §7): si la fecha límite se guarda sin zona horaria clara, al sincronizar con Google se crearán eventos a horas erróneas. Riesgo alto y directamente originado en la creación.
- Semántica de "fecha límite": si es día completo vs. hora concreta cambia tanto la UX de creación como el tipo de evento en Calendar (all-day event vs. timed event).
- Ausencia de límites de longitud: riesgo de abuso/errores de almacenamiento y de rendimiento en el listado (§4 exige <1s con hasta 200 tareas).

---

5. CONFLICTO / AMBIGÜEDAD A RESOLVER EN REFINAMIENTO

- Frontera exacta entre "guardar la tarea" (2.1) y "crear el evento en Calendar" (5.2): decidir si la creación del evento es un criterio de 2.1 (cuando hay fecha y Google conectado) o queda enteramente en 5.2. Hoy hay ambigüedad entre ambas historias.
- Definir si la fecha límite es date o datetime antes de comprometer 2.1, porque condiciona validación, UI y sincronización.