# 4.7 Detalle de casos de uso implementados

En esta sección se documentan los casos de uso seleccionados para el desarrollo del MVP. Cada caso relaciona la acción visible en la interfaz con las rutas, controladores, servicios y modelos implicados.

## CU-04. Abrir mesa

![Abrir Mesa](imagenes/Sala2.png)

### Descripción funcional

Este caso de uso inicia el servicio de una mesa. Cuando el camarero o el administrador selecciona una mesa libre, el sistema crea una comanda en estado `ABIERTA`, asocia esa comanda a la mesa y cambia el estado de la mesa a `OCUPADA`.

El endpoint principal es `POST /api/comandas`. Aunque funcionalmente el usuario percibe que abre una mesa, técnicamente se crea una comanda activa vinculada a esa mesa.

### Parámetros de entrada

| Parámetro | Tipo | Descripción |
|---|---|---|
| `mesa_id` | ObjectId | Identificador de la mesa principal que se desea abrir. |
| `mesas_ids` | ObjectId[] opcional | Lista de mesas adicionales si se abre una comanda sobre mesas unidas. |

### Qué puede hacer el usuario

- Visualizar mesas agrupadas por zona desde la vista de mesas.
- Seleccionar una mesa libre o reservada.
- Abrir la mesa y comenzar una comanda.
- Acceder posteriormente a `ComandaView` para añadir platos.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `App.tsx -> abrirMesa()` | Ejecuta la acción desde la vista de mesas. |
| 2 | API client | `api.abrirMesa(...)` | Envía `POST /api/comandas` con `mesa_id`. |
| 3 | Routes | `comanda.routes.js` | Valida JWT, rol `ADMIN` o `CAMARERO` y cuerpo de la petición. |
| 4 | Controller | `comandaController.abrir()` | Extrae usuario autenticado y delega en el servicio. |
| 5 | Service | `comandaService.abrirComanda()` | Comprueba disponibilidad de mesa, crea comanda y ocupa la mesa. |
| 6 | Models | `Comanda`, `Mesa`, `LogAuditoria` | Persiste la comanda, actualiza la mesa y registra auditoría. |
| 7 | Sockets | `emitirEvento('comanda:abierta')` | Notifica la actualización al resto de vistas. |

### Restricciones

- La mesa no puede estar `OCUPADA` ni `FUERA_SERVICIO`.
- Una mesa no puede tener dos comandas activas.
- La operación requiere usuario autenticado con rol `ADMIN` o `CAMARERO`.

### Implementación

Archivos implicados: `frontend/src/App.tsx`, `frontend/src/api/client.ts`, `backend/src/routes/comanda.routes.js`, `backend/src/controllers/comandaController.js`, `backend/src/services/comandaService.js`, `backend/src/models/Comanda.js` y `backend/src/models/Mesa.js`.

## CU-06. Tomar comanda

![Tomar Comanda](imagenes/Comanda.png)

### Descripción funcional

Permite registrar los platos solicitados por los clientes de una mesa. La comanda se construye línea a línea, indicando plato, cantidad, observaciones, alérgenos y si pertenece a carta o menú del día.

El endpoint principal es `POST /api/comandas/:id/lineas`. La vista implicada es `ComandaView`.

### Parámetros de entrada

| Parámetro | Tipo | Descripción |
|---|---|---|
| `plato_id` | ObjectId | Plato seleccionado de la carta. |
| `cantidad` | number | Número de unidades solicitadas. |
| `observaciones` | string | Indicaciones para cocina. |
| `alergenos` | string[] | Alérgenos declarados y revisados por el camarero. |
| `esMenu` | boolean | Indica si la línea pertenece al menú del día. |
| `tarifa_menu_id` | ObjectId opcional | Tarifa aplicada cuando la línea pertenece al menú. |

### Qué puede hacer el usuario

- Seleccionar mesa activa.
- Filtrar platos por categoría.
- Alternar entre carta y menú.
- Indicar cantidad y observaciones.
- Confirmar alérgenos cuando el plato los contiene.
- Añadir líneas a la comanda activa.
- Solicitar segundos o postres cuando corresponda.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | El usuario selecciona plato, cantidad y observaciones. |
| 2 | Frontend | `App.tsx -> addLinea()` | Valida datos básicos y alérgenos revisados. |
| 3 | API client | `api.crearLinea(...)` | Envía `POST /api/comandas/:id/lineas`. |
| 4 | Routes | `comanda.routes.js` | Valida JWT, rol y esquema `lineaComandaSchema`. |
| 5 | Controller | `comandaController.agregarLinea()` | Inyecta `comanda_id` y `usuario_id`. |
| 6 | Service | `comandaService.agregarLineaComanda()` | Valida comanda abierta, plato disponible, tarifa y alérgenos. |
| 7 | Models | `LineaComanda` | Crea la línea con estado `PENDIENTE`. |
| 8 | Sockets | `lineaComanda:creada`, `kds:actualizado` | Actualiza KDS y comanda en tiempo real. |

### Restricciones

- Solo se pueden añadir líneas a comandas `ABIERTA`.
- El plato debe estar disponible.
- Si es menú, debe existir una tarifa vigente.
- Los alérgenos del plato deben estar confirmados.
- Segundos y postres de menú quedan retenidos hasta que el camarero solicite el pase.

### Implementación

Archivos implicados: `frontend/src/components/views/ComandaView.tsx`, `frontend/src/App.tsx`, `frontend/src/api/client.ts`, `backend/src/routes/comanda.routes.js`, `backend/src/controllers/comandaController.js`, `backend/src/services/comandaService.js`, `backend/src/models/LineaComanda.js`, `backend/src/models/Plato.js` y `backend/src/models/TarifaMenu.js`.

## CU-08. Editar comanda

### Descripción funcional

Permite corregir una línea de comanda mientras cocina todavía no ha empezado a prepararla. El sistema permite modificar cantidad u observaciones, y también cancelar una línea pendiente. Esta restricción evita cambios tardíos sobre platos que ya están en preparación.

Los endpoints principales son `PUT /api/comandas/lineas/:lineaId` y `PATCH /api/comandas/lineas/:lineaId/cancelar`.

### Parámetros de entrada

| Parámetro | Tipo | Descripción |
|---|---|---|
| `lineaId` | ObjectId | Identificador de la línea que se quiere modificar. |
| `cantidad` | number opcional | Nueva cantidad. |
| `observaciones` | string opcional | Nuevas observaciones para cocina. |
| `alergenos` | string[] opcional | Alérgenos confirmados de la línea. |

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | Muestra acciones de editar o cancelar por línea. |
| 2 | Frontend | `App.tsx -> guardarEdicionLinea()` | Recoge nueva cantidad y observaciones. |
| 3 | API client | `api.actualizarLinea(...)` | Envía `PUT /api/comandas/lineas/:lineaId`. |
| 4 | Routes | `comanda.routes.js` | Valida JWT, rol y `lineaId`. |
| 5 | Controller | `comandaController.actualizarLinea()` | Delega en el servicio. |
| 6 | Service | `comandaService.actualizarLineaComanda()` | Comprueba que la comanda esté abierta y la línea siga `PENDIENTE`. |
| 7 | Models | `LineaComanda`, `LogAuditoria` | Actualiza la línea y registra auditoría. |
| 8 | Sockets | `lineaComanda:actualizada`, `kds:actualizado` | Refresca KDS y comanda. |

### Restricciones

- Solo se pueden editar líneas en estado `PENDIENTE`.
- No se pueden editar líneas `EN_PREPARACION`, `LISTO` o `SERVIDO`.
- La cancelación no elimina físicamente la línea: cambia su estado a `CANCELADO`.

## CU-12. Marcar plato como listo

### Descripción funcional

Permite a cocina cambiar el estado de una línea de comanda hasta `LISTO`. Este cambio informa a sala de que el plato puede servirse y actualiza el KDS en tiempo real.

El endpoint principal es `PATCH /api/kds/lineas/:lineaId/estado`.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `KdsView` | Cocina pulsa el cambio de estado. |
| 2 | Frontend | `App.tsx -> updateKds()` | Llama al cliente HTTP o encola offline si procede. |
| 3 | API client | `api.kdsEstado(...)` | Envía `PATCH /api/kds/lineas/:lineaId/estado`. |
| 4 | Routes | `kds.routes.js` | Valida JWT, rol y estado permitido. |
| 5 | Controller | `kdsController.cambiarEstado()` | Delega en `kdsService`. |
| 6 | Service | `kdsService.cambiarEstadoLineaKds()` | Valida transición de estado. |
| 7 | Models | `LineaComanda`, `Comanda`, `LogAuditoria` | Actualiza estado y registra auditoría. |
| 8 | Sockets | `kds:actualizado`, notificación de platos listos | Actualiza cocina y sala. |

### Restricciones

- No se permiten transiciones incoherentes: por ejemplo, de `PENDIENTE` a `SERVIDO`.
- Las líneas canceladas no avanzan en el KDS.
- El KDS solo muestra líneas cuyo pase haya sido solicitado.

## CU-14. Enviar ticket a caja

### Descripción funcional

Genera un ticket a partir de la comanda activa de una mesa y lo deja disponible en caja para su cobro. El ticket contiene el desglose de platos, tarifas de menú y total económico.

El endpoint principal es `POST /api/tickets`.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | El usuario pulsa `Enviar ticket a caja`. |
| 2 | Frontend | `App.tsx -> enviarTicket()` | Llama al cliente HTTP o registra cola offline. |
| 3 | API client | `api.generarTicket(...)` | Envía `POST /api/tickets`. |
| 4 | Routes | `ticket.routes.js` | Valida JWT, rol y body `ticketSchema`. |
| 5 | Controller | `ticketController.generar()` | Delega en el servicio. |
| 6 | Service | `ticketService.generarTicket()` | Comprueba comanda, evita duplicados y calcula total. |
| 7 | Models | `Ticket`, `LineaComanda`, `Comanda`, `LogAuditoria` | Crea ticket y registra auditoría. |
| 8 | Sockets | `ticket:generado` | Actualiza caja. |

### Restricciones

- No se genera ticket de una comanda cancelada.
- No se duplica un ticket si ya existe.
- No se genera ticket si quedan líneas pendientes, en preparación o listas sin servir.
- Las líneas canceladas no suman al total.
- Los menús se cobran por tarifa, no por cada plato individual.

## CU-17. Cobrar ticket en caja

### Descripción funcional

Permite al administrador confirmar el cobro de un ticket pendiente. El ticket pasa a estado `COBRADO`, queda asociado al usuario que realiza la acción y registra la fecha de cobro.

El endpoint principal es `PATCH /api/tickets/:id/cobrar`.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `CajaView` | El administrador pulsa `Cobrar`. |
| 2 | Frontend | `App.tsx -> cobrar()` | Solicita el cobro al backend. |
| 3 | API client | `api.cobrarTicket(...)` | Envía `PATCH /api/tickets/:id/cobrar`. |
| 4 | Routes | `ticket.routes.js` | Valida JWT y restringe la acción al rol `ADMIN`. |
| 5 | Controller | `ticketController.cobrar()` | Delega en `ticketService`. |
| 6 | Service | `ticketService.cobrarTicket()` | Comprueba que el ticket esté `PENDIENTE`. |
| 7 | Models | `Ticket`, `LogAuditoria` | Cambia estado a `COBRADO`, guarda usuario y fecha. |
| 8 | Sockets | `ticket:cobrado`, `comanda:actualizada` | Refresca caja, sala y comanda. |

### Restricciones

- Solo el rol `ADMIN` puede cobrar tickets.
- No se puede cobrar dos veces el mismo ticket.
- Cobrar el ticket no libera automáticamente la mesa.

## CU-05. Cerrar mesa



### Descripción funcional

Cierra el servicio de una mesa una vez que el ticket asociado ya ha sido cobrado. La mesa vuelve a estado `LIBRE`, la comanda queda cerrada y la mesa puede utilizarse en un nuevo servicio.

El endpoint principal es `PATCH /api/mesas/:id/cerrar`.

### Flujo técnico

| Paso | Capa | Clase / función | Acción |
|---|---|---|---|
| 1 | Frontend | `CajaView` | El administrador pulsa `Cerrar mesa`. |
| 2 | Frontend | `App.tsx -> cerrarMesaCaja()` | Solicita cierre al backend. |
| 3 | API client | `api.cerrarMesa(...)` | Envía `PATCH /api/mesas/:id/cerrar`. |
| 4 | Routes | `mesa.routes.js` | Valida JWT y restringe la acción al rol `ADMIN`. |
| 5 | Controller | `mesaController.cerrar()` | Delega en `mesaService`. |
| 6 | Service | `mesaService.cerrarMesa()` | Comprueba comanda activa y ticket cobrado. |
| 7 | Models | `Mesa`, `Comanda`, `Ticket`, `LogAuditoria` | Cierra comanda, libera mesa y registra auditoría. |
| 8 | Sockets | `mesa:actualizada` | Refresca la vista de mesas. |

### Restricciones

- Solo el rol `ADMIN` puede cerrar mesas.
- No se puede cerrar una mesa sin comanda activa, salvo que ya estuviera libre.
- No se puede cerrar una mesa sin ticket cobrado.
- El cierre libera todas las mesas asociadas a la misma comanda en caso de mesas unidas.

[← Volver al índice del capítulo](README.md)
