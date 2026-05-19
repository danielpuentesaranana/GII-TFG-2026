# Capitulo 4

## Mapa Navegacion
![Navegacion](/Estudiantes/daniel-puente/Capitulo-4/imagenes/MapaNav.svg)

## Vistas principales
### Login

![Login](/Estudiantes/daniel-puente/Capitulo-4/imagenes/login.png)

### Sala

![Sala](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Sala.png)

### Comanda

![Comanda](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Comanda.png)

### KDS

![KDS](/Estudiantes/daniel-puente/Capitulo-4/imagenes/KDS.png)

### Carta

![Carta](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Carta.png)

### Usuarios

![Usuarios](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Usuarios.png)

### Reservas

![Reservas](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Reservas.png)

### Caja

![Caja](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Caja.png)

### Auditoria

![Auditoria](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Auditoria.png)

### Offline

![Offline](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Offline.png)

## Estructura de directorios

La solucion separa frontend y backend para mantener responsabilidades claras.

```text
Restaurante_La_Union/
  backend/
    src/
      config/          Configuracion de entorno, MongoDB y Socket.IO
      controllers/     Entrada HTTP de cada recurso
      middleware/      Autenticacion, roles, validacion y errores
      models/          Modelos Mongoose
      routes/          Rutas REST agrupadas por recurso
      scripts/         Seed, reset de base de datos y smoke tests
      services/        Logica de negocio
      sockets/         Conexion y eventos en tiempo real
      validators/      Esquemas de validacion
  frontend/
    src/
      api/             Cliente HTTP centralizado
      components/      Layout, login y vistas
      components/views Vistas funcionales del sistema
      offline/         IndexedDB y cola local
      socket/          Cliente Socket.IO
      types/           Tipos de dominio TypeScript
  docs/
    capturas/          Evidencia visual de vistas
```

##  Namespaces y capas logicas

### Frontend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `src/api` | Centraliza llamadas REST y token JWT |
| `src/components` | Componentes de interfaz y layout comun |
| `src/components/views` | Vistas de negocio: sala, comanda, KDS, caja, reservas, carta, usuarios, auditoria y offline |
| `src/offline` | Persistencia local con IndexedDB para operaciones pendientes |
| `src/socket` | Conexion en tiempo real con el backend |
| `src/types` | Tipos compartidos por las vistas |

### Backend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `controllers` | Reciben peticiones y delegan en servicios |
| `services` | Contienen reglas de negocio y validaciones funcionales |
| `models` | Definen entidades persistentes en MongoDB |
| `routes` | Declaran endpoints REST |
| `middleware` | Autenticacion, autorizacion por rol y tratamiento de errores |
| `sockets` | Eventos en tiempo real para sala, KDS, caja y reservas |
| `scripts` | Automatizacion de datos iniciales y pruebas de flujo |

### Namespaces REST principales

| Namespace API | Uso |
|---|---|
| `/api/auth` | Login, perfil y logout |
| `/api/mesas` | Mesas, zonas, apertura/cierre y comanda activa |
| `/api/comandas` | Creacion de comandas, lineas, edicion, cancelacion y pases |
| `/api/kds` | Consulta y cambio de estado de lineas en cocina |
| `/api/tickets` | Generacion, listado y cobro de tickets |
| `/api/reservas` | Gestion de reservas y asignacion de mesa |
| `/api/carta` | Platos y tarifas de menu |
| `/api/usuarios` | Usuarios y roles |
| `/api/auditoria` | Trazabilidad de acciones |
| `/api/sync` | Registro y ejecucion de operaciones offline |

## Logica de negocio 

## Casos de Uso
