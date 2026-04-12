# Capítulo 2

# Modelo de Dominio

El sistema es una aplicación web progresiva (PWA) pensada para funcionar directamente desde el navegador de una tablet. Según el rol con el que se inicie sesión (Camarero, Cocinero o Administrador), la interfaz se adapta mostrando solo lo que cada usuario necesita.

##  Glosario

- **Usuario:** Cualquier persona que accede al sistema con sus credenciales. Puede ser un camarero, un cocinero o el administrador del restaurante. Su rol determina qué puede ver y hacer.

- **Zona:** Representa cada área física del restaurante: comedor, bar, terraza, plaza, mirador, balcón y escaleras. Cada zona tiene sus propias mesas con numeración independiente, lo que permite organizar el servicio por espacios diferenciados.

- **Mesa:** Unidad básica del servicio. Pertenece a una zona concreta, puede tener una comanda activa en curso y recibir reservas en distintos tramos horarios a lo largo del día.

- **Reserva:** Recoge la ocupación futura de una mesa: quién viene, cuántos son, a qué hora y en qué fecha. Siempre está vinculada a una mesa y una zona concretas, y es gestionada por el administrador.

- **Comanda:** Registro digital de todo lo que piden los comensales de una mesa. Se construye línea a línea y cuando llega el momento del cobro se genera automáticamente el ticket correspondiente.

- **LineaComanda:** Representa cada plato concreto dentro de una comanda: nombre, cantidad, alérgenos, observaciones y su estado (pendiente, en preparación o listo). Si el plato forma parte del menú del día, también indica la modalidad elegida: un plato o dos platos.

- **Plato:** Cada ítem disponible en la carta del restaurante, con su nombre, precio y categoría (primero, segundo, postre, bebida o café). Además, se indica si ese plato puede pedirse como parte del menú del día.

- **TarifaMenu:** Define los precios fijos del menú del día según el tipo de jornada. Entre semana, el menú de un plato cuesta 13 € y el de dos platos 17 €. En fines de semana y festivos, los precios son 18 € y 22 € respectivamente. El administrador puede modificar estas tarifas cuando sea necesario.

- **Ticket:** Resumen económico del servicio de una mesa: qué se consumió, precio y suma total. Solo el administrador puede cobrarlo, y en ese momento la mesa queda liberada para el siguiente servicio.

- **LogAuditoria:** Registro inmutable de todo lo que ocurre en el sistema: envíos y ediciones de tickets, cambios de estado en las comandas y cobros en caja. Cada entrada guarda quién hizo la acción y cuándo, lo que permite rastrear cualquier incidencia con precisión.

## Diagrama de Clases

![Diagrama de Clases](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaClases.svg)

## Diagrama de Objetos

![Diagrama de Objetos](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaObjetos.svg)

## Diagrama de Estados

![Diagrama de Estados 1](/Estudiantes/daniel-puente/Capitulo%202/imagenes/EstadosComanda.svg)
![Diagrama de Estados 2](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaEstadosMesa.svg)
![Diagrama de Estados 3](/Estudiantes/daniel-puente/Capitulo%202/imagenes/EstadosReserva.png)

## Requisitos Funcionales

Los siguientes requisitos funcionales han sido identificados como esenciales para el correcto funcionamiento del sistema de gestión interna del Restaurante La Unión:

### Gestión de usuarios y acceso

| ID | Descripción |
|----|-------------|
| RF01 | El sistema debe permitir al Administrador autenticar usuarios mediante email y contraseña, generando un token de acceso JWT y un token de actualización. |
| RF02 | Cada usuario debe tener un rol asociado (Camarero, Cocinero o Administrador) que limite su acceso según permisos definidos. |
| RF03 | El sistema debe bloquear temporalmente el acceso tras 5 intentos fallidos de autenticación. |
| RF04 | El sistema debe permitir al Administrador crear, editar y eliminar usuarios del sistema. |

### Gestión de mesas y zonas

| ID | Descripción |
|----|-------------|
| RF05 | El sistema debe permitir visualizar el estado en tiempo real de todas las mesas agrupadas por zona (libre, ocupada, reservada). |
| RF06 | El sistema debe permitir abrir y cerrar mesas, siendo el cierre exclusivo del Administrador tras el cobro. |
| RF07 | El sistema debe permitir al Administrador configurar el plano del restaurante: agregar, eliminar, mover, unir y separar mesas por zona. |

### Gestión de comandas

| ID | Descripción |
|----|-------------|
| RF08 | El sistema debe permitir al camarero tomar comandas digitales seleccionando platos de la carta o del menú del día con su cantidad. |
| RF09 | El sistema debe exigir el registro obligatorio de alérgenos en cada línea de comanda. |
| RF10 | El sistema debe guardar las comandas localmente en IndexedDB cuando no haya conexión WiFi y sincronizarlas automáticamente al recuperar la conexión utilizando Background Sync. |
| RF11 | El sistema debe permitir editar una orden activa, bloqueando la modificación de los platos que ya están en preparación o listos en la cocina. |
| RF12 | El sistema debe permitir al camarero solicitar los segundos platos a la cocina únicamente cuando los primeros hayan sido marcados como listos. |
| RF13 | Cualquier edición sobre una comanda debe quedar registrada en el log de auditoría con usuario y timestamp. |

### Gestión de cocina (KDS)

| ID | Descripción |
|----|-------------|
| RF14 | El sistema debe mostrar al Cocinero las comandas pendientes en tiempo real en la pantalla KDS, con alérgenos y observaciones destacadas visualmente. |
| RF15 | El sistema debe permitir al cocinero marcar comandas en preparación y platos como listos, actualizando el estado en tiempo real. |
| RF16 | El sistema debe notificar automáticamente al camarero vía WebSocket cuando un plato esté listo para servir. |
| RF17 | El sistema debe actualizar el KDS en tiempo real cuando un camarero edite una comanda que ya se encuentre en cocina. |

### Gestión de tickets y caja

| ID | Descripción |
|----|-------------|
| RF18 | El sistema debe permitir al camarero enviar el ticket de una mesa a caja, generando un desglose de platos, precios y total. |
| RF19 | El ticket debe incluir obligatoriamente: identificador del camarero, mesa, zona, timestamp y desglose de platos con precios. |
| RF20 | El sistema debe permitir al camarero editar y reclamar un ticket enviado siempre que no haya sido cobrado. |
| RF21 | El cobro de un ticket debe ser exclusivo del Administrador, quien tras cobrarlo cerrará la mesa asociada. |

### Gestión de reservas

| ID | Descripción |
|----|-------------|
| RF22 | El sistema debe permitir al Administrador crear, editar y cancelar reservas, asignando mesa, zona, fecha, hora y número de comensales. |
| RF23 | El sistema debe impedir la creación de dos reservas activas para la misma mesa en el mismo tramo horario. |
| RF24 | El sistema debe enviar automáticamente un aviso al camarero vía WebSocket cuando queden 5 minutos o menos para una reserva sobre una mesa ocupada. |

### Gestión de carta y menú del día

| ID | Descripción |
|----|-------------|
| RF25 | El sistema debe permitir al Administrador agregar, editar y eliminar platos de la carta con nombre, precio y categoría. |
| RF26 | El sistema debe permitir al Administrador configurar las tarifas del menú del día definiendo dos tipos (entre semana y festivo/fin de semana), cada uno con precio para modalidad de un plato y modalidad de dos platos. |
| RF27 | El sistema debe permitir al camarero registrar un menú del día seleccionando la modalidad (un plato o dos platos) y aplicando automáticamente la tarifa correspondiente según si el día es entre semana o festivo/fin de semana, independientemente de la combinación de platos elegida por el comensal. |

### Auditoría

| ID | Descripción |
|----|-------------|
| RF28 | El sistema debe registrar de forma inmutable todas las acciones relevantes (envío y edición de tickets, cambios de estado de comandas, cobros) con usuario y marca de tiempo. |
| RF29 | El sistema debe permitir al administrador consultar el registro de auditoría completo. |

---

## Requisitos No Funcionales

### Seguridad

| ID | Descripción |
|----|-------------|
| RNF01 | El sistema debe utilizar HTTPS en todas sus comunicaciones entre cliente y servidor. |
| RNF02 | Todos los endpoints de la API REST deben validar el token JWT y el rol del usuario antes de ejecutar cualquier operación. |
| RNF03 | Los alérgenos registrados en una comanda enviada a cocina deben ser inmutables para garantizar la seguridad alimentaria conforme al Reglamento (UE) Nº 1169/2011. |
| RNF04 | Los tokens de actualización deben almacenarse en cookies seguras (HttpOnly) para prevenir ataques. |

### Disponibilidad y rendimiento

| ID | Descripción |
|----|-------------|
| RNF05 | El sistema debe funcionar de forma completa ante fallos puntuales de la red WiFi local mediante arquitectura local-first. |
| RNF06 | El sistema debe sincronizar automáticamente los datos pendientes en cuanto se recupere la conexión, sin intervención del usuario. |
| RNF07 | El tiempo de respuesta de la API ante cualquier petición no debe superar los 3 segundos en condiciones normales de uso. |
| RNF08 | Las notificaciones en tiempo real (plato listo, aviso de reserva) deben entregarse en menos de 2 segundos tras producirse el evento. |

### Usabilidad

| ID | Descripción |
|----|-------------|
| RNF09 | La interfaz debe ser instalable en cualquier tableta con navegador moderno sin necesidad de tienda de aplicaciones, mediante Web App Manifest. |
| RNF10 | La interfaz debe adaptarse a pantallas táctiles de tablets y ser operable con los dedos, sin necesidad de teclado físico durante el servicio. |
| RNF11 | Cada rol debe acceder directamente a su pantalla principal después del inicio de sesión, sin pasos intermedios innecesarios. |

### Mantenibilidad y escalabilidad

| ID | Descripción |
|----|-------------|
| RNF12 | El backend debe seguir una arquitectura modular por dominio (mesas, comandas, reservas, usuarios…) para facilitar su evolución independiente. |
| RNF13 | El sistema debe desplegarse sobre la infraestructura existente del restaurante (red WiFi y tabletas disponibles) sin requerir hardware adicional. |

# Disciplina de Requisitos

Esta disciplina tiene como finalidad establecer, de manera clara y estructurada, quiénes interactúan con el sistema y qué funcionalidades deben estar disponibles para cada uno de ellos. A partir de esto, se definen los casos de uso principales, su prioridad, y se detalla su comportamiento esperado.

## Identificación de Actores

![Actores](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Actores.svg)

## Casos de Uso Principales

En esta sección se detallan los casos de uso que conforman el flujo principal de operaciones y administración del Restaurante La Unión.

### Tabla de Actores y Casos de Uso 

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
| CU-19 | Gestionar reservas (Crear, Editar, Cancelar) | | ✅ | | |
| CU-20 | Asignar mesa a reserva | | ✅ | | |
| CU-21 | Gestionar usuarios y roles | | ✅ | | |
| CU-22 | Configurar plano de sala (Zonas y Mesas) | | ✅ | | |
| CU-23 | Unir / Separar mesas | | ✅ | | |
| CU-24 | Consultar log de auditoría | | ✅ | | |
| CU-25 | Gestionar carta (Añadir, Editar, Eliminar platos) | | ✅ | | |
| CU-26 | Configurar Menú del Día | | ✅ | | |
| CU-27 | Enviar notificaciones automáticas (plato listo, reservas próximas, actualizar KDS) | | | | ✅ |
| CU-28 | Marcar plato como agotado / disponible | | ✅ | ✅ | |

### Tabla de Actores y Casos de Uso Negativos

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

## Diagrama de Casos de Uso por Actor

### Cocinero
![CDU-Cocinero](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Cocinero.svg)

### Camarero
![CDU-Camarero](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Camarero.svg)

### Administrador
![CDU-Admin](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Admin.svg)

### Sistema de Notificaciones
![CDU-SistNoti](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-NotificacionesAuto.svg)

### Casos de Uso Negativos
![CDU-Negativos](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Negativos.svg) 

## Diagrama de Secuencia
![Secuencia](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Secuencia.svg) 


## Priorización MoSCoW

| ID | Caso de uso | Prioridad |
|---|---|:---:|
| **CU-01** | Iniciar sesión | 🔴 Must |
| **CU-02** | Cerrar sesión | 🔴 Must |
| **CU-03** | Ver estado de mesas por zona | 🔴 Must |
| **CU-04** | Abrir mesa | 🔴 Must |
| **CU-05** | Cerrar mesa | 🔴 Must |
| **CU-06** | Tomar comanda | 🔴 Must |
| **CU-07** | Ver comanda de una mesa | 🔴 Must |
| **CU-08** | Editar comanda | 🔴 Must |
| **CU-09** | Solicitar segundos platos / postres | 🔴 Must |
| **CU-10** | Ver comandas pendientes en KDS | 🔴 Must |
| **CU-11** | Marcar comanda en preparación | 🔴 Must |
| **CU-12** | Marcar plato como listo | 🔴 Must |
| **CU-13** | Ver alérgenos y observaciones | 🔴 Must |
| **CU-14** | Enviar ticket a caja | 🔴 Must |
| **CU-17** | Cobrar ticket en caja | 🔴 Must |
| **CU-18** | Ver listado de reservas del día | 🔴 Must |
| **CU-19** | Gestionar reservas (Crear, Editar, Cancelar) | 🔴 Must |
| **CU-20** | Asignar mesa a reserva | 🔴 Must |
| **CU-21** | Gestionar usuarios y roles | 🔴 Must |
| **CU-25** | Gestionar carta (Añadir, Editar, Eliminar platos) | 🔴 Must |
| **CU-27** | Enviar notificaciones automáticas | 🔴 Must |
| **CU-N01** | Denegar acceso por credenciales incorrectas  | 🔴 Must |
| **CU-N02** | Bloquear operación por rol insuficiente | 🔴 Must |
| **CU-N03** | Rechazar comanda por alérgenos incompletos | 🔴 Must |
| **CU-N05** | Bloquear edición de línea en preparación | 🔴 Must |
| **CU-N06** | Rechazar ticket por mesa ya cobrada | 🔴 Must |
| **CU-15** | Editar ticket | 🟠 Should |
| **CU-16** | Reclamar ticket enviado | 🟠 Should |
| **CU-24** | Consultar log de auditoría | 🟠 Should |
| **CU-26** | Configurar Menú del Día | 🟠 Should |
| **CU-28** | Marcar plato como agotado / disponible | 🟠 Should |
| **CU-N04** | Rechazar reserva por conflicto de horario | 🟠 Should |
| **CU-N07** | Restringir pedido por falta de stock | 🟠 Should |
| **CU-N10** | Desbloquear cuenta por intentos fallidos | 🟠 Should |
| **CU-22** | Configurar plano de sala (Zonas y Mesas) | 🟡 Could |
| **CU-23** | Unir / Separar mesas | 🟡 Could |
| **CU-N08** | Anular ticket ya cobrado por error de cobro | 🟡 Could |
| **CU-N09** | Marcar reserva como no presentado | 🟡 Could |

### 🔴 Must have — Alta prioridad

Los 26 casos de uso clasificados como Must cubren el núcleo operativo del sistema. Sin ellos el restaurante no puede funcionar con la aplicación: autenticación, gestión de mesas, comandas, KDS de cocina, tickets, carta, usuarios y gestión completa de reservas son funcionalidades críticas que conforman el MVP del proyecto. Se incluyen también los casos de uso negativos esenciales de seguridad y bloqueo operativo, como el control de acceso por rol o la protección de alérgenos en comanda.

### 🟠 Should have — Media prioridad

Los 8 casos de uso Should son importantes para la calidad del servicio pero no bloquean la operativa básica. Incluyen la edición y reclamación de tickets, la configuración del menú del día, el log de auditoría, la gestión de stock por agotamiento y el desbloqueo de cuentas por intentos fallidos. Se implementarán tras completar los Must.

### 🟡 Could have — Baja prioridad

Los 4 casos de uso Could añaden valor al sistema pero no son críticos para el lanzamiento del MVP. Cubren funcionalidades avanzadas como la configuración del plano de sala, la unión y separación de mesas, la anulación de tickets cobrados por error y el registro de reservas no presentadas. Se abordarán si el tiempo de desarrollo lo permite.

## Selección de Casos de Uso Para el Desarrollo

| ID    | Nombre                | Priorización |
|-------|-----------------------|:------------:|
| CU-04 | Abrir mesa            | 🔴 Must      |
| CU-06 | Tomar Comanda         | 🔴 Must      |
| CU-08 | Editar Comanda        | 🔴 Must      |
| CU-12 | Marcar plato como listo | 🔴 Must    |
| CU-14 | Enviar ticket a caja  | 🔴 Must      |
| CU-17 | Cobrar ticket en caja | 🔴 Must      |
| CU-05 | Cerrar mesa           | 🔴 Must      |

## Detallar Casos de Uso

# Detallar Casos De Uso

## CU-01 — Iniciar sesión

- **ID:** CU-01
- **Nombre:** Iniciar sesión
- **Actor/es:** Camarero, Administrador, Cocinero
- **Prioridad:** Must
- **Precondición:** El usuario tiene una cuenta activa en el sistema
- **Postcondición:** El usuario accede a su pantalla principal según su rol

**Flujo principal**
1. El usuario abre la aplicación en su dispositivo.
2. El sistema muestra el formulario de inicio de sesión.
3. El usuario introduce su email y contraseña.
4. El sistema valida las credenciales contra la base de datos.
5. El sistema genera un access token JWT y un refresh token.
6. El sistema redirige al usuario a su pantalla principal según su rol.

**Flujos alternativos**
- *CU-N01:* Si las credenciales son incorrectas, el sistema muestra un mensaje de error y no permite el acceso.
- *CU-N10:* Si se producen 5 intentos fallidos consecutivos, el sistema bloquea la cuenta temporalmente.

---

## CU-02 — Cerrar sesión

- **ID:** CU-02
- **Nombre:** Cerrar sesión
- **Actor/es:** Camarero, Administrador, Cocinero
- **Prioridad:** Must
- **Precondición:** El usuario tiene una sesión activa
- **Postcondición:** La sesión queda invalidada y el usuario es redirigido al login

**Flujo principal**
1. El usuario pulsa el botón de cerrar sesión.
2. El sistema invalida el access token y el refresh token.
3. El sistema elimina los datos de sesión del dispositivo.
4. El sistema redirige al usuario a la pantalla de inicio de sesión.

---

## CU-03 — Ver estado de mesas por zona

- **ID:** CU-03
- **Nombre:** Ver estado de mesas por zona
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** El usuario ha iniciado sesión
- **Postcondición:** El usuario visualiza el plano de sala actualizado en tiempo real

**Flujo principal**
1. El usuario accede a la pantalla principal de sala.
2. El sistema muestra todas las zonas configuradas (comedor, bar, terraza, etc.).
3. Cada mesa se muestra con su estado actual: libre, ocupada o reservada.
4. El sistema actualiza el estado automáticamente vía WebSocket ante cualquier cambio.

---

## CU-04 — Abrir mesa

- **ID:** CU-04
- **Nombre:** Abrir mesa
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** La mesa está en estado libre
- **Postcondición:** La mesa pasa a estado ocupada y queda lista para recibir comandas

**Flujo principal**
1. El usuario selecciona una mesa libre en el plano de sala.
2. El sistema solicita confirmación para abrir la mesa.
3. El usuario confirma.
4. El sistema registra la apertura con usuario y timestamp.
5. La mesa pasa a estado ocupada y se actualiza el plano en tiempo real.

---

## CU-05 — Cerrar mesa

- **ID:** CU-05
- **Nombre:** Cerrar mesa
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** La mesa está ocupada y el ticket ha sido cobrado
- **Postcondición:** La mesa pasa a estado libre y queda disponible para nuevos comensales

**Flujo principal**
1. El Administrador selecciona una mesa ocupada en el plano.
2. El sistema verifica que el ticket asociado ha sido cobrado.
3. El sistema registra el cierre con usuario y timestamp en el log de auditoría.
4. La mesa pasa a estado libre y se actualiza el plano en tiempo real.

---

## CU-06 — Tomar comanda

- **ID:** CU-06
- **Nombre:** Tomar comanda
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** La mesa está en estado ocupada
- **Postcondición:** La comanda queda registrada y enviada a cocina

**Flujo principal**
1. El usuario selecciona una mesa ocupada.
2. El sistema muestra la carta y el menú del día disponible.
3. El usuario añade platos línea a línea, indicando cantidad, alérgenos y observaciones.
4. El sistema valida que todos los campos obligatorios estén completos.
5. El usuario confirma el envío de la comanda.
6. El sistema guarda la comanda y la envía al KDS de cocina vía WebSocket.
7. Si no hay conexión, la comanda se guarda en IndexedDB y se sincroniza al recuperar la red.

**Flujos alternativos**
- *CU-N03:* Si los alérgenos están incompletos, el sistema bloquea el envío y avisa al usuario.
- *CU-N07:* Si algún plato está marcado como agotado, el sistema impide añadirlo a la comanda.

---

## CU-07 — Ver comanda de una mesa

- **ID:** CU-07
- **Nombre:** Ver comanda de una mesa
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** La mesa está ocupada y tiene una comanda activa
- **Postcondición:** El usuario visualiza el detalle completo de la comanda

**Flujo principal**
1. El usuario selecciona una mesa ocupada.
2. El sistema muestra el detalle de la comanda activa: platos, cantidades, alérgenos, observaciones y estado de cada línea.

---

## CU-08 — Editar comanda

- **ID:** CU-08
- **Nombre:** Editar comanda
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** La mesa tiene una comanda activa con líneas en estado pendiente
- **Postcondición:** La comanda queda actualizada y los cambios se reflejan en el KDS

**Flujo principal**
1. El usuario accede a la comanda activa de una mesa.
2. El usuario modifica, añade o elimina líneas en estado pendiente.
3. El sistema valida los cambios (alérgenos, disponibilidad).
4. El sistema guarda los cambios, actualiza el KDS en tiempo real y registra la edición en el log de auditoría.

**Flujos alternativos**
- *CU-N05:* Si el usuario intenta editar una línea ya en preparación o lista, el sistema bloquea la acción.

---

## CU-09 — Solicitar segundos platos / postres

- **ID:** CU-09
- **Nombre:** Solicitar segundos platos / postres
- **Actor/es:** Camarero, Administrador, Sistema de Notificaciones
- **Prioridad:** Must
- **Precondición:** Los primeros platos de la mesa han sido marcados como listos por cocina
- **Postcondición:** La solicitud llega a cocina y aparece en el KDS

**Flujo principal**
1. El sistema detecta que los primeros platos de una mesa están listos.
2. El camarero recibe una notificación automática en su dispositivo.
3. El camarero pulsa "Solicitar segundos" desde la comanda de la mesa.
4. El sistema envía la solicitud a cocina vía WebSocket y actualiza el KDS.

---

## CU-10 — Ver comandas pendientes en KDS

- **ID:** CU-10
- **Nombre:** Ver comandas pendientes en KDS
- **Actor/es:** Cocinero
- **Prioridad:** Must
- **Precondición:** El cocinero ha iniciado sesión
- **Postcondición:** El cocinero visualiza todas las comandas pendientes en tiempo real

**Flujo principal**
1. El cocinero accede a la pantalla KDS.
2. El sistema muestra todas las comandas pendientes ordenadas por hora de llegada.
3. Cada comanda muestra mesa, zona, platos, cantidades, alérgenos y observaciones destacadas visualmente.
4. El KDS se actualiza automáticamente en tiempo real vía WebSocket ante nuevas comandas o ediciones.

---

## CU-11 — Marcar comanda en preparación

- **ID:** CU-11
- **Nombre:** Marcar comanda en preparación
- **Actor/es:** Cocinero
- **Prioridad:** Must
- **Precondición:** Existe al menos una comanda en estado pendiente en el KDS
- **Postcondición:** La comanda pasa a estado en preparación y queda bloqueada para edición

**Flujo principal**
1. El cocinero selecciona una comanda pendiente en el KDS.
2. El cocinero pulsa "En preparación".
3. El sistema actualiza el estado de la comanda y lo notifica al camarero vía WebSocket.
4. Las líneas de esa comanda quedan bloqueadas para cualquier edición desde sala.

---

## CU-12 — Marcar plato como listo

- **ID:** CU-12
- **Nombre:** Marcar plato como listo
- **Actor/es:** Cocinero, Sistema de Notificaciones
- **Prioridad:** Must
- **Precondición:** El plato está en estado en preparación
- **Postcondición:** El plato pasa a estado listo y el camarero recibe una notificación automática

**Flujo principal**
1. El cocinero selecciona un plato en preparación en el KDS.
2. El cocinero pulsa "Listo".
3. El sistema actualiza el estado del plato.
4. El Sistema de Notificaciones envía un aviso automático al camarero correspondiente vía WebSocket.

---

## CU-13 — Ver alérgenos y observaciones

- **ID:** CU-13
- **Nombre:** Ver alérgenos y observaciones
- **Actor/es:** Cocinero
- **Prioridad:** Must
- **Precondición:** Existe una comanda activa con alérgenos u observaciones registradas
- **Postcondición:** El cocinero visualiza los alérgenos y observaciones con indicador visual destacado

**Flujo principal**
1. El cocinero accede al detalle de una comanda en el KDS.
2. El sistema muestra cada línea con sus alérgenos e indicador visual destacado.
3. Las observaciones especiales aparecen resaltadas junto al plato correspondiente.

---

## CU-14 — Enviar ticket a caja

- **ID:** CU-14
- **Nombre:** Enviar ticket a caja
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** La mesa tiene una comanda activa
- **Postcondición:** El ticket queda generado en caja con el desglose completo

**Flujo principal**
1. El usuario selecciona la mesa y pulsa "Enviar a caja".
2. El sistema genera el ticket con desglose de platos, precios y total.
3. El ticket incluye: camarero, mesa, zona y timestamp.
4. El sistema registra el envío en el log de auditoría.
5. El ticket queda disponible en caja para ser cobrado por el Administrador.

**Flujos alternativos**
- *CU-N06:* Si la mesa ya ha sido cobrada, el sistema rechaza el envío y muestra un aviso.

---

## CU-17 — Cobrar ticket en caja

- **ID:** CU-17
- **Nombre:** Cobrar ticket en caja
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** El ticket ha sido enviado a caja y está pendiente de cobro
- **Postcondición:** El ticket queda cerrado, la mesa se libera y el cobro queda registrado en auditoría

**Flujo principal**
1. El Administrador accede a la vista de caja.
2. El sistema muestra los tickets pendientes de cobro.
3. El Administrador selecciona el ticket y confirma el cobro.
4. El sistema cierra el ticket, libera la mesa y registra el cobro en el log de auditoría con usuario y timestamp.

---

## CU-18 — Ver listado de reservas del día

- **ID:** CU-18
- **Nombre:** Ver listado de reservas del día
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** El Administrador ha iniciado sesión
- **Postcondición:** El Administrador visualiza todas las reservas del día ordenadas por hora

**Flujo principal**
1. El Administrador accede al módulo de reservas.
2. El sistema muestra el listado de reservas del día con: nombre, mesa, zona, hora y número de comensales.
3. Cada reserva muestra su estado: pendiente, confirmada o cancelada.

---

## CU-19 — Gestionar reservas

- **ID:** CU-19
- **Nombre:** Gestionar reservas (Crear, Editar, Cancelar)
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** El Administrador ha iniciado sesión
- **Postcondición:** La reserva queda creada, modificada o cancelada en el sistema

**Flujo principal**
1. El Administrador accede al módulo de reservas.
2. El Administrador selecciona crear, editar o cancelar una reserva.
3. Para crear o editar: introduce nombre, mesa, zona, fecha, hora y número de comensales.
4. El sistema valida que no exista conflicto de horario para esa mesa.
5. El sistema guarda los cambios y actualiza el listado.

**Flujos alternativos**
- *CU-N04:* Si ya existe una reserva activa para esa mesa en el mismo tramo horario, el sistema rechaza la operación.

---

## CU-20 — Asignar mesa a reserva

- **ID:** CU-20
- **Nombre:** Asignar mesa a reserva
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** Existe una reserva sin mesa asignada o se desea reasignar
- **Postcondición:** La reserva queda vinculada a una mesa concreta y la mesa aparece como reservada en el plano

**Flujo principal**
1. El Administrador selecciona una reserva del listado.
2. El sistema muestra el plano de sala con las mesas disponibles para el tramo horario.
3. El Administrador selecciona una mesa disponible.
4. El sistema vincula la mesa a la reserva y actualiza su estado a reservada en el plano.

---

## CU-21 — Gestionar usuarios y roles

- **ID:** CU-21
- **Nombre:** Gestionar usuarios y roles
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** El Administrador ha iniciado sesión
- **Postcondición:** El usuario queda creado, modificado o eliminado con su rol asignado

**Flujo principal**
1. El Administrador accede al panel de gestión de usuarios.
2. El sistema muestra el listado de usuarios activos con su rol.
3. El Administrador puede crear un nuevo usuario introduciendo nombre, email, contraseña y rol.
4. El Administrador puede editar el rol o datos de un usuario existente.
5. El Administrador puede desactivar o eliminar un usuario.
6. El sistema guarda los cambios y los aplica de forma inmediata.

**Flujos alternativos**
- *CU-N02:* Si un usuario sin rol de Administrador intenta acceder, el sistema bloquea la operación.

---

## CU-25 — Gestionar carta

- **ID:** CU-25
- **Nombre:** Gestionar carta (Añadir, Editar, Eliminar platos)
- **Actor/es:** Administrador
- **Prioridad:** Must
- **Precondición:** El Administrador ha iniciado sesión
- **Postcondición:** El plato queda añadido, actualizado o eliminado de la carta

**Flujo principal**
1. El Administrador accede al módulo de gestión de carta.
2. El sistema muestra el listado de platos agrupados por categoría.
3. El Administrador puede añadir un plato nuevo con nombre, precio, categoría e indicador de menú del día.
4. El Administrador puede editar cualquier campo de un plato existente.
5. El Administrador puede eliminar un plato de la carta.
6. Los cambios se aplican de forma inmediata en la vista de toma de comandas.

---

## CU-27 — Enviar notificaciones automáticas

- **ID:** CU-27
- **Nombre:** Enviar notificaciones automáticas
- **Actor/es:** Sistema de Notificaciones
- **Prioridad:** Must
- **Precondición:** Se ha producido un evento relevante en el sistema
- **Postcondición:** La notificación llega al actor correspondiente en menos de 2 segundos

**Flujo principal**
1. Se produce un evento: plato listo, reserva próxima o actualización de KDS.
2. El Sistema de Notificaciones detecta el evento.
3. El sistema envía la notificación al actor correspondiente vía WebSocket:
   - Plato listo → camarero de la mesa.
   - Reserva próxima (≤5 min) → camarero de la mesa afectada.
   - Actualización de KDS → cocinero.
4. El dispositivo del receptor muestra el aviso de forma visible.

---

## CU-N01 — Denegar acceso por credenciales incorrectas

- **ID:** CU-N01
- **Nombre:** Acceso denegado por credenciales incorrectas
- **Actor/es:** Camarero, Administrador, Cocinero
- **Prioridad:** Must
- **Precondición:** El usuario introduce credenciales incorrectas en el formulario de login
- **Postcondición:** El sistema deniega el acceso y muestra un mensaje de error

**Flujo principal**
1. El usuario introduce email o contraseña incorrectos.
2. El sistema valida las credenciales y detecta el error.
3. El sistema muestra un mensaje de error genérico sin revelar qué campo es incorrecto.
4. El intento queda registrado internamente para el control de bloqueo.

---

## CU-N02 — Bloquear operación por rol insuficiente

- **ID:** CU-N02
- **Nombre:** Operación bloqueada por rol insuficiente
- **Actor/es:** Camarero, Cocinero
- **Prioridad:** Must
- **Precondición:** Un usuario intenta ejecutar una acción para la que no tiene permisos
- **Postcondición:** El sistema bloquea la operación y muestra un mensaje informativo

**Flujo principal**
1. El usuario intenta acceder a una funcionalidad restringida.
2. El sistema comprueba el rol del token JWT.
3. El sistema detecta que el rol no tiene permisos para esa operación.
4. El sistema bloquea la acción y muestra un aviso de acceso denegado.

---

## CU-N03 — Rechazar comanda por alérgenos incompletos

- **ID:** CU-N03
- **Nombre:** Comanda rechazada por alérgenos incompletos
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** El usuario intenta enviar una comanda con alérgenos sin completar
- **Postcondición:** El sistema bloquea el envío y señala las líneas con alérgenos pendientes

**Flujo principal**
1. El usuario pulsa "Enviar comanda".
2. El sistema valida que todas las líneas tienen los alérgenos registrados.
3. El sistema detecta una o más líneas con alérgenos incompletos.
4. El sistema bloquea el envío, resalta visualmente las líneas afectadas y muestra un aviso.

---

## CU-N05 — Bloquear edición  de línea en preparación

- **ID:** CU-N05
- **Nombre:** Edición bloqueada de línea en preparación
- **Actor/es:** Camarero, Administrador
- **Prioridad:** Must
- **Precondición:** Una línea de comanda está en estado en preparación o lista
- **Postcondición:** El sistema bloquea la edición y muestra un aviso al usuario

**Flujo principal**
1. El usuario intenta modificar o eliminar una línea de comanda.
2. El sistema comprueba el estado de la línea.
3. El sistema detecta que la línea está en preparación o lista.
4. El sistema bloquea la edición e informa de que el cocinero ya ha iniciado la preparación.

---

## CU-N06 — Rechazar ticket por mesa ya cobrada

- **ID:** CU-N06
- **Nombre:** Ticket rechazado por mesa ya cobrada
- **Actor/es:** Camarero
- **Prioridad:** Must
- **Precondición:** El usuario intenta enviar o modificar el ticket de una mesa ya cobrada
- **Postcondición:** El sistema rechaza la operación y muestra un aviso

**Flujo principal**
1. El usuario intenta enviar a caja o editar el ticket de una mesa.
2. El sistema comprueba el estado del ticket.
3. El sistema detecta que la mesa ya ha sido cobrada y cerrada.
4. El sistema bloquea la operación e informa de que la mesa ya está cerrada.


## Boceto de Prototipos Casos de Uso

### Boceto 1 — Pantalla KDS y Panel Admin
![Boceto 1](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos1.jpeg)


### Boceto 2  Reservas
![Prototipo 2](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos2.jpeg)

### Boceto 3  
![Prototipo 3](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos3.jpeg)


### Boceto 4
![Prototipo 4](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos4.jpeg)


## Estructurar Casos de Uso

### Objetivo

El objetivo de este paso es estructurar el modelo de casos de uso del sistema de gestión del Restaurante La Unión, identificando funcionalidades compartidas mediante relaciones de inclusión y comportamientos opcionales o alternativos mediante relaciones de extensión, reduciendo así la redundancia entre casos de uso y mejorando la trazabilidad del modelo.

### Relaciones Include y Extend

| Include | Extend |
|---|---|
| Representa funcionalidad obligatoria y común entre casos de uso | Representa comportamiento opcional o alternativo |
| Reduce la duplicación de especificaciones | Permite modelar flujos alternativos y excepciones |


### Relación `<<include>>`

| Caso de uso principal | Caso de uso incluido | Explicación |
|---|---|---|
| **CU-06 Tomar comanda** | CU-01 Iniciar sesión | Para tomar una comanda el usuario debe estar autenticado en el sistema |
| **CU-08 Editar comanda** | CU-07 Ver comanda de una mesa | Para editar una comanda es necesario consultarla previamente |
| **CU-14 Enviar ticket a caja** | CU-07 Ver comanda de una mesa | El ticket se genera a partir de la comanda activa de la mesa |
| **CU-17 Cobrar ticket en caja** | CU-05 Cerrar mesa | El cobro del ticket implica obligatoriamente el cierre de la mesa |
| **CU-19 Gestionar reservas** | CU-18 Ver listado de reservas del día | La gestión de reservas requiere consultar el listado previo |
| **CU-20 Asignar mesa a reserva** | CU-19 Gestionar reservas | Asignar una mesa es parte del flujo de gestión de reservas |
| **CU-11 Marcar comanda en preparación** | CU-10 Ver comandas pendientes en KDS | El cocinero debe ver las comandas pendientes antes de marcarlas |
| **CU-12 Marcar plato como listo** | CU-11 Marcar comanda en preparación | Un plato solo puede marcarse como listo si está en preparación |
| **CU-09 Solicitar segundos platos** | CU-12 Marcar plato como listo | Solo se pueden solicitar segundos si los primeros están marcados como listos |

### Relación `<<extend>>`

| Caso de uso principal | Caso de uso extendido | Explicación |
|---|---|---|
| **CU-01 Iniciar sesión** | CU-N01 Denegar acceso por credenciales incorrectas | El acceso se deniega si las credenciales introducidas son incorrectas |
| **CU-01 Iniciar sesión** | CU-N10 Desbloquear cuenta por intentos fallidos | Tras 5 intentos fallidos el sistema bloquea la cuenta |
| **CU-06 Tomar comanda** | CU-N03 Rechazar comanda por alérgenos incompletos | El envío se bloquea si alguna línea tiene alérgenos sin completar |
| **CU-06 Tomar comanda** | CU-N07 Restringir pedido por falta de stock | El sistema impide añadir platos marcados como agotados |
| **CU-08 Editar comanda** | CU-N05 Bloquear edición de línea en preparación | La edición se bloquea si la línea ya está en preparación o lista |
| **CU-14 Enviar ticket a caja** | CU-N06 Rechazar ticket por mesa ya cobrada | El envío se rechaza si la mesa ya ha sido cobrada y cerrada |
| **CU-19 Gestionar reservas** | CU-N04 Rechazar reserva por conflicto de horario | La reserva se rechaza si existe otra activa en el mismo tramo horario |
| **CU-21 Gestionar usuarios y roles** | CU-N02 Bloquear operación por rol insuficiente | La operación se bloquea si el usuario no tiene rol de Administrador |