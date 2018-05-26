# Expresiones Regulares

{{quote {author: "Jamie Zawinski", chapter: true}

Algunas personas, cuando confrontadas con un problema, piensan 'Ya sé, usaré
expresiones regulares.' Ahora tienen dos problemas.

quote}}

{{index "Zawinski, Jamie"}}

{{if interactive

{{quote {author: "Master Yuan-Ma", title: "The Book of Programming", chapter: true}

Yuan-Ma dijo: 'Cuando cortas contra el grano de la madera, mucha
fuerza se necesita. Cuando programas contra el grano del problema,
mucho código se necesita.

quote}}

if}}

{{figure {url: "img/chapter_picture_9.jpg", alt: "A railroad diagram", chapter: "square-framed"}}}

{{index evolution, adoption, integration}}

Las ((herramientas)) y técnicas de la programación sobreviven y se propagan de
una forma caótica y evolutiva. No siempre son los bonitas o las brillantes
las que ganan, sino más bien las que funcionan lo suficientemente bien
dentro del nicho correcto o que sucede se integran con otra pieza exitosa de
tecnología.

{{index "domain-specific language"}}

En este capítulo, discutiré una de esas herramientas, _((expresiones regulare))s_.
Las expresiones regulares son una forma de describir ((patrones)) en datos
de tipo string. Estas forman un lenguaje pequeño e independiente que es parte de
JavaScript y de muchos otros lenguajes y sistemas.

{{index [interface, design]}}

Las expresiones regulares son terriblemente incómodas y extremadamente útiles.
Su sintaxis es críptica, y la ((interfaz)) de programación que JavaScript
proporciona para ellas es torpe. Pero son una poderosa ((herramienta))  para
inspeccionar y procesar cadenas. Entender apropiadamente a las expresiones
regulares te hará un programador más efectivo.

## Creando una expresión regular

{{index ["regular expression", creation], "RegExp class", "literal expression", "slash character"}}

Una expresión regular es un tipo de objeto. Puede ser construido
con el constructor `RegExp` o escrito como un valor literal al
envolver un patrón en caracteres de barras diagonales (`/`).

```
let re1 = new RegExp("abc");
let re2 = /abc/;
```

Ambos objetos de expresión regular representan el mismo
((patrón)): un carácter _a_ seguido por una _b_ seguida de una _c_.

{{index "backslash character", "RegExp class"}}

Cuando se usa el constructor `RegExp`, el patrón se escribe como un
string normal, por lo que las reglas habituales se aplican a las barras
invertidas.

{{index ["regular expression", escaping], [escaping, "in regexps"], "slash character"}}

La segunda notación, donde el patrón aparece entre caracteres de barras diagonales,
trata a las barras invertidas de una forma diferente. Primero, dado que una
barra diagonal termina el patrón, tenemos que poner una barra invertida antes
de cualquier barra diagonal que queremos sea _parte_ del patrón. En adición,
las barras invertidas que no sean parte de códigos especiales de caracteres
(como `\n`) seran _preservadas_, en lugar de ignoradas, ya que están en strings, y
cambian el significado del patrón. Algunos caracteres, como los signos de
interrogación pregunta y los signos de adición, tienen significados especiales
en las expresiones regulares y deben ir precedidos por una barra inversa
si se pretende que representen al caracter en sí mismo.

```
let dieciochoMas = /dieciocho\+/;
```

## Probando por coincidencias

{{index matching, "test method", ["regular expression", methods]}}

Los objetos de expresión regular tienen varios métodos. El más simple
es `test` ("probar"). Si le pasas un string, retornar un ((Booleano))
diciéndote si el string contiene una coincidencia del patrón en la
expresión.

```
console.log(/abc/.test("abcde"));
// → true
console.log(/abc/.test("abxde"));
// → false
```

{{index pattern}}

Una ((expresión regular)) que consista solamente de caracteres no especiales
simplemente representara esa secuencia de caracteres. Si _abc_ ocurre
en cualquier parte del string con la que estamos probando (no solo al comienzo),
`test` retornara `true`.

## Conjuntos de caracteres

{{index "regular expression", "indexOf method"}}

Averiguar si un string contiene _abc_ bien podría hacerse con una llamada a
`indexOf`. Las expresiones regulares nos permiten expresar ((patrones)) más
complicados.

Digamos que queremos encontrar cualquier ((número)). En una expresión regular,
poner un ((conjunto)) de caracteres entre corchetes hace que esa parte de la
expresión coincida con cualquiera de los caracteres entre los corchetes.

Ambas expresiones coincidiran con todas los strings que contengan un ((dígito)):

```
console.log(/[0123456789]/.test("en 1992"));
// → true
console.log(/[0-9]/.test("en 1992"));
// → true
```

{{index "dash character"}}

Dentro de los corchetes, un guion (`-`) entre dos caracteres puede ser
utilizado para indicar un ((rango)) de caracteres, donde el orden es
determinado por el número ((Unicode)) del carácter. Los caracteres 0 a 9
estan uno al lado del otro en este orden (códigos 48 a 57), por lo que
`[0-9]` los cubre a todos y coincide con cualquier ((dígito)).

{{index whitespace, "alphanumeric character", "period character"}}

Un numero de caracteres comunes tienen sus propios atajos incorporados.
Los dígitos son uno de ellos: `\d` significa lo mismo que `[0-9]`.

{{index "newline character"}}

{{table {cols: [1, 5]}}}

| `\d`    | Cualquier caracter ((dígito))
| `\w`    | Un caracter alfanumérico
| `\s`    | Cualquier carácter de espacio en blanco (espacio, tabulación, nueva línea y similar)
| `\D`    | Un caracter que _no_ es un dígito
| `\W`    | Un caracter no alfanumérico
| `\S`    | Un caracter que no es un espacio en blanco
| `.`     | Cualquier caracter a excepción de una nueva línea

Por lo que podrías coincidir con un formato de ((fecha)) y ((hora)) como 30-01-2003
15:20 con la siguiente expresión:

```
let fechaHora = /\d\d-\d\d-\d\d\d\d \d\d:\d\d/;
console.log(fechaHora.test("30-01-2003 15:20"));
// → true
console.log(fechaHora.test("30-jan-2003 15:20"));
// → false
```

{{index "backslash character"}}

Eso se ve completamente horrible, no? La mitad de la expresión son barras invertidas,
produciendo un ruido de fondo que hace que sea difícil detectar el ((patrón))
real que queremos expresar. Veremos una versión ligeramente mejorada de esta
expresión [más tarde](regexp#date_regexp_counted).

{{index [escaping, "in regexps"], "regular expression", set}}

Estos códigos de barra invertida también pueden usarse dentro de ((corchetes)).
Por ejemplo, `[\d.]` representa cualquier dígito o un carácter de punto.
Pero el punto en sí mismo, entre corchetes, pierde su significado especial.
Lo mismo va para otros caracteres especiales, como `+`.

{{index "square brackets", inversion, "caret character"}}

Para _invertir_ un conjunto de caracteres, es decir, para expresar que deseas
coincidir con cualquier carácter _excepto_  con los que están en el
conjunto—puedes escribir un carácter de intercalación (`^`) después del
corchete de apertura.

```
let noBinario = /[^01]/;
console.log(noBinario.test("1100100010100110"));
// → false
console.log(noBinario.test("1100100010200110"));
// → true
```

## Repitiendo partes de un patrón

{{index ["regular expression", repetition]}}

Ya sabemos cómo hacer coincidir un solo dígito. Qué pasa si queremos hacer
coincidir un número completo—una ((secuencia)) de uno o más ((dígito))s?

{{index "plus character", repetition, "+ operator"}}

Cuando pones un signo más (`+`) después de algo en una expresión regular,
este indica que el elemento puede repetirse más de
una vez. Por lo tanto, `/\d+/` coincide con uno o más caracteres de dígitos.

```
console.log(/'\d+'/.test("'123'"));
// → true
console.log(/'\d+'/.test("''"));
// → false
console.log(/'\d*'/.test("'123'"));
// → true
console.log(/'\d*'/.test("''"));
// → true
```

{{index "* operator", asterisk}}

La estrella (`*`) tiene un significado similar pero también permite que el
patrón coincida cero veces. Algo con una estrella después de el nunca evitara un
patrón de coincidirlo—este solo coincidirá con cero instancias si no
puede encontrar ningun texto adecuado para coincidir.

{{index "British English", "American English", "question mark"}}

Un signo de interrogación hace que alguna parte de un patrón sea _((opcional))_,
lo que significa que puede ocurrir cero o mas veces. En el siguiente ejemplo,
el carácter _h_ está permitido, pero el patrón también retorna verdadero
cuando esta letra no esta.

```
let reusar = /reh?usar/;
console.log(reusar.test("rehusar"));
// → true
console.log(reusar.test("reusar"));
// → true
```

{{index repetition, "curly braces"}}

Para indicar que un patrón deberia ocurrir un número preciso de veces, usa
llaves. Por ejemplo, al poner `{4}` después de un elemento, hace que requiera
que este ocurra exactamente cuatro veces. También es posible especificar un
((rango)) de esta manera: `{2,4}` significa que el elemento debe ocurrir al
menos dos veces y como máximo cuatro veces.

{{id date_regexp_counted}}

Aquí hay otra versión del patrón ((fecha)) y ((hora)) que
permite días tanto en ((dígitos)) individuales como dobles, meses y horas.
Es también un poco más fácil de descifrar.

```
let fechaHora = /\d{1,2}-\d{1,2}-\d{4} \d{1,2}:\d{2}/;
console.log(fechaHora.test("30-1-2003 8:45"));
// → true
```

También puedes especificar ((rangos)) de final abierto al usar ((llaves))
omitiendo el número después de la coma. Entonces, `{5,}` significa cinco o más
veces.

## Agrupando subexpresiones

{{index ["regular expression", grouping], grouping}}

Para usar un operador como `*` o `+` en más de un elemento a la vez,
tienes que usar ((paréntesis)). Una parte de una expresión regular que
se encierre entre paréntesis cuenta como un elemento único en cuanto a
los operadores que la siguen están preocupados.

```
let caricaturaLlorando = /boo+(hoo+)+/i;
console.log(caricaturaLlorando.test("Boohoooohoohooo"));
// → true
```

{{index crying}}

El primer y segundo caracter `+` aplican solo a la segunda _o_ en
_boo_ y _hoo_, respectivamente. El tercer `+` se aplica a la totalidad
del grupo `(hoo+)`, haciendo coincidir una o más secuencias como esa.

{{index "case sensitivity", capitalization, ["regular expression", flags]}}

La `i` al final de la expresión en el ejemplo hace que esta expresión regular
sea insensible a mayúsculas y minúsculas, lo que permite que coincida con la
letra mayúscula _B_ en el string que se le da de entrada, asi el
patrón en sí mismo este en minúsculas.

## Coincidencias y grupos

{{index ["regular expression", grouping], "exec method", array}}

El método `test` es la forma más simple de hacer coincidir una
expresión. Solo te dice si coincide y nada más.
Las expresiones regulares también tienen un método `exec` ("ejecutar") que
retorna `null` si no se encontró una coincidencia y retorna un objeto con
información sobre la coincidencia de lo contrario.

```
let coincidencia = /\d+/.exec("uno dos 100");
console.log(coincidencia);
// → ["100"]
console.log(coincidencia.index);
// → 8
```

{{index "index property", [string, indexing]}}

Un objeto retornado por `exec` tiene una propiedad `index` ("indice") que nos
dice _donde_ en el string comienza la coincidencia exitosa. Aparte de eso,
el objeto parece (y de hecho es) un array de strings, cuyo
primer elemento es el string que coincidio—en el ejemplo anterior,
esta es la secuencia de ((dígito))s que estábamos buscando.

{{index [string, methods], "match method"}}

Los valores de tipo string tienen un método `match` que se comporta de
manera similar.

```
console.log("uno dos 100".match(/\d+/));
// → ["100"]
```

{{index grouping, "capture group", "exec method"}}

Cuando la expresión regular contenga subexpresiones agrupadas con
paréntesis, el texto que coincida con esos grupos también aparecerá en
el array. La coincidencia completa es siempre el primer elemento. El siguiente
elemento es la parte que coincidio con el primer grupo (el que abre
paréntesis primero en la expresión), luego el segundo grupo, y
asi sucesivamente.

```
let textoCitado = /'([^']*)'/;
console.log(textoCitado.exec("ella dijo 'hola'"));
// → ["'hola'", "hola"]
```

{{index "capture group"}}

Cuando un grupo no termina siendo emparejado en absoluto (por ejemplo, cuando
es seguido de un signo de interrogación), su posición en el array de salida
sera `undefined`. Del mismo modo, cuando un grupo coincida multiples veces,
solo la ultima coincidencia termina en el array.

```
console.log(/mal(isimo)?/.exec("mal"));
// → ["mal", undefined]
console.log(/(\d)+/.exec("123"));
// → ["123", "3"]
```

{{index "exec method", ["regular expression", methods], extraction}}

Los grupos pueden ser útiles para extraer partes de un string. Si no solo
queremos verificar si un string contiene una ((fecha)) pero también
extraerla y construir un objeto que la represente, podemos envolver
paréntesis alrededor de los patrones de dígitos y tomar directamente la fecha
del resultado de `exec`.

Pero primero, un breve desvío, en el que discutiremos la forma incorporada de
representar valores de fecha y ((hora)) en JavaScript.

## La clase Date ("Fecha")

{{index constructor, "Date class"}}

JavaScript tiene una clase estándar para representar ((fecha))s—o mejor dicho,
puntos en el ((tiempo)). Se llama `Date`. Si simplemente creas un objeto
fecha usando `new`, obtienes la fecha y hora actual.

```{test: no}
console.log(new Date());
// → Mon Nov 13 2017 16:19:11 GMT+0100 (CET)
```

{{index "Date class"}}

También puedes crear un objeto para un tiempo específico.

```
console.log(new Date(2009, 11, 9));
// → Wed Dec 09 2009 00:00:00 GMT+0100 (CET)
console.log(new Date(2009, 11, 9, 12, 59, 59, 999));
// → Wed Dec 09 2009 12:59:59 GMT+0100 (CET)
```

{{index "zero-based counting", [interface, design]}}

JavaScript usa una convención en donde los números de los meses comienzan en
cero (por lo que Diciembre es 11), sin embargo, los números de los días
comienzan en uno. Esto es confuso y tonto. Ten cuidado.

Los últimos cuatro argumentos (horas, minutos, segundos y milisegundos)
son opcionales y se toman como cero cuando no se dan.

{{index "getTime method"}}

Las marcas de tiempo se almacenan como la cantidad de milisegundos desde el
inicio de 1970, en la ((zona horaria)) UTC. Esto sigue una convención
establecida por el "((Tiempo Unix))", el cual se inventó en ese momento.
Puedes usar números negativos para los tiempos anteriores a 1970. Usar el método
`getTime` ("obtenerTiempo") en un objeto fecha retorna este número.
Es bastante grande, como te puedes imaginar.

```
console.log(new Date(2013, 11, 19).getTime());
// → 1387407600000
console.log(new Date(1387407600000));
// → Thu Dec 19 2013 00:00:00 GMT+0100 (CET)
```

{{index "Date.now function", "Date class"}}

Si le das al constructor `Date` un único argumento, ese argumento sera
tratado como un conteo de milisegundos. Puedes obtener el recuento
de milisegundos actual creando un nuevo objeto `Date` y llamando
`getTime` en él o llamando a la función `Date.now`.

{{index "getFullYear method", "getMonth method", "getDate method", "getHours method", "getMinutes method", "getSeconds method", "getYear method"}}

Los objetos de fecha proporcionan métodos como `getFullYear`
("obtenerAñoCompleto"), `getMonth` ("obtenerMes"), `getDate` ("obtenerFecha"),
`getHours` ("obtenerHoras"), `getMinutes` ("obtenerMinutos"), y `getSeconds`
("obtenerSegundos") para extraer sus componentes. Además de `getFullYear`,
también existe `getYear` ("obtenerAño"), que te da como resultado un valor
de año de dos dígitos bastante inútil (como `93` o `14`).

{{index "capture group", "getDate function"}}

Al poner ((paréntesis)) alrededor de las partes de la expresión en las que
estamos interesados, ahora podemos crear un objeto de fecha a partir de un
string.

```
function obtenerFecha(string) {
  let [_, dia, mes, año] =
    /(\d{1,2})-(\d{1,2})-(\d{4})/.exec(string);
  return new Date(año, mes - 1, dia);
}
console.log(obtenerFecha("30-1-2003"));
// → Thu Jan 30 2003 00:00:00 GMT+0100 (CET)
```

{{index destructuring, "underscore character"}}

La vinculación `_` (guion bajo) es ignorada, y solo se usa para omitir el
elemento de coincidencia completa en el array retornado por `exec`.

## Word and string boundaries

{{index matching, ["regular expression", boundary]}}

Unfortunately, `getDate` will also happily extract the nonsensical
date 00-1-3000 from the string `"100-1-30000"`. A match may happen
anywhere in the string, so in this case, it'll just start at the
second character and end at the second-to-last character.

{{index boundary, "caret character", "dollar sign"}}

If we want to enforce that the match must span the whole string, we
can add the markers `^` and `$`. The caret matches the start of the
input string, whereas the dollar sign matches the end. So, `/^\d+$/`
matches a string consisting entirely of one or more digits, `/^!/`
matches any string that starts with an exclamation mark, and `/x^/`
does not match any string (there cannot be an _x_ before the start of
the string).

{{index "word boundary", "word character"}}

If, on the other hand, we just want to make sure the date starts and
ends on a word boundary, we can use the marker `\b`. A word boundary
can be the start or end of the string or any point in the string that
has a word character (as in `\w`) on one side and a nonword character
on the other.

```
console.log(/cat/.test("concatenate"));
// → true
console.log(/\bcat\b/.test("concatenate"));
// → false
```

{{index matching}}

Note that a boundary marker doesn't match an actual character. It just
enforces that the regular expression matches only when a certain
condition holds at the place where it appears in the pattern.

## Choice patterns

{{index branching, ["regular expression", alternatives], "farm example"}}

Say we want to know whether a piece of text contains not only a number
but a number followed by one of the words _pig_, _cow_, or _chicken_,
or any of their plural forms.

We could write three regular expressions and test them in turn, but
there is a nicer way. The ((pipe character)) (`|`) denotes a
((choice)) between the pattern to its left and the pattern to its
right. So I can say this:

```
let animalCount = /\b\d+ (pig|cow|chicken)s?\b/;
console.log(animalCount.test("15 pigs"));
// → true
console.log(animalCount.test("15 pigchickens"));
// → false
```

{{index parentheses}}

Parentheses can be used to limit the part of the pattern that the pipe
operator applies to, and you can put multiple such operators next to
each other to express a choice between more than two alternatives.

## The mechanics of matching

{{index ["regular expression", matching], [matching, algorithm], searching}}

Conceptually, when you use `exec` or `test` the regular expression
engine looks for a match in your string by trying to match the
expression first from the start of the string, then from the second
character, and so on until it finds a match or reaches the end of the
string. It'll either return the first match that can be found or fail
to find any match at all.

{{index ["regular expression", matching], [matching, algorithm]}}

To do the actual matching, the engine treats a regular expression
something like a ((flow diagram)). This is the diagram for the
livestock expression in the previous example:

{{figure {url: "img/re_pigchickens.svg", alt: "Visualization of /\\b\\d+ (pig|cow|chicken)s?\\b/"}}}

{{index traversal}}

Our expression matches if we can find a path from the left side of the
diagram to the right side. We keep a current position in the string,
and every time we move through a box, we verify that the part of the
string after our current position matches that box.

So if we try to match `"the 3 pigs"` from position 4, our progress
through the flow chart would look like this:

 - At position 4, there is a word ((boundary)), so we can move past
   the first box.

 - Still at position 4, we find a digit, so we can also move past the
   second box.

 - At position 5, one path loops back to before the second (digit)
   box, while the other moves forward through the box that holds a
   single space character. There is a space here, not a digit, so we
   must take the second path.

 - We are now at position 6 (the start of "pigs") and at the three-way
   branch in the diagram. We don't see "cow" or "chicken" here, but we
   do see "pig", so we take that branch.

 - At position 9, after the three-way branch, one path skips the _s_
   box and goes straight to the final word boundary, while the other
   path matches an _s_. There is an _s_ character here, not a word
   boundary, so we go through the _s_ box.

 - We're at position 10 (the end of the string) and can match only a
   word ((boundary)). The end of a string counts as a word boundary,
   so we go through the last box and have successfully matched this
   string.

{{id backtracking}}

## Backtracking

{{index ["regular expression", backtracking], "binary number", "decimal number", "hexadecimal number", "flow diagram", [matching, algorithm], backtracking}}

The regular expression `/\b([01]+b|[\da-f]+h|\d+)\b/` matches either a
binary number followed by a _b_, a hexadecimal number (that is, base
16, with the letters _a_ to _f_ standing for the digits 10 to 15)
followed by an _h_, or a regular decimal number with no suffix
character. This is the corresponding diagram:

{{figure {url: "img/re_number.svg", alt: "Visualization of /\\b([01]+b|\\d+|[\\da-f]+h)\\b/"}}}

{{index branching}}

When matching this expression, it will often happen that the top
(binary) branch is entered even though the input does not actually
contain a binary number. When matching the string `"103"`, for
example, it becomes clear only at the 3 that we are in the wrong
branch. The string _does_ match the expression, just not the branch we
are currently in.

{{index backtracking, searching}}

So the matcher _backtracks_. When entering a branch, it remembers its
current position (in this case, at the start of the string, just past
the first boundary box in the diagram) so that it can go back and try
another branch if the current one does not work out. For the string
`"103"`, after encountering the 3 character, it will start trying the
branch for hexadecimal numbers, which fails again because there is no
_h_ after the number. So it tries the decimal number branch. This one
fits, and a match is reported after all.

{{index [matching, algorithm]}}

The matcher stops as soon as it finds a full match. This means that if
multiple branches could potentially match a string, only the first one
(ordered by where the branches appear in the regular expression) is
used.

Backtracking also happens for ((repetition)) operators like + and `*`.
If you match `/^.*x/` against `"abcxe"`, the `.*` part will first try
to consume the whole string. The engine will then realize that it
needs an _x_ to match the pattern. Since there is no _x_ past the end
of the string, the star operator tries to match one character less.
But the matcher doesn't find an _x_ after `abcx` either, so it
backtracks again, matching the star operator to just `abc`. _Now_ it
finds an _x_ where it needs it and reports a successful match from
positions 0 to 4.

{{index performance, complexity}}

It is possible to write regular expressions that will do a _lot_ of
backtracking. This problem occurs when a pattern can match a piece of
input in many different ways. For example, if we get confused while
writing a binary-number regular expression, we might accidentally
write something like `/([01]+)+b/`.

{{figure {url: "img/re_slow.svg", alt: "Visualization of /([01]+)+b/",width: "6cm"}}}

{{index "inner loop", [nesting, "in regexps"]}}

If that tries to match some long series of zeros and ones with no
trailing _b_ character, the matcher first goes through the inner
loop until it runs out of digits. Then it notices there is no _b_, so
it backtracks one position, goes through the outer loop once, and
gives up again, trying to backtrack out of the inner loop once more.
It will continue to try every possible route through these two loops.
This means the amount of work _doubles_ with each additional
character. For even just a few dozen characters, the resulting match
will take practically forever.

## The replace method

{{index "replace method", "regular expression"}}

String values have a `replace` method that can be used to replace
part of the string with another string.

```
console.log("papa".replace("p", "m"));
// → mapa
```

{{index ["regular expression", flags], ["regular expression", global]}}

The first argument can also be a regular expression, in which case the
first match of the regular expression is replaced. When a `g` option
(for _global_) is added to the regular expression, _all_ matches in
the string will be replaced, not just the first.

```
console.log("Borobudur".replace(/[ou]/, "a"));
// → Barobudur
console.log("Borobudur".replace(/[ou]/g, "a"));
// → Barabadar
```

{{index [interface, design], argument}}

It would have been sensible if the choice between replacing one match
or all matches was made through an additional argument to `replace` or
by providing a different method, `replaceAll`. But for some
unfortunate reason, the choice relies on a property of the regular
expression instead.

{{index grouping, "capture group", "dollar sign", "replace method", ["regular expression", grouping]}}

The real power of using regular expressions with `replace` comes from
the fact that we can refer back to matched groups in the replacement
string. For example, say we have a big string containing the names of
people, one name per line, in the format `Lastname, Firstname`. If we
want to swap these names and remove the comma to get a `Firstname
Lastname` format, we can use the following code:

```
console.log(
  "Liskov, Barbara\nMcCarthy, John\nWadler, Philip"
    .replace(/(\w+), (\w+)/g, "$2 $1"));
// → Barbara Liskov
//   John McCarthy
//   Philip Wadler
```

The `$1` and `$2` in the replacement string refer to the parenthesized
groups in the pattern. `$1` is replaced by the text that matched
against the first group, `$2` by the second, and so on, up to `$9`.
The whole match can be referred to with `$&`.

{{index [function, "higher-order"], grouping, "capture group"}}

It is possible to pass a function—rather than a string—as the second
argument to `replace`. For each replacement, the function will be
called with the matched groups (as well as the whole match) as
arguments, and its return value will be inserted into the new string.

Here's a small example:

```
let s = "the cia and fbi";
console.log(s.replace(/\b(fbi|cia)\b/g,
            str => str.toUpperCase()));
// → the CIA and FBI
```

And here's a more interesting one:

```
let stock = "1 lemon, 2 cabbages, and 101 eggs";
function minusOne(match, amount, unit) {
  amount = Number(amount) - 1;
  if (amount == 1) { // only one left, remove the 's'
    unit = unit.slice(0, unit.length - 1);
  } else if (amount == 0) {
    amount = "no";
  }
  return amount + " " + unit;
}
console.log(stock.replace(/(\d+) (\w+)/g, minusOne));
// → no lemon, 1 cabbage, and 100 eggs
```

This takes a string, finds all occurrences of a number followed by an
alphanumeric word, and returns a string wherein every such occurrence
is decremented by one.

The `(\d+)` group ends up as the `amount` argument to the function,
and the `(\w+)` group gets bound to `unit`. The function converts
`amount` to a number—which always works, since it matched `\d+`—and
makes some adjustments in case there is only one or zero left.

## Greed

{{index greed, "regular expression"}}

It is possible to use `replace` to write a function that removes all
((comment))s from a piece of JavaScript ((code)). Here is a first
attempt:

```{test: wrap}
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*\*\//g, "");
}
console.log(stripComments("1 + /* 2 */3"));
// → 1 + 3
console.log(stripComments("x = 10;// ten!"));
// → x = 10;
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1  1
```

{{index "period character", "slash character", "newline character", "empty set", "block comment", "line comment"}}

The part before the _or_ operator matches two slash characters
followed by any number of non-newline characters. The part for
multiline comments is more involved. We use `[^]` (any character that
is not in the empty set of characters) as a way to match any
character. We cannot just use a period here because block comments can
continue on a new line, and the period character does not match
newline characters.

But the output for the last line appears to have gone wrong. Why?

{{index backtracking, greed, "regular expression"}}

The `[^]*` part of the expression, as I described in the section on
backtracking, will first match as much as it can. If that causes the
next part of the pattern to fail, the matcher moves back one character
and tries again from there. In the example, the matcher first tries to
match the whole rest of the string and then moves back from there. It
will find an occurrence of `*/` after going back four characters and
match that. This is not what we wanted—the intention was to match a
single comment, not to go all the way to the end of the code and find
the end of the last block comment.

Because of this behavior, we say the repetition operators (`+`, `*`,
`?`, and `{}`) are _((greed))y_, meaning they match as much as they
can and backtrack from there. If you put a ((question mark)) after
them (`+?`, `*?`, `??`, `{}?`), they become nongreedy and start by
matching as little as possible, matching more only when the remaining
pattern does not fit the smaller match.

And that is exactly what we want in this case. By having the star
match the smallest stretch of characters that brings us to a `*/`, we
consume one block comment and nothing more.

```{test: wrap}
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*?\*\//g, "");
}
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1 + 1
```

A lot of ((bug))s in ((regular expression)) programs can be traced to
unintentionally using a greedy operator where a nongreedy one would
work better. When using a ((repetition)) operator, consider the
nongreedy variant first.

## Dynamically creating RegExp objects

{{index ["regular expression", creation], "underscore character", "RegExp class"}}

There are cases where you might not know the exact ((pattern)) you
need to match against when you are writing your code. Say you want to
look for the user's name in a piece of text and enclose it in
underscore characters to make it stand out. Since you will know the
name only once the program is actually running, you can't use the
slash-based notation.

But you can build up a string and use the `RegExp` ((constructor)) on
that. Here's an example:

```
let name = "harry";
let text = "Harry is a suspicious character.";
let regexp = new RegExp("\\b(" + name + ")\\b", "gi");
console.log(text.replace(regexp, "_$1_"));
// → _Harry_ is a suspicious character.
```

{{index ["regular expression", flags], "backslash character"}}

When creating the `\b` ((boundary)) markers, we have to use two
backslashes because we are writing them in a normal string, not a
slash-enclosed regular expression. The second argument to the `RegExp`
constructor contains the options for the regular expression—in this
case, `"gi"` for global and case-insensitive.

But what if the name is `"dea+hl[]rd"` because our user is a ((nerd))y
teenager? That would result in a nonsensical regular expression that
won't actually match the user's name.

{{index "backslash character", [escaping, "in regexps"], ["regular expression", escaping]}}

To work around this, we can add backslashes before any character that
has a special meaning.

```
let name = "dea+hl[]rd";
let text = "This dea+hl[]rd guy is super annoying.";
let escaped = name.replace(/[\\[.+*?(){|^$]/g, "\\$&");
let regexp = new RegExp("\\b" + escaped + "\\b", "gi");
console.log(text.replace(regexp, "_$&_"));
// → This _dea+hl[]rd_ guy is super annoying.
```

## The search method

{{index searching, ["regular expression", methods], "indexOf method", "search method"}}

The `indexOf` method on strings cannot be called with a regular
expression. But there is another method, `search`, that does expect a
regular expression. Like `indexOf`, it returns the first index on
which the expression was found, or -1 when it wasn't found.

```
console.log("  word".search(/\S/));
// → 2
console.log("    ".search(/\S/));
// → -1
```

Unfortunately, there is no way to indicate that the match should start
at a given offset (like we can with the second argument to `indexOf`),
which would often be useful.

## The lastIndex property

{{index "exec method", "regular expression"}}

The `exec` method similarly does not provide a convenient way to start
searching from a given position in the string. But it does provide an
*in*convenient way.

{{index ["regular expression", matching], matching, "source property", "lastIndex property"}}

Regular expression objects have properties. One such property is
`source`, which contains the string that expression was created from.
Another property is `lastIndex`, which controls, in some limited
circumstances, where the next match will start.

{{index [interface, design], "exec method", ["regular expression", global]}}

Those circumstances are that the regular expression must have the
global (`g`) or sticky (`y`) option enabled, and the match must happen
through the `exec` method. Again, a less confusing solution would have
been to just allow an extra argument to be passed to `exec`, but
confusion is an essential feature of JavaScript's regular expression
interface.

```
let pattern = /y/g;
pattern.lastIndex = 3;
let match = pattern.exec("xyzzy");
console.log(match.index);
// → 4
console.log(pattern.lastIndex);
// → 5
```

{{index "side effect", "lastIndex property"}}

If the match was successful, the call to `exec` automatically updates
the `lastIndex` property to point after the match. If no match was
found, `lastIndex` is set back to zero, which is also the value it has
in a newly constructed regular expression object.

The difference between the global and the sticky options is that, when
sticky is enabled, the match will only succeed if it starts directly
at `lastIndex`, whereas with global, it will search ahead for a
position where a match can start.

```
let global = /abc/g;
console.log(global.exec("xyz abc"));
// → ["abc"]
let sticky = /abc/y;
console.log(sticky.exec("xyz abc"));
// → null
```

{{index bug}}

When using a shared regular expression value for multiple `exec`
calls, these automatic updates to the `lastIndex` property can cause
problems. Your regular expression might be accidentally starting at an
index that was left over from a previous call.

```
let digit = /\d/g;
console.log(digit.exec("here it is: 1"));
// → ["1"]
console.log(digit.exec("and now: 1"));
// → null
```

{{index ["regular expression", global], "match method"}}

Another interesting effect of the global option is that it changes the
way the `match` method on strings works. When called with a global
expression, instead of returning an array similar to that returned by
`exec`, `match` will find _all_ matches of the pattern in the string
and return an array containing the matched strings.

```
console.log("Banana".match(/an/g));
// → ["an", "an"]
```

So be cautious with global regular expressions. The cases where they
are necessary—calls to `replace` and places where you want to
explicitly use `lastIndex`—are typically the only places where you
want to use them.

### Looping over matches

{{index "lastIndex property", "exec method", loop}}

A common thing to do is to scan through all occurrences of a pattern
in a string, in a way that gives us access to the match object in the
loop body. We can do this by using `lastIndex` and `exec`.

```
let input = "A string with 3 numbers in it... 42 and 88.";
let number = /\b\d+\b/g;
let match;
while (match = number.exec(input)) {
  console.log("Found", match[0], "at", match.index);
}
// → Found 3 at 14
//   Found 42 at 33
//   Found 88 at 40
```

{{index "while loop", "= operator"}}

This makes use of the fact that the value of an ((assignment))
expression (`=`) is the assigned value. So by using `match =
number.exec(input)` as the condition in the `while` statement, we
perform the match at the start of each iteration, save its result in a
((binding)), and stop looping when no more matches are found.

{{id ini}}
## Parsing an INI file

{{index comment, "file format", "enemies example", "INI file"}}

To conclude the chapter, we'll look at a problem that calls for
((regular expression))s. Imagine we are writing a program to
automatically collect information about our enemies from the
((Internet)). (We will not actually write that program here, just the
part that reads the ((configuration)) file. Sorry.) The configuration
file looks like this:

```{lang: "text/plain"}
searchengine=https://duckduckgo.com/?q=$1
spitefulness=9.7

; comments are preceded by a semicolon...
; each section concerns an individual enemy
[larry]
fullname=Larry Doe
type=kindergarten bully
website=http://www.geocities.com/CapeCanaveral/11451

[davaeorn]
fullname=Davaeorn
type=evil wizard
outputdir=/home/marijn/enemies/davaeorn
```

{{index grammar}}

The exact rules for this format (which is a widely used format,
usually called an _INI_ file) are as follows:

- Blank lines and lines starting with semicolons are ignored.

- Lines wrapped in `[` and `]` start a new ((section)).

- Lines containing an alphanumeric identifier followed by an `=`
  character add a setting to the current section.

- Anything else is invalid.

Our task is to convert a string like this into an object whose
properties hold strings for sectionless settings and sub-objects for
sections, with those sub-objects holding the section's settings.

{{index "carriage return", "line break", "newline character"}}

Since the format has to be processed ((line)) by line, splitting up
the file into separate lines is a good start. We used
`string.split("\n")` to do this in [Chapter ?](data#split).
Some operating systems, however, use not just a newline character to
separate lines but a carriage return character followed by a newline
(`"\r\n"`). Given that the `split` method also allows a regular
expression as its argument, we can use a regular expression like
`/\r?\n/` to split in a way that allows both `"\n"` and `"\r\n"`
between lines.

```{startCode: true}
function parseINI(string) {
  // Start with an object to hold the top-level fields
  let result = {};
  let section = result;
  string.split(/\r?\n/).forEach(line => {
    let match;
    if (match = line.match(/^(\w+)=(.*)$/)) {
      section[match[1]] = match[2];
    } else if (match = line.match(/^\[(.*)\]$/)) {
      section = result[match[1]] = {};
    } else if (!/^\s*(;.*)?$/.test(line)) {
      throw new Error("Line '" + line + "' is not valid.");
    }
  });
  return result;
}

console.log(parseINI(`
name=Vasilis
[address]
city=Tessaloniki`));
// → {name: "Vasilis", address: {city: "Tessaloniki"}}
```

{{index "parseINI function", parsing}}

The code goes over the file's lines and builds up an object.
Properties at the top are stored directly into that object, whereas
properties found in sections are stored in a separate section object.
The `section` binding points at the object for the current section.

There are two kinds of significant lines—section headers or property
lines. When a line is a regular property, it is stored in the current
section. When it is a section header, a new section object is created,
and `section` is set to point at it.

{{index "caret character", "dollar sign", boundary}}

Note the recurring use of `^` and `$` to make sure the expression
matches the whole line, not just part of it. Leaving these out results
in code that mostly works but behaves strangely for some input, which
can be a difficult bug to track down.

{{index "if keyword", assignment, "= operator"}}

The pattern `if (match = string.match(...))` is similar to the trick
of using an assignment as the condition for `while`. You often aren't
sure that your call to `match` will succeed, so you can access the
resulting object only inside an `if` statement that tests for this. To
not break the pleasant chain of `else if` forms, we assign the result
of the match to a binding and immediately use that assignment as the
test for the `if` statement.

If a line is not a section header or a property, the function checks
whether it is a comment or an empty line using the expression
`/^\s*(;.*)?$/`. Do you see how it works? The part between the
((parentheses)) will match comments, and the `?` makes sure it also
matches lines containing only whitespace. When a line doesn't match
any of the expected forms, the function throws an exception.

## International characters

{{index internationalization, Unicode, ["regular expression", internationalization]}}

Because of JavaScript's initial simplistic implementation and the fact
that this simplistic approach was later set in stone as ((standard))
behavior, JavaScript's regular expressions are rather dumb about
characters that do not appear in the English language. For example, as
far as JavaScript's regular expressions are concerned, a "((word
character))" is only one of the 26 characters in the Latin alphabet
(uppercase or lowercase), decimal digits, and, for some reason, the
underscore character. Things like _é_ or _β_, which most definitely
are word characters, will not match `\w` (and _will_ match uppercase
`\W`, the nonword category).

{{index whitespace}}

By a strange historical accident, `\s` (whitespace) does not have this
problem and matches all characters that the Unicode standard considers
whitespace, including things like the ((nonbreaking space)) and the
((Mongolian vowel separator)).

Another problem is that, by default, regular expressions work on code
units, as discussed in [Chapter ?](higher_order#code_units), not
actual characters. This means that characters that are composed of two
code units behave strangely.

```
console.log(/🍎{3}/.test("🍎🍎🍎"));
// → false
console.log(/<.>/.test("<🌹>"));
// → false
console.log(/<.>/u.test("<🌹>"));
// → true
```

The problem is that the 🍎 in the first line is treated as two code
units, and the `{3}` part is applied only to the second one.
Similarly, the dot matches a single code unit, not the two that make
up the rose ((emoji)).

You must add a `u` option (for ((Unicode))) to your regular
expression to make it treat such characters properly. The wrong
behavior remains the default, unfortunately, because changing that
might cause problems for existing code that depends on it.

{{index "character category", [Unicode, property]}}

Though this was only just standardized and is, at the time of writing,
not widely supported yet, it is possible to use `\p` in a regular
expression (that must have the Unicode option enabled) to match all
characters to which the Unicode standard assigns a given property.

```{test: never}
console.log(/\p{Script=Greek}/u.test("α"));
// → true
console.log(/\p{Script=Arabic}/u.test("α"));
// → false
console.log(/\p{Alphabetic}/u.test("α"));
// → true
console.log(/\p{Alphabetic}/u.test("!"));
// → false
```

Unicode defines a number of useful properties, though finding the one
that you need may not always be trivial. You can use the
`\p{Property=Value}` notation to match any character that has the
given value for that property. If the property name is left off, as in
`\p{Name}`, the name is assumed to either be a binary property such as
`Alphabetic` or a category such as `Number`.

{{id summary_regexp}}

## Summary

Regular expressions are objects that represent patterns in strings.
They use their own language to express these patterns.

{{table {cols: [1, 5]}}}

| `/abc/`     | A sequence of characters
| `/[abc]/`   | Any character from a set of characters
| `/[^abc]/`  | Any character _not_ in a set of characters
| `/[0-9]/`   | Any character in a range of characters
| `/x+/`      | One or more occurrences of the pattern `x`
| `/x+?/`     | One or more occurrences, nongreedy
| `/x*/`      | Zero or more occurrences
| `/x?/`      | Zero or one occurrence
| `/x{2,4}/`  | Two to four occurrences
| `/(abc)/`   | A group
| `/a|b|c/`   | Any one of several patterns
| `/\d/`      | Any digit character
| `/\w/`      | An alphanumeric character ("word character")
| `/\s/`      | Any whitespace character
| `/./`       | Any character except newlines
| `/\b/`      | A word boundary
| `/^/`       | Start of input
| `/$/`       | End of input

A regular expression has a method `test` to test whether a given
string matches it. It also has a method `exec` that, when a match is
found, returns an array containing all matched groups. Such an array
has an `index` property that indicates where the match started.

Strings have a `match` method to match them against a regular
expression and a `search` method to search for one, returning only the
starting position of the match. Their `replace` method can replace
matches of a pattern with a replacement string or function.

Regular expressions can have options, which are written after the
closing slash. The `i` option makes the match case-insensitive. The
`g` option makes the expression _global_, which, among other things,
causes the `replace` method to replace all instances instead of just
the first. The `y` option makes it sticky, which means that it will
not search ahead and skip part of the string when looking for a match.
The `u` option turns on Unicode mode, which fixes a number of problems
around the handling of characters that take up two code units.

Regular expressions are a sharp ((tool)) with an awkward handle. They
simplify some tasks tremendously but can quickly become unmanageable
when applied to complex problems. Part of knowing how to use them is
resisting the urge to try to shoehorn things that they cannot cleanly
express into them.

## Exercises

{{index debugging, bug}}

It is almost unavoidable that, in the course of working on these
exercises, you will get confused and frustrated by some regular
expression's inexplicable ((behavior)). Sometimes it helps to enter
your expression into an online tool like
[_debuggex.com_](https://www.debuggex.com/) to see whether its
visualization corresponds to what you intended and to ((experiment))
with the way it responds to various input strings.

### Regexp golf

{{index "program size", "code golf", "regexp golf (exercise)"}}

_Code golf_ is a term used for the game of trying to express a
particular program in as few characters as possible. Similarly,
_regexp golf_ is the practice of writing as tiny a regular expression
as possible to match a given pattern, and _only_ that pattern.

{{index boundary, matching}}

For each of the following items, write a ((regular expression)) to
test whether any of the given substrings occur in a string. The
regular expression should match only strings containing one of the
substrings described. Do not worry about word boundaries unless
explicitly mentioned. When your expression works, see whether you can
make it any smaller.

 1. _car_ and _cat_
 2. _pop_ and _prop_
 3. _ferret_, _ferry_, and _ferrari_
 4. Any word ending in _ious_
 5. A whitespace character followed by a period, comma, colon, or semicolon
 6. A word longer than six letters
 7. A word without the letter _e_ (or _E_)

Refer to the table in the [chapter summary](regexp#summary_regexp) for
help. Test each solution with a few test strings.

{{if interactive
```
// Fill in the regular expressions

verify(/.../,
       ["my car", "bad cats"],
       ["camper", "high art"]);

verify(/.../,
       ["pop culture", "mad props"],
       ["plop", "prrrop"]);

verify(/.../,
       ["ferret", "ferry", "ferrari"],
       ["ferrum", "transfer A"]);

verify(/.../,
       ["how delicious", "spacious room"],
       ["ruinous", "consciousness"]);

verify(/.../,
       ["bad punctuation ."],
       ["escape the period"]);

verify(/.../,
       ["hottentottententen"],
       ["no", "hotten totten tenten"]);

verify(/.../,
       ["red platypus", "wobbling nest"],
       ["earth bed", "learning ape", "BEET"]);


function verify(regexp, yes, no) {
  // Ignore unfinished exercises
  if (regexp.source == "...") return;
  for (let str of yes) if (!regexp.test(str)) {
    console.log(`Failure to match '${str}'`);
  }
  for (let str of no) if (regexp.test(str)) {
    console.log(`Unexpected match for '${str}'`);
  }
}
```

if}}

### Quoting style

{{index "quoting style (exercise)", "single-quote character", "double-quote character"}}

Imagine you have written a story and used single ((quotation mark))s
throughout to mark pieces of dialogue. Now you want to replace all the
dialogue quotes with double quotes, while keeping the single quotes
used in contractions like _aren't_.

{{index "replace method"}}

Think of a pattern that distinguishes these two
kinds of quote usage and craft a call to the `replace` method that
does the proper replacement.

{{if interactive
```{test: no}
let text = "'I'm the cook,' he said, 'it's my job.'";
// Change this call.
console.log(text.replace(/A/g, "B"));
// → "I'm the cook," he said, "it's my job."
```
if}}

{{hint

{{index "quoting style (exercise)", boundary}}

The most obvious solution is to only replace quotes with a nonword
character on at least one side. Something like `/\W'|'\W/`. But you
also have to take the start and end of the line into account.

{{index grouping, "replace method"}}

In addition, you must ensure that the replacement also includes the
characters that were matched by the `\W` pattern so that those are not
dropped. This can be done by wrapping them in ((parentheses)) and
including their groups in the replacement string (`$1`, `$2`). Groups
that are not matched will be replaced by nothing.

hint}}

### Numbers again

{{index sign, "fractional number", syntax, minus, "plus character", exponent, "scientific notation", "period character"}}

Write an expression that matches only JavaScript-style ((number))s. It
must support an optional minus _or_ plus sign in front of the number,
the decimal dot, and exponent notation—`5e-3` or `1E10`— again with an
optional sign in front of the exponent. Also note that it is not
necessary for there to be digits in front of or after the dot, but the
number cannot be a dot alone. That is, `.5` and `5.` are valid
JavaScript numbers, but a lone dot _isn't_.

{{if interactive
```{test: no}
// Fill in this regular expression.
let number = /^...$/;

// Tests:
for (let str of ["1", "-1", "+15", "1.55", ".5", "5.",
                 "1.3e2", "1E-4", "1e+12"]) {
  if (!number.test(str)) {
    console.log(`Failed to match '${str}'`);
  }
}
for (let str of ["1a", "+-1", "1.2.3", "1+1", "1e4.5",
                 ".5.", "1f5", "."]) {
  if (number.test(str)) {
    console.log(`Incorrectly accepted '${str}'`);
  }
}
```

if}}

{{hint

{{index ["regular expression", escaping], "backslash character"}}

First, do not forget the backslash in front of the period.

Matching the optional ((sign)) in front of the ((number)), as well as
in front of the ((exponent)), can be done with `[+\-]?` or `(\+|-|)`
(plus, minus, or nothing).

{{index "pipe character"}}

The more complicated part of the exercise is the problem of matching
both `"5."` and `".5"` without also matching `"."`. For this, a good
solution is to use the `|` operator to separate the two cases—either
one or more digits optionally followed by a dot and zero or more
digits _or_ a dot followed by one or more digits.

{{index exponent, "case sensitivity", ["regular expression", flags]}}

Finally, to make the _e_ case-insensitive, either add an `i` option to
the regular expression or use `[eE]`.

hint}}
