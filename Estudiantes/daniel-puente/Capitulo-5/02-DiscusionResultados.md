# 5.2 Discusión de resultados

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

[← Volver al índice del capítulo](README.md)
