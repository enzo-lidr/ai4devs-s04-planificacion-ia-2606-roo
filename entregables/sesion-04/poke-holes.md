# Poke-holes — US-5.1 Conectar la cuenta de Google

Análisis tipo "AI as poke-holes" sobre la story de conexión OAuth con Google. No reescribe la story ni propone solución técnica; solo señala lo que falta, lo asumido o lo que podría romperse. Cinco hallazgos seleccionados por relevancia, descartando lo genérico.

## 1. La validez del token después de conectar no está cubierta

Los criterios solo describen el instante de la conexión ("queda marcada como conectada"). No se contempla qué ocurre cuando, más tarde, el usuario revoca el acceso desde su cuenta de Google, el refresh token caduca o Google invalida los permisos. La cuenta podría quedar marcada como "conectada" en FlowSync pero sin capacidad real de sincronizar — un estado falso-positivo que rompe silenciosamente toda la épica de sincronización. Es un escenario distinto del fallo puntual de API que cubre US-5.5.

## 2. Consentimiento parcial de permisos (scopes)

El criterio de éxito asume que "autorización OAuth correctamente" equivale a haber concedido todos los permisos necesarios. Google permite al usuario otorgar solo un subconjunto de los scopes solicitados. Falta el escenario en que el usuario completa el flujo pero deniega el permiso de escritura sobre Calendar: para FlowSync la conexión "se completó", pero es inservible para su propósito. No es ni "completa" ni "cancela/rechaza" — es un tercer resultado no modelado.

## 3. La zona horaria se fija implícitamente en la conexión

El PRD marca las zonas horarias como riesgo explícito (§129): la relación entre la fecha límite de una tarea y la hora del evento debe definirse con cuidado. El momento de conectar Google es donde se determina la zona horaria del calendario destino, y la story no la menciona en absoluto. Una decisión tomada (o no tomada) aquí condiciona si todos los eventos posteriores se crean a la hora correcta o desplazada. Es un supuesto implícito con impacto en cascada sobre US-5.2 y US-5.3.

## 4. Calendario destino y cuenta de Google asumidos como únicos

La story dice "mi Google Calendar" como si fuera una entidad única. No se define en qué calendario se sincroniza cuando el usuario tiene varios (primario vs. secundarios), ni si la cuenta de Google a conectar debe coincidir con el email de login de FlowSync. Sin esa regla, un usuario podría conectar una cuenta distinta a la esperada o ver sus eventos en un calendario que no consulta — fricción directa contra la métrica de éxito del ≥40% de conexiones (§120).

## 5. Tareas preexistentes con fecha límite al momento de conectar

El usuario del PRD ya gestiona pendientes en otra herramienta y probablemente creará tareas en FlowSync antes de conectar Google. La story no define si, al conectar, las tareas con fecha límite ya existentes se reflejan retroactivamente como eventos o si la sincronización solo aplica a tareas futuras. Es una ambigüedad de comportamiento, no de UI: cambia lo que el usuario ve en su calendario justo después de conectar, que es precisamente el momento de validación de la propuesta de valor.
