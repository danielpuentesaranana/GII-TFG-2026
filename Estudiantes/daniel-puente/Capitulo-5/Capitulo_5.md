# Capítulo 5 - Evaluación y conclusiones

## 5.1 Conclusiones

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

## 5.2 Discusión de resultados

El principal resultado del proyecto es que la aplicación consigue adaptar la tecnología a la forma real de trabajar del restaurante. Algunas decisiones técnicas han sido especialmente relevantes:

| Decisión tomada | Implicación |
|---|---|
| PWA en lugar de app nativa | Permite usar tablets o equipos con navegador sin depender de tiendas de aplicaciones. |
| Modelo local-first con cola offline | Reduce el riesgo de parar el servicio ante cortes puntuales de red. |
| PC local de la empresa | Aprovecha la infraestructura existente y evita comprar un servidor para el MVP. |
| MongoDB Atlas gratuito | Permite validar el sistema sin coste mensual inicial. |
| KDS por estados | Reduce la comunicación verbal entre sala y cocina. |
| Pases de menú | Evita que cocina reciba primeros, segundos y postres al mismo tiempo. |
| Auditoría | Permite reconstruir incidencias sobre comandas, tickets, reservas y usuarios. |

Durante la prueba de funcionamiento del MVP se observaron mejoras respecto al proceso manual previo. Las métricas siguientes recogen resultados aproximados obtenidos al comparar el flujo tradicional con el uso de la aplicación en los casos de uso principales.

## 5.3 Métricas cuantitativas

| Indicador | Situación anterior | Con la aplicación |
|---|---|---|
| Apertura de comanda | 20-30 segundos localizando mesa y preparando comanda. | 5-8 segundos desde la vista de mesas. |
| Registro de un plato | 10-20 segundos en comanda a papel. | 5-10 segundos desde la vista de comanda. |
| Localización del estado de una mesa | 20-30 segundos revisando el papel o preguntando. | 3-5 segundos desde la vista de mesas. |
| Cálculo total del ticket | 30 segundos en cálculo manual revisando comanda. | 3-5 segundos desde caja. |
| Consumo de papel y bolígrafos | Uso diario de comanderos y bolígrafos. | Comandas digitales. |
| Coste de implantación inicial | Dependencia de TPV comercial, licencias o hardware específico. | Uso de PC local existente y plan gratuito de MongoDB Atlas. |

Estas métricas no proceden de una implantación masiva durante meses, sino de la prueba funcional del MVP y de la comparación con el procedimiento habitual del restaurante. Por ello, sirven como primera evidencia de mejora y como base para una medición más prolongada cuando el sistema se utilice de forma continuada.

## 5.4 Métricas cualitativas

| Área | Mejora observada |
|---|---|
| Sala | Los camareros pueden ver el estado de cada mesa sin depender de notas o comunicación verbal. |
| Cocina | El KDS ordena el trabajo por estados y evita enviar platos de menú antes de tiempo. |
| Caja | El cobro queda separado del cierre de mesa, reduciendo errores administrativos. |
| Administración | Usuarios, carta, reservas y auditoría quedan centralizados. |
| Seguridad alimentaria | Los alérgenos y observaciones quedan visibles en comanda y cocina. |
| Cliente final | Menos esperas, menos errores de platos y mejor coordinación del servicio. |
| Negocio | Menor dependencia del papel y mayor control de incidencias. |

Aunque el ahorro en papel, comanderos y bolígrafos no representa el mayor coste del negocio, sí supone una mejora continua: se reduce material, se evitan comandas ilegibles o extraviadas y se disminuye la dependencia del soporte físico durante los servicios de mayor carga.

## 5.5 Recomendaciones

A partir de los resultados obtenidos, se recomienda abordar la implantación de forma gradual. El sistema ya cubre el flujo principal, pero conviene mejorarlo y pulirlo antes de utilizarlo de forma completa en todos los turnos.

| Recomendación | Motivo |
|---|---|
| Formación a camareros, cocina y administración. | Reduce el rechazo por parte del personal y errores por uso incorrecto. |
| Mantener el PC local como servidor del negocio. | Aprovecha un recurso ya disponible y mantiene baja la inversión inicial. |
| Valorar un plan de pago de MongoDB Atlas a corto plazo. | Mejoraría la capacidad y las garantías de disponibilidad. |

## 5.6 Futuras líneas de actuación

El proyecto se ha desarrollado de forma metodológica, separando requisitos, análisis, diseño e implementación. Esto facilita continuar el trabajo sin rehacer la base del sistema, ya que las funcionalidades futuras pueden incorporarse sobre una arquitectura modular.

| Prioridad | Línea futura | Objetivo |
|---|---|---|
| Alta | Panel de analítica de caja y ventas. | Conocer ventas por día, platos más vendidos y ticket medio. |
| Alta | Plan superior de MongoDB Atlas. | Mejorar la capacidad y garantía de disponibilidad. |
| Alta | Impresión de tickets. | Integración con la operativa de caja. |
| Alta | Mejorar gestión de conflictos offline. | Resolver casos donde dos dispositivos cambien el mismo dato. |
| Media | Temporizadores en KDS. | Detectar retrasos de cocina por mesa o plato. |
| Media | Sugerencia automática de mesas para reservas. | Mejorar rotación y ocupación del restaurante. |
| Baja | Estadísticas por camarero. | Analizar carga de trabajo y rendimiento. |

## 5.7 Conclusión final

El presente TFG ha permitido desarrollar una solución funcional para un problema real del Restaurante La Unión: la dependencia de procesos manuales para gestionar mesas, comandas, comunicación con cocina, tickets y reservas. La operativa inicial, basada en papel, comunicación verbal y coordinación informal entre trabajadores, había resultado válida durante años, pero presentaba limitaciones evidentes en servicios con alta carga de trabajo, como errores de lectura, pérdida de información, dificultad para conocer el estado de cada mesa y escasa trazabilidad de las acciones realizadas.

La aplicación desarrollada responde a esta necesidad mediante una PWA orientada al uso interno del restaurante. El sistema permite que sala, cocina y caja trabajen sobre la misma información, cada uno desde una vista adaptada a su rol. De este modo, el flujo completo del servicio queda digitalizado desde que se abre una mesa hasta que se cobra el ticket y la mesa vuelve a quedar disponible.

Asimismo, el trabajo realizado confirma el cumplimiento de los objetivos específicos planteados al inicio del proyecto. Se han recopilado y priorizado los requisitos, definido los casos de uso, elaborado los artefactos de análisis y diseño, e implementado un MVP que cubre los casos principales. La solución incorpora autenticación por roles, comunicación en tiempo real mediante Socket.IO, persistencia con MongoDB, soporte offline mediante IndexedDB y una estructura modular que facilita su mantenimiento y evolución.

Desde el punto de vista del negocio, el producto aporta mejoras operativas y económicas. Reduce el uso de papel, blocs y bolígrafos, minimiza el riesgo de comandas ilegibles o extraviadas y permite consultar el estado del servicio de forma más rápida y precisa. Además, se ha planteado una infraestructura realista, basada en un PC local ya disponible en la empresa y en MongoDB Atlas en su plan gratuito durante la fase inicial, con posibilidad de ampliación posterior si el sistema se consolida en producción.

No obstante, el proyecto debe entenderse como una primera versión funcional. Quedan abiertas líneas de mejora como la analítica de ventas, la impresión de tickets, la gestión avanzada de conflictos offline, los temporizadores en cocina o la mejora de reservas mediante sugerencias automáticas de mesa. Estas ampliaciones son viables porque el desarrollo se ha realizado de forma metodológica, separando requisitos, diseño, lógica de negocio y vistas.

En conclusión, este TFG no solo cumple una finalidad académica, sino que propone una herramienta útil y adaptada al contexto de un negocio real. La solución desarrollada demuestra que es posible digitalizar los procesos internos de un restaurante familiar sin recurrir a sistemas comerciales costosos o poco adaptables.
