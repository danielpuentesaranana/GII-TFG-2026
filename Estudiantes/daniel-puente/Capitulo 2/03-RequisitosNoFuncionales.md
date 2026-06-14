# 2.3 Requisitos no funcionales

## Seguridad

| ID | Requisito no funcional |
|---|---|
| RNF01 | El sistema debe estar preparado para utilizar HTTPS en despliegues de producción. |
| RNF02 | Los endpoints protegidos de la API REST deben validar el token JWT y el rol del usuario antes de ejecutar operaciones restringidas. |
| RNF03 | La información de alérgenos registrada en una línea de comanda debe mantenerse controlada para reforzar la seguridad alimentaria y la trazabilidad. |
| RNF04 | Los tokens de actualización deben almacenarse de forma segura para reducir riesgos de uso indebido de sesión. |

## Disponibilidad y rendimiento

| ID | Requisito no funcional |
|---|---|
| RNF05 | El sistema debe mantener operativas las acciones principales ante fallos puntuales de red mediante una arquitectura local-first. |
| RNF06 | El sistema debe sincronizar automáticamente las operaciones pendientes cuando se recupere la conexión, sin intervención manual del usuario. |
| RNF07 | El tiempo de respuesta de la API debe mantenerse en valores adecuados para un servicio de restauración en condiciones normales de uso. |
| RNF08 | Las notificaciones en tiempo real, como platos listos o reservas próximas, deben entregarse con la menor latencia posible dentro de la red local. |

## Usabilidad

| ID | Requisito no funcional |
|---|---|
| RNF09 | La interfaz debe ser instalable en dispositivos compatibles con navegador moderno mediante Web App Manifest. |
| RNF10 | La interfaz debe adaptarse a pantallas táctiles y permitir el uso durante el servicio sin depender de teclado físico. |
| RNF11 | Cada rol debe acceder a las vistas correspondientes a sus responsabilidades después del inicio de sesión. |

## Mantenibilidad y escalabilidad

| ID | Requisito no funcional |
|---|---|
| RNF12 | El backend debe seguir una arquitectura modular por dominio, separando rutas, controladores, servicios, modelos, validadores y middleware. |
| RNF13 | El sistema debe poder ejecutarse sobre la infraestructura existente del restaurante, utilizando un PC local, la red WiFi y dispositivos con navegador. |

## Restricciones técnicas

| ID | Requisito no funcional |
|---|---|
| RNF14 | El frontend del sistema debe desarrollarse como una aplicación web progresiva, utilizando React, Vite y TypeScript, para permitir una interfaz modular, mantenible y accesible desde dispositivos con navegador. |
| RNF15 | El backend debe implementarse sobre Node.js y Express, separando rutas, controladores, servicios, modelos, middleware y validadores para mantener una arquitectura modular y facilitar la evolución del sistema. |
| RNF16 | La persistencia de datos debe realizarse mediante MongoDB Atlas y Mongoose, permitiendo trabajar con una base de datos documental flexible, validaciones de esquema y referencias entre entidades del dominio. |
| RNF17 | La aplicación debe poder ejecutarse sobre el PC local ya disponible en la empresa, actuando como servidor dentro de la red local del restaurante y evitando la compra inicial de un servidor dedicado para el MVP. |
| RNF18 | El sistema debe ser accesible desde tablets o equipos con navegador moderno conectados a la red del restaurante, sin requerir la instalación de una aplicación nativa. |
| RNF19 | La solución debe incorporar capacidades PWA, Service Worker e IndexedDB para permitir soporte offline, almacenamiento local de operaciones pendientes y sincronización posterior con el backend. |

[← Volver al índice del capítulo](README.md)
