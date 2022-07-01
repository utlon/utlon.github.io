---
title: Microtipografía en LaTeX
layout: post
post-image: https://upload.wikimedia.org/wikipedia/commons/a/a0/Buchdrucker-1568.png
description: La microtipografía es un aspecto importante para la creación de textos de calidad. En este artículo mencionamos algunos de éstos y cómo se implementan en LaTeX.
katex: yes
tags:
- LaTeX
---

## Introducción
En $$\LaTeX$$ todas las cosas que escribimos se vuelven cajas con algún contenido, por ejemplo una letra. Luego estas cajas se van pegando para formar
palabras o sucesiones de símbolos. Finalmente, $$\LaTeX$$ tiene un sistema de penalizaciones, para evitar salirse del margen, que usa para saber dónde cortar
una línea y empezar otra.[^1] Aunque parece ser un sistema bastante simple, pegado de cajas y penalizaciones, el algoritmo de $$\LaTeX$$ para cortes de línea hoy en día sigue siendo
el *gold standard* de los procesadores de texto.

Las capacidades de $$\LaTeX$$ respecto a la formación de texto incluyen la justificación de texto, la separación silábica y el *kerning*.[^1] Esta es uno lo de los aspectos que forman la [microtipografía](https://en.wikipedia.org/wiki/Microtypography) para ayudar a mejorar el aspecto y la legibilidad de un texto.

En este artículo veremos cómo cuidar esa parte de la formación de un texto con ayuda de algunos paquetes de $$\LaTeX$$.


## Microtype
Como su  nombre lo índica, el paquete `microtype` se encargará de realizar casi todos los aspectos microtipográficos del texto. Por lo tanto, describiremos algunas de sus funciones y cómo se usan.

#### Cortes de palabra
Algunas funciones son cargadas por defecto y la diferencia es evidente en el texto. Por ejemplo, cuando se cortan líneas no es tan recomendable que se
generen tantos cortes de palabra, sobretodo en renglones consecutivos. Si cargamos el paquete `\usepackage{microtype}` en el mismo documento podremos ver
que hay menos cortes de palabra. En el [ejemplo 1](https://texlive.net/run?%5Cdocumentclass%7Barticle%7D%0A%5Cusepackage%7Blipsum%7D%0A%5Cusepackage%7Bshowframe%7D%0A%5Crenewcommand*%5CShowFrameColor%7B%5Ccolor%7Bgray!30%7D%7D%0A%5Cbegin%7Bdocument%7D%0A%5Clipsum%0A%5Cend%7Bdocument%7D) hay un texto de página y media donde
se generaron 6 cortes de palabra. Si cargamos `microtype` en el mismo documento se obtiene la misma página y media pero ahora no hay ningún corte de palabra,
como se muestra en el [ejemplo 2](https://texlive.net/run?%5Cdocumentclass%7Barticle%7D%0A%5Cusepackage%7Blipsum%7D%0A%5Cusepackage%7Bmicrotype%7D%0A%5Cusepackage%7Bshowframe%7D%0A%5Crenewcommand*%5CShowFrameColor%7B%5Ccolor%7Bgray!30%7D%7D%0A%5Cbegin%7Bdocument%7D%0A%5Clipsum%0A%5Cend%7Bdocument%7D).

#### Prutrusión de caracteres (Protrusion)
En los ejemplos del párrafo anterior usamos el paquete `showframe` para mostrar otra aspecto de microtipografía añadido por defecto con el paquete `microtype`. Este aspecto se llama protrusión de caracteres y consiste en permitir la salida de caracteres pequeños al margen. En el ejemplo 2 se puede ver que los puntos y las comas salen de la caja de texto e invaden el margen. Al hacer esta invasión al margen se genera una caja de texto más "pareja". En el ejemplo 1 se puede notar que las comas, puntos y rayas, hacen que el texto no pareciera estar bien justificado.

#### Rastreado (Tracking)
Con esta opción se puede reducir o aumentar el espacio entre letras. Por defecto no está cargada por el paquete, así que para usarla hay que escribir explícitamente que queremos usar la opción
```
\usepackage[tracking=true]{microtype}
```

Esta opción ayudará a mejorar el espacio entre palabras, evitar cortes de palabra y eventualmente puede ser útil para evitar los `underfull` que aparecen cuando una línea que debería usar todo un renglón no lo hace.

#### Expansión de fuente (Expansion)
La expansión permite usar diferentes longitudes para el mismo símbolo. Al igual que el rastreado, permite mejorar la formación de líneas de texto. `microtype` intenta hacer expansión por defecto, así que no es necesario especificar su uso con una opción.

#### Comentario final
Hay más aspectos que `microtype` toma en cuenta para mejorar el texto que no han sido cubiertos. Éstos son *kerning* y *spacing*. La primera es para mejorar el *kerning* que ya hace $$\LaTeX$$ y la segunda no necesariamente mejora el texto. Por lo tanto, no veremos estas opciones que además están restringidas a `pdflatex`.

Una opción que también es útil es `babel`. Si se está usando `babel` o `polyglossia` para el manejo de idiomas, `microtype` usará los patrones del idioma principal para hacer su trabajo.

Finalmente, aunque `microtype` tiene algunos valores puestos en `true` por defecto a veces es útil tener una lista de los que estamos usando. Así, una forma de cargar `microtype` con sus opciones es:
```
\usepackage[protrusion=true,expansion,tracking=true]{microtype}
```


## Lua-typo
Algunos otros aspectos microtipográficos de un texto consisten en evitar los siguientes:
- cortes de palabra consecutivos,
- viudas: cuando un párrafo termina con una línea en la siguiente página donde inicio, éstas parecen en la parte de arriba de la página,
- huérfanas: cuando un párrafo sólo tiene la primer línea en una página y el resto en la siguiente, aparecen en la parte de abajo de la página,
- homeoarchy: cuando dos o más renglones consecutivos inician o terminan con la misma palabra o repiten una parte común de algunas palabras.

Los problemas listados arriba son, en general, baste difícil de detectar y corregir. En $$\LaTeX$$ se necesita usar `Lua` para poder detectarlos de manera automática, pero una corrección automática es un problema mucho más difícil.

Un paquete para la detección automática de los problemas mencionados antes es `lua-typo`. Como su nombre lo sugiere, éste usa `Lua` y así el documento debe ser
compilado usando $$Lua\LaTeX$$. Funciona marcando con colores los problemas del texto y el autor deberá hacer alguna corrección manual para evitarlos
(tratar de decir la misma idea con palabras diferentes, por ejemplo). Como colorea el texto no debe estar activo en la compilación final del documento. 
Por último, es posible especificar qué problemas queremos que detecte. Lo más fácil es pedirle que encuentre todos los posibles problemas, en este caso se escribe:
```
\usepackage[All]{lua-typo}
```

[^1]: Una traducción de *kerning* que parece correcta en este contexto es "interletrado". Este es un proceso de espaciado de letras que se lleva a cabo antes de la microtipografía.
