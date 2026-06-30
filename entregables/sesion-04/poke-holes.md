# Prompt: Critica de historia de usuario
 Critica la siguiente historia de usuario:
  **Historia de usuario**
  Como usuario, quiero conectar mi cuenta de Google a FlowSync, para que mis tareas con fecha límite aparezcan automáticamente como eventos en mi Google Calendar.

  **Criterios de aceptación (Given/When/Then)**
  - Given que estoy autenticado y sin Google conectado, When inicio la conexión, Then se me lleva al flujo de autorización OAuth de Google y, al aceptar, FlowSync queda autorizado para leer y escribir en mi calendario.
  - Given que acabo de conectar Google, When la conareas existentes con fecha límite se crean como
 
 Tu tarea: identifica edge cases, supuestos implícitos,
  escenarios faltantes, y dependencias o riesgos no
  mencionados. No reescribas la story, solo lista lo
  que falta o lo que asumiste.


# Resultado: Poke-holes · US-10 — Conectar Google Calendar

> **Patrón "AI as poke-holes"**: se le pide a la IA que critique una de sus propias historias buscando lo que falta, no lo que está bien.
> **Historia analizada**: US-10 (Conectar la cuenta de Google y reflejar tareas como eventos), del backlog en `output.md`.
> **Tarea**: identificar edge cases, supuestos implícitos, escenarios faltantes y dependencias/riesgos. No se reescribe la story.

---

## Top 5 hallazgos (los no triviales)

1. **La hora del evento está sin definir — y es el riesgo nº1 del PRD.**
   Los AC dicen "aparece el evento" pero nunca *a qué hora*. Una "fecha límite" no es una hora: ¿evento de todo el día?, ¿a las 9:00?, ¿en qué zona horaria (la del navegador, la de la cuenta Google, la del servidor)? El PRD lo marca explícitamente como riesgo (§7 y §129: "no crear eventos a horas erróneas"). Tal como está, dos implementaciones válidas darían resultados distintos.

2. **El AC 2 (backfill de tareas existentes) choca con los rate limits de Google.**
   "Mis tareas existentes con fecha límite se crean como eventos" asume creación inmediata y síncrona al confirmar la conexión. Con hasta 200 tareas (PRD §98) son 200 llamadas a la API sujetas a rate limits (§7). No se define si es asíncrono, con progreso, ni qué pasa si falla a mitad (¿queda medio sincronizado?, ¿es idempotente?, ¿se reanuda?).

3. **No hay manejo del flujo OAuth fallido.**
   El AC 1 solo cubre "al aceptar". Faltan escenarios reales: el usuario **deniega** el consentimiento, cierra la ventana, hay timeout, o concede **scopes parciales** (lectura sí, escritura no). El PRD §4 exige "errores claros, no técnicos" y aquí no hay ningún AC de error.

4. **Riesgo de eventos duplicados por reconexión, y eventos no identificables.**
   No se marca de ningún modo que un evento "pertenezca" a FlowSync, ni se contempla reconectar tras desconectar o pulsar "conectar" dos veces. Resultado realista: eventos duplicados en el calendario y eventos de FlowSync indistinguibles de los que el usuario crea a mano.

5. **El AC de privacidad/seguridad no es verificable (viola la "T" de INVEST).**
   "Se guardan de forma segura" no es testeable: no concreta cifrado en reposo, no exponer tokens en logs, etc. Y "nunca accesibles por otro usuario" se afirma pero no se prueba con un criterio negativo (dos usuarios, uno no ve los tokens/eventos del otro).

---

## Inventario completo (para refinamiento)

### Supuestos implícitos
- **Un solo calendario**: una cuenta Google tiene varios (personal, trabajo, compartidos); no se dice en cuál se escribe ni si el usuario elige.
- **Una sola cuenta Google por usuario**: no se contempla conectar dos, ni el caso de "ya hay una conectada".
- **Cuenta de Google ≠ cuenta de FlowSync**: FlowSync se registra con email/contraseña (PRD §3.1) y conecta Google por OAuth; nadie valida ni aclara si los emails coinciden.
- **El evento es propiedad de FlowSync**: se asume distinguible de los eventos manuales del usuario, pero no hay marca/identificador.

### Edge cases faltantes
- Usuario **deniega/cancela** OAuth o cierra la ventana.
- **Consentimiento parcial** de scopes.
- **Token/refresh revocado fuera de FlowSync** (desde la cuenta de Google) → "conectado pero ya no válido".
- **Tarea con fecha límite en el pasado** → ¿se crea evento retroactivo?
- **Fallo a mitad del backfill** → estado intermedio inconsistente.
- **Reconexión / doble clic** → duplicados.
- **Usuario borra/edita a mano el evento que FlowSync creó** → estado inconsistente (la sync inversa está fuera del MVP, pero el borrado manual es realista).

### Escenarios / criterios verificables que faltan
- Feedback de progreso y de error al usuario durante la conexión y el backfill.
- **Estado visible de la conexión** (si está conectado, desde cuándo, a qué cuenta) — sin esto US-12 (desconectar) no tiene contexto.
- Definir *cómo* "se confirma la conexión" (pantalla, badge, redirección) para que sea observable.
- Criterio negativo de privacidad verificable.

### Dependencias y riesgos no mencionados
- **Setup en Google Cloud Console** y revisión de permisos (PRD §128): precondición que bloquea cualquier demo y "puede llevar más tiempo del esperado".
- **OAuth consent screen / verificación de app**: los scopes de Calendar son sensibles; Google puede exigir verificación antes de usuarios reales (riesgo de cronograma).
- **Rate limits y cuotas** de la API (PRD §7): ausentes como criterio, en conflicto directo con el AC 2.
- **Spike técnico previo** recomendado por el PRD (§91, §127): la historia se da por estimable sin reconocer esa incertidumbre.
- **Tamaño (INVEST)**: la historia esconde tres cosas — flujo OAuth, backfill de tareas existentes, y creación de eventos para tareas nuevas. Probablemente debería partirse.
