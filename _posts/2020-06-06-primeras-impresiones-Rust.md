---
layout: post
title: "Primeras impresiones con Rust"
comments: true
categories: rust
---

Hace tiempo venía escuchando maravillas sobre [Rust](https://www.rust-lang.org/), un nuevo lenguaje de bajo nivel enfocado no solo en el rendimiento, sino también en hacer software confiable. Fue esto último lo que despertó mi interés, ya que no me entraba en la cabeza como Rust podría dar lugar a aplicaciones más fiables que los lenguajes de más alto nivel, evitando problemas habituales como desbordamientos de pila, punteros nulos....  

Así que empecé investigando un poco, y rápidamente acabé leyendo el libro *The Rust Programming Language*. Es el libro de introducción a Rust por excelencia, y se encuentra [disponible online de forma gratuita](https://doc.rust-lang.org/book/title-page.html).  

Tras haberlo terminado y dedicar unas pocas horas de programación con Rust, debo decir que me ha sorprendido gratamente. Efectivamente tiene un rendimiento muy bueno, lo que me ha resultado ideal para desarrollar algunos **MICRO**servicios. A modo de ejemplo, [he publicado en GitHub](https://github.com/jesmg/rust-caesar-api) un microservicio que expone dos APIs REST para realizar cifrado/descifrado Cesar.  

Rust introduce dos conceptos que pueden ser complejos de entender al principio, pero sin embargo resultan muy útiles: propiedad y tiempos de vida. Estas dos funcionalidades permiten asegurarse, en tiempo de compilación, de que las variables y las referencias a variables se utilizan de forma adecuada. Así se evitan problemas asociados a los lenguajes de este tipo, como pueden ser referencias nulas, leaks de memoria, escrituras concurrente en una misma posición de memoria....  

El uso correcto de variables y referencias no es lo único que comprobará el compilador, señalando otras malas prácticas y obligando a su corrección para poder llegar a ejecutar el código. Es gracias a esta funcionalidad que podemos darnos cuenta de distintos errores en cuanto los introducimos.  

Así mismo, Rust tiene un [equipo de trabajo](https://rust-lang-nursery.github.io/wg-verification/) cuyo objetivo es verificar con métodos formales la implementación del lenguaje, asegurando su máxima fiabilidad.  

En conclusión, me quedo con una sensación muy buena de Rust, habiéndose ganado espacio como uno de mis lenguajes predilectos.