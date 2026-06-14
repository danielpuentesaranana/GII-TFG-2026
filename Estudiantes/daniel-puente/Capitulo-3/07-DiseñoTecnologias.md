# 3.7 Diseño y tecnologías utilizadas

La selección tecnológica se ha realizado buscando equilibrio entre coste, mantenibilidad y adecuación al contexto real del restaurante. La solución debía poder ejecutarse sobre dispositivos con navegador, aprovechar un PC local existente y mantener sincronización con MongoDB Atlas.

| Categoría | Tecnología | Propósito | Justificación |
|---|---|---|---|
| Frontend | React + Vite | Biblioteca de interfaz y empaquetado | Desarrollo de una SPA modular con tiempos de carga optimizados. |
| Frontend | TypeScript | Tipado estático | Mejora la robustez, mantenibilidad y reducción de errores en desarrollo. |
| Frontend | Material UI (MUI) | Diseño de interfaz | Implementación de componentes visuales consistentes. |
| Frontend | PWA, Service Worker e IndexedDB | Soporte offline | Estrategia local-first para mantener operativa ante fallos de red. |
| Backend | Node.js + Express | Entorno y framework de servidor | Ejecución de lógica de servidor bajo un modelo asíncrono y sencillo de desplegar. |
| Backend | Socket.IO | Tiempo real | Comunicación bidireccional entre sala, cocina y caja. |
| Backend | JWT + bcrypt | Seguridad | Gestión de sesiones y hash de contraseñas. |
| Persistencia | MongoDB Atlas | Base de datos | Almacenamiento documental flexible en la nube. |
| Persistencia | Mongoose | ODM | Modelado de datos con validaciones y gestión de referencias. |
| Herramientas | Git + GitHub | Control de versiones | Gestión del código fuente, trazabilidad de cambios y respaldo del proyecto. |
| Herramientas | PlantUML | Documentación | Generación de diagramas técnicos. |
| Herramientas | Postman | Pruebas de API | Validación técnica de endpoints, cabeceras y respuestas del servidor. |

[← Volver al índice del capítulo](README.md)
