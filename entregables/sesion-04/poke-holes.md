# Poke-holes — AUTH-02 (Iniciar y cerrar sesión)

Story analizada: **AUTH-02 Iniciar y cerrar sesión** (`entregables/sesion-04/output.md`).

## Hallazgos

1. **Inconsistencia de enumeración de usuarios entre AUTH-01 y AUTH-02.** AUTH-01 indica explícitamente que "si el email ya está registrado, el sistema lo indica" durante el registro. AUTH-02, en cambio, asume un mensaje de error *genérico* en login precisamente para no revelar si el email existe. Ambas decisiones son contradictorias entre sí: si el flujo de registro ya confirma qué emails están en uso, el mensaje genérico del login no aporta la protección de enumeración que su propio `(asumido)` justifica.

2. **Sin protección contra fuerza bruta.** Ningún criterio de aceptación contempla qué ocurre tras varios intentos de login fallidos consecutivos (bloqueo temporal, rate limiting, CAPTCHA, backoff). Al ser el único punto de entrada por contraseña de la aplicación, es un vector de ataque relevante que ni la story ni el PRD mencionan.

3. **Expiración y duración de sesión no definidas.** La story dice que "la sesión se termina" al cerrar sesión, pero no hay ningún AC sobre cuánto dura un token de acceso, si expira automáticamente, ni qué pasa si expira mientras el usuario tiene la app abierta (¿redirección silenciosa?, ¿aviso?, ¿pérdida de cambios no guardados en una tarea a medio editar?).

4. **Desajuste entre el "para" de la story y sus criterios de aceptación en el caso multi-dispositivo.** El beneficio declarado es "proteger mi cuenta en dispositivos compartidos", lo que implica un escenario de varios dispositivos/navegadores en uso. Sin embargo, ningún AC cubre si cerrar sesión invalida solo el token del dispositivo actual o todos los tokens emitidos, ni si existe una opción de "cerrar sesión en todos los dispositivos". La story promete algo que sus propios criterios no verifican.

5. **Case-sensitivity del email no especificada.** Ningún AC aclara si la comparación de email en login es insensible a mayúsculas/minúsculas. Si el registro (AUTH-01) no fuerza normalización, un usuario que se registró como `Usuario@Mail.com` podría fallar al intentar entrar con `usuario@mail.com` — riesgo real de fricción y tickets de soporte, no cubierto por ningún criterio.

## Dependencia no mencionada

El AC4 (redirección a login desde rutas protegidas) presupone la existencia de una capa transversal de verificación de sesión que aplica a *toda* la aplicación, no solo a esta story. Esa dependencia técnica no está declarada como tal y afecta implícitamente al resto de stories (CRUD de tareas, filtrado, exportación, sincronización): si AUTH-02 no se implementa primero, ninguna de esas otras stories tiene forma de ser "solo del usuario autenticado".