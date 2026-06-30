# Reflexión — Ejercicio S4: Backlog inicial FlowSync

## ¿Qué me sorprendió del output de la IA?

Me sorprendió la claridad y eficacia con la que la IA estructuró
el output a partir del prompt. Esperaba algo más genérico o que
hubiera que corregir mucho, pero las 17 stories salieron bien
agrupadas por épica, en el formato correcto, con criterios de
aceptación verificables y usando (asumido) exactamente donde no
tenía información suficiente del PRD. El prompt hizo su trabajo —
las restricciones y el ejemplo de formato marcaron la diferencia.

## ¿Qué falló o tuve que corregir?

En el proceso de construir el prompt me di cuenta de que yo mismo
metí dos features fuera del scope del MVP antes de que lo hiciera
la IA: una verificación por email en el registro y un flujo de
recuperación de contraseña. Ambas suenan naturales y razonables,
pero el PRD las excluye explícitamente. Eso me enseñó por qué las
restricciones en el prompt son tan importantes — si yo, leyendo el
documento, las inventé sin darme cuenta, la IA lo haría mucho más
rápido y a mayor escala.

## ¿El patrón poke-holes me encontró algo que yo no había visto?

Sí. Varios gaps que no habría notado revisando el output por mi
cuenta. El más importante: nadie en el backlog define que hay que
guardar el ID del evento de Google al crearlo, pero sin ese dato
las stories de actualización y borrado de evento son imposibles de
implementar. También el caso del token revocado desde Google —
había pensado en fallos de API, pero no en que el usuario pudiera
revocar el acceso por fuera de FlowSync, que es un tipo de error
completamente distinto que no se resuelve reintentando. El patrón
fuerza una conversación que de otra forma no hubiera pasado.