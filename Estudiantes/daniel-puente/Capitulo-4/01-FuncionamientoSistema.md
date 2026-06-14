# 4.1 Descripción del funcionamiento del sistema

El MVP implementado funciona como una PWA conectada a un backend Express y a una base de datos MongoDB. Las vistas no trabajan de forma independiente: comparten el estado de mesas, comandas, líneas, tickets y usuarios. Por ello, una acción realizada en sala puede reflejarse después en cocina, caja o auditoría.

| Vista principal | Funcionamiento implementado |
|---|---|
| `LoginView` | El usuario inicia sesión y la aplicación carga el menú según su rol. |
| `SalaView` / vista de mesas | Se muestran las mesas por zona junto con su estado operativo: libre, ocupada, cocina trabajando, ticket en caja o pendiente de cierre. |
| `ComandaView` | El camarero abre la mesa y registra platos de carta o menú con cantidad, observaciones y alérgenos. |
| `ComandaView` / backend | Si la comanda es de menú, primeros, segundos y postres se gestionan por pases para no saturar cocina. |
| `KdsView` | Cocina cambia el estado de las líneas: `PENDIENTE`, `EN_PREPARACION`, `LISTO` y `SERVIDO`. |
| `ComandaView` | El camarero puede enviar el ticket a caja cuando la comanda está preparada para cobro. |
| `CajaView` | El administrador cobra el ticket y, una vez cobrado, puede cerrar la mesa para liberarla. |
| `SyncView` | Si hay un corte de red, las operaciones se guardan en IndexedDB y se sincronizan al recuperar conexión. |

De esta forma, el sistema cubre el flujo completo del servicio: mesa, comanda, cocina, caja y cierre, manteniendo trazabilidad y continuidad operativa.

[← Volver al índice del capítulo](README.md)
