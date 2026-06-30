
# Objetivo: Genera user stories del Producto Requirement Document (PRD.md) de Flowsync


## Contexto:
* FlowSync es el nombre del sistema que debe construirse como sitio web.
* FlowSync es un sistema de tareas que está sincronizado con Google Calendar
* La explicación detallada del sistema FlowSync se encuentra en el archivo PRD.md que está en el directorio docs.

## Rol de quien lee las historias de usuario
* Para el desarrollador senior con experienca básica en métodologías ágiles, por lo tanto, ser explicativo cuando expliques materia de Scrum y metodología de agilidad cuando generes las historias de usuario

## Formato exacto: 
las stories deben venir en el formato: Como [rol], quiero [acción], para [beneficio].

## Alcance acotado: 
solo funcionalidades del MVP (la sección correspondiente del PRD). sin inventar features que no estén en el documento.

## Los criterios de aceptación debe ser en Given/When/Then: 
cada story con 3-5 criterios de aceptación verificables, no genéricos.

## Agrupación: 
las stories agrupadas por módulo, caso de uso o épica, según tenga más sentido para FlowSync.

## Ejemplo formato de historia de usuario:
### Historia de usuario
Como administrador, quiero asignar roles a los usuarios, para controlar sus permisos dentro de la aplicación.

### Criterios de aceptación (Given/When/Then)
- Given que soy administrador, When asigno el rol “Editor” a un usuario, Then ese usuario puede crear y modificar contenido.

- Given que soy administrador, When asigno el rol “Viewer” a un usuario, Then ese usuario solo puede visualizar contenido sin editarlo.

- Given que retiro un rol previamente asignado, When el usuario intenta acceder a funcionalidades restringidas, Then recibe un mensaje de “Acceso denegado”.

## Marca
  Marca como asumido: profesional del conocimiento
