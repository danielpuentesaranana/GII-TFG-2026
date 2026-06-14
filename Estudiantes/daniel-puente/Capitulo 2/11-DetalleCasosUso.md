# 2.11 Detalle de casos de uso principales

## CU-01 - Iniciar sesión

| Campo | Valor |
|---|---|
| ID | CU-01 |
| Nombre | Iniciar sesión |
| Actores | Camarero, Administrador, Cocinero |
| Prioridad | Must |
| Precondición | El usuario tiene una cuenta activa en el sistema. |
| Postcondición | El usuario accede a su pantalla principal según su rol. |

**Flujo principal**

1. El usuario abre la aplicación en su dispositivo.
2. El sistema muestra el formulario de inicio de sesión.
3. El usuario introduce su email y contraseña.
4. El sistema valida las credenciales contra la base de datos.
5. El sistema genera un access token JWT y un refresh token.
6. El sistema redirige al usuario a su pantalla principal según su rol.

**Flujos alternativos**

- CU-N01: si las credenciales son incorrectas, el sistema muestra un mensaje de error y no permite el acceso.
- CU-N10: si se producen varios intentos fallidos consecutivos, el sistema bloquea temporalmente la cuenta.

## CU-02 - Cerrar sesión

| Campo | Valor |
|---|---|
| ID | CU-02 |
| Nombre | Cerrar sesión |
| Actores | Camarero, Administrador, Cocinero |
| Prioridad | Must |
| Precondición | El usuario tiene una sesión activa. |
| Postcondición | La sesión queda invalidada y el usuario es redirigido al login. |

**Flujo principal**

1. El usuario pulsa el botón de cerrar sesión.
2. El sistema invalida la sesión activa.
3. El sistema elimina los datos de sesión del dispositivo.
4. El sistema redirige al usuario a la pantalla de inicio de sesión.

## CU-03 - Ver estado de mesas por zona

| Campo | Valor |
|---|---|
| ID | CU-03 |
| Nombre | Ver estado de mesas por zona |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | El usuario ha iniciado sesión. |
| Postcondición | El usuario visualiza el plano de mesas actualizado. |

**Flujo principal**

1. El usuario accede a la pantalla principal de mesas.
2. El sistema muestra las zonas configuradas: comedor, bar, terraza, plaza, mirador, balcón y escalera.
3. Cada mesa se muestra con su estado actual: libre, ocupada, reservada o fuera de servicio.
4. El sistema actualiza el estado mediante eventos en tiempo real cuando se producen cambios relevantes.

## CU-04 - Abrir mesa

| Campo | Valor |
|---|---|
| ID | CU-04 |
| Nombre | Abrir mesa |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | La mesa está en estado libre o reservada y no tiene una comanda activa. |
| Postcondición | La mesa pasa a estado ocupada y queda asociada a una comanda activa. |

**Flujo principal**

1. El usuario selecciona una mesa disponible en el plano de mesas.
2. El sistema verifica que la mesa no tenga una comanda activa.
3. El sistema crea una comanda en estado abierta asociada a la mesa.
4. El sistema actualiza la mesa a estado ocupada.
5. El sistema registra la apertura en auditoría y actualiza el plano de mesas en tiempo real.

## CU-05 - Cerrar mesa

| Campo | Valor |
|---|---|
| ID | CU-05 |
| Nombre | Cerrar mesa |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | La mesa está ocupada y el ticket asociado ha sido cobrado. |
| Postcondición | La comanda queda cerrada y la mesa pasa a estado libre. |

**Flujo principal**

1. El Administrador selecciona una mesa pendiente de cierre.
2. El sistema verifica que existe una comanda activa asociada.
3. El sistema comprueba que el ticket correspondiente está cobrado.
4. El sistema cierra la comanda, libera la mesa y registra la acción en auditoría.
5. El plano de mesas se actualiza en tiempo real.

## CU-06 - Tomar comanda

| Campo | Valor |
|---|---|
| ID | CU-06 |
| Nombre | Tomar comanda |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | La mesa está ocupada y tiene una comanda activa. |
| Postcondición | Las líneas de comanda quedan registradas y disponibles para cocina cuando corresponda. |

**Flujo principal**

1. El usuario selecciona una mesa ocupada.
2. El sistema muestra la carta y las tarifas de menú disponibles.
3. El usuario añade platos línea a línea, indicando cantidad, alérgenos confirmados y observaciones.
4. El sistema valida que los datos obligatorios estén completos.
5. El sistema guarda cada línea de comanda.
6. Si la línea debe mostrarse en cocina, el sistema actualiza el KDS mediante eventos en tiempo real.
7. Si no hay conexión, la operación queda guardada en IndexedDB y se sincroniza al recuperar la red.

**Flujos alternativos**

- CU-N03: si los alérgenos requeridos no están confirmados, el sistema bloquea la operación.
- CU-N07: si un plato está marcado como no disponible, el sistema impide añadirlo a la comanda.

## CU-07 - Ver comanda de una mesa

| Campo | Valor |
|---|---|
| ID | CU-07 |
| Nombre | Ver comanda de una mesa |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | La mesa está ocupada y tiene una comanda activa. |
| Postcondición | El usuario visualiza el detalle completo de la comanda. |

**Flujo principal**

1. El usuario selecciona una mesa ocupada.
2. El sistema recupera la comanda activa asociada a la mesa.
3. El sistema muestra platos, cantidades, alérgenos, observaciones y estado de cada línea.

## CU-08 - Editar comanda

| Campo | Valor |
|---|---|
| ID | CU-08 |
| Nombre | Editar comanda |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | La mesa tiene una comanda activa con líneas en estado pendiente. |
| Postcondición | La comanda queda actualizada y los cambios se reflejan en el sistema. |

**Flujo principal**

1. El usuario accede a la comanda activa de una mesa.
2. El usuario modifica cantidades u observaciones, añade nuevas líneas o cancela líneas pendientes.
3. El sistema valida que la línea siga en estado pendiente.
4. El sistema guarda los cambios y actualiza la información en tiempo real.
5. El sistema registra la edición en auditoría.

**Flujos alternativos**

- CU-N05: si el usuario intenta editar una línea en preparación, lista o servida, el sistema bloquea la acción.

## CU-09 - Solicitar segundos platos o postres

| Campo | Valor |
|---|---|
| ID | CU-09 |
| Nombre | Solicitar segundos platos o postres |
| Actor principal | Camarero, Administrador |
| Actor secundario | Sistema de notificaciones |
| Prioridad | Must |
| Precondición | El pase anterior del menú está listo o servido. |
| Postcondición | El nuevo pase queda solicitado y aparece en el KDS de cocina. |

**Flujo principal**

1. El camarero accede a la comanda de la mesa.
2. El sistema muestra los pases de menú pendientes.
3. El camarero solicita segundos platos o postres cuando corresponde.
4. El sistema valida que el pase anterior esté listo o servido.
5. El sistema marca las líneas del nuevo pase como solicitadas para KDS.
6. El KDS se actualiza en tiempo real.

## CU-10 - Ver líneas pendientes en KDS

| Campo | Valor |
|---|---|
| ID | CU-10 |
| Nombre | Ver líneas pendientes en KDS |
| Actores | Cocinero |
| Prioridad | Must |
| Precondición | El cocinero ha iniciado sesión. |
| Postcondición | El cocinero visualiza las líneas pendientes de preparación. |

**Flujo principal**

1. El cocinero accede a la pantalla KDS.
2. El sistema muestra las líneas pendientes, en preparación y listas.
3. Cada línea muestra mesa, plato, cantidad, alérgenos y observaciones.
4. El KDS se actualiza en tiempo real ante nuevas líneas, ediciones o cambios de estado.

## CU-11 - Marcar línea en preparación

| Campo | Valor |
|---|---|
| ID | CU-11 |
| Nombre | Marcar línea en preparación |
| Actores | Cocinero |
| Prioridad | Must |
| Precondición | Existe al menos una línea pendiente en el KDS. |
| Postcondición | La línea pasa a estado EN_PREPARACION y queda bloqueada para edición desde sala. |

**Flujo principal**

1. El cocinero selecciona una línea pendiente en el KDS.
2. El cocinero pulsa la acción de iniciar preparación.
3. El sistema actualiza el estado de la línea.
4. El sistema notifica el cambio al resto de vistas conectadas.

## CU-12 - Marcar plato como listo

| Campo | Valor |
|---|---|
| ID | CU-12 |
| Nombre | Marcar plato como listo |
| Actor principal | Cocinero |
| Actor secundario | Sistema de notificaciones |
| Prioridad | Must |
| Precondición | La línea está en estado EN_PREPARACION. |
| Postcondición | La línea pasa a estado LISTO y sala recibe el aviso correspondiente. |

**Flujo principal**

1. El cocinero selecciona una línea en preparación en el KDS.
2. El cocinero pulsa la acción de marcar como listo.
3. El sistema actualiza el estado de la línea.
4. El sistema emite la notificación de platos listos para la mesa correspondiente.

## CU-13 - Ver alérgenos y observaciones

| Campo | Valor |
|---|---|
| ID | CU-13 |
| Nombre | Ver alérgenos y observaciones |
| Actores | Cocinero |
| Prioridad | Must |
| Precondición | Existe una línea de comanda con alérgenos u observaciones registradas. |
| Postcondición | El cocinero visualiza la información relevante para preparar el plato. |

**Flujo principal**

1. El cocinero accede al KDS.
2. El sistema muestra cada línea con sus alérgenos confirmados.
3. Las observaciones aparecen asociadas al plato correspondiente.
4. La información se mantiene visible durante la preparación.

## CU-14 - Enviar ticket a caja

| Campo | Valor |
|---|---|
| ID | CU-14 |
| Nombre | Enviar ticket a caja |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | La mesa tiene una comanda activa preparada para cobro. |
| Postcondición | El ticket queda generado en caja con el desglose completo. |

**Flujo principal**

1. El usuario revisa la comanda de la mesa.
2. El usuario pulsa la opción de enviar ticket a caja.
3. El sistema genera el ticket con desglose de líneas, tarifas de menú y total.
4. El sistema registra la generación del ticket en auditoría.
5. El ticket queda disponible en caja para ser cobrado por el Administrador.

**Flujos alternativos**

- CU-N06: si el ticket ya existe o la mesa ya está cobrada, el sistema rechaza la operación.

## CU-17 - Cobrar ticket en caja

| Campo | Valor |
|---|---|
| ID | CU-17 |
| Nombre | Cobrar ticket en caja |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | El ticket ha sido enviado a caja y está pendiente de cobro. |
| Postcondición | El ticket queda marcado como cobrado y la mesa queda pendiente de cierre. |

**Flujo principal**

1. El Administrador accede a la vista de caja.
2. El sistema muestra los tickets pendientes de cobro.
3. El Administrador selecciona el ticket y confirma el cobro.
4. El sistema marca el ticket como COBRADO.
5. El sistema registra el cobro en auditoría con usuario y fecha.
6. La mesa no se libera hasta ejecutar el caso de uso CU-05 Cerrar mesa.

## CU-18 - Ver listado de reservas del día

| Campo | Valor |
|---|---|
| ID | CU-18 |
| Nombre | Ver listado de reservas del día |
| Actores | Administrador, Camarero |
| Prioridad | Must |
| Precondición | El usuario ha iniciado sesión. |
| Postcondición | El usuario visualiza las reservas del día ordenadas por hora. |

**Flujo principal**

1. El usuario accede al módulo de reservas.
2. El sistema muestra las reservas del día con cliente, hora, comensales, mesa o zona asignada y estado.
3. El Administrador puede gestionar las reservas.
4. El Camarero puede consultar la información necesaria para atender la mesa reservada.

## CU-19 - Gestionar reservas

| Campo | Valor |
|---|---|
| ID | CU-19 |
| Nombre | Gestionar reservas |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | El Administrador ha iniciado sesión. |
| Postcondición | La reserva queda creada, modificada, cancelada o eliminada en el sistema. |

**Flujo principal**

1. El Administrador accede al módulo de reservas.
2. El Administrador selecciona crear, editar, cancelar o eliminar una reserva.
3. El sistema valida fecha, horario de servicio y disponibilidad de mesa o zona.
4. El sistema guarda los cambios y actualiza el listado.

**Flujos alternativos**

- CU-N04: si existe conflicto de horario para la mesa, el sistema rechaza la operación.

## CU-20 - Asignar mesa a reserva

| Campo | Valor |
|---|---|
| ID | CU-20 |
| Nombre | Asignar mesa a reserva |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | Existe una reserva sin mesa asignada o se desea reasignar. |
| Postcondición | La reserva queda vinculada a una mesa concreta. |

**Flujo principal**

1. El Administrador selecciona una reserva del listado.
2. El sistema muestra las mesas disponibles para el tramo horario.
3. El Administrador selecciona una mesa disponible.
4. El sistema vincula la mesa a la reserva y actualiza la información de la reserva.

## CU-21 - Gestionar usuarios y roles

| Campo | Valor |
|---|---|
| ID | CU-21 |
| Nombre | Gestionar usuarios y roles |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | El Administrador ha iniciado sesión. |
| Postcondición | El usuario queda creado, modificado, desbloqueado o eliminado con su rol correspondiente. |

**Flujo principal**

1. El Administrador accede al panel de gestión de usuarios.
2. El sistema muestra el listado de usuarios.
3. El Administrador puede crear un usuario con nombre, email, contraseña y rol.
4. El Administrador puede editar datos, cambiar contraseña, desbloquear o eliminar un usuario.
5. El sistema guarda los cambios y los aplica de forma inmediata.

**Flujos alternativos**

- CU-N02: si un usuario sin rol de Administrador intenta acceder, el sistema bloquea la operación.

## CU-25 - Gestionar carta

| Campo | Valor |
|---|---|
| ID | CU-25 |
| Nombre | Gestionar carta |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | El Administrador ha iniciado sesión. |
| Postcondición | El plato o tarifa de menú queda añadido, actualizado o eliminado. |

**Flujo principal**

1. El Administrador accede al módulo de gestión de carta.
2. El sistema muestra los platos y tarifas de menú.
3. El Administrador puede añadir, editar o eliminar platos.
4. El Administrador puede gestionar disponibilidad, alérgenos, precio, categoría y uso en menú.
5. Los cambios se reflejan en la vista de toma de comandas.

## CU-27 - Enviar notificaciones automáticas

| Campo | Valor |
|---|---|
| ID | CU-27 |
| Nombre | Enviar notificaciones automáticas |
| Actores | Sistema de notificaciones |
| Prioridad | Must |
| Precondición | Se produce un evento relevante en el sistema. |
| Postcondición | La notificación queda enviada al actor correspondiente mediante WebSocket. |

**Flujo principal**

1. Se produce un evento relevante, como platos listos o reserva próxima sobre una mesa ocupada.
2. El sistema detecta el evento.
3. El sistema envía la notificación correspondiente mediante Socket.IO.
4. Para platos listos, el aviso se muestra como "Platos listos - Mesa X".
5. Para reserva próxima, el aviso se muestra como "Reserva próxima - Mesa X".
6. El dispositivo del receptor muestra el aviso de forma visible.

[← Volver al índice del capítulo](README.md)
