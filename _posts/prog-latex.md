# Programando en $$\LaTeX$$

De una u otra forma siempre es posible beneficiarse de la programación en la generación de textos. Ya sea por la estandarización de la salida, rapidez de edición o simplemente por la coherencia de símbolos.

Hay muchas formas de programar en $$\LaTeX$$. La primera de ellas es simplemente usar $$\LaTeX$$ como un "lenguaje de programación". Este lenguaje es, sorprendentemente, suficiente para realizar cualquier tarea, es decir, es Turing completo. La desventaja es que es bastante tedioso.
Por ejemplo, si creamos un comando `\newcommand{\bla}{foo}` y luego lo usamos en algún lugar donde queremos usar sólo letras mayúsculas `\uppercase{bla \bla}` veremos que la salida es `BLA foo` ¡`foo`no está en mayúsculas! Esto se debe al orden de expansión de macros de $$\TeX$$ y las instrucciones para capitalizar cosas, cuando encuentra una `\` no expande el resto del comando. Este comportamiento es natural ¿cómo sería poner en mayúsculas el símbolo de raíz?
En este ejemplo basta con cambiar el orden de expansión para lo cual existe `\expandafter`. Pero si hacemos cuentas nos daremos cuenta de que si anidamos $$n$$ *tokens* (como si un `\newcommand` aceptara $$n$$ variables o tuviéramos $$n$$ `\newcommand`s anidados) y quisiéramos expandir el $$i$$-ésimo, entonces se necesitan $$2^{n-1}-1$$ `\expandafter`s. Lo cual se vuelve demasiado tedioso.


## $$\LaTeX$$3

De esta forma surge la necesidad de escribir todo este tipo de cosas de manera simple y consistente. Este es uno de los objetivos de la interfaz de programación de $$\LaTeX$$3. La documentación de esta interfaz se puede encontrar [aquí](https://ctan.math.utah.edu/ctan/tex-archive/macros/latex/contrib/l3kernel/interface3.pdf).

Con esta interfaz podemos hacer cualquier tarea pero con una forma un poco más sencilla de escribir. Es imposible tratar de explicar de manera breve cada detalle del [interface 3](https://ctan.math.utah.edu/ctan/tex-archive/macros/latex/contrib/l3kernel/interface3.pdf), así que sólo nos limitamos a dar un par de ejemplos.

Primero, veamos como solucionar el problema de expansión de arriba, pero antes lo más básico del lenguaje. Lo primero es especificar a $$\TeX$$ que usaremos la *interface 3*, es necesario hacer esto ya que se deben usar símbolos con un significado diferente al usual. Para abrir y cerrar este "ambiente de programación" se usa `\ExplSyntaxOn` y `\ExplSyntaxOff`, que prácticamente cambia el *catcode* (*category code*) de algunos símbolos. De esta forma podemos usar
```
\ExplSyntaxOn
\cs_set_eq:NN \my_uppercase:n \uppercase
\exp_args:Nx \my_uppercase:n {bla~\bla}
\ExplSyntaxOff
```

Lo que hace es que primero copia la definición de `\uppercase` en una función que llamamos `\my_uppercase`. Este es el propósito de `\cs_set_eq:NN`. La parte de `cs` indica que definiremos una función, el `set` hace explícito que daremos una definición, `eq` dice que copiaremos la definición de una función conocida y `:NN` significa que pasaremos dos funciones `\my_uppercase:n` y `\uppercase`. Luego tenemos que cambiar el orden de expansión de nuestra función. Para esto haremos una expansión recursiva, es decir, primero se expande lo de adentro del comando y luego el comando. Esto se logra con `\exp_args:Nx`. La `x` específica la expansión recursiva de la función que se pasa como argumento (especificado por `N`). Podemos ver un ejemplo en [esta liga](https://texlive.net/run?%5Cdocumentclass%7Barticle%7D%0A%5Cnewcommand%7B%5Cbla%7D%7Bfoo%7D%0A%5Cbegin%7Bdocument%7D%0AEste%20no%20tiene%20la%20expansi%C3%B3n%20deseada%20%5Cuppercase%7Bbla%20%5Cbla%7D%0A%0AEste%20s%C3%AD:%0A%5CExplSyntaxOn%0A%5Ccs_set_eq:NN%20%5Cmy_uppercase:n%20%5Cuppercase%0A%5Cexp_args:Nx%20%5Cmy_uppercase:n%20%7Bbla~%5Cbla%7D%0A%5CExplSyntaxOff%0A%0A%5Cend%7Bdocument%7D)

Manejar el orden de las expansiones no es lo único que puede hacer esta interfaz. Por ejemplo, también puede hacer cálculos, como el máximo común divisor de dos números.

Para calcular el máximo común divisor se usa el algoritmo de Euclides. Se calcula $$\text{mcd}(a,b)$$ de manera recursiva usando que si $$b=0$$ entonces $$\text{mcd}(a,b)=a$$ y si $$b\ne 0$$ entonces $$\text{mcd}(a,b)=\text{mcd}(b,\text{res}(a,b))$$, donde $$\text{res}(a,b)$$ es el residuo de la división de $$a$$ entre $$b$$. Todo esto se hace con el siguiente código
```
\ExplSyntaxOn
\cs_set:Npn \my_gcd:nn #1#2 {% una función llamada \my_gcd de dos variables
	\group_begin:% encierra la definición en un grupo
	\int_compare:nNnTF {#2} = {0}% revisa si "b" es cero
	     {% si b=0
		\int_set:Nn \l_tmpa_int {#1}% mcd(a,b)=a
	     }
	     {% si b no es 0
		\int_set:Nn \l_tmpa_int {\int_mod:nn {#1}{#2}}% toma el residuo de la division
		\exp_args:Nnx \my_gcd:nn {#2} {\int_use:N \l_tmpa_int}% mcd(b,res(a,b))
	     }
	\group_end:
}
% ponemos todo lo anterior en una función de 2 variables para usar en el texto
\NewDocumentCommand{\mcd}{ mm }{\my_gcd:nn {#1}{#2}
\operatorname{mcd}(#1,#2) = \int_use:N \g_tmpa_int}
\ExplSyntaxOff
```

El código es similar al anterior. En la primera línea `cs` índica que haremos una función, `set` significa que estamos definiendo algo (una función, por lo anterior) y `Npn` dice que pasaremos un comando (algo que empieza con `\`) que será el nombre de la función, `p` significa que daremos una lista de parámetros que recibirá la función (dos en nuestro caso y está indicado por `#1#2`) y, por último, `n` es el cuerpo (o definición) de la función y debe escribirse entre llaves.

La definición se puso dentro de un grupo. Esto fue hecho por `\group_begin:` y `\group_end:`. El resto es la implementación del algoritmo de Euclides en este lenguaje. Los comandos hacen lo que dice su nombre, por ejemplo `int` se refiere a que tomaremos variables en los enteros, `compare` significa que compararemos dos cosas y `mod` es el residuo de la división de dos números (o bien uno módulo el otro). Lo único que no tiene un nombre estándar es `tmpa` que es una variable temporal con "etiqueta" a

Después de haber hecho lo anterior podemos usar `\mcd{a}{b}` en nuestro documento y la salida será $$\mathtrm{mcd}(a,b)=\ldots$$, en lugar de los puntos aparecerá el máximo común divisor de los números. 
