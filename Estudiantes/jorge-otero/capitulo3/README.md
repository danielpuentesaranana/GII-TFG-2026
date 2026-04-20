# Análisis y Diseño 

## Introducción

En este capítulo se aborda el análisis y diseño del sistema a partir de los requisitos y casos de uso definidos previamente. En primer lugar, se realiza el análisis mediante la identificación de las clases y sus relaciones siguiendo el patrón arquitectónico Modelo-Vista-Controlador (MVC), así como la elaboración de diagramas de clases y de colaboración. A continuación, se presenta la transición hacia el diseño, donde se concreta la solución tecnológica adoptada y se detallan las clases con un enfoque implementable. Finalmente, se incluyen los diagramas de clases de diseño y los diagramas de secuencia que describen el comportamiento del sistema de forma más precisa.

## Análisis

El análisis del sistema tiene como finalidad definir la estructura lógica de la solución a partir de los requisitos y casos de uso obtenidos en el capítulo anterior. Para ello, se adopta el patrón Modelo-Vista-Controlador (MVC), siguiendo el esquema propuesto en la asignatura: cada caso de uso detallado da lugar a una clase controladora, la interfaz asociada al caso de uso se representa mediante una clase vista, y las entidades del dominio implicadas se modelan como clases del modelo.

De este modo, el análisis permite pasar de una descripción funcional del sistema a una primera organización estructurada de sus elementos, sin entrar todavía en detalles tecnológicos o de implementación. El resultado de esta fase es un diagrama de clases de análisis, complementado con diagramas de colaboración para los casos de uso más representativos.

En el caso de este proyecto, el sistema se centra en la gestión de solicitudes realizadas por el cliente, su posible ampliación mediante formularios, y el seguimiento posterior por parte del técnico. A partir de los diagramas de contexto definidos previamente, se identifican dos actores principales:

+ Cliente, que interactúa con el sistema para enviar solicitudes, recibir respuestas y completar formularios.
+ Técnico, que consulta las solicitudes pendientes y actualiza su estado.

A partir de estas interacciones, se construye el análisis del sistema siguiendo la estructura MVC.

### Identificación de clases de análisis

| Diagrama | Código Fuente |
|----------|---------------|
|![ModeloVistaControlador](./MVC/imagen/MVC.png)|[Ver Código](./MVC/codigo/MVC.puml)

El diagrama de clases de análisis representa la estructura lógica del sistema organizada según el patrón Modelo-Vista-Controlador (MVC). En esta fase, el objetivo es identificar las clases principales del sistema y clasificarlas en función de su responsabilidad, sin entrar todavía en detalles de implementación ni en las relaciones específicas entre ellas.

Para facilitar su comprensión, las clases se agrupan en tres paquetes: Vistas, Controladores y Modelos.

El paquete Vistas recoge las clases encargadas de representar los puntos de interacción entre los actores y el sistema. Estas clases abstraen las distintas interfaces a través de las cuales se realizan las operaciones principales, como el envío de solicitudes, la recepción de respuestas, la cumplimentación de formularios o la consulta de solicitudes pendientes. En esta fase de análisis, no se modelan como interfaces gráficas concretas, sino como representaciones conceptuales de dichas interacciones.

El paquete Controladores incluye las clases responsables de gestionar la lógica asociada a cada caso de uso. Siguiendo el enfoque adoptado, cada caso de uso detallado se corresponde con una clase controladora, encargada de coordinar el flujo de información entre las vistas y los modelos. De este modo, los controladores actúan como intermediarios que reciben las acciones desde las vistas y ejecutan las operaciones necesarias sobre las entidades del sistema.

Por último, el paquete Modelos contiene las entidades del dominio que representan la información gestionada por el sistema. En este caso, la clase Solicitud constituye el elemento central, ya que modela las peticiones realizadas por los usuarios y su evolución. La clase Formulario representa la información adicional que puede ser requerida para completar una solicitud, mientras que la clase Respuesta recoge el resultado del procesamiento de dicha solicitud.

### Diagramas de Colaboración

#### Enviar Solicitud

| Diagrama | Código Fuente |
|----------|---------------|
|![EnviarSolicitud](./DdC/imagen/EnviarSolicitud.png)|[Ver Código](./DdC/codigo/EnviarSolicitud.puml)

#### Recibir Respuesta

| Diagrama | Código Fuente |
|----------|---------------|
|![RecibirRespuesta](./DdC/imagen/RecibirRespuesta.png)|[Ver Código](./DdC/codigo/RecibirRespuesta.puml)

#### Ver Solicitudes Pendientes

| Diagrama | Código Fuente |
|----------|---------------|
|![RecibirRespuesta](./DdC/imagen/VerSolicitudesPendientes.png)|[Ver Código](./DdC/codigo/VerSolicitudesPendientes.puml)

#### Actualizar Estado

| Diagrama | Código Fuente |
|----------|---------------|
|![RecibirRespuesta](./DdC/imagen/ActualizarEstado.png)|[Ver Código](./DdC/codigo/ActualizarEstado.puml)

#### Completar Formulario

| Diagrama | Código Fuente |
|----------|---------------|
|![RecibirRespuesta](./DdC/imagen/CompletarFormulario.png)|[Ver Código](./DdC/codigo/CompletarFormulario.puml)
