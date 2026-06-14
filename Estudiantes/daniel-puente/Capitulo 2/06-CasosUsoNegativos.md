# 2.6 Actores y casos de uso negativos

Los casos de uso negativos describen situaciones en las que el sistema debe bloquear, rechazar o controlar una operación para mantener la coherencia funcional, la seguridad o la trazabilidad del negocio.

| ID | Caso de uso | Camarero | Administrador | Cocinero | Sist. Notif. |
|---|---|:---:|:---:|:---:|:---:|
| CU-N01 | Denegar acceso por credenciales incorrectas | ✅ | ✅ | ✅ | |
| CU-N02 | Bloquear operación por rol insuficiente | ✅ | | ✅ | |
| CU-N03 | Rechazar comanda por alérgenos incompletos | ✅ | ✅ | | |
| CU-N04 | Rechazar reserva por conflicto de horario | | ✅ | | |
| CU-N05 | Bloquear edición de línea en preparación | ✅ | ✅ | | |
| CU-N06 | Rechazar ticket por mesa ya cobrada | ✅ | | | |
| CU-N07 | Restringir pedido por falta de stock | ✅ | | | ✅ |
| CU-N08 | Anular ticket ya cobrado por error de cobro | | ✅ | | |
| CU-N09 | Marcar reserva como no presentado | | ✅ | | |
| CU-N10 | Desbloquear cuenta por intentos fallidos | | ✅ | | |

Cuando un plato se marca como agotado, queda como no disponible en carta y el backend impide que pueda añadirse a nuevas comandas.

Una vez una línea se encuentre en preparación, cualquier edición respecto a esa línea será bloqueada. Esta restricción evita cambios tardíos sobre platos que cocina ya ha empezado a preparar.

[← Volver al índice del capítulo](README.md)
