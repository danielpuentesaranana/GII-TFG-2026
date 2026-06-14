# 4.9 Extractos de código relevantes

En esta sección no se incluye todo el código fuente del proyecto, sino únicamente los fragmentos que representan reglas de negocio relevantes para el MVP. Se han seleccionado fragmentos vinculados directamente con los casos de uso principales: apertura de mesa, toma y edición de comanda, control del KDS, cobro de ticket y cierre de mesa.

## Apertura de comanda y ocupación de mesa

Archivo: `backend/src/services/comandaService.js`

Este fragmento representa el inicio real del servicio. Aunque el usuario percibe que abre una mesa, técnicamente el sistema crea una comanda activa y ocupa la mesa o las mesas asociadas.

```js
const mesaNoDisponible = mesas.find(
  (item) => !['LIBRE', 'RESERVADA'].includes(item.estado) || item.comanda_activa_id,
);

if (mesaNoDisponible) {
  throw new Error(`La mesa ${mesaNoDisponible.numero} no esta libre para abrir comanda`);
}

const comanda = await Comanda.create({
  mesa_id,
  camarero_id,
  estado: 'ABIERTA',
});

await Mesa.updateMany(
  { _id: { $in: idsMesasUnidas } },
  { estado: 'OCUPADA', comanda_activa_id: comanda._id },
);

emitirEvento('comanda:abierta', { comanda });
```

Este bloque justifica el caso de uso **CU-04 Abrir mesa**, ya que garantiza que la mesa queda asociada a una comanda activa y que el resto de vistas reciben la actualización.

## Validación de alérgenos y control de pases de menú

Archivo: `backend/src/services/comandaService.js`

La toma de comanda no consiste solo en guardar platos. El backend valida observaciones, alérgenos y disponibilidad. Además, si el plato pertenece al menú, segundos y postres quedan retenidos para que no aparezcan en cocina hasta que el camarero solicite el pase correspondiente.

```js
const CATEGORIAS_MENU_RETENIDAS = ['SEGUNDO', 'POSTRE'];

const lineaMenuVisibleInicialmente = (plato, esMenu) => (
  !esMenu || !CATEGORIAS_MENU_RETENIDAS.includes(normalizarCategoria(plato.categoria))
);

const observacionesValidadas = validarObservaciones(observaciones);
const alergenosConfirmados = validarAlergenosConfirmados(plato, alergenos);

const linea = await LineaComanda.create({
  comanda_id,
  plato_id,
  tarifa_menu_id: esMenu ? tarifa_menu_id : null,
  cantidad,
  observaciones: observacionesValidadas,
  alergenos: alergenosConfirmados,
  esMenu,
  solicitadoKds: lineaMenuVisibleInicialmente(plato, esMenu),
  estado: 'PENDIENTE',
});
```

Este fragmento justifica **CU-06 Tomar comanda** y **CU-09 Solicitar segundos platos o postres**, porque demuestra que el sistema valida la información crítica antes de enviar la línea al flujo de cocina.

## Edición segura de líneas de comanda

Archivo: `backend/src/services/comandaService.js`

La edición de una comanda está limitada a líneas pendientes. Si cocina ya ha empezado a preparar un plato, el backend bloquea la modificación aunque el frontend intentase enviarla.

```js
if (!comanda || comanda.estado !== 'ABIERTA') {
  throw new Error('Solo se pueden editar lineas de una comanda ABIERTA');
}

if (linea.estado !== 'PENDIENTE') {
  throw new Error('No se puede editar una linea que ya esta en preparacion');
}

validarAlergenosInmutables(linea, alergenos);
const observacionesFinal = validarObservaciones(observaciones ?? linea.observaciones);

linea.cantidad = cantidad ?? linea.cantidad;
linea.observaciones = observacionesFinal;
linea.alergenos = validarAlergenosConfirmados(plato, alergenos ?? linea.alergenos);
await linea.save();

emitirEvento('lineaComanda:actualizada', { linea });
emitirEvento('kds:actualizado', { comanda_id: comanda._id });
```

Este fragmento cubre **CU-08 Editar comanda** y el caso negativo de bloqueo de edición cuando una línea ya está en preparación.

## Transiciones de estado en KDS

Archivo: `backend/src/services/kdsService.js`

El KDS funciona mediante una máquina de estados simple. La cocina no puede mover una línea a cualquier estado, sino que debe seguir una transición coherente: pendiente, en preparación, listo y servido.

```js
const TRANSICIONES_VALIDAS = {
  PENDIENTE: ['EN_PREPARACION', 'CANCELADO'],
  EN_PREPARACION: ['LISTO'],
  LISTO: ['SERVIDO'],
  SERVIDO: [],
  CANCELADO: [],
};

const transiciones = TRANSICIONES_VALIDAS[linea.estado] || [];

if (!transiciones.includes(estado)) {
  throw new Error(`No se puede cambiar una linea de ${linea.estado} a ${estado}`);
}

linea.estado = estado;
await linea.save();

emitirEvento('lineaComanda:actualizada', { linea });
emitirEvento('kds:actualizado', { comanda_id: comanda._id });

if (estado === 'LISTO') {
  await notificarPlatosListos(comanda);
}
```

Este fragmento justifica **CU-12 Marcar plato como listo**, ya que controla el avance de cocina y activa la notificación hacia sala cuando el plato queda preparado.

## Cobro de ticket y cierre de mesa

Archivos: `backend/src/services/ticketService.js` y `backend/src/services/mesaService.js`

El sistema separa el cobro del ticket y el cierre de mesa. Esta decisión evita liberar una mesa por error antes de que el pago haya sido confirmado.

```js
if (ticket.estado !== 'PENDIENTE') {
  throw new Error('Solo se puede cobrar un ticket PENDIENTE');
}

ticket.estado = 'COBRADO';
ticket.cobradoEn = new Date();
ticket.cobradoPor = usuario_id;
await ticket.save();

emitirEvento('ticket:cobrado', { ticket });
```

```js
const ticket = await Ticket.findOne({ comanda_id: mesa.comanda_activa_id });

if (!ticket || ticket.estado !== 'COBRADO') {
  throw new Error('No se puede cerrar una mesa sin ticket cobrado');
}

const comandaId = mesa.comanda_activa_id;

await Comanda.findByIdAndUpdate(comandaId, { estado: 'CERRADA' });
await Mesa.updateMany(
  { comanda_activa_id: comandaId },
  { estado: 'LIBRE', comanda_activa_id: null },
);
```

Estos fragmentos cubren **CU-17 Cobrar ticket en caja** y **CU-05 Cerrar mesa**. La regla principal es que cobrar no libera automáticamente la mesa; el cierre es una acción posterior y controlada.

[← Volver al índice del capítulo](README.md)
