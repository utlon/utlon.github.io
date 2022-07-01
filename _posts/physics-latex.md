---
title: El paquete physics en LaTeX
layout: post
post-image: https://pixabay.com/get/g9b643b83c662207ff3871aad07d43344ffd07a3d82f872a0c8c4014ea11164997afb9409b6feb4451b210454e2d21682b9663f89d7989feee8dfe3aeb4006199b00e348c5b987f59ab01e384995f6b9d_1920.jpg
description: Aunque parece ser un paquete muy útil por los símbolos y operadores que define, no es recomendable usar este paquete en nuestro documentos.
katex: yes
tags:
- LaTeX
---

# Physics
El paquete `physics` parece ser una buena solución para escribir fácilmente textos científicos. Provee muchas construcciones útiles con nombres que son fáciles de recordar. Tiene comandos para notación vectorial, pone los vectores en negritas y define cosas como laplaciano, divergencia, etc. También tiene comandos para derivadas, notación de Dirac y algunas otras cosas.

## Problemas en physics
Entonces, ¿por qué no deberíamos usar el paquete `physics`? Aunque puede facilitarnos la vida hace que se pierda la calidad del documento.

El problema con physics es su implementación. Muchos comandos, en particular los delimitadores, agregan espacios que no deberían y además lo hace de forma
inconsistente. Para ver un ejemplo de este espacio
[da click aquí](https://texlive.net/run?%5Cdocumentclass%7Barticle%7D%0A%5Cusepackage%7Bamsmath,amssymb%7D%0A%5Cusepackage%7Bphysics%7D%0A%5Cfboxsep=0pt%0A%5Cbegin%7Bdocument%7D%0Abla%20%5Cfbox%7B%5C(%5Cket*%7Bx%7D%5C)%7D%20bla%0A%5Cend%7Bdocument%7D), donde se puede ver que la caja que contiene al ket tiene más espacio a la izquierda que a la derecha.

Los $$\TeX$$pertos en [stackexchange](https://tex.stackexchange.com/) opinan que no deberíamos usar este paquete:
>  My best advice is to keep at arm's length from `physics`. <br>
> [Enrico Gregorio](https://tex.stackexchange.com/users/4427/egreg) en [esta liga](https://tex.stackexchange.com/a/470842/140456)

> The package code is also full of spurious spaces. Considering the quality of the implementation, the best way to fix this issue is by not using the `physics` package. <br>
> [Henri Menke](https://tex.stackexchange.com/users/10995/henri-menke)  en [esta liga](https://tex.stackexchange.com/a/453275/140456)

## ¿Qué hacer?
Como es útil tener los comandos de `physics` queremos tenerlos disponibles pero corrigiendo los espacios y otras cosas problemáticas. Entonces, definamos
algunos de los comandos de `physics` de cero para tener algunos ejemplos y se pueda completar la lista de comandos como ejercicio. Como `physics` usa
$$\LaTeX3$$, entonces los definiremos usando este lenguaje. Además, en algunos casos haremos uso de `mathtools`, ya que también usa $$\LaTeX3$$ y será más fácil hacer algunas definiciones con este paquete.

## Delimitadores
Algunas cosas como el valor absoluto y la notación de Dirac son delimitadores, para estos usamos `mathtools`. Por ejemplo, para definir el valor absoluto usamos el siguiente código
```tex
...
\usepackage{mathtools}
\DeclarePairedDelimiter{\abs}{\lvert}{\rvert}
```

Con esto podemos usar `\abs{x}` que resultará en \\(\lvert x\rvert\\). Además `\DeclarePairedDelimiter` crea una versión con estrella para que los delimitadores crezcan según el contenido. Para ver esta diferencia escribimos `\abs{\frac{1}{2}}` y `\abs*{\frac{1}{2}}` que resultan, respectivamente, en

$$|\frac{1}{2}| \quad \text{y} \quad \left\lvert\frac{1}{2}\right\rvert.$$

De la misma forma se pueden hacer los bra y los ket de la notación de Dirac:
```tex
\DeclarePairedDelimiter{\bra}{\langle}{\rvert}% definición de bra
\DeclarePairedDelimiter{\ket}{\lvert}{\rangle}% definición de ket
```

Uno un poco más complejo es el braket, ya que debe se le deben pasar dos argumentos obligatorios y `\DeclarePairedDelimiter` sólo admite uno. Como esto hubiera una desventaja muy grande en el comando (y el paquete) se creó `\DeclarePairedDelimiterX` para que acepte muchos argumentos, como un `\newcommand`. Así, podemos definir el braket con
```tex
\DeclarePairedDelimiterX{\braket}[2]{\langle}{\rangle}{#1\,\delimsize\vert\,\mathopen{}#2}
```

Ahora la notación de Dirac tiene los comandos del paquete `physics` con la salida esperada. En la siguiente tabla podemos ver el resultado de cada uno de los comandos que definimos.

| comando         | resultado              |
| :-------------  | :-------------         |
| `\bra{x}`       | $$\langle x\rvert$$  |
| `\ket{x}`       | $$\lvert x\rangle$$  |
| `\braket{x}{y}` | $$\langle x\,\vert\,\mathopen{}y\rangle$$ |

Con estos delimitadores con argumentos obligatorios es posible definir muchos comandos del paquete `physics`.


## Derivadas, operadores y vectores
Ahora veamos algunos ejemplos de cómo están definidos los comandos para derivadas en `physics`. Primero la lista de operadores es muy fácil de replicar, para esto usamos el paquete `amsmath` que es cargado por `mathtools`. Así, no es necesario cargar más paquetes que en la sección anterior:
```tex
...
\usepackage{mathtools}
\DeclareMathOperator{\rank}{rank}
```

Con esto ya podemos usar el operador rango `\rank M`  y se vuelve $$\mathrm{rank}\,M$$.

Si sólo nos interesa que los vectores estén en negritas, entonces es algo fácil de conseguir. Haremos un ejemplo usando $$\LaTeX3$$ que está instalado en la base y no requiere ningún paquete adicional, pero usaremos el paquete `bm` para obtener las negritas. Así, los vectores están definidos con
```tex
...
\usepackage{bm}
\NewDocumentCommand{\vb}{m}{\bm{#1}}
```
donde el amrgumento `m` dice que nuestro comando llevará un argumento obligatorio (*mandatory*). `physics` tiene una variante interesante del comando, cuando se pone una estrella hace un cambio de alfabeto. Esto se hace de la siguiente manera:
```tex
...
\usepackage{bm}
\NewDocumentCommand{\vb}{s m}{
  \IfBooleanTF{#1}{\bm{#2}}{\bm{\mathrm{#2}}}
}
```
En este ejemplo `s` (de *star*) es verdadero/falso para una estrella y el comando `\IfBooleanTF{1}{2}{3}` revisa el argumento 1, si es verdadero hace 2 y si es falso hace 3.

Finalmente, las derivadas tienen una definición más compleja ya que usan más tipos de argumentos en los `\NewDocumentCommand`. Además hay una inconsistencia de notación como nota Enrico Gregorio en el link de su comentario. Un intento de hacer la definición de derivada y tener una notación consistente es, por ejemplo:
```tex
\documentclass{article}
\usepackage{mathtools}
\usepackage{xfrac}

\NewDocumentCommand{\dd}{O{} m}
{
  \IfNoValueTF{#2}
    { \mathrm{d}^{#1} }
    { \mathrm{d}^{#1}#2 }
}

\NewDocumentCommand{\dv}{ s O{} m m }
{
  \IfBooleanTF{#1}
    { \let\myfrac\sfrac }
    { \let\myfrac\frac }
    \IfNoValueTF{#3}
      { \myfrac{\mathrm{d}^{#2}}{#4} }
      { \myfrac{\mathrm{d}^{#2}{#3}}{#4} }
}

\begin{document}
Test de derivadas: $\dd{}$ vs $\dd{x}$ vs $\dd[3]{x}$ vs $\dd[n]$

Test en modo in-line $\dv*[2]{f}{x}$ vs $\dv*{f}{x}$ vs $\dv*[2]{}{x}$ vs $\dv*{}{x}$

Test en modo in-line con fracciones de texto $\dv[2]{f}{x}$ vs $\dv{f}{x}$ vs $\dv[2]{}{x}$ vs $\dv{}{x}$

Test en modo display con fracciones en display
\[ \dv[2]{f}{x} \quad \dv{f}{x} \quad \dv[2]{}{x} \quad \dv{}{x} \]

\end{document}
```

Puedes probar este documento en [TeXLive](https://texlive.net/run), que por alguna razón (¿un bug?) cambia los comandos cuando copio el url.


## Más derivadas
El paquete `physics` tiene definidas más derivadas y diferenciales con un comportamiento más complejo del que hemos descrito arriba. Si se quieren obtener estas funciones y algunas más lo recomendable es usar el paquete `derivative` con el cual incluso es posible definir nuevas diferenciales. El manual incluye muchos ejemplos que describen las funciones del paquete. Para acceder al manual puedes escribir
```
texdoc derivative
```
en la terminal, si tienes una instalación local de $$\LaTeX$$ o buscar "derivative" en [texdoc.org](https://texdoc.org/index.html), donde obtendrás el [manual](https://texdoc.org/serve/derivative/0) de la versión actual del paquete, que podría ser diferente a la instalada en [overleaf](https://www.overleaf.com/).


## Conclusión
Es relativamente fácil definir los comandos del paquete `physics` por nuestra propia cuenta. Además de ser un buen ejercicio para definir funciones con `\NewDocumentCommand` es posible eliminar las inconsistencias de espacio y de sintaxis que han causado mucha crítica al paquete.

Una vez que se hayan hecho las definciones de los comandos que se desean, se pueden escribir en un archivo separado con extensión `.sty` y usarlo como un paquete que puedes instalar en una distribución local siguiendo las instrucciones en [esta respuesta](https://tex.stackexchange.com/a/1138).

Además, la parte más compleja de recrear de `physics` es lo que tiene que ver con derivadas. Para no tener que hacer cosas demasiado complejas tan pronto una posibilidad es usar el paquete [derivative](https://texdoc.org/serve/derivative/0) que usa la misma sintaxis que `physics` en esta parte y permite definir nuevo tipos de derivada.

De esta manera puedes usar otros paquetes útiles, como `siunitx`, sin generar errores o *warnings*. 
