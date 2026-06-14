# 4.8 Lógica de negocio más relevante

## Ciclo de vida de mesa

La mesa no se considera solo un registro con estado libre u ocupada. El backend enriquece la información con un estado operativo derivado de comanda, líneas y ticket:

- Libre o reservada.
- Sentados, cuando hay comanda abierta sin platos.
- Cocina trabajando, cuando hay líneas pendientes o en preparación.
- Segundos pendientes o postres pendientes, cuando un menú tiene pases retenidos.
- Listo para servir o todo servido.
- Ticket en caja.
- Cobrada, cerrar mesa.

Esto permite que sala actúe de un vistazo sin entrar en cada mesa.

## Control de pases de menú

Uno de los puntos más importantes para un restaurante de alto tránsito es que la cocina no reciba todos los platos de golpe. La lógica implementada distingue entre carta y menú:

- Los primeros platos del menú se solicitan inicialmente.
- Los segundos y postres quedan retenidos con `solicitadoKds = false`.
- El camarero solicita segundos o postres desde la comanda cuando el cliente lo pide.
- El KDS solo muestra líneas solicitadas.

Con esta regla, cocina trabaja por pase y por mesa, evitando acumulación visual y errores de prioridad.

## Cálculo correcto de menús

El ticket agrupa las líneas de menú por tarifa y cantidad, evitando cobrar un primero y un segundo como dos menús separados. En lugar de sumar cada plato del menú, se cobra la tarifa del menú una vez por comensal/tarifa. La carta se suma por precio de plato y cantidad.

## KDS y estados de cocina

El KDS gestiona el flujo operativo:

1. `PENDIENTE`.
2. `EN_PREPARACION`.
3. `LISTO`.
4. `SERVIDO`.

La separación por estado permite a cocina distinguir lo que entra, lo que ya se está cocinando y lo que debe retirar sala. Esta trazabilidad reduce problemas entre camarero y cocina.

## Alérgenos y observaciones

Los platos contienen alérgenos declarados. Al añadir una línea, el camarero debe confirmar los alérgenos si existen. La información viaja hasta la comanda y el KDS, por lo que la cocina puede ver observaciones y alertas relevantes.

## Caja y cierre de mesa

El sistema separa cobrar ticket de cerrar mesa:

- Cobrar ticket cambia el estado del ticket.
- Cerrar mesa libera la mesa solo cuando el ticket está cobrado.
- Esta operación queda reservada para administración.

La separación evita que una mesa quede libre por error antes de haber finalizado el cobro.

## Reservas y horarios válidos

Las reservas están limitadas a horarios reales de servicio:

- Comidas: 13:00 a 16:00.
- Cenas: 20:00 a 23:00.

Además, se controla el solapamiento de reservas por mesa y se permite doblar mesa si hay margen temporal suficiente.

## Offline y sincronización

El frontend guarda operaciones pendientes en IndexedDB cuando hay problemas de conexión. Al recuperar conectividad, el sistema sincroniza con el backend. Las operaciones contempladas incluyen tomar comanda, crear, editar o cancelar líneas, cambiar estados de KDS, generar o cobrar ticket y crear o editar reservas.

## Auditoría

Las acciones sensibles se registran como logs de auditoría: cambios de usuarios, reservas, carta, caja, comandas y mesas. Esto permite reconstruir incidencias del servicio.

[← Volver al índice del capítulo](README.md)
