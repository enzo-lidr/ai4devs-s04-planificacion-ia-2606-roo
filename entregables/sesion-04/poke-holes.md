# Poke-holes — US-13 · Exportar tareas a CSV

**Story analizada:** US-13 de [output.md](output.md). Se eligió porque es la única con 5 criterios de aceptación (el máximo del rango pedido) y los cinco parecen completos a primera vista.

**Prompt usado** (plantilla del enunciado, con la story pegada tal cual; sesión nueva y sin acceso al PRD):

```text
Aquí tienes esta user story con sus criterios de aceptación:

[story US-13]

Tu tarea: identifica edge cases, supuestos implícitos,
escenarios faltantes, y dependencias o riesgos no
mencionados. No reescribas la story, solo lista lo
que falta o lo que asumiste.
```

## Hallazgos genuinos (5)

Cada hallazgo se contrastó con el PRD (§3.3, §3.4, §4, §7).

**1. Inyección de fórmulas en el CSV (riesgo de seguridad no cubierto).**
Título y descripción son texto libre del usuario. Si empiezan por `=`, `+`, `-` o `@`, Excel y Google Sheets los interpretan como fórmulas al abrir el archivo. El AC 2 cubre comas, comillas y saltos de línea, pero no esto. Hay que decidir la mitigación (p. ej. prefijar con `'`) y qué se acepta perder en fidelidad de datos. Un usuario exporta para «llevarse sus datos» y puede abrirlos en una hoja de cálculo.

**2. «Mis tareas»: ¿todas, o solo las filtradas? ¿incluye archivadas?**
El AC 1 dice «una fila por tarea» y el PRD §3.4 solo dice «exportar sus tareas». Pero §3.3 permite filtrar por estado y sugiere que «archived» puede quedar fuera de la vista por defecto (el estado vacío se describe para «todas archivadas»). Sin una decisión, dos implementaciones distintas pueden cumplir el AC y producir archivos diferentes. Es el único hallazgo que cambia el *resultado* observable para el usuario, y exige respuesta de producto.

**3. El AC 2 («se abre sin desplazar columnas») no es verificable tal como está escrito.**
Depende de la codificación (UTF-8 con o sin BOM: sin BOM, Excel en Windows rompe tildes y ñ, relevante en un producto en español) y del delimitador (Excel con configuración regional española espera `;`, no `,`). El criterio habla de «se abre» pero no dice en qué programa ni con qué configuración. QA no puede dar un veredicto objetivo sin fijar codificación, delimitador y programa de referencia.

**4. Formato de los datos exportados: fecha límite y estado.**
El AC 1 enumera columnas, pero no cómo se serializan. Para la fecha límite falta formato (ISO 8601 o local) y zona horaria; el propio PRD §7 identifica las zonas horarias como riesgo en la sincronización, y el mismo problema aparece aquí: un desfase puede mover la fecha un día. Para el estado, falta decidir si se exportan los valores internos del PRD (`pending`, `completed`, `archived`) o etiquetas legibles, y en qué idioma. Tampoco hay nombres de cabecera, necesarios si algún día se importa el archivo.

**5. No hay criterios para fallo ni volumen.**
Ni qué ve el usuario si la descarga falla o la sesión caduca, ni qué ocurre con muchas tareas. El PRD §4 solo fija un rendimiento para el *listado* (menos de 1 s con 200 tareas), no para la exportación. El AC 5 («reviso el archivo» y solo hay mis tareas) tampoco indica cómo comprobarlo: requiere una prueba con dos usuarios con datos distintos, y que el filtrado se aplique en el servidor, no en el cliente.

## Descartado como ruido

| Punto de la crítica | Motivo |
|---|---|
| Tareas compartidas/asignadas, etiquetas, prioridad, subtareas, adjuntos | Fuera de alcance explícito del MVP (PRD §1) |
| Valor de estado `in_progress` | No existe en el PRD (la IA no lo vio); el hallazgo válido es el 4 |
| Derecho de portabilidad RGPD | Requisito no presente en el PRD; tangencial |
| Rate limiting, librería de generación de CSV, cabeceras HTTP, accesibilidad, móvil | Diseño o implementación, no gap de la story (el PRD prohíbe prescribir arquitectura en el backlog) |
| Nombre del archivo, caracteres nulos, límite de celda de Excel (32.767) | Detalle de bajo impacto |
| «El título nunca es vacío» | Ya garantizado por PRD §3.2 (título obligatorio) |

## Anexo — respuesta cruda de la IA

### Análisis de US-13 · Exportar tareas a CSV

Nota: no he podido ver el documento fuente (§3.4, §4), así que parte de lo que sigue son preguntas que dependen de él.

#### Edge cases de formato y contenido

1. **Inyección de fórmulas (CSV injection).** Títulos o descripciones que empiezan por `=`, `+`, `-`, `@`, tab o CR se ejecutan como fórmulas al abrirse en Excel o Sheets. Es un riesgo de seguridad real, porque el contenido lo controla el usuario (o un tercero si hay tareas compartidas). Falta decidir la mitigación (prefijar con `'`) y qué pasa con la fidelidad de los datos.
2. **Codificación.** No se define UTF-8, ni si lleva BOM. Sin BOM, Excel en Windows rompe tildes, ñ y emojis, lo cual es especialmente relevante en un producto en español.
3. **Delimitador y localización.** Excel con configuración regional española espera `;` y no `,`. El criterio 2 dice que "se abre sin desplazar columnas", pero eso depende de la herramienta y de la configuración regional del usuario.
4. **Saltos de línea.** No se especifica `\r\n` frente a `\n` (RFC 4180). Los saltos dentro de campos tienen que conservarse y estar entrecomillados.
5. **Formato de fecha límite.** No se define si es ISO 8601 (`2026-11-26`) o local, ni qué zona horaria se usa. Si la fecha tiene hora, hay que decidir si se exporta. El desfase de zona horaria puede mover el día.
6. **Valores del estado.** No se dice si se exportan los códigos internos (`in_progress`) o etiquetas legibles, ni en qué idioma.
7. **Campos largos o con caracteres de control.** Descripciones muy largas, que superan el límite de celda de Excel (32.767 caracteres), y caracteres nulos o de control.
8. **Cabeceras.** No se especifican los nombres exactos, el idioma ni el orden de las columnas. Esto importa si en el futuro se quiere importar el CSV (US de importación).
9. **Nombre del archivo.** Falta definirlo (por ejemplo `tareas-2026-11-26.csv`) y qué hacer con caracteres no válidos.

#### Escenarios faltantes

- **Alcance de la exportación.** No se aclara si se exportan todas las tareas o solo las filtradas o visibles. Tampoco si se incluyen tareas archivadas, completadas o eliminadas (borrado lógico).
- **Volumen.** ¿Qué pasa con miles de tareas? Faltan criterios sobre generación en streaming o en servidor, timeouts, límite máximo e indicador de progreso.
- **Fallo de la exportación.** Error de red o de servidor, y sesión expirada durante la descarga. Falta un mensaje de error y la posibilidad de reintentar.
- **Doble clic o clics repetidos.** Estado de carga y deshabilitado del botón.
- **Consistencia.** Qué ocurre si las tareas cambian mientras se genera el archivo (snapshot frente a datos en vivo).
- **Usuario no autenticado o sesión caducada.** Se menciona "usuario registrado", pero no el comportamiento si no lo es (401, redirección).
- **Accesibilidad y usabilidad.** Etiqueta del botón, navegación con teclado, feedback al terminar la descarga y comportamiento en móvil (algunos navegadores móviles gestionan mal las descargas).
- **Tareas con campos adicionales.** Etiquetas, prioridad, fecha de creación, subtareas, adjuntos o responsables: no se aclara si quedan fuera a propósito.
- **Tareas compartidas o asignadas.** Si existen, ¿"mis tareas" incluye las que me asignaron o solo las que creé?

#### Supuestos implícitos

- Que existe un modelo de tarea con exactamente esos cuatro campos y que `descripción` y `fecha límite` son los únicos opcionales.
- Que el título nunca es vacío.
- Que "mis tareas" está bien definido (propiedad, y no colaboración).
- Que el usuario abre el CSV en una hoja de cálculo y no en otro sistema.
- Que la descarga se hace desde el navegador (no API ni app móvil nativa).
- El criterio 4 (CSV vacío con cabecera) ya está marcado como asumido, pero habría que confirmarlo con negocio. Alternativa: deshabilitar el botón o mostrar un aviso.

#### Dependencias y riesgos

- **Privacidad y seguridad (§4).** El criterio 5 es verificable solo con pruebas explícitas de aislamiento entre usuarios: la autorización debe aplicarse en el servidor (IDOR), no solo filtrando en el cliente. Añadir logs de auditoría de exportaciones podría ser un requisito.
- **RGPD.** La exportación puede encajar con el derecho de portabilidad. Habría que confirmar si se espera cubrir ese requisito (formato estructurado, uso común y lectura mecánica), lo que podría implicar incluir más campos.
- **Rendimiento y abuso.** Sin límite de frecuencia (rate limiting), un usuario puede lanzar exportaciones masivas y cargar el servidor.
- **Dependencias técnicas.** Librería de generación de CSV (no concatenar strings a mano), soporte de descarga de blobs en navegadores objetivo y cabeceras `Content-Disposition` y `Content-Type`.
- **Pruebas.** Faltan datos de prueba para los casos límite: emojis, comillas dobles consecutivas, saltos CRLF, cadenas que empiezan por `=`, texto muy largo y cero tareas.
- **Coherencia con otras stories.** Si hay una US de importación, el formato debe ser simétrico (round-trip). Si hay filtros, borrado lógico o idiomas, afectan a esta story.

#### Preguntas abiertas para el responsable de producto

1. ¿Se exporta todo o lo filtrado?
2. ¿Qué delimitador y codificación se esperan por defecto?
3. ¿Se aplica mitigación contra inyección de fórmulas, aunque altere ligeramente los datos?
4. ¿Hay un límite máximo de tareas por exportación?
5. ¿Se debe cubrir portabilidad RGPD con esta story?