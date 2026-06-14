# 4.4 Estructura de directorios

La solución separa frontend y backend para mantener responsabilidades claras. El backend concentra API REST, modelos, validaciones, servicios y eventos en tiempo real. El frontend agrupa las vistas, el cliente HTTP, la conexión Socket.IO y el soporte offline.

```text
Restaurante_La_Union/
  backend/
    src/
      config/          Configuración de entorno, MongoDB y Socket.IO
      controllers/     Entrada HTTP de cada recurso
      middleware/      Autenticación, roles, validación y errores
      models/          Modelos Mongoose
      routes/          Rutas REST agrupadas por recurso
      scripts/         Seed, reset de base de datos y pruebas de endpoints
      services/        Lógica de negocio
      sockets/         Conexión y eventos en tiempo real
      validators/      Esquemas de validación
  frontend/
    src/
      api/             Cliente HTTP centralizado
      components/      Layout, login y vistas
      components/views Vistas funcionales del sistema
      offline/         IndexedDB y cola local
      socket/          Cliente Socket.IO
      types/           Tipos de dominio TypeScript
```


[← Volver al índice del capítulo](README.md)
