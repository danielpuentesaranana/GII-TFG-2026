# 3.1 Análisis

La solución propuesta se ha diseñado bajo una arquitectura modular por capas, inspirada en el patrón Modelo-Vista-Controlador (MVC). Esta organización permite separar la representación de los datos, la lógica de negocio, los puntos de entrada de la API y la interfaz de usuario.

Con esta separación se mejora la mantenibilidad, se facilita la evolución del sistema y se reduce el acoplamiento entre las distintas partes de la aplicación. El desarrollo del MVP se ha centrado en los casos de uso más relevantes para la operativa interna del Restaurante La Unión: abrir mesa, tomar comanda, editar comanda, marcar platos como listos, enviar ticket a caja, cobrar ticket y cerrar mesa.

El análisis parte de los requisitos definidos en el capítulo anterior y los transforma en componentes técnicos concretos:

- Modelos de dominio persistidos en MongoDB mediante Mongoose.
- Vistas adaptadas a los roles del restaurante.
- Controladores y servicios para concentrar la lógica de negocio.
- Rutas REST protegidas mediante JWT y control de roles.
- Eventos Socket.IO para sincronización en tiempo real.
- Soporte offline mediante IndexedDB.

[← Volver al índice del capítulo](README.md)
