# PRESENTACIÓN TFG

## Digitalización de la Gestión Interna del Restaurante La Unión
--------------
### Introducción 

El restaurante La Unión es un local familiar ubicado en Puente Viesgo y se dedica principalmente al sector de la hostería. Este establecimiento ofrece  menú del dia y carta durante el servicio de comidas, mientras durante el servicio nocturno solo ofrece carta.

### Problema 

La gestión actual del restaurante se basa en procesos analógicos:

- Comandas en libretas de camarero.
- Reservas en libro físico.
- Comunicación sala-cocina mediante telefonillo.
- Avisos verbales como "sube nota" o "segundos de la mesa 5".

Aunque este modelo ha funcionado durante años, genera limitaciones en servicios con alta carga de trabajo: pérdida de información, errores de comunicación, menor eficiencia y poca trazabilidad.

![FlujoAnterior](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo-1/imagenes/FlujoAnterior.svg)

### Solución propuesta

Se propone una PWA interna para digitalizar la gestión del Restaurante La Unión, organizada por roles:

| Rol | Funciones principales |
|---|---|
| Camarero | Gestionar mesas, tomar comandas, registrar alérgenos, solicitar pases y enviar tickets a caja. |
| Cocinero | Trabajar desde el KDS, ver platos pendientes/en preparación/listos y avisar a sala. |
| Administrador | Gestionar reservas, usuarios, carta, caja, auditoría, cobros y cierre de mesas. |

La aplicación incorpora notificaciones en tiempo real y una arquitectura local-first, permitiendo guardar operaciones pendientes en el dispositivo y sincronizarlas cuando vuelva la conexión.

#### ¿Por qué esta solución y no una comercial?

| Aspecto | Soluciones comerciales | La Unión App |
|---|---|---|
| Enfoque | Pensadas para restaurantes estándar. | Diseñada para la operativa real del Restaurante La Unión. |
| Coste | Pueden requerir licencias, hardware o personalización. | Aprovecha tablets, navegador y PC local existente. |
| Adaptación | Las reglas específicas suelen depender del proveedor. | Desarrollo a medida para zonas, mesas, menú y reservas. |
| Conectividad | Muchas dependen parcial o totalmente de la nube. | Enfoque local-first con IndexedDB para cortes puntuales. |
| Cocina | KDS disponible según producto o configuración. | KDS adaptado a pases de menú y avisos en tiempo real. |
| Menú del día | Difícil de ajustar si el menú es variable. | Reglas propias para carta, menú, segundos y postres. |
| Reservas | Gestión general de reservas. | Aviso de reserva próxima sobre mesa ocupada. |
| Trazabilidad | Más completa en soluciones empresariales costosas. | Auditoría de comandas, tickets, caja y usuarios. |

En resumen, no se eligió una solución comercial porque habría supuesto adaptar el restaurante al software. En este TFG se plantea lo contrario: adaptar el software a la forma real de trabajar del Restaurante La Unión.

### Objetivos

#### Objetivo general

Desarrollar una PWA local-first para digitalizar la operativa del Restaurante La Unión, cubriendo comandas, cocina, reservas, mesas, caja y auditoría, con roles diferenciados y funcionamiento ante cortes puntuales de red.

#### Objetivos específicos

- **Requisitos:** recoger y priorizar requisitos mediante entrevistas, casos de uso validados y MoSCoW.
- **Análisis y diseño:** definir el modelo de datos, la API REST, los eventos WebSocket, la arquitectura y los prototipos por rol.
- **MVP:** implementar los casos Must-have con autenticación JWT, arquitectura local-first y notificaciones en tiempo real.

### Modelo de Dominio
![Dominio](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/modeloDominio.png)
### Diagrama de Clases
![Clases](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaClases.svg)
### Diagrama de Objetos
![Objetos](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaObjetos.svg)
### Diagrama de Estados
![Estados](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaEstadosMesa.svg)

### Actores

![Actores](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/Actores.svg)

| Actor | Descripción |
|---|---|
| Camarero | Usuario encargado de gestionar mesas, tomar comandas, consultar estados, solicitar pases y enviar tickets a caja. |
| Cocinero | Usuario encargado de trabajar sobre el KDS, consultar líneas pendientes, marcar platos en preparación, listos o servidos, y revisar observaciones y alérgenos. |
| Administrador | Usuario con permisos de gestión sobre caja, reservas, usuarios, carta, auditoría y cierre de mesas. |
| Sistema de notificaciones | Componente interno encargado de emitir avisos automáticos, como platos listos, reservas próximas o actualización del KDS. No representa un perfil humano de acceso. |

### Casos de uso

| ID | Caso de uso | Camarero | Administrador | Cocinero | Sist. Notif. |
|---|---|:---:|:---:|:---:|:---:|
| CU-01 | Iniciar sesión | ✅ | ✅ | ✅ | |
| CU-02 | Cerrar sesión | ✅ | ✅ | ✅ | |
| CU-03 | Ver estado de mesas por zona | ✅ | ✅ | | |
| CU-04 | Abrir mesa | ✅ | ✅ | | |
| CU-05 | Cerrar mesa | | ✅ | | |
| CU-06 | Tomar comanda | ✅ | ✅ | | |
| CU-07 | Ver comanda de una mesa | ✅ | ✅ | | |
| CU-08 | Editar comanda | ✅ | ✅ | | |
| CU-09 | Solicitar segundos platos / postres | ✅ | ✅ | | ✅ |
| CU-10 | Ver comandas pendientes en KDS | | | ✅ | |
| CU-11 | Marcar comanda en preparación | | | ✅ | |
| CU-12 | Marcar plato como listo | | | ✅ | ✅ |
| CU-13 | Ver alérgenos y observaciones | | | ✅ | |
| CU-14 | Enviar ticket a caja | ✅ | ✅ | | |
| CU-15 | Editar ticket | ✅ | ✅ | | |
| CU-16 | Reclamar ticket enviado | ✅ | ✅ | | |
| CU-17 | Cobrar ticket en caja | | ✅ | | |
| CU-18 | Ver listado de reservas del día | | ✅ | | |
| CU-19 | Gestionar reservas (crear, editar, cancelar) | | ✅ | | |
| CU-20 | Asignar mesa a reserva | | ✅ | | |
| CU-21 | Gestionar usuarios y roles | | ✅ | | |
| CU-22 | Configurar plano de sala (zonas y mesas) | | ✅ | | |
| CU-23 | Unir  mesas | | ✅ | | |
| CU-24 | Consultar log de auditoría | | ✅ | | |
| CU-25 | Gestionar carta (añadir, editar, eliminar platos) | | ✅ | | |
| CU-26 | Configurar menú del día | | ✅ | | |
| CU-27 | Enviar notificaciones automáticas (plato listo, reservas próximas, actualizar KDS) | | | | ✅ |
| CU-28 | Marcar plato como agotado / disponible | | ✅ | ✅ | |

| ID | Caso de uso | Camarero | Administrador | Cocinero | Sist. Notif. |
|---|---|:---:|:---:|:---:|:---:|
| CU-N01 | Denegar acceso por credenciales incorrectas | ✅ | ✅ | ✅ | |
| CU-N02 | Bloquear operación por rol insuficiente | ✅ | | ✅ | |
| CU-N03 | Rechazar comanda por alérgenos incompletos | ✅ | ✅ | | |
| CU-N04 | Rechazar reserva por conflicto de horario | | ✅ | | |
| CU-N05 | Bloquear edición de línea en preparación | ✅ | ✅ | | |
| CU-N06 | Rechazar ticket por mesa ya cobrada | ✅ | | | |
| CU-N07 | Restringir pedido por falta de stock | ✅ | | | ✅ |
| CU-N08 | Anular ticket ya cobrado por error de cobro | | ✅ | | |
| CU-N09 | Marcar reserva como no presentado | | ✅ | | |
| CU-N10 | Desbloquear cuenta por intentos fallidos | | ✅ | | |

### Casos de uso por actor

![Camarero](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Camarero.svg)
![Admin](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Admin.svg)
![Cocinero](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Cocinero.svg)
![Notis](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-NotificacionesAuto.svg)
![Negativos](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Negativos.svg)

### Casos de uso más relevantes

| ID | Nombre | Priorización |
|---|---|---|
| CU-04 | Abrir mesa | 🔴 Must |
| CU-06 | Tomar comanda | 🔴 Must |
| CU-08 | Editar comanda | 🔴 Must |
| CU-12 | Marcar plato como listo | 🔴 Must |
| CU-14 | Enviar ticket a caja | 🔴 Must |
| CU-17 | Cobrar ticket en caja | 🔴 Must |
| CU-05 | Cerrar mesa | 🔴 Must |

![Flujo de casos de uso seleccionados](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/flujo-casos-uso-seleccionados.png)

### Diagrama de Contexto

![DiagramaContexto](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaContextoCasosUsoPrincipales.svg)

### Casos seleccionados

### Casos de uso seleccionados

| Caso de uso | Detalle | Actividad | Secuencia | MVC | Boceto / Wireframe |
|---|---|---|---|---|---|
| **CU-06 Tomar comanda** | [Detalle](Capitulo%202/11-DetalleCasosUso.md#cu-06---tomar-comanda) | [Actividad](Capitulo%202/13-DiagramasActividad.md#cu-06-tomar-comanda) | [Secuencia](Capitulo%202/08-DiagramasSecuencia.md#cu-06-tomar-comanda) | [MVC](Capitulo-3/04-CasosUsoSeleccionadosMVC.md#cu-06-tomar-comanda) | [Wireframe](Capitulo-3/13-Wireframes.md#tomar-comanda)| 
| **CU-14 Enviar ticket a caja** | [Detalle](Capitulo%202/11-DetalleCasosUso.md#cu-14---enviar-ticket-a-caja) | [Actividad](Capitulo%202/13-DiagramasActividad.md#cu-14-enviar-ticket-a-caja) | [Secuencia](Capitulo%202/08-DiagramasSecuencia.md#cu-14-enviar-ticket-a-caja) | [MVC](Capitulo-3/04-CasosUsoSeleccionadosMVC.md#cu-14-enviar-ticket-a-caja) | [Wireframe caja](Capitulo-3/13-Wireframes.md#ticket-y-caja) |


### Tecnologías utilizadas

La selección tecnológica busca una solución de bajo coste, mantenible y adecuada al contexto real del restaurante: uso desde navegador, aprovechamiento de un PC local y sincronización con MongoDB Atlas.

| Área | Tecnologías | Motivo principal |
|---|---|---|
| Frontend | React, Vite, TypeScript y MUI | Crear una PWA modular, mantenible y usable desde tablets o PC. |
| Soporte offline | Service Worker e IndexedDB | Mantener operaciones pendientes ante cortes puntuales de red. |
| Backend | Node.js, Express y Socket.IO | Gestionar la API REST y la comunicación en tiempo real entre sala, cocina y caja. |
| Seguridad | JWT y bcrypt | Controlar sesiones, roles y contraseñas de forma segura. |
| Persistencia | MongoDB Atlas y Mongoose | Almacenar datos de forma flexible con modelos y validaciones. |
| Herramientas | Git, GitHub, PlantUML y Postman | Control de versiones, documentación técnica y pruebas de API. |

### Analisis de Paquetes

![DiagramaPaquetes](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo-3/imagenes/analisisPaquetes.png)

El sistema se organiza en dos bloques principales: **frontend PWA** y **backend Node/Express**, conectados mediante API REST y eventos WebSocket.

| Bloque | Responsabilidad |
|---|---|
| Frontend | Contiene las vistas de sala, comanda, KDS, caja, reservas y administración. También gestiona IndexedDB, Socket.IO y llamadas a la API. |
| Backend | Centraliza rutas, middleware, controladores, servicios, validaciones, sockets y modelos Mongoose. |
| Base de datos | MongoDB Atlas almacena usuarios, mesas, comandas, tickets, reservas y auditoría. |

Esta separación permite mantener una estructura ordenada: las vistas se encargan de la interfaz, los servicios concentran la lógica de negocio y los modelos gestionan la persistencia.

### Diseño arquitectura 



### Diagrama de Despligue

![DiagramaDespliegue](/GII-TFG-2026/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaDespligue.png)

### Conclusión

El MVP desarrollado demuestra que la solución propuesta es viable para digitalizar la operativa interna del Restaurante La Unión. No se limita a sustituir el papel por pantallas, sino que incorpora reglas propias del negocio: comandas por pases, KDS para cocina, caja separada del cierre de mesa, auditoría y soporte offline.

Los casos de uso principales del flujo sala-cocina-caja quedan cubiertos: abrir mesa, tomar comanda, editarla, marcar platos como listos, enviar ticket a caja, cobrarlo y cerrar la mesa. Además, se cumplen los objetivos planteados inicialmente: requisitos, análisis y diseño, e implementación del MVP.

| Tipo de métrica | Evidencia principal |
|---|---|
| Cuantitativa | Reducción de tiempos en apertura de comanda, registro de platos, consulta del estado de mesas y cálculo del ticket. |
| Cualitativa | Mejor coordinación entre sala, cocina y caja, mayor trazabilidad, menos dependencia del papel y mejor control de alérgenos y observaciones. |

### Futuras mejoras

| Prioridad | Línea futura | Objetivo |
|---|---|---|
| Alta | Panel de analítica de caja y ventas. | Conocer ventas por día, platos más vendidos y ticket medio. |
| Alta | Plan superior de MongoDB Atlas. | Mejorar la capacidad y garantía de disponibilidad. |
| Alta | Impresión de tickets. | Integración con la operativa de caja. |
| Alta | Mejorar gestión de conflictos offline. | Resolver casos donde dos dispositivos cambien el mismo dato. |
| Media | Temporizadores en KDS. | Detectar retrasos de cocina por mesa o plato. |
| Media | Sugerencia automática de mesas para reservas. | Mejorar rotación y ocupación del restaurante. |
| Baja | Estadísticas por camarero. | Analizar carga de trabajo y rendimiento. |
