## Reflexion

Bueno realmente me sorprendió el output de  la IA considero la mayoría de los casos que genero fueron en base a lo que normalmente se hace según cada evento pero en si lo que mas me sorprendió fueron los hallazgos del poke-holes, cuando lei las historias de usuario elegí esa que a que a mi parecer es la que mas atención se el debió dar por el estado de criticidad con la conexión a un programa externo y además por que pude notar que aquí no asumió nada  por ese pensé que seria el mas completo pero no si tenia bastantes hoyos y muy importantes, realmente nunca considere que pudiera hacer la conexión después de utilizar el sistema y tener ya tareas creadas en mi mente el caminiro era de que había que estar logueado a google para poder crear tareas y también la parte de la autenticación del token según mi entendimiento es que desde google puedes quitar el acceso a las aplicaciones pero pues si que va pasar si se lo quitan ya no se va a poder usar junto con el calendario creo yo aquí ya esto pierde una gran parte del sentido por que como humanos suelen olvidársenos tareas y mas si estamos con mucho trabajo y el tenerlo en el calendario como recordatorio es de mucha ayuda.
No corregí nada el prompt la verdad es que me tome mucho tiempo en leer los ejemplos y entenderlos para poder llegar a la mejor solución posible ya que adopte lo que sentí para mi era mejor.

Tambien se que esto no viene en lo del entregable  y espero no me reste puntos por ello pero quiero compartir mis respuestas a las preguntas que deberías poder responder después de mirar:

¿Cómo estructuraría YO mi proyecto de forma diferente?
Viendo la estructura de ambos ejemplos y teniendo en cuenta que cada tecnología tienen sus propias y muy distintas maneras de como se maneja la estructura, yo tomaría un workspace donde pueda tener seccionando backend,frontend,documentacion (aquí iría todo lo que es diagramas, mockups), dentro de las carpetas de backend,frontend tendría para cada una la carpeta de test ya que se pueden dar pruebas por separado.

¿Qué patrones de esos 2 ejemplos quiero usar?
Entiendo que el tener separado a lo mejor en dos proyectos el front y el back sea como lo mas común al menos que los proyecto que he visto y o trabajado, considero que si se tiene un buen manejo de la estructura pueden vivir ambos como en el ejemplo 1 y pienso que esto puede ser benéfico para que al momento de estar diseñando algo del back también se diseñe desde el SDD para el front este todo conectado y tenga un panorama mas amplio del contexto del proyecto la IA

¿Dónde voy a poner documentación en mi repo?
Considero que a lo que lei debe ir en el archivo README referenciado en caso archivos que sean necesario mencionar de la carpeta de documentación antes mencionada.

¿Cómo voy a organizar mis tests?
Los organizaría como lo comente anteriormente en test para el back y test para el front.