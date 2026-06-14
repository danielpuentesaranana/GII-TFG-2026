# 3.3 Rutas principales

Las rutas principales de la API REST se agrupan por recurso. Todas las operaciones protegidas requieren autenticación mediante JWT y, cuando corresponde, autorización por rol.

## Autenticación

| Método | Ruta | Controlador principal | Propósito |
|---|---|---|---|
| `POST` | `/api/auth/login` | `AuthController` | Iniciar sesión y generar token JWT. |
| `POST` | `/api/auth/refresh` | `AuthController` | Renovar la sesión mediante token de actualización. |
| `POST` | `/api/auth/logout` | `AuthController` | Cerrar sesión activa. |
| `POST` | `/api/auth/registro` | `AuthController` | Registrar usuario desde administración. |
| `GET` | `/api/auth/perfil` | `AuthController` | Obtener el perfil del usuario autenticado. |
| `PATCH` | `/api/auth/password` | `AuthController` | Actualizar contraseña del usuario autenticado. |

## Mesas y zonas

| Método | Ruta | Controlador principal | Propósito |
|---|---|---|---|
| `GET` | `/api/mesas` | `MesaController` | Listar mesas y estados por zona. |
| `GET` | `/api/mesas/:id` | `MesaController` | Consultar una mesa concreta. |
| `GET` | `/api/mesas/:id/comanda-activa` | `MesaController` | Obtener la comanda activa de una mesa. |
| `POST` | `/api/mesas` | `MesaController` | Crear una nueva mesa. |
| `PUT` | `/api/mesas/:id` | `MesaController` | Editar datos de una mesa. |
| `PATCH` | `/api/mesas/:id/estado` | `MesaController` | Cambiar el estado operativo de una mesa. |
| `PATCH` | `/api/mesas/:id/asignar-comanda` | `MesaController` | Asociar una comanda activa a una mesa. |
| `PATCH` | `/api/mesas/:id/liberar` | `MesaController` | Liberar una mesa. |
| `PATCH` | `/api/mesas/:id/cerrar` | `MesaController` | Cerrar mesa tras ticket cobrado. |
| `DELETE` | `/api/mesas/:id` | `MesaController` | Eliminar una mesa. |
| `GET` | `/api/zonas` | `ZonaController` | Listar zonas del restaurante. |
| `GET` | `/api/zonas/:id` | `ZonaController` | Consultar una zona concreta. |
| `POST` | `/api/zonas` | `ZonaController` | Crear una zona. |
| `PUT` | `/api/zonas/:id` | `ZonaController` | Actualizar una zona. |
| `DELETE` | `/api/zonas/:id` | `ZonaController` | Eliminar una zona. |

## Comandas y KDS

| Método | Ruta | Controlador principal | Propósito |
|---|---|---|---|
| `GET` | `/api/comandas` | `ComandaController` | Listar comandas. |
| `GET` | `/api/comandas/:id` | `ComandaController` | Obtener una comanda concreta. |
| `POST` | `/api/comandas` | `ComandaController` | Abrir mesa creando una comanda activa. |
| `POST` | `/api/comandas/:id/lineas` | `ComandaController` | Añadir línea a una comanda. |
| `PUT` | `/api/comandas/lineas/:lineaId` | `ComandaController` | Editar una línea pendiente. |
| `PATCH` | `/api/comandas/lineas/:lineaId/cancelar` | `ComandaController` | Cancelar una línea pendiente. |
| `PATCH` | `/api/comandas/:id/solicitar-pase` | `ComandaController` | Solicitar segundos o postres de menú. |
| `PATCH` | `/api/comandas/:id/estado` | `ComandaController` | Cambiar estado de una comanda. |
| `PATCH` | `/api/comandas/:id/cerrar` | `ComandaController` | Cerrar una comanda. |
| `GET` | `/api/kds` | `KDSController` | Listar líneas visibles en cocina. |
| `PATCH` | `/api/kds/lineas/:lineaId/estado` | `KDSController` | Cambiar estado de una línea de cocina. |
| `PATCH` | `/api/kds/lineas/:lineaId/iniciar` | `KDSController` | Marcar línea como en preparación. |
| `PATCH` | `/api/kds/lineas/:lineaId/lista` | `KDSController` | Marcar plato como listo. |
| `PATCH` | `/api/kds/lineas/:lineaId/servida` | `KDSController` | Marcar plato como servido. |

## Tickets, reservas, carta y usuarios

| Método | Ruta | Controlador principal | Propósito |
|---|---|---|---|
| `GET` | `/api/tickets` | `TicketController` | Listar tickets. |
| `GET` | `/api/tickets/:id` | `TicketController` | Obtener detalle de un ticket. |
| `POST` | `/api/tickets` | `TicketController` | Generar ticket desde una comanda. |
| `PATCH` | `/api/tickets/:id/cobrar` | `TicketController` | Cobrar ticket en caja. |
| `PATCH` | `/api/tickets/:id/reclamar` | `TicketController` | Reclamar un ticket. |
| `PATCH` | `/api/tickets/:id/cancelar` | `TicketController` | Cancelar un ticket. |
| `GET` | `/api/reservas` | `ReservaController` | Listar reservas. |
| `GET` | `/api/reservas/dia` | `ReservaController` | Consultar reservas del día. |
| `GET` | `/api/reservas/:id` | `ReservaController` | Obtener una reserva concreta. |
| `POST` | `/api/reservas` | `ReservaController` | Crear reserva. |
| `PUT` | `/api/reservas/:id` | `ReservaController` | Editar reserva. |
| `PATCH` | `/api/reservas/:id/asignar-mesa` | `ReservaController` | Asignar mesa a una reserva. |
| `PATCH` | `/api/reservas/:id/estado` | `ReservaController` | Cambiar estado de una reserva. |
| `DELETE` | `/api/reservas/:id` | `ReservaController` | Eliminar reserva. |
| `GET` | `/api/carta/platos` | `CartaController` | Listar platos de la carta. |
| `GET` | `/api/carta/platos/:id` | `CartaController` | Obtener un plato concreto. |
| `POST` | `/api/carta/platos` | `CartaController` | Crear plato. |
| `PUT` | `/api/carta/platos/:id` | `CartaController` | Editar plato. |
| `DELETE` | `/api/carta/platos/:id` | `CartaController` | Eliminar plato. |
| `GET` | `/api/carta/tarifas-menu` | `CartaController` | Listar tarifas de menú. |
| `GET` | `/api/carta/tarifas-menu/:id` | `CartaController` | Obtener una tarifa de menú. |
| `POST` | `/api/carta/tarifas-menu` | `CartaController` | Crear tarifa de menú. |
| `PUT` | `/api/carta/tarifas-menu/:id` | `CartaController` | Editar tarifa de menú. |
| `DELETE` | `/api/carta/tarifas-menu/:id` | `CartaController` | Eliminar tarifa de menú. |
| `GET` | `/api/usuarios` | `UsuarioController` | Listar usuarios. |
| `GET` | `/api/usuarios/:id` | `UsuarioController` | Obtener un usuario concreto. |
| `POST` | `/api/usuarios` | `UsuarioController` | Crear usuario. |
| `PUT` | `/api/usuarios/:id` | `UsuarioController` | Editar usuario. |
| `PATCH` | `/api/usuarios/:id/password` | `UsuarioController` | Actualizar contraseña de usuario. |
| `PATCH` | `/api/usuarios/:id/desbloquear` | `UsuarioController` | Desbloquear usuario. |
| `DELETE` | `/api/usuarios/:id` | `UsuarioController` | Eliminar usuario. |

## Sincronización y auditoría

| Método | Ruta | Controlador principal | Propósito |
|---|---|---|---|
| `GET` | `/api/sync/tipos` | `OfflineSyncController` | Consultar tipos de operaciones offline. |
| `GET` | `/api/sync/pendientes` | `OfflineSyncController` | Listar operaciones pendientes. |
| `GET` | `/api/sync/historial` | `OfflineSyncController` | Consultar historial de operaciones offline. |
| `POST` | `/api/sync/operacion` | `OfflineSyncController` | Registrar una operación offline. |
| `POST` | `/api/sync/operaciones` | `OfflineSyncController` | Registrar varias operaciones offline. |
| `POST` | `/api/sync/sincronizar` | `OfflineSyncController` | Sincronizar operaciones pendientes. |
| `GET` | `/api/auditoria` | `AuditoriaController` | Consultar registros de auditoría. |

[← Volver al índice del capítulo](README.md)
