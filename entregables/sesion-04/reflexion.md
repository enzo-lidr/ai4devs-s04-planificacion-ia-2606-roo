# Reflexión

- **Qué me sorprendió:** con el patrón *rol → contexto → restricciones → ejemplo →
  transparencia*, la IA respetó el alcance sorprendentemente bien: no coló ni una
  feature del "out of scope" (nada de equipos, etiquetas, Outlook…) y usó las
  etiquetas `(asumido)` justo donde inferí de más. El prompt estructurado marcó la
  diferencia frente a pedir "sácame historias" a secas.
- **Qué falló o corregí:** la IA **heredó las ambigüedades del PRD en vez de
  señalarlas** — el AC "se elimina o se marca según corresponda" salió tal cual,
  vago e intesteable. También agrupó dos preocupaciones en una story (la #12 mezcla
  "desconectar" con "resiliencia ante fallos"), y algún `(asumido)` es discutible
  (pedir confirmación al borrar). El output es un buen borrador, no un backlog
  listo para sprint.
- **Qué me encontró poke-holes que yo no había visto:** sí, y esto fue lo más
  valioso. A primera vista la story de sync parecía la más completa; el análisis
  crítico destapó dos huecos que se me habían pasado: que **no existe campo para
  guardar el `google_event_id`** (sin él no puedes actualizar/borrar el evento
  correcto) y el **evento huérfano** al quitarle la fecha a una tarea ya
  sincronizada. Ninguno era obvio leyendo la story; los dos romperían la feature.
