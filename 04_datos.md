{{meta {load_files: ["code/journal.js", "code/chapter/04_data.js"], zip: "node/html"}}}

# Estructuras de Datos: Objetos y Arrays

{{quote {author: "Charles Babbage", title: "Passages from the Life of a Philosopher (1864)", chapter: true}

En dos ocasiones me han preguntado, 'Dinos, Sr. Babbage, si pones
montos equivocadas en la máquina, saldrán las respuestas correctas?
[...] No soy capaz de comprender correctamente el tipo de confusión de
ideas que podrían provocar tal pregunta.

quote}}

{{index "Babbage, Charles"}}

{{figure {url: "img/chapter_picture_4.jpg", alt: "Picture of a weresquirrel", chapter: framed}}}

{{index object, "data structure"}}

Los números, los booleanos y los strings son los átomos que constituyen las  
estructuras de ((datos)). Sin embargo, muchos tipos de información
requieren más de un átomo. Los _objetos_ nos permiten agrupar valores—incluidos
otros objetos— para construir estructuras más complejas.

Los programas que hemos construido hasta ahora han estado limitados por el
hecho de que estaban operando solo en tipos de datos simples. Este capítulo
introducira estructuras de datos básicas. Al final de el, sabrás lo suficiente
como para comenzar a escribir programas útiles.

El capítulo trabajara a través de un ejemplo de programación más o menos
realista, presentando nuevos conceptos según se apliquen al problema en
cuestión. El código de ejemplo a menudo se basara en funciones y vinculaciones
que fueron introducidas anteriormente en el texto.

{{if book

La ((caja de arena)) en línea para el libro
([_eloquentjavascript.net/code_](https://eloquentjavascript.net/code)]
proporciona una forma de ejecutar código en el contexto de un capítulo en
específico. Si decides trabajar con los ejemplos en otro entorno,
asegúrate de primero descargar el código completo de este capítulo
desde la página de la caja de arena.

if}}

## El Hombre Ardilla

{{index "weresquirrel example", lycanthropy}}

De vez en cuando, generalmente entre las ocho y las diez de la noche,
((Jacques)) se encuentra a si mismo
transformándose en un pequeño roedor peludo con una cola espesa.

Por un lado, Jacques está muy contento de no tener la licantropía clásica.
Convertirse en una ardilla causa menos problemas que convertirse en un lobo.
En lugar de tener que preocuparse por accidentalmente comerse al vecino
(_eso_ sería incómodo), le preocupa ser comido por el gato del vecino.
Después de dos ocasiones en las que se despertó en una rama precariamente
delgada de la copa de un roble, desnudo y desorientado, Jacques se ha dedicado
a bloquear las puertas y ventanas de su habitación por la noche y
pone algunas nueces en el piso para mantenerse ocupado.

Eso se ocupa de los problemas del gato y el árbol. Pero Jacques preferiría
deshacerse de su condición por completo. Las ocurrencias irregulares de la
transformación lo hacen sospechar que estas podrían ser provocadas por
algo en especifico. Por un tiempo, creyó que solo sucedia en los días
en los que el había estado cerca de árboles de roble. Pero evitar los robles
no detuvo el problema.

{{index journal}}

Cambiando a un enfoque más científico, Jacques ha comenzado a mantener un
registro diario de todo lo que hace en un día determinado y si su forma
cambio. Con esta información el espera reducir las condiciones que
desencadenan las transformaciones.

Lo primero que el necesita es una estructura de datos para almacenar esta
información.

## Conjuntos de datos

{{index "data structure"}}

Para trabajar con una porción de datos digitales, primero debemos encontrar
una manera de representarlo en la ((memoria)) de nuestra máquina. Digamos,
por ejemplo, que queremos representar una ((colección)) de los números
2, 3, 5, 7 y 11.

{{index string}}

Podríamos ponernos creativos con los strings—después de todo, los strings pueden
tener cualquier longitud, por lo que podemos poner una gran cantidad de
datos en ellos—y usar `"2 3 5 7 11"` como nuestra representación.
Pero esto es incómodo. Tendrías que extraer los dígitos de alguna manera
y convertirlos a números para acceder a ellos.

{{index [array, creation], "[] (array)"}}

Afortunadamente, JavaScript proporciona un tipo de datos específicamente
para almacenar secuencias de valores. Es llamado _array_ y está escrito
como una lista de valores entre ((corchetes)), separados por comas.

```
let listaDeNumeros = [2, 3, 5, 7, 11];
console.log(listaDeNumeros[2]);
// → 5
console.log(listaDeNumeros[0]);
// → 2
console.log(listaDeNumeros[2 - 1]);
// → 3
```

{{index "[] (subscript)", [array, indexing]}}

La notación para llegar a los elementos dentro de un array también utiliza
((corchetes)). Un par de corchetes inmediatamente después de una
expresión, con otra expresión dentro de ellos, buscará al
elemento en la expresión de la izquierda que corresponde al
_((índice))_ dado por la expresión entre corchetes.

{{id array_indexing}}
{{index "zero-based counting"}}

El primer índice de un array es cero, no uno. Entonces el primer elemento es
alcanzado con `listaDeNumeros[0]`. El conteo basado en cero tiene una larga
tradición en el mundo de la tecnología, y en ciertas maneras tiene mucho
sentido, pero toma algo de tiempo acostumbrarse. Piensa en el índice como
la cantidad de elementos a saltar, contando desde el comienzo del array.

{{id properties}}

## Propiedades

{{index "Math object", "Math.max function", ["length property", "for string"], [object, property], "period character"}}

Hasta ahora hemos visto algunas expresiones sospechosas como `miString.length`
(para obtener la longitud de un string) y `Math.max` (la función máxima)
en capítulos anteriores. Estas son expresiones que acceden a la _((propiedad))_
de algún valor. En el primer caso, accedemos a la propiedad `length` de
el valor en `miString`. En el segundo, accedemos a la propiedad llamada
`max` en el objeto `Math` (que es una colección de
constantes y funciones relacionadas con las matemáticas).

{{index property, null, undefined}}

Casi todos los valores de JavaScript tienen propiedades. Las excepciones son
`null` y `undefined`. Si intentas acceder a una propiedad en alguno de
estos no-valores, obtienes un error.

```{test: no}
null.length;
// → TypeError: null has no properties
```

{{indexsee "dot character", "period character"}}
{{index "[] (subscript)", "period character", "square brackets", "computed property"}}

Las dos formas principales de acceder a las propiedades en JavaScript son
con un punto y con corchetes. Tanto `valor.x` como `valor[x]` acceden una
((propiedad)) en `valor`—pero no necesariamente la misma propiedad. La
diferencia está en cómo se interpreta `x`. Cuando se usa un punto, la palabra
después del punto es el nombre literal de la propiedad. Cuando usas
corchetes, la expresión entre corchetes es _evaluada_ para obtener
el nombre de la propiedad. Mientras `valor.x` obtiene la propiedad de `valor`
llamada "x", `valor[x]` intenta evaluar la expresión `x` y usa
el resultado, convertido en un string, como el nombre de la propiedad.

Entonces, si sabes que la propiedad que te interesa se llama
_color_, dices `valor.color`. Si quieres extraer la propiedad
nombrado por el valor mantenido en la vinculación `i`, dices `valor[i]`.
Los nombres de las propiedades son strings. Pueden ser cualquier string,
pero la notación de puntos solo funciona con nombres que se vean como nombres
de vinculaciones válidos. Entonces, si quieres acceder a una
propiedad llamada _2_ o _Juan Perez_, debes usar corchetes:
`valor[2]` o `valor["Juan Perez"]`.

Los elementos en un ((array)) son almacenados como propiedades del array,
usando números como nombres de propiedad. Ya que no puedes usar la notación
de puntos con números, y que generalmente quieres utilizar una vinculación
que contenga el índice de cualquier manera,
debes de usar la notación de corchetes para llegar a ellos.

{{index ["length property", "for array"], [array, "length of"]}}

La propiedad `length` de un array nos dice cuántos elementos este tiene.
Este nombre de propiedad es un nombre de vinculación válido, y
sabemos su nombre en avance, así que para encontrar la longitud de un array,
normalmente escribes `array.length` ya que es más fácil de escribir que
`array["length"]`.

{{id methods}}

## Métodos

{{index [function, "as property"], method, string}}

Ambos objetos de string y array contienen, además de la propiedad
`length`, una serie de propiedades que tienen valores de función.

```
let ouch = "Ouch";
console.log(typeof ouch.toUpperCase);
// → function
console.log(ouch.toUpperCase());
// → OUCH
```

{{index "case conversion", "toUpperCase method", "toLowerCase method"}}

Cada string tiene una propiedad `toUpperCase` ("a mayúsculas").
Cuando se llame, regresará una copia del string en la que todas las
letras han sido convertido a mayúsculas. También hay `toLowerCase`
("a minúsculas"), que hace lo contrario.

{{index this}}

Curiosamente, a pesar de que la llamada a `toUpperCase` no pasa ningún
argumento, la función de alguna manera tiene acceso al string `"Ouch"`, el
valor de cuya propiedad llamamos. Cómo funciona esto se describe en el
[Capítulo 6](objeto#metodos_de_objeto).

Las propiedades que contienen funciones generalmente son llamadas _metodos_
del valor al que pertenecen. Como en, "`toUpperCase` es un método de
string".

{{id array_methods}}

Este ejemplo demuestra dos métodos que puedes usar para manipular
arrays:

```
let secuencia = [1, 2, 3];
secuencia.push(4);
secuencia.push(5);
console.log(secuencia);
// → [1, 2, 3, 4, 5]
console.log(secuencia.pop());
// → 5
console.log(secuencia);
// → [1, 2, 3, 4]
```

{{index collection, array, "push method", "pop method"}}

El método `push` agrega valores al final de un array, y el
el método `pop` hace lo contrario, eliminando el último valor en el array
y retornandolo.

Estos nombres algo tontos son los términos tradicionales para las operaciones en
una _((pila))_. Una pila, en programación, es una ((estructura de datos)) que
te permite agregar valores a ella y volverlos a sacar en el
orden opuesto, de modo que lo que se agregó de último se elimine primero.
Estas son comunes en la programación—es posible que recuerdes la ((pila))
de llamadas en [el capítulo anterior](funciones#pila), que es una
instancia de la misma idea.

## Objetos

{{index journal, "weresquirrel example", array, record}}

De vuelta al Hombre-Ardilla. Un conjunto de entradas diarias puede ser
representado como un array. Pero estas entradas no consisten en solo un
número o un string—cada entrada necesita almacenar una lista de actividades y
un valor booleano que indica si Jacques se convirtió en una ardilla
o no. Idealmente, nos gustaría agrupar estos en un solo
valor y luego agrupar estos valores en un array de registro de entradas.

{{index syntax, property, "curly braces", "{} (object)"}}

Los valores del tipo _((objeto))_ son colecciones arbitrarias de
propiedades. Una forma de crear un objeto es mediante el uso de llaves
como una expresión.

```
let dia1 = {
  ardilla: false,
  eventos: ["trabajo", "toque un arbol", "pizza", "salir a correr"]
};
console.log(dia1.ardilla);
// → false
console.log(dia1.lobo);
// → undefined
dia1.lobo = false;
console.log(dia1.lobo);
// → false
```

{{index [quoting, "of object properties"], "colon character"}}

Dentro de las llaves, hay una lista de propiedades separadas por comas.
Cada propiedad tiene un nombre seguido de dos puntos y un valor. Cuando un
objeto está escrito en varias líneas, indentar como en el
ejemplo ayuda con la legibilidad. Las propiedades cuyos nombres no sean
nombres válidos de vinculaciones o números válidos deben estar entre comillas.

```
let descripciones = {
  trabajo: "Fui a trabajar",
  "toque un arbol": "Toque un arbol"
};
```

Esto significa que las ((llaves)) tienen _dos_ significados en JavaScript. Al
comienzo de una ((declaración)), comienzan un ((bloque)) de declaraciones. En
cualquier otra posición, describen un objeto. Afortunadamente, es raramente
útil comenzar una declaración con un objeto en llaves, por lo que
la ambigüedad entre estas dos acciones no es un gran problema.

{{index undefined}}

Leer una propiedad que no existe te dará el valor `undefined`.

{{index [property, assignment], mutability, "= operator"}}

Es posible asignarle un valor a una expresión de propiedad con un
operador `=`. Esto reemplazará el valor de la propiedad si ya tenia uno
o crea una nueva propiedad en el objeto si no fuera así.

{{index "tentacle (analogy)", [property, "model of"]}}

Para volver brevemente a nuestro modelo de ((vinculaciones)) como
tentáculos—Las vinculaciones de propiedad son similares. Ellas _agarran_
valores, pero otras vinculaciones y propiedades pueden estar agarrando
esos mismos valores. Puedes pensar en los objetos como pulpos con cualquier
cantidad de tentáculos, cada uno de los cuales tiene un nombre tatuado en él.

{{index "delete operator", [property, deletion]}}

El operador `delete` ("eliminar") corta un tentáculo de dicho pulpo. Es
un operador unario que, cuando se aplica a la propiedad de un objeto,
eliminará la propiedad nombrada de dicho objeto. Esto no es algo que hagas
todo el tiempo, pero es posible.

```
let unObjeto = {izquierda: 1, derecha: 2};
console.log(unObjeto.izquierda);
// → 1
delete unObjeto.izquierda;
console.log(unObjeto.izquierda);
// → undefined
console.log("izquierda" in unObjeto);
// → false
console.log("derecha" in unObjeto);
// → true
```

{{index "in operator", [property, "testing for"], object}}

El operador binario `in` ("en"), cuando se aplica a un string y un objeto,
te dice si ese objeto tiene una propiedad con ese nombre. La diferencia
entre darle un valor de `undefined` a una propiedad y eliminarla realmente es
que, en el primer caso, el objeto todavía _tiene_ la propiedad (solo que
no tiene un valor muy interesante), mientras que en el segundo caso
la propiedad ya no está presente e `in` retornara `false`.

{{index "Object.keys function"}}

Para saber qué propiedades tiene un objeto, puedes usar la función
`Object.keys`. Le das un objeto y devuelve un array
de strings—los nombres de las propiedades del objeto.

```
console.log(Object.keys({x: 0, y: 0, z: 2}));
// → ["x", "y", "z"]
```

Hay una función `Object.assign` que copia todas las propiedades de
un objeto a otro.

```
let objetoA = {a: 1, b: 2};
Object.assign(objetoA, {b: 3, c: 4});
console.log(objetoA);
// → {a: 1, b: 3, c: 4}
```

{{index array, collection}}

Los arrays son, entonces, solo un tipo de objeto especializado para almacenar
secuencias de cosas. Si evalúas `typeof []`, este produce
`"object"`. Podrias imaginarlos como pulpos largos y planos con todos sus
tentáculos en una fila ordenada, etiquetados con números.

{{index journal, "weresquirrel example"}}

Representaremos el diario de Jacques como un array de objetos.

```{test: wrap}
let diario = [
  {eventos: ["trabajo", "toque un arbol", "pizza",
            "sali a correr", "television"],
   ardilla: false},
  {eventos: ["trabajo", "helado", "coliflor",
            "lasaña", "toque un arbol", "me cepille los dientes"],
   ardilla: false},
  {eventos: ["fin de semana", "monte la bicicleta", "descanso", "nueces",
            "cerveza"],
   ardilla: true},
  /* y asi sucesivamente... */
];
```

## Mutabilidad

Llegaremos a la programación real _pronto_. Pero primero, hay una pieza
más de teoría por entender.

{{index mutability, "side effect", number, string, Boolean, object}}

Vimos que los valores de objeto pueden ser modificados. Los tipos de valores
discutidos en capítulos anteriores, como números, strings y booleanos,
son todos _((inmutables))_—es imposible cambiar los valores de aquellos
tipos. Puedes combinarlos y obtener nuevos valores a partir de ellos, pero cuando
tomas un valor de string específico, ese valor siempre será el
mismo. El texto dentro de él no puede ser cambiado. Si tienes un string que
contiene `"gato"`, no es posible que otro código cambie un
carácter en tu string para que deletree `"rato"`.

Los objetos funcionan de una manera diferente. Tu _puedes_ cambiar sus
propiedades, haciendo que un único valor de objeto tenga contenido diferente en
diferentes momentos.

{{index [object, identity], identity, memory, mutability}}

Cuando tenemos dos números, 120 y 120, podemos considerarlos el mismo número
precisamente, ya sea que hagan referencia o no a los mismos bits físicos.
Con los objetos, hay una diferencia entre tener dos referencias a
el mismo objeto y tener dos objetos diferentes que contengan las mismas
propiedades. Considera el siguiente código:

```
let objeto1 = {valor: 10};
let objeto2 = objeto1;
let objeto3 = {valor: 10};

console.log(objeto1 == objeto2);
// → true
console.log(objeto1 == objeto3);
// → false

objeto1.valor = 15;
console.log(objeto2.valor);
// → 15
console.log(objeto3.valor);
// → 10
```

{{index "tentacle (analogy)", [binding, "model of"]}}

Las vinculaciones `objeto1` y `objeto2` agarran el _mismo_ objeto, que es
la razon por la cual cambiar `objeto1` también cambia el valor de `objeto2`.
Se dice que tienen la misma _identidad_. La vinculación `objeto3` apunta a un
objeto diferente, que inicialmente contiene las mismas propiedades que
`objeto1` pero vive una vida separada.

{{index "const keyword", "let keyword"}}

Las vinculaciones también pueden ser cambiables o constantes, pero esto es
independiente de la forma en la que se comportan sus valores. Aunque los
valores numéricos no cambian, puedes usar una ((vinculación)) `let` para hacer
un seguimiento de un número que cambia al cambiar el valor al que apunta la
vinculación. Del mismo modo, aunque una vinculación `const` a un objeto no
pueda ser cambiada en si misma y continuará apuntando al mismo objeto,
los _contenidos_ de ese objeto pueden cambiar.

```{test: no}
const puntuacion = {visitantes: 0, locales: 0};
// Esto esta bien
puntuacion.visitantes = 1;
// Esto no esta permitido
puntuacion = {visitantes: 1, locales: 1};
```

{{index "== operator", [comparison, "of objects"], "deep comparison"}}

Cuando comparas objetos con el operador `==` en JavaScript, este los compara
por identidad: producirá `true` solo si ambos objetos son precisamente
el mismo valor. Comparar diferentes objetos retornara `false`, incluso
si tienen propiedades idénticas. No hay una operación de comparación "profunda"
incorporada en JavaScript, que compare objetos por contenidos,
pero es posible que la escribas tu mismo (que es uno de los
[ejercicios](datos#ejercicio_comparacion_profunda) al final de este capítulo).

## El diario del licántropo

{{index "weresquirrel example", lycanthropy, "addEntry function"}}

Asi que Jacques inicia su intérprete de JavaScript y establece el
entorno que necesita para mantener su ((diario)).

```{includeCode: true}
let diario = [];

function añadirEntrada(eventos, ardilla) {
  diario.push({eventos, ardilla});
}
```

{{index "curly braces", "{} (object)"}}

Ten en cuenta que el objeto agregado al diario se ve un poco extraño. En lugar
de declarar propiedades como `eventos: eventos`, simplemente da un
nombre de ((propiedad)). Este es un atajo que representa lo mismo—si el
nombre de propiedad en la notación de llaves no es seguido por un valor, su
el valor se toma de la vinculación con el mismo nombre.

Entonces, todas las noches a las diez—o algunas veces a la mañana siguiente,
después de bajar del estante superior de su biblioteca—Jacques registra el
día.


So then, every evening at ten—or sometimes the next morning, after
climbing down from the top shelf of his bookcase—Jacques records the
day.

```
añadirEntrada(["trabajo", "toque un arbol", "pizza", "sali a correr",
          "television"], false);
añadirEntrada(["trabajo", "helado", "coliflor", "lasaña",
          "toque un arbol", "me cepille los dientes"], false);
añadirEntrada(["fin de semana", "monte la bicicleta", "descanso", "nueces",
          "cerveza"], true);
```

Una vez que tiene suficientes puntos de datos, tiene la intención de utilizar
estadísticas para encontrar cuál de estos eventos puede estar
relacionado con la transformación a ardilla.

{{index correlation}}

La _correlación_ es una medida de ((dependencia)) entre variables estadísticas.
Una variable estadística no es lo mismo que una variable de programación.
En las estadísticas, normalmente tienes un conjunto de _medidas_,
y cada variable se mide para cada medida. La correlación entre variables
generalmente se expresa como un valor que
varia de -1 a 1. Una correlación de cero significa que las variables no estan
relacionadas. Una correlación de uno indica que las dos están perfectamente
relacionadas—si conoces una, también conoces la otra. Uno negativo también
significa que las variables están perfectamente relacionadas pero que son
opuestas—cuando una es verdadera, la otra es falsa.

{{index "phi coefficient"}}

Para calcular la medida de correlación entre dos variables booleanas,
podemos usar el _coeficiente phi_ (_ϕ_). Esta es una fórmula cuya entrada
es una ((tabla de frecuencias)) que contiene la cantidad de veces que
las diferentes combinaciones de las variables fueron observadas.
El resultado de la fórmula es un número entre -1 y 1 que describe
la correlación.

Podríamos tomar el evento de comer ((pizza)) y poner eso en una
tabla de frecuencias como esta, donde cada número indica la cantidad de
veces que ocurrió esa combinación en nuestras mediciones:

{{figure {url: "img/pizza-squirrel.svg", alt: "Eating pizza versus turning into a squirrel", width: "7cm"}}}

Si llamamos a esa tabla _n_, podemos calcular _ϕ_ usando la siguiente fórmula:

{{if html

<div>
<table style="border-collapse: collapse; margin-left: 1em;"><tr>
  <td style="vertical-align: middle"><em>ϕ</em> =</td>
  <td style="padding-left: .5em">
    <div style="border-bottom: 1px solid black; padding: 0 7px;"><em>n</em><sub>11</sub><em>n</em><sub>00</sub> −
      <em>n</em><sub>10</sub><em>n</em><sub>01</sub></div>
    <div style="padding: 0 7px;">√<span style="border-top: 1px solid black; position: relative; top: 2px;">
      <span style="position: relative; top: -4px"><em>n</em><sub>1•</sub><em>n</em><sub>0•</sub><em>n</em><sub>•1</sub><em>n</em><sub>•0</sub></span>
    </span></div>
  </td>
</tr></table>
</div>

if}}

{{if tex

[\begin{equation}\varphi = \frac{n_{11}n_{00}-n_{10}n_{01}}{\sqrt{n_{1\bullet}n_{0\bullet}n_{\bullet1}n_{\bullet0}}}\end{equation}]{latex}

if}}

(Si en este momento estas bajando el libro para enfocarte en un terrible
flashback a la clase de matemática de 10° grado—espera!
No tengo la intención de torturarte con infinitas páginas de notación
críptica—solo esta fórmula para ahora. E incluso con esta,
todo lo que haremos es convertirla en JavaScript.)

La notación [_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} indica
el número de mediciones donde la primera variable (ardilla) es
falso (0) y la segunda variable (pizza) es verdadera (1). En la tabla
de pizza, [_n_~01~]{if html}[[$n_{01}$]{latex}]{if tex} es 9.

El valor [_n_~1•~]{if html}[[$n_{1\bullet}$]{latex}]{if tex} se refiere
a la suma de todas las medidas donde la primera variable es verdadera, que
es 5 en la tabla de ejemplo. Del mismo modo, [_n_~•0~]{if
html}[[$n_{\bullet0}$]{latex}]{if tex} se refiere a la suma de las
mediciones donde la segunda variable es falsa.

{{index correlation, "phi coefficient"}}

Entonces para la tabla de pizza, la parte arriba de la línea de división (el
dividendo) sería 1×76−4×9 = 40, y la parte inferior (el
divisor) sería la raíz cuadrada de 5×85×10×80, o [√340000]{if
html}[[$\sqrt{340000}$]{latex}]{if tex}. Esto da _ϕ_ ≈
0.069, que es muy pequeño. Comer ((pizza)) no parece tener
influencia en las transformaciones.

## Calculando correlación

{{index [array, "as table"], [nesting, "of arrays"]}}

Podemos representar una ((tabla)) de dos-por-dos en JavaScript con un
array de cuatro elementos (`[76, 9, 4, 1]`). También podríamos usar otras
representaciones, como un array que contiene dos arrays de dos elementos
(`[[76, 9], [4, 1]]`) o un objeto con nombres de propiedad como `"11"` y
`"01"`, pero el array plano es simple y hace que las expresiones que
acceden a la tabla agradablemente cortas. Interpretaremos los índices del
array como ((número binario))s de dos-((bits)) , donde el dígito más a la
izquierda (más significativo) se refiere a la variable ardilla y el digito
mas a la derecha (menos significativo) se refiere a la variable de evento.
Por ejemplo, el número binario `10` se refiere al caso en que Jacques se
convirtió en una ardilla, pero el evento (por ejemplo, "pizza") no ocurrió.
Esto ocurrió cuatro veces. Y dado que el `10` binario es 2 en notación decimal,
almacenaremos este número en el índice 2 del array.

{{index "phi coefficient", "phi function"}}

{{id phi_function}}

Esta es la función que calcula el coeficiente _ϕ_ de tal array:

```{includeCode: strip_log, test: clip}
function phi(tabla) {
  return (tabla[3] * tabla[0] - tabla[2] * tabla[1]) /
    Math.sqrt((tabla[2] + tabla[3]) *
              (tabla[0] + tabla[1]) *
              (tabla[1] + tabla[3]) *
              (tabla[0] + tabla[2]));
}

console.log(phi([76, 9, 4, 1]));
// → 0.068599434
```

{{index "square root", "Math.sqrt function"}}

Esta es una traducción directa de la fórmula _ϕ_ a JavaScript.
`Math.sqrt` es la función de raíz cuadrada, proporcionada por el objeto
`Math` en un entorno de JavaScript estándar. Tenemos que sumar dos campos
de la tabla para obtener campos como [n~1•~]{if
html}[[$n_{1\bullet}$]{latex}]{if tex} porque las sumas de filas o
columnas no se almacenan directamente en nuestra estructura de datos.

{{index "JOURNAL data set"}}

Jacques mantuvo su diario por tres meses. El ((conjunto de datos)) resultante
está disponible en la [caja de arena](https://eloquentjavascript.net/code#4)
para este capítulo[([_eloquentjavascript.net/code#4_](https://eloquentjavascript.net/code#4))]{if
book}, donde se almacena en la vinculación `JOURNAL`, y en un
[archivo](https://eloquentjavascript.net/code/journal.js) descargable.

{{index "tableFor function"}}

Para extraer una ((tabla)) de dos por dos para un evento en específico del
diario, debemos hacer un ciclo a traves de todas las entradas y contar
cuántas veces ocurre el evento en relación a las transformaciones de ardilla.

```{includeCode: strip_log}
function tablaPara(evento, diario) {
  let tabla = [0, 0, 0, 0];
  for (let i = 0; i < diario.length; i++) {
    let entrada = diario[i], index = 0;
    if (entrada.eventos.includes(evento)) index += 1;
    if (entrada.ardilla) index += 2;
    tabla[index] += 1;
  }
  return tabla;
}

console.log(tablaPara("pizza", JOURNAL));
// → [76, 9, 4, 1]
```

{{index [array, searching], "includes method"}}

Los array tienen un método `includes` ("incluye") que verifica si un valor dado
existe en el array. La función usa eso para determinar si el nombre del evento
en el que estamos interesados forma parte de la lista de eventos para
un determinado día.

{{index [array, indexing]}}

El cuerpo del ciclo en `tablaPara` determina en cual caja de la tabla
cae cada entrada del diario al verificar si la entrada contiene
el evento específico que nos interesa y si el evento ocurre
junto con un incidente de ardilla. El ciclo luego agrega uno a la caja correcta
en la tabla.

Ahora tenemos las herramientas que necesitamos para calcular las
((correlaciónes)) individuales. El único paso que queda es encontrar una
correlación para cada tipo de evento que se escribio en el diario
y ver si algo se destaca.

{{id for_of_loop}}

## Ciclos de array

{{index "for loop", loop, [array, iteration]}}

En la función `tablaPara`, hay un ciclo como este:

```
for (let i = 0; i < DIARIO.length; i++) {
  let entrada = DIARIO[i];
  // Hacer con algo con la entrada
}
```

Este tipo de ciclo es común en JavaScript clasico—ir a traves de los arrays
un elemento a la vez es algo que surge mucho, y para hacer eso
correrias un contador sobre la longitud del array y elegirías cada
elemento en turnos.

Hay una forma más simple de escribir tales ciclos en JavaScript moderno.

```
for (let entrada of DIARIO) {
  console.log(`${entrada.eventos.length} eventos.`);
}
```

{{index "for/of loop"}}

Cuando un ciclo `for` se vea de esta manera, con la palabra `of` ("de")
después de una definición de variable, recorrerá los elementos del valor
dado después `of`. Esto funciona no solo para arrays, sino también para
strings y algunas otras estructuras de datos. Vamos a discutir _como_ funciona
en el [Capítulo 6](objeto).

{{id analysis}}

## El análisis final

{{index journal, "weresquirrel example", "journalEvents function"}}

Necesitamos calcular una correlación para cada tipo de evento que ocurra
en el conjunto de datos. Para hacer eso, primero tenemos que _encontrar_
cada tipo de evento.

{{index "includes method", "push method"}}

```{includeCode: "strip_log"}
function eventosDiario(diario) {
  let eventos = [];
  for (let entrada of diario) {
    for (let evento of entrada.eventos) {
      if (!eventos.includes(evento)) {
        eventos.push(evento);
      }
    }
  }
  return eventos;
}

console.log(eventosDiario(DIARIO));
// → ["zanahoria", "ejercicio", "fin de semana", "pan", …]
```

Yendo a traves de todos los eventos, y agregando aquellos que aún no están en
allí en el array `eventos`, la función recolecta cada tipo de evento.

Usando eso, podemos ver todos las ((correlaciones)).

```{test: no}
for (let evento of eventosDiario(DIARIO)) {
  console.log(evento + ":", phi(tablaPara(evento, DIARIO)));
}
// → zanahoria:      0.0140970969
// → ejercicio:      0.0685994341
// → fin de semana:  0.1371988681
// → pan:           -0.0757554019
// → pudin:         -0.0648203724
// and so on...
```

La mayoría de las correlaciones parecen estar cercanas a cero. Come
zanahorias, pan o pudín aparentemente no desencadena la licantropía de ardilla.
_Parece_ ocurrir un poco más a menudo los fines de semana. Filtremos los
resultados para solo mostrar correlaciones mayores que 0.1 o menores que -0.1.

```{test: no, startCode: true}
for (let evento of eventosDiario(DIARIO)) {
  let correlacion = phi(tablaPara(evento, DIARIO));
  if (correlacion > 0.1 || correlacion < -0.1) {
    console.log(evento + ":", correlacion);
  }
}
// → fin de semana:           0.1371988681
// → me cepille los dientes: -0.3805211953
// → dulces:                  0.1296407447
// → trabajo:                -0.1371988681
// → spaghetti:               0.2425356250
// → leer:                    0.1106828054
// → nueces:                  0.5902679812
```

A-ha! Hay dos factores con una ((correlación)) que es claramente más fuerte
que las otras. Comer ((nueces)) tiene un fuerte efecto positivo en
la posibilidad de convertirse en una ardilla, mientras que cepillarse
los dientes tiene un significativo efecto negativo.

Interesante. Intentemos algo.

```{includeCode: strip_log}
for (let entrada of DIARIO) {
  if (entrada.eventos.includes("nueces") &&
     !entrada.eventos.includes("me cepille los dientes")) {
    entrada.eventos.push("dientes con nueces");
  }
}
console.log(phi(tablaPara("dientes con nueces", DIARIO)));
// → 1
```

Ese es un resultado fuerte. El fenómeno ocurre precisamente cuando Jacques
come ((nueces)) y no se cepilla los dientes. Si tan solo él no hubiese sido
tan flojo con su higiene dental, él nunca habría notado su aflicción.

Sabiendo esto, Jacques deja de comer nueces y descubre que sus transformaciones
no vuelven.

{{index "weresquirrel example"}}

Durante algunos años, las cosas van bien para Jacques. Pero en algún momento él
pierde su trabajo. Porque vive en un país desagradable donde no tener trabajo
significa que no tiene servicios médicos, se ve obligado a trabajar con
a ((circo)) donde actua como _El Increible Hombre-Ardilla_,
llenando su boca con mantequilla de maní antes de cada presentación.

Un día, harto de esta existencia lamentable, Jacques no puede cambiar
de vuelta a su forma humana, salta a través de una grieta en la carpa del
circo, y se desvanece en el bosque. Nunca se le ve de nuevo.

## Further arrayology

{{index [array, methods], method}}

Before finishing the chapter, I want to introduce you to a few more
object-related concepts. I'll start by introducing some generally
useful array methods.

{{index "push method", "pop method", "shift method", "unshift method"}}

We saw `push` and `pop`, which add and remove elements at the
end of an array, [earlier](data#array_methods) in this
chapter. The corresponding methods for adding and removing things at
the start of an array are called `unshift` and `shift`.

```
let todoList = [];
function remember(task) {
  todoList.push(task);
}
function getTask() {
  return todoList.shift();
}
function rememberUrgently(task) {
  todoList.unshift(task);
}
```

{{index "task management example"}}

That program manages a queue of tasks. You add tasks to the end of the
queue by calling `remember("groceries")`, and when you're ready to do
something, you call `getTask()` to get (and remove) the front item
from the queue. The `rememberUrgently` function also adds a task but
adds it to the front instead of the back of the queue.

{{index [array, searching], "indexOf method", "lastIndexOf method"}}

To search for a specific value, arrays provide an `indexOf` method. It
searches through the array from the start to the end and returns the
index at which the requested value was found—or -1 if it wasn't found.
To search from the end instead of the start, there's a similar method
called `lastIndexOf`.

```
console.log([1, 2, 3, 2, 1].indexOf(2));
// → 1
console.log([1, 2, 3, 2, 1].lastIndexOf(2));
// → 3
```

Both `indexOf` and `lastIndexOf` take an optional second argument that
indicates where to start searching.

{{index "slice method", [array, indexing]}}

Another fundamental array method is `slice`, which takes start and end
indices and returns an array that has only the elements between them.
The start index is inclusive, the end index exclusive.

```
console.log([0, 1, 2, 3, 4].slice(2, 4));
// → [2, 3]
console.log([0, 1, 2, 3, 4].slice(2));
// → [2, 3, 4]
```

{{index [string, indexing]}}

When the end index is not given, `slice` will take all of the elements
after the start index. You can also omit the start index to copy the
entire array.

{{index concatenation, "concat method"}}

The `concat` method can be used to glue arrays together to create a
new array, similar to what the `+` operator does for strings.

The following example shows both `concat` and `slice` in action. It takes
an array and an index, and it returns a new array that is a copy of
the original array with the element at the given index removed:

```
function remove(array, index) {
  return array.slice(0, index)
    .concat(array.slice(index + 1));
}
console.log(remove(["a", "b", "c", "d", "e"], 2));
// → ["a", "b", "d", "e"]
```

If you pass `concat` an argument that is not an array, that value will
be added to the new array as if it were a one-element array.

## Strings and their properties

{{index [string, properties]}}

We can read properties like `length` and `toUpperCase` from string
values. But if you try to add a new property, it doesn't stick.

```
let kim = "Kim";
kim.age = 88;
console.log(kim.age);
// → undefined
```

Values of type string, number, and Boolean are not objects, and though
the language doesn't complain if you try to set new properties on
them, it doesn't actually store those properties. As mentioned before,
such values are immutable and cannot be changed.

{{index [string, methods], "slice method", "indexOf method", [string, searching]}}

But these types do have built-in properties. Every string value has a
number of methods. Some very useful ones are `slice` and `indexOf`,
which resemble the array methods of the same name.

```
console.log("coconuts".slice(4, 7));
// → nut
console.log("coconut".indexOf("u"));
// → 5
```

One difference is that a string's `indexOf` can search for a string
containing more than one character, whereas the corresponding array
method looks only for a single element.

```
console.log("one two three".indexOf("ee"));
// → 11
```

{{index whitespace, "trim method"}}

The `trim` method removes whitespace (spaces, newlines, tabs, and
similar characters) from the start and end of a string.

```
console.log("  okay \n ".trim());
// → okay
```

The `zeroPad` function from the [previous chapter](functions) also
exists as a method. It is called `padStart` and takes the desired
length and padding character as arguments.

```
console.log(String(6).padStart(3, "0"));
// → 006
```

{{id split}}

You can split a string on every occurrence of another string with
`split`, and join it together again with `join`.

```
let sentence = "Secretarybirds specialize in stomping";
let words = sentence.split(" ");
console.log(words);
// → ["Secretarybirds", "specialize", "in", "stomping"]
console.log(words.join(". "));
// → Secretarybirds. specialize. in. stomping
```

{{index "repeat method"}}

A string can be repeated with the `repeat` method, which creates a new
string containing multiple copies of the original string, glued
together.

```
console.log("LA".repeat(3));
// → LALALA
```

{{index ["length property", "for string"], [string, indexing]}}

We have already seen the string type's `length` property. Accessing
the individual characters in a string looks like accessing array
elements (with a caveat that we'll discuss in [Chapter
?](higher_order#code_units)).

```
let string = "abc";
console.log(string.length);
// → 3
console.log(string[1]);
// → b
```

{{id rest_parameters}}

## Rest parameters

{{index "Math.max function"}}

It can be useful for a function to accept any number of ((argument))s.
For example, `Math.max` computes the maximum of _all_ the arguments it
is given.

{{indexsee "period character", "max example", spread}}

To write such a function, you put three dots before the function's
last ((parameter)), like this:

```
function max(...numbers) {
  let result = -Infinity;
  for (let number of numbers) {
    if (number > result) result = number;
  }
  return result;
}
console.log(max(4, 1, 9, -2));
// → 9
```

When such a function is called, the _((rest parameter))_ is bound to
an array containing all further arguments. If there are other
parameters before it, their values aren't part of that array. When, as
in `max`, it is the only parameter, it will hold all arguments.

{{index "function application"}}

You can use a similar three-dot notation to _call_ a function with an
array of arguments.

```
let numbers = [5, 1, 7];
console.log(max(...numbers));
// → 7
```

This "((spread))s" out the array into the function call, passing its
elements as separate arguments. It is possible to include an array
like that along with other arguments, as in `max(9, ...numbers, 2)`.

{{index array, "square brackets"}}

Square bracket array notation similarly allows the triple-dot operator
to spread another array into the new array:

```
let words = ["never", "fully"];
console.log(["will", ...words, "understand"]);
// → ["will", "never", "fully", "understand"]
```

## The Math object

{{index "Math object", "Math.min function", "Math.max function", "Math.sqrt function", minimum, maximum, "square root"}}

As we've seen, `Math` is a grab-bag of number-related utility
functions, such as `Math.max` (maximum), `Math.min` (minimum), and
`Math.sqrt` (square root).

{{index namespace, "namespace pollution", object}}

{{id namespace_pollution}}

The `Math` object is used as a container to group a bunch of related
functionality. There is only one `Math` object, and it is almost never
useful as a value. Rather, it provides a _namespace_ so that all these
functions and values do not have to be global bindings.

{{index [binding, naming]}}

Having too many global bindings "pollutes" the namespace. The more
names have been taken, the more likely you are to accidentally
overwrite the value of some existing binding. For example, it's not
unlikely to want to name something `max` in one of your programs.
Since JavaScript's built-in `max` function is tucked safely inside the
`Math` object, we don't have to worry about overwriting it.

{{index "let keyword", "const keyword"}}

Many languages will stop you, or at least warn you, when you are
defining a binding with a name that is already taken. JavaScript does
this for bindings you declared with `let` or `const`
but—perversely—not for standard bindings, nor for bindings declared
with `var` or `function`.

{{index "Math.cos function", "Math.sin function", "Math.tan function", "Math.acos function", "Math.asin function", "Math.atan function", "Math.PI constant", cosine, sine, tangent, "PI constant", pi}}

Back to the `Math` object. If you need to do ((trigonometry)), `Math`
can help. It contains `cos` (cosine), `sin` (sine), and `tan`
(tangent), as well as their inverse functions, `acos`, `asin`, and
`atan`, respectively. The number π (pi)—or at least the closest
approximation that fits in a JavaScript number—is available as
`Math.PI`. There is an old programming tradition of writing the names
of ((constant)) values in all caps.

```{test: no}
function randomPointOnCircle(radius) {
  let angle = Math.random() * 2 * Math.PI;
  return {x: radius * Math.cos(angle),
          y: radius * Math.sin(angle)};
}
console.log(randomPointOnCircle(2));
// → {x: 0.3667, y: 1.966}
```

If sines and cosines are not something you are familiar with, don't
worry. When they are used in this book, in [Chapter ?](dom#sin_cos),
I'll explain them.

{{index "Math.random function", "random number"}}

The previous example used `Math.random`. This is a function that
returns a new pseudorandom number between zero (inclusive) and one
(exclusive) every time you call it.

```{test: no}
console.log(Math.random());
// → 0.36993729369714856
console.log(Math.random());
// → 0.727367032552138
console.log(Math.random());
// → 0.40180766698904335
```

{{index "pseudorandom number", "random number"}}

Though computers are deterministic machines—they always react the same
way if given the same input—it is possible to have them produce
numbers that appear random. To do that, the machine keeps some hidden
value, and whenever you ask for a new random number, it performs
complicated computations on this hidden value to create a new value.
It stores a new value and returns some number derived from it. That
way, it can produce ever new, hard-to-predict numbers in a way that
_seems_ random.

{{index rounding, "Math.floor function"}}

If we want a whole random number instead of a fractional one, we can
use `Math.floor` (which rounds down to the nearest whole number) on
the result of `Math.random`.

```{test: no}
console.log(Math.floor(Math.random() * 10));
// → 2
```

Multiplying the random number by 10 gives us a number greater than or
equal to zero and below 10. Since `Math.floor` rounds down, this
expression will produce, with equal chance, any number from 0 through
9.

{{index "Math.ceil function", "Math.round function", "Math.abs function", "absolute value"}}

There are also the functions `Math.ceil` (for "ceiling", which rounds
up to a whole number), `Math.round` (to the nearest whole number), and
`Math.abs`, which takes the absolute value of a number, meaning it
negates negative values but leaves positive ones as they are.

## Destructuring

{{index "phi function"}}

Let's go back to the `phi` function for a moment:

```{test: wrap}
function phi(table) {
  return (table[3] * table[0] - table[2] * table[1]) /
    Math.sqrt((table[2] + table[3]) *
              (table[0] + table[1]) *
              (table[1] + table[3]) *
              (table[0] + table[2]));
}
```

{{index "destructuring binding", parameter}}

One of the reasons this function is awkward to read is that we have a
binding pointing at our array, but we'd much prefer to have bindings
for the _elements_ of the array, that is, `let n00 = table[0]` and so on.
Fortunately, there is a succinct way to do this in JavaScript.

```
function phi([n00, n01, n10, n11]) {
  return (n11 * n00 - n10 * n01) /
    Math.sqrt((n10 + n11) * (n00 + n01) *
              (n01 + n11) * (n00 + n10));
}
```

{{index "let keyword", "var keyword", "const keyword"}}

This also works for ((binding))s created with `let`, `var`, or
`const`. If you know the value you are binding is an array, you can
use ((square brackets)) to "look inside" of the value, binding its
contents.

{{index object, "curly braces"}}

A similar trick works for objects, using braces instead of square
brackets.

```
let {name} = {name: "Faraji", age: 23};
console.log(name);
// → Faraji
```

{{index null, undefined}}

Note that if you try to destructure `null` or `undefined`, you get an
error, much as you would if you directly try to access a property
of those values.

## JSON

{{index [array, representation], [object, representation], "data format"}}

Because properties only grasp their value, rather than contain it,
objects and arrays are stored in the computer's ((memory)) as
sequences of bits holding the _((address))es_—the place in memory—of
their contents. So an array with another array inside of it consists
of (at least) one memory region for the inner array, and another for
the outer array, containing (among other things) a binary number that
represents the position of the inner array.

If you want to save data in a file for later, or send it to another
computer over the network, you have to somehow convert these tangles
of memory addresses to a description that can be stored or sent. You
_could_ send over your entire computer memory along with the address
of the value you're interested in, I suppose, but that doesn't seem
like the best approach.

{{indexsee "JavaScript Object Notation", JSON}}

{{index serialization, "World Wide Web"}}

What we can do is _serialize_ the data. That means it is converted
into a flat description. A popular serialization format is called
_((JSON))_ (pronounced "Jason"), which stands for JavaScript Object
Notation. It is widely used as a data storage and communication format
on the Web, even in languages other than JavaScript.

{{index array, object, [quoting, "in JSON"], comment}}

JSON looks similar to JavaScript's way of writing arrays and objects,
with a few restrictions. All property names have to be surrounded by
double quotes, and only simple data expressions are allowed—no
function calls, bindings, or anything that involves actual
computation. Comments are not allowed in JSON.

A journal entry might look like this when represented as JSON data:

```{lang: "application/json"}
{
  "squirrel": false,
  "events": ["work", "touched tree", "pizza", "running"]
}
```

{{index "JSON.stringify function", "JSON.parse function", serialization, deserialization, parsing}}

JavaScript gives us the functions `JSON.stringify` and `JSON.parse` to
convert data to and from this format. The first takes a JavaScript
value and returns a JSON-encoded string. The second takes such a
string and converts it to the value it encodes.

```
let string = JSON.stringify({squirrel: false,
                             events: ["weekend"]});
console.log(string);
// → {"squirrel":false,"events":["weekend"]}
console.log(JSON.parse(string).events);
// → ["weekend"]
```

## Summary

Objects and arrays (which are a specific kind of object) provide ways
to group several values into a single value. Conceptually, this allows
us to put a bunch of related things in a bag and run around with the
bag, instead of wrapping our arms around all of the individual things
and trying to hold on to them separately.

Most values in JavaScript have properties, the exceptions being `null`
and `undefined`. Properties are accessed using `value.prop` or
`value["prop"]`. Objects tend to use names for their properties
and store more or less a fixed set of them. Arrays, on the other hand,
usually contain varying amounts of conceptually identical values and
use numbers (starting from 0) as the names of their properties.

There _are_ some named properties in arrays, such as `length` and a
number of methods. Methods are functions that live in properties and
(usually) act on the value they are a property of.

You can iterate over arrays using a special kind of `for` loop—`for
(let element of array)`.

## Exercises

### The sum of a range

{{index "summing (exercise)"}}

The [introduction](intro) of this book alluded to the following as a
nice way to compute the sum of a range of numbers:

```{test: no}
console.log(sum(range(1, 10)));
```

{{index "range function", "sum function"}}

Write a `range` function that takes two arguments, `start` and `end`,
and returns an array containing all the numbers from `start` up to
(and including) `end`.

Next, write a `sum` function that takes an array of numbers and
returns the sum of these numbers. Run the example program and see
whether it does indeed return 55.

{{index "optional argument"}}

As a bonus assignment, modify your `range` function to take an
optional third argument that indicates the "step" value used when
building the array. If no step is given, the elements go up by
increments of one, corresponding to the old behavior. The function
call `range(1, 10, 2)` should return `[1, 3, 5, 7, 9]`. Make sure it
also works with negative step values so that `range(5, 2, -1)`
produces `[5, 4, 3, 2]`.

{{if interactive

```{test: no}
// Your code here.

console.log(range(1, 10));
// → [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log(range(5, 2, -1));
// → [5, 4, 3, 2]
console.log(sum(range(1, 10)));
// → 55
```

if}}

{{hint

{{index "summing (exercise)", [array, creation], "square brackets"}}

Building up an array is most easily done by first initializing a
binding to `[]` (a fresh, empty array) and repeatedly calling its
`push` method to add a value. Don't forget to return the array at the
end of the function.

{{index [array, indexing], comparison}}

Since the end boundary is inclusive, you'll need to use the `<=`
operator rather than `<` to check for the end of your loop.

{{index "arguments object"}}

The step parameter can be an optional parameter that defaults (using
the `=` operator) to 1.

{{index "range function", "for loop"}}

Having `range` understand negative step values is probably best done
by writing two separate loops—one for counting up and one for counting
down—because the comparison that checks whether the loop is finished
needs to be `>=` rather than `<=` when counting downward.

It might also be worthwhile to use a different default step, namely
-1, when the end of the range is smaller than the start. That way,
`range(5, 2)` returns something meaningful, rather than getting stuck
in an ((infinite loop)). It is possible to refer to previous
parameters in the default value of a parameter.

hint}}

### Reversing an array

{{index "reversing (exercise)", "reverse method", [array, methods]}}

Arrays have a `reverse` method which changes the array by inverting
the order in which its elements appear. For this exercise, write two
functions, `reverseArray` and `reverseArrayInPlace`. The first,
`reverseArray`, takes an array as argument and produces a _new_ array
that has the same elements in the inverse order. The second,
`reverseArrayInPlace`, does what the `reverse` method does: it
_modifies_ the array given as argument by reversing its elements.
Neither may use the standard `reverse` method.

{{index efficiency, "pure function", "side effect"}}

Thinking back to the notes about side effects and pure functions in
the [previous chapter](functions#pure), which variant do you expect to
be useful in more situations? Which one runs faster?

{{if interactive

```{test: no}
// Your code here.

console.log(reverseArray(["A", "B", "C"]));
// → ["C", "B", "A"];
let arrayValue = [1, 2, 3, 4, 5];
reverseArrayInPlace(arrayValue);
console.log(arrayValue);
// → [5, 4, 3, 2, 1]
```

if}}

{{hint

{{index "reversing (exercise)"}}

There are two obvious ways to implement `reverseArray`. The first is
to simply go over the input array from front to back and use the
`unshift` method on the new array to insert each element at its start.
The second is to loop over the input array backwards and use the `push`
method. Iterating over an array backwards requires a (somewhat awkward)
`for` specification, like `(let i = array.length - 1; i >= 0; i--)`.

{{index "slice method"}}

Reversing the array in place is harder. You have to be careful not to
overwrite elements that you will later need. Using `reverseArray` or
otherwise copying the whole array (`array.slice(0)` is a good way to
copy an array) works but is cheating.

The trick is to _swap_ the first and last elements, then the second
and second-to-last, and so on. You can do this by looping over half
the length of the array (use `Math.floor` to round down—you don't need
to touch the middle element in an array with an odd number of
elements) and swapping the element at position `i` with the one at
position `array.length - 1 - i`. You can use a local binding to
briefly hold on to one of the elements, overwrite that one with its
mirror image, and then put the value from the local binding in the
place where the mirror image used to be.

hint}}

{{id list}}

### A list

{{index "data structure", "list (exercise)", "linked list", object, array, collection}}

Objects, as generic blobs of values, can be used to build all sorts of
data structures. A common data structure is the _list_ (not to be
confused with array). A list is a nested set of objects, with the
first object holding a reference to the second, the second to the
third, and so on.

```{includeCode: true}
let list = {
  value: 1,
  rest: {
    value: 2,
    rest: {
      value: 3,
      rest: null
    }
  }
};
```

The resulting objects form a chain, like this:

{{figure {url: "img/linked-list.svg", alt: "A linked list",width: "8cm"}}}

{{index "structure sharing", memory}}

A nice thing about lists is that they can share parts of their
structure. For example, if I create two new values `{value: 0, rest:
list}` and `{value: -1, rest: list}` (with `list` referring to the
binding defined earlier), they are both independent lists, but they
share the structure that makes up their last three elements. The
original list is also still a valid three-element list.

Write a function `arrayToList` that builds up a list structure like
the one shown when given `[1, 2, 3]` as argument. Also write a
`listToArray` function that produces an array from a list. Then add a
helper function `prepend`, which takes an element and a list and
creates a new list that adds the element to the front of the input
list, and `nth`, which takes a list and a number and returns the
element at the given position in the list (with zero referring to the
first element) or `undefined` when there is no such element.

{{index recursion}}

If you haven't already, also write a recursive version of `nth`.

{{if interactive

```{test: no}
// Your code here.

console.log(arrayToList([10, 20]));
// → {value: 10, rest: {value: 20, rest: null}}
console.log(listToArray(arrayToList([10, 20, 30])));
// → [10, 20, 30]
console.log(prepend(10, prepend(20, null)));
// → {value: 10, rest: {value: 20, rest: null}}
console.log(nth(arrayToList([10, 20, 30]), 1));
// → 20
```

if}}

{{hint

{{index "list (exercise)", "linked list"}}

Building up a list is easier when done back to front. So `arrayToList`
could iterate over the array backwards (see previous exercise) and, for
each element, add an object to the list. You can use a local binding
to hold the part of the list that was built so far and use an
assignment like `list = {value: X, rest: list}` to add an element.

{{index "for loop"}}

To run over a list (in `listToArray` and `nth`), a `for` loop
specification like this can be used:

```
for (let node = list; node; node = node.rest) {}
```

Can you see how that works? Every iteration of the loop, `node` points
to the current sublist, and the body can read its `value` property to
get the current element. At the end of an iteration, `node` moves to
the next sublist. When that is null, we have reached the end of the
list and the loop is finished.

{{index recursion}}

The recursive version of `nth` will, similarly, look at an ever
smaller part of the "tail" of the list and at the same time count down
the index until it reaches zero, at which point it can return the
`value` property of the node it is looking at. To get the zeroeth
element of a list, you simply take the `value` property of its head
node. To get element _N_ + 1, you take the _N_th element of the list
that's in this list's `rest` property.

hint}}

{{id exercise_deep_compare}}

### Deep comparison

{{index "deep comparison (exercise)", comparison, "deep comparison", "== operator"}}

The `==` operator compares objects by identity. But sometimes you'd
prefer to compare the values of their actual properties.

Write a function `deepEqual` that takes two values and returns true
only if they are the same value or are objects with the same
properties, where the values of the properties are equal when compared
with a recursive call to `deepEqual`.

{{index null, "=== operator", "typeof operator"}}

To find out whether values should be compared directly (use the `===`
operator for that) or have their properties compared, you can use the
`typeof` operator. If it produces `"object"` for both values, you
should do a deep comparison. But you have to take one silly exception
into account: because of a historical accident, `typeof null` also
produces `"object"`.

{{index "Object.keys function"}}

The `Object.keys` function will be useful when you need to go over the
properties of objects to compare them.

{{if interactive

```{test: no}
// Your code here.

let obj = {here: {is: "an"}, object: 2};
console.log(deepEqual(obj, obj));
// → true
console.log(deepEqual(obj, {here: 1, object: 2}));
// → false
console.log(deepEqual(obj, {here: {is: "an"}, object: 2}));
// → true
```

if}}

{{hint

{{index "deep comparison (exercise)", "typeof operator", object, "=== operator"}}

Your test for whether you are dealing with a real object will look
something like `typeof x == "object" && x != null`. Be careful to
compare properties only when _both_ arguments are objects. In all
other cases you can just immediately return the result of applying
`===`.

{{index "Object.keys function"}}

Use `Object.keys` to go over the properties. You need to test whether
both objects have the same set of property names and whether those
properties have identical values. One way to do that is to ensure that
both objects have the same number of properties (the lengths of the
property lists are the same). And then, when looping over one of the
object's properties in order to compare them, always first make sure
the other actually has a property by that name. If they have the same
number of properties, and all properties in one also exist in the
other, they have the same set of property names.

{{index "return value"}}

Returning the correct value from the function is best done by
immediately returning false when a mismatch is found and returning
true at the end of the function.

hint}}
