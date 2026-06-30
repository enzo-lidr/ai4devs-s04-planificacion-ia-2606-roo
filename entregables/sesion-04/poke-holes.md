# Hallazgos de la Parte 3

1. La historia original combinaba creación/actualización y eliminación en un solo criterio, lo que reduce su trazabilidad para un agente IA.
2. El AC sobre fallos de la API de Google era demasiado general: no especificaba el estado visible para el usuario ni el comportamiento de reintento.
3. El PRD no deja claro si la sincronización es solo `FlowSync → Google` o si admite cambios desde Google, por lo que un agente podría inferir erróneamente bidireccionalidad completa.
4. No había criterio explícito sobre qué ocurre con las tareas con fecha cuando el usuario desconecta Google: el flujo local debe seguir funcionando sin intentar sincronizar.
5. El enunciado de la historia necesitaba separar la lógica de “tarea con fecha” de la lógica de “tarea borrada”, para evitar que un agente rellene con supuestos sobre eventos intermedios.
