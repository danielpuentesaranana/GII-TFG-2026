Debido a todo lo comentado en los otros apartados, la solución propuesta consiste en diseñar una arquitectura basada en agentes de Inteligencia Artificial con ADK Google utilizando el modelo de OpenAI, para que apoye distintas fases del proceso de testing de QA de CIC Consulting Informático.

La idea central es estructurar el flujo de trabajo en tareas especializadas. 

De este modo, un agente puede encargarse de analizar el DRF (Documento Requisitos Funcionales) y el DDS (Documentos Diseño Sistema) y con ellos extraer requisitos o casos de uso relevantes; otro puede transformar esa información a un plan de pruebas, como Gherkin (Scenarios (Given,When,And,Then); y otro puede coger esos planes de prueba en dicho lenguaje para inyectarlos dentro KiwiTCMS a través de su API.

La propuesta se plantea como un prototipo controlado, de manera que sea posible comprobar su viabilidad técnica y valorar su utilidad dentro de un contexto real.
