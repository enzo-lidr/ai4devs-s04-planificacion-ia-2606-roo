Análisis de TASK-2.4 — Borrar una tarea:

1. Contrato del endpoint ante tarea inexistente

El AC asume que la tarea existe, pero no define qué responde el sistema si el taskId no existe o ya fue borrado. Sin este contrato, cada implementador decide por su cuenta: algunos devolverán 404, otros 200, otros 204. Esto genera inconsistencias entre frontend y backend y complica el manejo de errores en el cliente.

2. Ausencia del caso de usuario no autenticado

El tercer AC cubre autorización entre usuarios autenticados, pero omite el escenario más básico de seguridad: un request sin token válido. Es un hueco explícito en la cobertura de seguridad que podría dejar el endpoint desprotegido si el desarrollador solo implementa lo que los ACs describen.

3. Comportamiento de datos relacionados no especificado

Si las tareas tienen entidades asociadas (comentarios, etiquetas, archivos), el AC no define qué ocurre con ellas al borrar la tarea padre. Un cascade delete no declarado puede eliminar datos no intencionados; ignorarlo puede dejar registros huérfanos que rompan integridad referencial o consuman almacenamiento indefinidamente.

4. Falta de confirmación antes del borrado

La operación es explícitamente irreversible, pero los ACs no requieren ningún paso de confirmación. Esto expone al usuario a pérdidas permanentes por un clic accidental. Para una acción sin posibilidad de deshacer, la confirmación no es un detalle de UX opcional, es una salvaguarda funcional.

5. Hard delete puede entrar en conflicto con obligaciones legales

"No es posible recuperarla" implica eliminación física de los datos. Dependiendo de la jurisdicción y el tipo de dato almacenado en la tarea, puede existir obligación legal de retención por un período mínimo (GDPR, normativas fiscales, auditoría). Implementar un hard delete sin validar este punto convierte una decisión técnica de MVP en un riesgo legal difícil de revertir.
