---
layout: post
title: "Mi primera especificación en TLA+"
comments: true
categories: tla
---

[TLA+](https://lamport.azurewebsites.net/tla/tla.html) es un lenguaje de especificación formal. En otras palabras, se trata de un lenguaje de alto nivel que permite modelar algoritmos y sistemas, para posteriormente verificar programáticamente la ausencia de errores en dichos modelos.

Su principal desarrollador es [Leslie Lamport](https://es.wikipedia.org/wiki/Leslie_Lamport), sin desmerecer al resto de la comunidad que hay tras dicho lenguaje. Por supuesto es de código abierto, bajo licencia MIT. 

He empezado a estudiar en serio este lenguaje, dada su utilidad y sus aplicaciones en el desarrollo seguro de software.

Una pregunta recurrente son las ventajas de un lenguaje de programación formal, como TLA+, frente a las pruebas unitarias clásicas.
Mientras que las pruebas unitarias (y otros tipos de pruebas) solo verifican el funcionamiento del programa para unos valores de entrada (escenario) determinado, un lenguaje formal verifica el funcionamiento y determinadas restricciones para todos los posibles estados del programa (¡incluso si se trata de programación concurrente!).

[Este artículo](https://lamport.azurewebsites.net/tla/formal-methods-amazon.pdf) muestra un caso de éxito interesante de aplicación de TLA+ en el desarrollo de componentes de AWS.

Como parte del proceso de aprendizaje me planteé modelar la siguiente especificación simple por mí mismo.

Sea la gestión de turnos de un hospital, donde:
- El hospital tiene una capacidad máxima de 10 personas.
- La atención de las personas se clasifica en 5 niveles de prioridad.
- Una persona puede acudir al hospital sin prioridad previa.
- En el caso de que una persona venga en ambulancia o a través de otro hospital, traerá un nivel de prioridad previa asignado.
- Si el hospital excede su capacidad, clasificará al paciente con una prioridad y será derivado a otro hospital.

El hospital dispone de 5 médicos que organizan su trabajo según la siguiente lista de prioridad:
1. Atender a los pacientes con prioridad 4 o 5.
2. Clasificar pacientes que no tengan un nivel de prioridad asignado.
3. Atender a los pacientes con nivel de prioridad 3 o inferior.
4. En caso de que el hospital se encuentre desbordado, se dará prioridad a la clasificación de pacientes y su derivación hasta volver a la capacidad máxima.

En el momento de empezar a modelar la especificación, pensé que era correcta.

Sin embargo, al ejecutar una primera versión del modelo, TLC (el verificador de modelos de TLA+) reportó un error.
No había tenido en cuenta la posibilidad de que el hospital estuviese desbordado y todos los pacientes tuviesen prioridad asignada (es decir, no hay pacientes sin clasificar).

Para corregirlo, modifiqué el modelo para que en ese caso se atienda a los pacientes de la misma forma que si el hospital no estuviese desbordado.

Este es un ejemplo claro del tipo de fallos que TLA+ puede detectar.

He subido la especificación completa al siguiente repositorio de GitHub: [https://github.com/jesmg/TLAplus-hospital](https://github.com/jesmg/TLAplus-hospital).
