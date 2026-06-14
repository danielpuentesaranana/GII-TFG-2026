# 2.2 Requisitos funcionales

Los siguientes requisitos funcionales han sido identificados como esenciales para el correcto funcionamiento del sistema de gestión interna del Restaurante La Unión.

## Gestión de usuarios y acceso

| ID | Requisito funcional |
|---|---|
| RF01 | El sistema debe permitir a los usuarios iniciar sesión mediante email y contraseña, generando un token de acceso JWT y un token de actualización. |
| RF02 | Cada usuario debe tener un rol asociado, Camarero, Cocinero o Administrador, que limite su acceso según los permisos definidos. |
| RF03 | El sistema debe bloquear temporalmente el acceso tras varios intentos fallidos de autenticación. |
| RF04 | El sistema debe permitir al Administrador crear, editar, desbloquear y eliminar usuarios del sistema. |

## Gestión de mesas y zonas

| ID | Requisito funcional |
|---|---|
| RF05 | El sistema debe permitir visualizar el estado de todas las mesas agrupadas por zona: libre, ocupada, reservada o fuera de servicio. |
| RF06 | El sistema debe permitir abrir y cerrar mesas, siendo el cierre exclusivo del Administrador tras el cobro del ticket. |
| RF07 | El sistema debe permitir al Administrador crear, editar y eliminar mesas, así como asociarlas a una zona del restaurante. |

## Gestión de comandas

| ID | Requisito funcional |
|---|---|
| RF08 | El sistema debe permitir al camarero tomar comandas digitales seleccionando platos de la carta o del menú del día, indicando cantidad, observaciones y alérgenos confirmados. |
| RF09 | El sistema debe exigir observaciones en cada línea de comanda y la confirmación de los alérgenos declarados cuando el plato los tenga asociados. |
| RF10 | El sistema debe guardar operaciones pendientes en IndexedDB cuando no haya conexión y sincronizarlas automáticamente al recuperar la red. |
| RF11 | El sistema debe permitir editar una comanda activa, bloqueando la modificación de líneas que ya estén en preparación, listas o servidas. |
| RF12 | El sistema debe permitir al camarero solicitar segundos platos o postres únicamente cuando el pase anterior haya sido marcado como listo o servido. |
| RF13 | Cualquier edición relevante sobre una comanda debe quedar registrada en el log de auditoría con usuario y fecha. |

## Gestión de cocina (KDS)

| ID | Requisito funcional |
|---|---|
| RF14 | El sistema debe mostrar al Cocinero las líneas de comanda pendientes en tiempo real en la pantalla KDS, con alérgenos y observaciones visibles. |
| RF15 | El sistema debe permitir al Cocinero marcar líneas como en preparación, listas o servidas, actualizando su estado en tiempo real. |
| RF16 | El sistema debe notificar automáticamente a sala mediante WebSocket cuando un plato esté listo para servir. |
| RF17 | El sistema debe actualizar el KDS en tiempo real cuando un camarero añada, edite o cancele líneas de comanda que afecten a cocina. |

## Gestión de tickets y caja

| ID | Requisito funcional |
|---|---|
| RF18 | El sistema debe permitir al camarero enviar el ticket de una mesa a caja, generando un desglose de platos, tarifas de menú y total. |
| RF19 | El ticket debe incluir identificador de la comanda, mesa, usuario asociado, fecha, desglose de líneas y total. |
| RF20 | El sistema debe permitir reclamar o cancelar un ticket según su estado y los permisos del usuario. |
| RF21 | El cobro de un ticket debe ser exclusivo del Administrador. El cierre de mesa será una acción posterior y separada del cobro. |

## Gestión de reservas

| ID | Requisito funcional |
|---|---|
| RF22 | El sistema debe permitir al Administrador crear, editar, cancelar y eliminar reservas, asignando mesa o zona, fecha, hora y número de comensales. |
| RF23 | El sistema debe impedir la creación de reservas incompatibles para la misma mesa en el mismo tramo horario. |
| RF24 | El sistema debe enviar automáticamente un aviso mediante WebSocket cuando exista una reserva próxima sobre una mesa ocupada. |

## Gestión de carta y menú del día

| ID | Requisito funcional |
|---|---|
| RF25 | El sistema debe permitir al Administrador agregar, editar y eliminar platos de la carta con nombre, precio, categoría, disponibilidad y alérgenos. |
| RF26 | El sistema debe permitir al Administrador configurar tarifas de menú del día con modalidad, tipo, precio y vigencia. |
| RF27 | El sistema debe permitir al camarero registrar platos pertenecientes al menú del día aplicando la tarifa de menú vigente correspondiente. |

## Auditoría

| ID | Requisito funcional |
|---|---|
| RF28 | El sistema debe registrar las acciones relevantes del sistema, como cambios de usuarios, reservas, carta, comandas, tickets, cobros y cierre de mesas, con usuario y fecha. |
| RF29 | El sistema debe permitir al Administrador consultar el registro de auditoría. |

[← Volver al índice del capítulo](README.md)
