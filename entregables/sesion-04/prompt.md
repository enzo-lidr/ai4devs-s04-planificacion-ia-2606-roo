# Prompt — Descomposición del PRD de FlowSync en backlog

> Este archivo contiene el prompt completo utilizado para extraer el backlog
> (épicas + user stories + criterios de aceptación) a partir de `docs/PRD.md`.

---

## Rol

Asume el rol de **Product Owner Senior** con más de 10 años de experiencia en
gestión de proyectos de aplicaciones web, especializado en proyectos de
sistemas de gestión de tareas personales.

## Insumo de entrada

Basándote en el archivo `docs/PRD.md` (léelo detenidamente y a detalle para dar
la respuesta más eficiente y óptima posible), genera lo siguiente:

## Requisitos del entregable

1. **Historias de usuario (8 a 12 mínimo).** Usa el formato exacto:
   *Como [rol], quiero [acción], para [beneficio]*. Analiza y genera todas las
   historias de usuario necesarias para abarcar las funcionalidades del proyecto.
   Cada historia debe incluir:
   - **Notas Adicionales:** cualquier consideración adicional.
   - **Historias de Usuario Relacionadas:** relaciones con otras historias.
   - Un **código de identificación** con formato `HDU-XXX` (ej. `HDU-001`),
     incremental y secuencial conforme se van creando.
   - Las HDU se agrupan dentro de **épicas**. Cada épica tiene nombre
     representativo y codificación `EP-XXX` (ej. `EP-001`), también secuencial e
     incremental.
   - Tanto épica como HDU deben tener un **título descriptivo** claro y conciso,
     sin ambigüedades.

2. **Aterrizaje de la idea de negocio.** Información que ayude a definir el
   alcance del **MVP** (producto mínimo viable), las **decisiones de arquitectura
   más importantes** y las **métricas con las que se medirá el éxito**.
   Planificación enfocada en proporcionar un MVP funcional y completo, poniendo
   atención y esfuerzo en las **3 funcionalidades más críticas** que aportan
   valor real al usuario.

3. **Criterios de aceptación en lenguaje Gherkin.** Para mejor comunicación y
   entendimiento del comportamiento de las historias. Agrupados por módulo, caso
   de uso o épica, según tenga más sentido para FlowSync. Definidos claramente
   con casos de prueba, priorizando el valor de negocio y la complejidad técnica.
   Detalle específico de funcionalidad.

4. **Marca con `(asumido)`** todo lo que infieras y que no esté literal en el PRD.

5. **Non-goals:**
   - No propongas criterios de aceptación imposibles; solo se acepta si cumple
     con los criterios descritos.
   - No inventes metas.
   - No inventes ni propongas features fuera de los solicitados.
   - No propongas arquitectura: ya está el stack técnico en el contexto.

## Formato de salida esperado (ejemplo)

```
Título de la Historia de Usuario: HDU-001 - Captura de personal

*Como* dueño *quiero* poder dar de alta, baja o realizar algún cambio en el
personal que contrato *para* que pueda tener una base de datos de mi personal
actualizada y fiable.

**Escenario 1: Apertura de formulario Empleados**
- **Dado que** el usuario quiere hacer alguna alta en el personal
- **Cuando** hace clic en el botón de +
- **Entonces** se abre la interfaz del formulario para llenar los datos del nuevo personal
- **Y** se posiciona el cursor sobre el primer campo a capturar.

**Escenario 2: Selección de puestos**
- **Dado que** el usuario quiere asignar un puesto
- **Cuando** hace click en el dropdown de puestos
- **Entonces** se abre el listado de los puestos activos previamente capturados en el formulario de puestos
- **Y** al seleccionar uno asignar al usuario.

**✅ Criterios de Aceptación**
- El usuario pueda capturar toda la información requerida para la alta del personal.
- Se muestran correctamente los campos de catálogos en la captura como el del puesto.
- Se muestra un mensaje claro de que el nuevo personal se dio de alta sin ningún problema.

**🎯 Definición de Terminado (DoD)**
- [ ] Formulario implementado y funcional
- [ ] Responsive design para desktop y móvil
- [ ] Tests unitarios con cobertura > 90% backend y frontend
- [ ] Documentación técnica actualizada

#### **🔗 Historias Relacionadas**
- HDU-002: Captura de Puestos.
```
