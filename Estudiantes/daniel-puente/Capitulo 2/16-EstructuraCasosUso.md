# 2.16 Estructuración de casos de uso

El objetivo de este paso es estructurar el modelo de casos de uso del sistema de gestión del Restaurante La Unión, identificando funcionalidades compartidas mediante relaciones de inclusión y comportamientos opcionales o alternativos mediante relaciones de extensión. Con ello se reduce la redundancia entre casos de uso y se mejora la trazabilidad del modelo.

## Relaciones `include` y `extend`

| Include | Extend |
|---|---|
| Representa funcionalidad obligatoria y común entre casos de uso. | Representa comportamiento opcional, alternativo o excepcional. |
| Reduce la duplicación de especificaciones. | Permite modelar flujos alternativos y casos negativos. |
| Se ejecuta siempre que se ejecuta el caso de uso principal. | Solo se ejecuta si aparece una condición concreta. |

## Relación `<<include>>`

La relación `<<include>>` se utiliza cuando un caso de uso necesita incorporar siempre otro comportamiento para completarse correctamente.

| Caso de uso principal | Caso de uso incluido | Explicación |
|---|---|---|
| **CU-06 Tomar comanda** | CU-01 Iniciar sesión | Para tomar una comanda, el usuario debe estar autenticado en el sistema. |
| **CU-08 Editar comanda** | CU-07 Ver comanda de una mesa | Para editar una comanda, es necesario consultarla previamente. |
| **CU-14 Enviar ticket a caja** | CU-07 Ver comanda de una mesa | El ticket se genera a partir de la comanda activa de la mesa. |
| **CU-05 Cerrar mesa** | CU-17 Cobrar ticket en caja | Para cerrar la mesa, debe existir previamente un ticket cobrado. |
| **CU-19 Gestionar reservas** | CU-18 Ver listado de reservas del día | La gestión de reservas requiere consultar el listado previo. |
| **CU-20 Asignar mesa a reserva** | CU-19 Gestionar reservas | Asignar una mesa forma parte del flujo de gestión de reservas. |
| **CU-11 Marcar comanda en preparación** | CU-10 Ver comandas pendientes en KDS | El Cocinero debe ver las líneas pendientes antes de marcarlas en preparación. |
| **CU-12 Marcar plato como listo** | CU-11 Marcar comanda en preparación | Un plato solo puede marcarse como listo si previamente está en preparación. |
| **CU-09 Solicitar segundos platos / postres** | CU-12 Marcar plato como listo | Solo se pueden solicitar nuevos pases cuando el pase anterior está listo o servido. |


## Relación `<<extend>>`

La relación `<<extend>>` se utiliza cuando un comportamiento adicional aparece únicamente bajo ciertas condiciones, normalmente como flujo alternativo o caso negativo.

| Caso de uso principal | Caso de uso extendido | Explicación |
|---|---|---|
| **CU-01 Iniciar sesión** | CU-N01 Denegar acceso por credenciales incorrectas | El acceso se deniega si las credenciales introducidas son incorrectas. |
| **CU-01 Iniciar sesión** | CU-N10 Bloquear cuenta por intentos fallidos | Tras varios intentos fallidos, el sistema bloquea temporalmente la cuenta. |
| **CU-06 Tomar comanda** | CU-N03 Rechazar comanda por alérgenos incompletos | El registro se bloquea si alguna línea tiene alérgenos sin confirmar. |
| **CU-06 Tomar comanda** | CU-N07 Restringir pedido por falta de stock | El sistema impide añadir platos marcados como agotados o no disponibles. |
| **CU-08 Editar comanda** | CU-N05 Bloquear edición de línea en preparación | La edición se bloquea si la línea ya está en preparación, lista o servida. |
| **CU-14 Enviar ticket a caja** | CU-N06 Rechazar ticket por mesa ya cobrada | El envío se rechaza si el ticket ya está cobrado o la mesa ya ha sido cerrada. |
| **CU-19 Gestionar reservas** | CU-N04 Rechazar reserva por conflicto de horario | La reserva se rechaza si existe otra activa en el mismo tramo horario. |
| **CU-21 Gestionar usuarios y roles** | CU-N02 Bloquear operación por rol insuficiente | La operación se bloquea si el usuario no tiene rol de Administrador. |


[← Volver al índice del capítulo](README.md)
