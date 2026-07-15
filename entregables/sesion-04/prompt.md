
# Prompt — Descomposición PRD FlowSync MVP en User Stories

## Rol

Actúa como un **Product Owner Senior especializado en productos SaaS**, con experiencia en análisis de PRDs y creación de backlog para equipos de desarrollo.

Tu objetivo es transformar el PRD de FlowSync en User Stories listas para desarrollo, manteniendo foco en alcance MVP, valor de usuario y criterios de aceptación verificables.

---

## Fuente de verdad

Analiza exclusivamente el PRD de FlowSync MVP proporcionado.

Usa las siguientes secciones como referencia:

* **Sección 1 — Resumen ejecutivo:** para entender producto, problema y alcance MVP.
* **Sección 2 — Usuario objetivo:** para definir roles y beneficios de las historias.
* **Sección 3 — Requisitos funcionales:** para extraer las funcionalidades a descomponer.
* **Sección 4 — Requisitos no funcionales:** solo considerar como criterios de aceptación cuando aplique.
* **Sección 5 — Stack técnico:** no crear historias con la información de esta sección.
* **Sección 6 — Criterios de éxito:** usar únicamente como contexto, no crear historias a partir de métricas.
* **Sección 7 — Riesgos conocidos:** no convertir riesgos en funcionalidades.
* **Sección 8 — Glosario:** conceptos que debes usar para evitar ambiguedades.

---

## Objetivo

Genera User Stories únicamente para funcionalidades incluidas en el MVP.

Cada historia debe seguir exactamente:

**Como [rol], quiero [acción], para [beneficio].**

Ejemplo:

**Como usuario registrado, quiero crear una tarea con fecha límite, para organizar mis pendientes y reflejarlos en mi calendario.**

Agrupa las historias por el criterio que tenga más sentido para FlowSync:

* Épica.
* Módulo.
* Caso de uso.

---

## Restricciones

No debes:

* Inventar funcionalidades.
* Agregar features fuera del MVP.
* Proponer mejoras futuras.
* Crear arquitectura o decisiones técnicas.
* Crear tareas internas de desarrollo.
* Estimar tiempos o esfuerzo.
* Convertir riesgos o deseos futuros del PRD en historias.

Si algo no está explícito en el PRD pero necesitas interpretarlo, márcalo como:

**(asumido)**

Ejemplo:

**(asumido) Interpreté que el usuario debe completar una autorización antes de sincronizar Google Calendar porque el PRD menciona conexión mediante OAuth.**

---

## Criterios de aceptación

Cada User Story debe incluir entre 3 y 5 criterios de aceptación.

Formato obligatorio:

**Given / When / Then**

Los criterios deben ser:

* Específicos.
* Verificables.
* Relacionados directamente con la historia.
* Útiles para QA.

Evita criterios genéricos como:

* "funciona correctamente".
* "es fácil de usar".
* "tiene buen rendimiento".

Ejemplo:

User Story:

**Como usuario, quiero crear una tarea, para registrar un pendiente.**

Criterios de aceptación:

* Given que el usuario está autenticado,
  When completa el título obligatorio y guarda la tarea,
  Then la tarea aparece en su listado con estado `pending`.

* Given que el usuario intenta crear una tarea sin título,
  When confirma el formulario,
  Then el sistema muestra un mensaje indicando que el título es obligatorio.

---

## Formato de salida

Usa esta estructura:

# [Épica / Módulo]

## User Story 1

**Como [rol], quiero [acción], para [beneficio].**

### Criterios de aceptación

* Given...
  When...
  Then...

* Given...
  When...
  Then...

## User Story 2

**Como [rol], quiero [acción], para [beneficio].**

### Criterios de aceptación

* Given...
  When...
  Then...

---

## Validación final

Antes de entregar verifica:

* Todas las historias pertenecen al MVP.
* Todas siguen formato Como/Quiero/Para.
* Todas tienen criterios Given/When/Then.
* No existen funcionalidades inventadas.
* Los supuestos están marcados como (asumido).
* La agrupación refleja la estructura funcional de FlowSync.
