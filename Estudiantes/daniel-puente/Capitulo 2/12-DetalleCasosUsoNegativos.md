# 2.12 Detalle de casos de uso negativos

## CU-N01 - Denegar acceso por credenciales incorrectas

| Campo | Valor |
|---|---|
| ID | CU-N01 |
| Nombre | Acceso denegado por credenciales incorrectas |
| Actores | Camarero, Administrador, Cocinero |
| Prioridad | Must |
| Precondición | El usuario introduce credenciales incorrectas en el login. |
| Postcondición | El sistema deniega el acceso y muestra un mensaje de error. |

**Flujo principal**

1. El usuario introduce email o contraseña incorrectos.
2. El sistema valida las credenciales y detecta el error.
3. El sistema muestra un mensaje genérico sin revelar qué campo es incorrecto.
4. El intento queda registrado para el control de bloqueo.

## CU-N02 - Bloquear operación por rol insuficiente

| Campo | Valor |
|---|---|
| ID | CU-N02 |
| Nombre | Operación bloqueada por rol insuficiente |
| Actores | Camarero, Cocinero |
| Prioridad | Must |
| Precondición | Un usuario intenta ejecutar una acción para la que no tiene permisos. |
| Postcondición | El sistema bloquea la operación. |

**Flujo principal**

1. El usuario intenta acceder a una funcionalidad restringida.
2. El sistema comprueba el rol incluido en el token JWT.
3. El sistema detecta que el rol no tiene permisos.
4. El sistema bloquea la acción y muestra un aviso de acceso denegado.

## CU-N03 - Rechazar comanda por alérgenos incompletos

| Campo | Valor |
|---|---|
| ID | CU-N03 |
| Nombre | Comanda rechazada por alérgenos incompletos |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | El usuario intenta registrar una línea cuyo plato tiene alérgenos no confirmados. |
| Postcondición | El sistema bloquea la operación y muestra un aviso. |

**Flujo principal**

1. El usuario intenta añadir o editar una línea de comanda.
2. El sistema comprueba los alérgenos declarados del plato.
3. El sistema detecta que no han sido confirmados correctamente.
4. El sistema bloquea la operación y avisa al usuario.

## CU-N04 - Rechazar reserva por conflicto horario

| Campo | Valor |
|---|---|
| ID | CU-N04 |
| Nombre | Reserva rechazada por conflicto horario |
| Actores | Administrador |
| Prioridad | Must |
| Precondición | El Administrador intenta crear o modificar una reserva. |
| Postcondición | El sistema rechaza la operación si existe solapamiento. |

**Flujo principal**

1. El Administrador introduce los datos de la reserva.
2. El sistema comprueba mesa, zona, fecha y hora.
3. El sistema detecta una reserva incompatible en el mismo tramo.
4. El sistema bloquea la operación y muestra un aviso.

## CU-N05 - Bloquear edición de línea en preparación

| Campo | Valor |
|---|---|
| ID | CU-N05 |
| Nombre | Edición bloqueada de línea en preparación |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | Una línea está en preparación, lista o servida. |
| Postcondición | El sistema bloquea la edición. |

**Flujo principal**

1. El usuario intenta modificar o cancelar una línea.
2. El sistema comprueba el estado de la línea.
3. El sistema detecta que la línea ya no está pendiente.
4. El sistema bloquea la edición e informa al usuario.

## CU-N06 - Rechazar ticket por mesa ya cobrada

| Campo | Valor |
|---|---|
| ID | CU-N06 |
| Nombre | Ticket rechazado por mesa ya cobrada |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | El usuario intenta generar o modificar un ticket ya cobrado o asociado a una mesa cerrada. |
| Postcondición | El sistema rechaza la operación. |

**Flujo principal**

1. El usuario intenta enviar a caja o modificar un ticket.
2. El sistema comprueba el estado del ticket y de la mesa.
3. El sistema detecta que el ticket ya está cobrado o que la mesa ha sido cerrada.
4. El sistema bloquea la operación y muestra un aviso.

## CU-N07 - Rechazar plato no disponible

| Campo | Valor |
|---|---|
| ID | CU-N07 |
| Nombre | Plato rechazado por no estar disponible |
| Actores | Camarero, Administrador |
| Prioridad | Must |
| Precondición | El usuario intenta añadir un plato no disponible. |
| Postcondición | El sistema impide añadir el plato a la comanda. |

**Flujo principal**

1. El usuario selecciona un plato de la carta.
2. El sistema comprueba su disponibilidad.
3. El sistema detecta que el plato no está disponible.
4. El sistema bloquea la operación y muestra un aviso.

## CU-N10 - Bloquear cuenta por intentos fallidos

| Campo | Valor |
|---|---|
| ID | CU-N10 |
| Nombre | Cuenta bloqueada por intentos fallidos |
| Actores | Camarero, Administrador, Cocinero |
| Prioridad | Must |
| Precondición | El usuario acumula varios intentos de acceso incorrectos. |
| Postcondición | La cuenta queda bloqueada temporalmente. |

**Flujo principal**

1. El usuario introduce credenciales incorrectas varias veces.
2. El sistema incrementa el contador de intentos fallidos.
3. Al superar el límite establecido, el sistema bloquea temporalmente la cuenta.
4. El sistema informa al usuario de que no puede iniciar sesión hasta que finalice el bloqueo o sea desbloqueado.

[← Volver al índice del capítulo](README.md)
