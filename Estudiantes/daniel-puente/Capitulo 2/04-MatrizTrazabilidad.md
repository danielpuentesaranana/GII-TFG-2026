# 2.4 Matriz de trazabilidad entre casos de uso y requisitos

La siguiente matriz relaciona los requisitos funcionales y no funcionales más relevantes con los casos de uso que los materializan dentro del sistema. Su finalidad es comprobar que los requisitos definidos durante el análisis tienen correspondencia directa con funcionalidades implementadas o verificables en el MVP.

| Requisito | Caso de uso relacionado | Evidencia en el sistema |
|---|---|---|
| RF01. Inicio de sesión con JWT | CU-01 | Autenticación mediante email y contraseña, generación de token JWT y acceso según rol. |
| RF02. Control de acceso por rol | CU-01, CU-N02 | Separación de permisos entre Camarero, Cocinero y Administrador. |
| RF03. Bloqueo por intentos fallidos | CU-N10 | Bloqueo temporal de la cuenta tras varios accesos incorrectos. |
| RF04. Gestión de usuarios | CU-21 | Alta, edición, desbloqueo y eliminación de usuarios desde administración. |
| RF05. Visualización del estado de mesas | CU-03 | Plano de mesas agrupadas por zona con estados operativos. |
| RF06. Apertura y cierre de mesas | CU-04, CU-05 | Apertura de mesa con comanda activa y cierre tras ticket cobrado. |
| RF08. Toma de comanda digital | CU-06 | Registro de platos, cantidades, observaciones, alérgenos y modalidad de carta o menú. |
| RF09. Confirmación de alérgenos y observaciones | CU-06, CU-13, CU-N03 | Validación de alérgenos confirmados y observaciones antes de crear la línea. |
| RF10. Operaciones offline con IndexedDB | CU-06, CU-08, CU-14, CU-17 | Registro local de operaciones pendientes y sincronización al recuperar conexión. |
| RF11. Edición limitada de comanda | CU-08, CU-N05 | Solo se editan o cancelan líneas en estado pendiente. |
| RF12. Gestión de pases de menú | CU-09 | Segundos y postres quedan retenidos hasta que el camarero solicita el pase. |
| RF14. Visualización de líneas en KDS | CU-10 | Cocina consulta líneas pendientes o en preparación desde la vista KDS. |
| RF15. Cambio de estado en cocina | CU-11, CU-12 | Transiciones de línea: pendiente, en preparación, listo y servido. |
| RF16. Notificación de platos listos | CU-12, CU-27 | Aviso en tiempo real a sala cuando cocina marca un plato como listo. |
| RF17. Actualización en tiempo real del KDS | CU-06, CU-08, CU-12 | Refresco del KDS mediante eventos WebSocket al añadir, editar o cambiar líneas. |
| RF18. Envío de ticket a caja | CU-14 | Generación de ticket pendiente con desglose de comanda. |
| RF19. Desglose del ticket | CU-14 | Ticket con líneas, tarifas de menú, total, comanda asociada y fecha. |
| RF21. Cobro exclusivo por Administrador | CU-17, CU-05, CU-N02 | El Administrador cobra el ticket y posteriormente cierra la mesa. |
| RF22. Gestión de reservas | CU-18, CU-19 | Creación, edición, cancelación y consulta de reservas. |
| RF23. Control de reservas incompatibles | CU-19, CU-N04 | Bloqueo de reservas solapadas para una misma mesa. |
| RF24. Aviso de reserva próxima | CU-20, CU-27 | Notificación WebSocket cuando hay una reserva próxima sobre una mesa ocupada. |
| RF25. Gestión de carta | CU-25 | Alta, edición, baja lógica y disponibilidad de platos. |
| RF26. Gestión de tarifas de menú | CU-25 | Configuración de tarifas vigentes del menú del día. |
| RF28. Auditoría de acciones relevantes | CU-04, CU-06, CU-08, CU-12, CU-14, CU-17, CU-05 | Registro de acciones sobre mesas, comandas, cocina, tickets y cierre. |
| RF29. Consulta de auditoría | CU-24 | El Administrador puede revisar el historial de acciones relevantes. |
| RNF01. Preparación para HTTPS | Arquitectura / Despliegue | Comunicación preparada para despliegue seguro en producción. |
| RNF02. Validación JWT y roles | CU-01, CU-N02 | Middleware de autenticación y autorización por rol en endpoints protegidos. |
| RNF03. Seguridad alimentaria y trazabilidad de alérgenos | CU-06, CU-13, CU-N03 | Alérgenos confirmados en comanda y visibles para cocina. |
| RNF04. Protección de sesión | CU-01 | Uso de token de acceso y token de actualización. |
| RNF05. Continuidad ante fallos de red | CU-06, CU-08, CU-14, CU-17 | Arquitectura local-first con operaciones pendientes en IndexedDB. |
| RNF06. Sincronización automática | CU-06, CU-08, CU-14, CU-17 | Sincronización de operaciones pendientes al recuperar conexión. |
| RNF07. Tiempo de respuesta adecuado | CU-04, CU-06, CU-12, CU-14 | API REST organizada por rutas, controladores y servicios. |
| RNF08. Baja latencia en notificaciones | CU-12, CU-27 | Actualizaciones mediante Socket.IO para KDS, sala y caja. |
| RNF09. Instalación como PWA | Login / Navegación | Uso de Web App Manifest y Service Worker. |
| RNF10. Adaptación a pantallas táctiles | CU-03, CU-06, CU-12, CU-17 | Vistas orientadas a tablets para sala, cocina y caja. |
| RNF11. Vistas según rol | CU-01 | Menú y navegación adaptados a Camarero, Cocinero y Administrador. |
| RNF12. Arquitectura modular | Diseño / Código | Separación entre rutas, controladores, servicios, modelos, validadores, sockets y middleware. |
| RNF13. Uso de infraestructura existente | Despliegue | Ejecución sobre PC local de la empresa y acceso desde dispositivos con navegador. |

[← Volver al índice del capítulo](README.md)
