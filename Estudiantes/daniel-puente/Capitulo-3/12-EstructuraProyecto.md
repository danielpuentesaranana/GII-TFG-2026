# 3.12 Estructura del proyecto

## Backend

El backend utiliza una estructura basada en rutas, controladores, servicios y modelos. Esta separación permite aislar la lógica de negocio de los puntos de entrada de la API REST.

| Carpeta / archivo | Descripción |
|---|---|
| `src/` | Directorio raíz del backend. |
| `config/` | Configuración de entorno, conexión a MongoDB y Socket.IO. |
| `controllers/` | Manejo de peticiones HTTP y gestión de respuestas. |
| `services/` | Lógica de negocio y coordinación con los modelos de datos. |
| `models/` | Definición de esquemas Mongoose. |
| `routes/` | Definición de endpoints de la API REST. |
| `middleware/` | Autenticación, autorización por rol, validación y gestión de errores. |
| `sockets/` | Eventos y manejadores de comunicación en tiempo real. |
| `validators/` | Esquemas de validación de entrada. |
| `scripts/` | Scripts de seed, reset de base de datos y pruebas de endpoints. |
| `app.js` e `index.js` | Configuración del servidor Express y punto de entrada de la aplicación. |

## Frontend

El frontend se ha estructurado para soportar la PWA, la comunicación con el backend, la sincronización offline y la separación de vistas por rol.

| Carpeta / archivo | Descripción |
|---|---|
| `public/` | Contiene `manifest.json`, `sw.js` y recursos públicos de la PWA. |
| `src/` | Código principal desarrollado en React y TypeScript. |
| `api/` | Cliente HTTP centralizado para la API REST. |
| `components/` | Componentes generales de interfaz y estructura. |
| `components/views/` | Vistas principales: sala, comanda, KDS, caja, reservas, carta, usuarios, auditoría y offline. |
| `offline/` | Gestión de IndexedDB y cola de operaciones pendientes. |
| `socket/` | Cliente Socket.IO para eventos en tiempo real. |
| `types/` | Tipos de dominio compartidos por las vistas. |
| `assets/` | Recursos estáticos. |
| `App.tsx` y `main.tsx` | Composición principal de la aplicación y punto de entrada de React. |

Esta estructura facilita explicar el proyecto en defensa porque permite seguir una petición desde la vista hasta la base de datos: vista, cliente API, ruta, controlador, servicio, modelo y persistencia.

[← Volver al índice del capítulo](README.md)
