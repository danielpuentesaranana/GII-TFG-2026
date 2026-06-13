# 5.1 Conclusiones

El desarrollo del TFG ha permitido comprobar que la solución propuesta en los capítulos iniciales es viable para digitalizar la operativa interna del Restaurante La Unión. El resultado no se limita a trasladar el papel a una pantalla, sino que incorpora reglas de negocio propias del restaurante: control de mesas, comandas por pases, KDS de cocina, caja separada del cierre de mesa, auditoría y soporte offline.

Los casos de uso seleccionados para el desarrollo quedan cubiertos por el MVP:

| Caso de uso | Evidencia de cumplimiento |
|---|---|
| Abrir mesa | La mesa queda asociada a una comanda activa y pasa a estado ocupada. |
| Tomar comanda | El camarero registra platos, cantidades, observaciones y alérgenos. |
| Editar comanda | Solo se modifican líneas pendientes, evitando cambios sobre platos ya trabajados por cocina. |
| Marcar plato como listo | Cocina cambia el estado desde KDS y sala recibe el estado actualizado. |
| Enviar ticket a caja | El sistema genera el ticket desde la comanda activa. |
| Cobrar ticket en caja | El administrador registra el cobro y evita dobles cobros. |
| Cerrar mesa | La mesa solo se libera cuando existe un ticket cobrado. |

La relación entre los objetivos específicos planteados al inicio del trabajo y el resultado obtenido queda resumida en la siguiente tabla:

| Objetivo específico | Requisitos | Resultado |
|---|---|---|
| Objetivo I: Requisitos | Catálogo de casos de uso, requisitos funcionales/no funcionales y priorización MoSCoW. | Cumplido. |
| Objetivo II: Análisis y diseño | Modelo de datos, API REST, eventos WebSocket, arquitectura y prototipos por rol. | Cumplido. |
| Objetivo III: MVP | Implementación del flujo sala-cocina-caja, JWT, WebSocket, MongoDB e IndexedDB. | Cumplido. |

[← Volver al índice del capítulo](README.md)
