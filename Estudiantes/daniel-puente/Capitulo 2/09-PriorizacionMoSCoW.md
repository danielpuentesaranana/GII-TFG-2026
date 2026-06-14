# 2.9 Priorización MoSCoW

La priorización MoSCoW permite delimitar el alcance del MVP y distinguir entre funcionalidades imprescindibles, recomendables y ampliables. En este proyecto se han priorizado como `Must` los casos de uso necesarios para cubrir el flujo completo sala-cocina-caja y las restricciones principales de seguridad, roles, auditoría y continuidad operativa.

| ID | Caso de uso | Prioridad |
|---|---|---|
| CU-01 | Iniciar sesión | 🔴 Must |
| CU-02 | Cerrar sesión | 🔴 Must |
| CU-03 | Ver estado de mesa por zona | 🔴 Must |
| CU-04 | Abrir mesa | 🔴 Must |
| CU-05 | Cerrar mesa | 🔴 Must |
| CU-06 | Tomar comanda | 🔴 Must |
| CU-07 | Ver comanda de una mesa | 🔴 Must |
| CU-08 | Editar comanda | 🔴 Must |
| CU-09 | Solicitar segundos platos / postres | 🔴 Must |
| CU-10 | Ver comandas pendientes en KDS | 🔴 Must |
| CU-11 | Marcar comanda en preparación | 🔴 Must |
| CU-12 | Marcar plato como listo | 🔴 Must |
| CU-13 | Ver alérgenos y observaciones | 🔴 Must |
| CU-14 | Enviar ticket a caja | 🔴 Must |
| CU-17 | Cobrar ticket en caja | 🔴 Must |
| CU-18 | Ver listado de reservas del día | 🔴 Must |
| CU-19 | Gestionar reservas (crear, editar, cancelar) | 🔴 Must |
| CU-20 | Asignar mesa a reserva | 🔴 Must |
| CU-21 | Gestionar usuarios y roles | 🔴 Must |
| CU-25 | Gestionar carta (añadir, editar, eliminar platos) | 🔴 Must |
| CU-27 | Enviar notificaciones automáticas | 🔴 Must |
| CU-N01 | Denegar acceso por credenciales incorrectas | 🔴 Must |
| CU-N02 | Bloquear operación por rol insuficiente | 🔴 Must |
| CU-N03 | Rechazar comanda por alérgenos incompletos | 🔴 Must |
| CU-N05 | Bloquear edición de línea en preparación | 🔴 Must |
| CU-N06 | Rechazar ticket por mesa ya cobrada | 🔴 Must |
| CU-15 | Editar ticket | 🟠 Should |
| CU-16 | Reclamar ticket enviado | 🟠 Should |
| CU-24 | Consultar log de auditoría | 🟠 Should |
| CU-26 | Configurar menú del día | 🟠 Should |
| CU-28 | Marcar plato como agotado / disponible | 🟠 Should |
| CU-N04 | Rechazar reserva por conflicto de horario | 🟠 Should |
| CU-N07 | Restringir pedido por falta de stock | 🟠 Should |
| CU-N10 | Desbloquear cuenta por intentos fallidos | 🟠 Should |
| CU-22 | Configurar plano de sala (zonas y mesas) | 🟡 Could |
| CU-23 | Unir / separar mesas | 🟡 Could |
| CU-N08 | Anular ticket ya cobrado por error de cobro | 🟡 Could |
| CU-N09 | Marcar reserva como no presentado | 🟡 Could |

[← Volver al índice del capítulo](README.md)
