# Aplicado el patrón "AI as poke-holes" sobre la historia:

## US01: Registro de cuenta con email y contraseña
### Historia: 
Como visitante de FlowSync, quiero crear una cuenta con mi email y contraseña, para acceder a la aplicación y comenzar a gestionar mis tareas.

### Criterios de Aceptación:
* CA1: Dado que soy un visitante en la página de registro / Cuando introduzco un email con formato válido y una contraseña de al menos 8 caracteres y confirmo el envío / Entonces el sistema crea mi cuenta correctamente y me redirige a la pantalla de bienvenida.

* CA2: Dado que soy un visitante en la página de registro / Cuando introduzco una contraseña con menos de 8 caracteres / Entonces el sistema muestra un mensaje de error comprensible indicando que la contraseña debe tener mínimo 8 caracteres, e impide el envío del formulario.
* CA3: Dado que soy un visitante en la página de registro / Cuando introduzco un email que ya está registrado en el sistema / Entonces el sistema lo indica con un mensaje claro y ofrece una opción para ir al inicio de sesión.
* CA4: Dado que soy un visitante en la página de registro / Cuando introduzco un email con formato inválido (p.ej. sin "@" o sin dominio) / Entonces el sistema muestra un error de validación comprensible e impide el envío. [Asumido: la validación de formato de email se realiza antes de enviar al servidor, bien en cliente o en el primer response del servidor.]
* CA5: Dado que el registro se completa con éxito / Cuando el sistema crea la cuenta / Entonces no se envía ningún email de verificación ni se requiere ningún paso adicional para activar la cuenta.

----

# HALLAZGOS GENUINOS:

## 1. Edge cases no cubiertos
* Email en mayúsculas (USER@MAIL.COM): ¿es el mismo usuario que user@mail.com? La CA3 asume unicidad pero no define si la comparación es case-sensitive.
* Email con longitud extrema (RFC 5321 limita a 254 caracteres): no hay límite superior definido.
* Longitud máxima de contraseña: sin límite superior. Contraseñas de miles de caracteres pueden causar problemas de rendimiento en el hashing.
* Caracteres especiales, unicode o emojis en contraseña: ¿permitidos o bloqueados? No definido.
* ¿Existe campo de confirmación de contraseña ("repite tu contraseña")? El PRD no lo menciona y la story tampoco. Si no existe, es una decisión de UX deliberada que debe documentarse; si existe, necesita sus propios criterios.


## 2. Supuestos implícitos no declarados
* La contraseña se almacena hasheada, no en texto plano. No está en el PRD ni en la story. Es una dependencia de implementación con impacto en seguridad y en el stack (@adonisjs/auth). Debería ser un criterio o al menos una nota de seguridad.

## 3. Escenarios faltantes

### Seguridad y abuso:
* No hay ningún mecanismo de protección contra creación masiva de cuentas (rate limiting, CAPTCHA, detección de bots). En producción esto es un vector de abuso directo.
* La CA3 (email ya registrado) con su mensaje y enlace al login es técnicamente un vector de enumeración de usuarios: cualquiera puede saber qué emails están registrados probando el formulario. No se dice si esto es aceptable en el MVP o si debe mitigarse con un mensaje genérico.



## 4. Dependencias y riesgos no mencionados

| Riesgo | Impacto | Observación |
|---|---|---|
| **Unicidad del email a nivel de base de datos** | Alto | Si la restricción `UNIQUE` solo se aplica en la capa de aplicación y no en la base de datos, dos registros simultáneos con el mismo email pueden saltarse la CA3 por race condition. |
| **Definición de "token de acceso" post-registro** | Alto | `@adonisjs/auth` soporta distintos modos. No está claro si el registro emite el token directamente o si obliga a pasar por el login de US02. Esto afecta al flujo de onboarding de US04. |
| **Regla de validación de email compartida con US02** | Medio | Si el criterio de "email válido" en registro y en login difieren, un usuario puede registrarse con un email que luego no puede usar para iniciar sesión. |
| **Complejidad de contraseña** | Bajo-Medio | Solo se exige longitud mínima (8 caracteres). Sin requisito de mayúsculas, números o caracteres especiales. Si es una decisión de producto debe documentarse; si es un olvido, puede generar contraseñas débiles en producción. |

---


