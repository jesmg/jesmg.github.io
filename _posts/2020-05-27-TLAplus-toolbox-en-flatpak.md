---
layout: post
title: "Flatpakeando el TLA+ Toolbox"
comments: true
categories: tla
---

En las últimas entradas he hablado sobre el lenguaje (o mejor dicho, la pila de herramientas) de verificación formal TLA+. Se trata de una tecnología que, si bien aún no ha sido ampliamente adoptada, merece la pena apostar por ella dados sus [grandes beneficios](https://www.hillelwayne.com/post/augmenting-agile/).

Hace unas semanas aporté mi grano de arena a favor de la adopción de TLA+, creando un paquete de Flatpak con las herramientas principales (el llamado TLA+ Toolbox).

¿Por qué Flatpak?

Hasta hace poco las opciones de instalación del TLA+ Toolbox eran las más clásicas: Descargar el .rpm o .dev del [repositorio oficial](https://github.com/tlaplus/tlaplus/releases), o directamente los binarios y archivos necesarios para la ejecución.

Flatpak es un sistema de paquetería independiente de la distribución utilizada, fácil de usar, y quizás lo más importante: [ejecuta la aplicación en un sandbox](https://flatpak.org/faq/) con todas sus dependencias. El tamaño de las instalaciones aumentará, a cambio de las ventajas derivadas de que la aplicación se ejecute en un entorno con las condiciones que los desarrolladores determinen.

Preparar el paquete no ha sido complicado, y ya [se encuentra disponible en el store oficial de Flatpak](https://flathub.org/apps/details/org.lamport.tla.toolbox). 

La aplicación se distribuye bajo un entorno Freedesktop. Actualmente descarga las releases del repositorio oficial y simplemente las instala. Esto no es la mejor práctica a la hora de empaquetar aplicaciones en Flatpak, siempre que pueda evitarse. Aún hay trabajo futuro y el repositorio se encuentra abierto a contribuciones. 

Las principales tareas a abordar son:
- Construir el paquete de TLA+ Toolbox a partir del código fuente, y no de una release.
- Instalar un backend para TLAPS (TLA+ Proof System).
- Instalación de la aplicación pdflatex en el paquete, para poder exportar especificaciones escritas en TLA+ a documentos PDF. Quizás podría hacerse instalando todo el entorno de [texlive](https://github.com/flathub/org.freedesktop.Sdk.Extension.texlive), pero esto incrementaría enormemente el peso del paquete final.