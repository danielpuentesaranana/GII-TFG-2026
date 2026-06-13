# 5.7 Conclusión final

El presente TFG ha permitido desarrollar una solución funcional para un problema real del Restaurante La Unión: la dependencia de procesos manuales para gestionar mesas, comandas, comunicación con cocina, tickets y reservas. La operativa inicial, basada en papel, comunicación verbal y coordinación informal entre trabajadores, había resultado válida durante años, pero presentaba limitaciones evidentes en servicios con alta carga de trabajo, como errores de lectura, pérdida de información, dificultad para conocer el estado de cada mesa y escasa trazabilidad de las acciones realizadas.

La aplicación desarrollada responde a esta necesidad mediante una PWA orientada al uso interno del restaurante. El sistema permite que sala, cocina y caja trabajen sobre la misma información, cada uno desde una vista adaptada a su rol. De este modo, el flujo completo del servicio queda digitalizado desde que se abre una mesa hasta que se cobra el ticket y la mesa vuelve a quedar disponible.

Asimismo, el trabajo realizado confirma el cumplimiento de los objetivos específicos planteados al inicio del proyecto. Se han recopilado y priorizado los requisitos, definido los casos de uso, elaborado los artefactos de análisis y diseño, e implementado un MVP que cubre los casos principales. La solución incorpora autenticación por roles, comunicación en tiempo real mediante Socket.IO, persistencia con MongoDB, soporte offline mediante IndexedDB y una estructura modular que facilita su mantenimiento y evolución.

Desde el punto de vista del negocio, el producto aporta mejoras operativas y económicas. Reduce el uso de papel, blocs y bolígrafos, minimiza el riesgo de comandas ilegibles o extraviadas y permite consultar el estado del servicio de forma más rápida y precisa. Además, se ha planteado una infraestructura realista, basada en un PC local ya disponible en la empresa y en MongoDB Atlas en su plan gratuito durante la fase inicial, con posibilidad de ampliación posterior si el sistema se consolida en producción.

No obstante, el proyecto debe entenderse como una primera versión funcional. Quedan abiertas líneas de mejora como la analítica de ventas, la impresión de tickets, la gestión avanzada de conflictos offline, los temporizadores en cocina o la mejora de reservas mediante sugerencias automáticas de mesa. Estas ampliaciones son viables porque el desarrollo se ha realizado de forma metodológica, separando requisitos, diseño, lógica de negocio y vistas.

En conclusión, este TFG no solo cumple una finalidad académica, sino que propone una herramienta útil y adaptada al contexto de un negocio real. La solución desarrollada demuestra que es posible digitalizar los procesos internos de un restaurante familiar sin recurrir a sistemas comerciales costosos o poco adaptables.

[← Volver al índice del capítulo](README.md)
