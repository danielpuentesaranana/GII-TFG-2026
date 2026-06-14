# 4.5 Namespaces y capas lógicas

## Frontend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `src/api` | Centraliza llamadas REST y token JWT. |
| `src/components` | Componentes de interfaz y layout común. |
| `src/components/views` | Vistas de negocio: mesas, comanda, KDS, caja, reservas, carta, usuarios, auditoría y offline. |
| `src/offline` | Persistencia local con IndexedDB para operaciones pendientes. |
| `src/socket` | Conexión en tiempo real con el backend. |
| `src/types` | Tipos compartidos por las vistas. |

## Backend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `controllers` | Reciben peticiones y delegan en servicios. |
| `services` | Contienen reglas de negocio y validaciones funcionales. |
| `models` | Definen entidades persistentes en MongoDB. |
| `routes` | Declaran endpoints REST. |
| `middleware` | Autenticación, autorización por rol y tratamiento de errores. |
| `sockets` | Eventos en tiempo real para sala, KDS, caja y reservas. |
| `scripts` | Automatización de datos iniciales y pruebas de flujo. |

## Namespaces REST principales

| Namespace API | Uso |
|---|---|
| `/api/auth` | Login, perfil y logout. |
| `/api/mesas` | Mesas, zonas, apertura, cierre y comanda activa. |
| `/api/comandas` | Creación de comandas, líneas, edición, cancelación y pases. |
| `/api/kds` | Consulta y cambio de estado de líneas en cocina. |
| `/api/tickets` | Generación, listado y cobro de tickets. |
| `/api/reservas` | Gestión de reservas y asignación de mesa. |
| `/api/carta` | Platos y tarifas de menú. |
| `/api/usuarios` | Usuarios y roles. |
| `/api/auditoria` | Trazabilidad de acciones. |
| `/api/sync` | Registro y ejecución de operaciones offline. |


[← Volver al índice del capítulo](README.md)
