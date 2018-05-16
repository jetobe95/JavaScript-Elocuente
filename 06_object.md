{{meta {load_files: ["code/chapter/06_object.js"], zip: "node/html"}}}

# La Vida Secreta de los Objetos

{{quote {author: "Barbara Liskov", title: "Programming with Abstract Data Types", chapter: true}

Un tipo de datos abstracto se realiza al escribir un tipo especial de programa
[...] que define el tipo en base a las operaciones que puedan ser
realizadas en él.

quote}}

{{index "Liskov, Barbara", "abstract data type"}}

{{figure {url: "img/chapter_picture_6.jpg", alt: "Picture of a rabbit with its proto-rabbit", chapter: framed}}}

El [Capítulo 4](datos) introdujo los ((objeto))s en JavaScript. En la cultura
de la programación, tenemos una cosa llamada _((programación orientada a objetos))_,
la cual es un conjunto de técnicas que usan objetos (y conceptos relacionados)
como el principio central de la organización del programa.

Aunque nadie realmente está de acuerdo con su definición exacta,
la programación orientada a objetos ha contribuido al diseño de muchos
lenguajes de programación, incluyendo JavaScript. Este capítulo describirá la
forma en la que estas ideas pueden ser aplicadas en JavaScript.

## Encapsulación

{{index encapsulation, isolation, modularity}}

La idea central en la programación orientada a objetos es dividir a los programas
en piezas más pequeñas y hacer que cada pieza sea responsable de gestionar su
propio estado.

De esta forma, los conocimientos acerca de como funciona una parte
del programa pueden mantenerse _locales_ a esa pieza. Alguien trabajando en otra
parte del programa no tiene que recordar o ni siquiera tener una idea
de ese conocimiento. Cada vez que los detalles locales cambien, solo
el código directamente a su alrededor debe ser actualizado.

{{id interface}}

Las diferentes piezas de un programa como tal, interactúan entre sí a través de
_((interfaces))_, las cuales son conjuntos limitados de funciones y
vinculaciones que proporcionan funcionalidades útiles en un nivel más
abstracto, ocultando asi su implementación interna.

{{index "public properties", "private properties", "access control"}}

Tales piezas del programa se modelan usando ((objeto))s. Sus interfaces
consisten en un conjunto específico de ((método))s y propiedades. Las propiedades
que son parte de la interfaz se llaman _publicas_. Las otras, las cuales no
deberian ser tocadas por el código externo , se les llama _privadas_.

{{index "underscore character"}}

Muchos lenguajes proporcionan una forma de distinguir entre propiedades publicas
y privadas, y ademas evitarán que el código externo pueda acceder a las privadas
por completo. JavaScript, una vez más tomando el enfoque minimalista,
no hace esto. Todavía no, al menos—hay trabajo en camino para agregar
esto al lenguaje.

Aunque el lenguaje no tenga esta distinción incorporada,
los programadores de JavaScript _estan_ usando esta idea con éxito .Típicamente,
la interfaz disponible se describe en la documentación o en los comentarios.
También es común poner un carácter de guión bajo (`_`) al comienzo de los
nombres de las propiedades para indicar que estas propiedades son privadas.

Separar la interfaz de la implementación es una gran idea. Esto
usualmente es llamado _((encapsulación))_.

{{id obj_methods}}

## Métodos

{{index "rabbit example", method, property}}

Los métodos no son más que propiedades que tienen valores de función.
Este es un método simple:

```
let conejo = {};
conejo.hablar = function(linea) {
  console.log(`El conejo dice '${linea}'`);
};

conejo.hablar("Estoy vivo.");
// → El conejo dice 'Estoy vivo.'
```

{{index this, "method call"}}

Por lo general, un método debe hacer algo en el objeto con que se llamó.
Cuando una función es llamada como un método—buscada como una propiedad y
llamada inmediatamente, como en `objeto.metodo()`—la vinculación llamada `this`
("este") en su cuerpo apunta automáticamente al objeto en la cual fue llamada.

```{includeCode: "top_lines:6", test: join}
function hablar(linea) {
  console.log(`El conejo ${this.tipo} dice '${linea}'`);
}
let conejoBlanco = {tipo: "blanco", hablar};
let conejoHambriento = {tipo: "hambriento", hablar};

conejoBlanco.hablar("Oh mis orejas y bigotes, " +
                  "que tarde se esta haciendo!");
// → El conejo blanco dice 'Oh mis orejas y bigotes, que
//   tarde se esta haciendo!'
conejoHambriento.hablar("Podria comerme una zanahoria ahora mismo.");
// → El conejo hambriento dice 'Podria comerme una zanahoria ahora mismo.'
```

{{id call_method}}

{{index "call method"}}

Puedes pensar en `this` como un ((parámetro)) extra que es pasado en
una manera diferente. Si quieres pasarlo explícitamente, puedes usar
el método `call` ("llamar") de una función, que toma el valor de `this`
como primer argumento y trata a los argumentos adicionales como parámetros
normales.

```
hablar.call(conejoHambriento, "Burp!");
// → El conejo hambriento dice 'Burp!'
```

Como cada función tiene su propia vinculación `this`, cuyo valor depende de
la forma en como esta se llama, no puedes hacer referencia al `this` del
alcance envolvente en una función regular definida con la palabra clave
`function`.

{{index this, "arrow function"}}

Las funciones de flecha son diferentes—no crean su propia vinculación `this`,
pero pueden ver la vinculación`this` del alcance a su alrededor. Por lo tanto,
puedes hacer algo como el siguiente código, que hace referencia a `this`
desde adentro de una función local:

```
function normalizar() {
  console.log(this.coordinadas.map(n => n / this.length));
}
normalizar.call({coordinadas: [0, 2, 3], length: 5});
// → [0, 0.4, 0.6]
```

{{index "map method"}}

Si hubieras escrito el argumento para `map` usando la palabra clave `function`,
el código no funcionaría.

{{id prototypes}}

## Prototipos

{{index "toString method"}}

Observa atentamente.

```
let vacio = {};
console.log(vacio.toString);
// → function toString(){…}
console.log(vacio.toString());
// → [object Object]
```

{{index magic}}

Saqué una propiedad de un objeto vacío. Magia!

{{index property, object}}

Bueno, en realidad no. Simplemente he estado ocultando información acerca de
como funcionan los objetos en JavaScript. En adición a su conjunto de propiedades,
la mayoría de los objetos también tienen un _((prototipo))_. Un prototipo es otro
objeto que se utiliza como una reserva de propiedades alternativa. Cuando un
objeto recibe una solicitud por una propiedad que este no tiene,
se buscará en su prototipo la propiedad, luego en el prototipo del prototipo y
asi sucesivamente.

{{index "Object prototype"}}

Asi que, quién es el ((prototipo)) de ese objeto vacío? Es el gran
prototipo ancestral, la entidad detrás de casi todos los objetos,
`Object.prototype` ("Objeto.prototipo").

```
console.log(Object.getPrototypeOf({}) ==
            Object.prototype);
// → true
console.log(Object.getPrototypeOf(Object.prototype));
// → null
```

{{index "getPrototypeOf function"}}

Como puedes adivinar, `Object.getPrototypeOf` ("Objeto.obtenerPrototipoDe")
retorna el prototipo de un objeto.

{{index "toString method"}}

Las relaciones prototipo de los objetos en JavaScript forman una estructura
en forma de ((árbol)), y en la raíz de esta estructura se encuentra
`Object.prototype`. Este proporciona algunos métodos que pueden ser accedidos
por todos los objetos, como `toString`, que convierte un objeto en una
representación de tipo string.

{{index inheritance, "Function prototype", "Array prototype", "Object prototype"}}

Muchos objetos no tienen `Object.prototype` directamente como su ((prototipo)),
pero en su lugar tienen otro objeto que proporciona un conjunto diferente de
propiedades predeterminadas. Las funciones derivan de `Function.prototype`, y
los arrays derivan de `Array.prototype`.

```
console.log(Object.getPrototypeOf(Math.max) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf([]) ==
            Array.prototype);
// → true
```

{{index "Object prototype"}}

Tal prototipo de objeto tendrá en si mismo un prototipo, a menudo `Object.prototype`,
por lo que aún proporciona indirectamente métodos como `toString`.

{{index "rabbit example", "Object.create function"}}

Puede usar `Object.create` para crear un objeto con un ((prototipo)) especifico.

```
let conejoPrototipo = {
  hablar(linea) {
    console.log(`El conejo ${this.tipo} dice '${linea}'`);
  }
};
let conejoAsesino = Object.create(conejoPrototipo);
conejoAsesino.tipo = "asesino";
conejoAsesino.hablar("SKREEEE!");
// → El conejo asesino dice 'SKREEEE!'
```

{{index "shared property"}}

Una propiedad como `hablar(linea)` en una expresión de objeto es un atajo
para definir un método. Esta crea una propiedad llamada `hablar` y le da
una función como su valor.

El conejo "prototipo" actúa como un contenedor para las propiedades que son
compartidas por todos los conejos. Un objeto de conejo individual, como el
conejo asesino, contiene propiedades que aplican solo a sí mismo—en este
caso su tipo—y deriva propiedades compartidas desde su prototipo.

{{id classes}}

## Clases

{{index "object-oriented programming"}}

El sistema de ((prototipos)) en JavaScript se puede interpretar como un
enfoque informal de un concepto orientado a objetos llamado _((clase))es_.
Una clase define la forma de un tipo de objeto—qué métodos y
propiedades tiene este. Tal objeto es llamado una _((instancia))_ de la
clase.

Los prototipos son útiles para definir propiedades en las cuales todas las
instancias de una clase compartan el mismo valor, como ((método))s.
Las propiedades que difieren por instancia, como la ((propiedad)) `tipo`
en nuestros conejos, necesitan almacenarse directamente en los objetos mismos.

{{id constructors}}

Entonces, para crear una instancia de una clase dada, debes crear
un objeto que derive del prototipo adecuado, pero _también_ debes
asegurarte de que, en sí mismo, este objeto tenga las propiedades que
las instancias de esta clase se supone que tengan.
Esto es lo que una función _((constructora))_ hace.

```
function crearConejo(tipo) {
  let conejo = Object.create(conejoPrototipo);
  conejo.tipo = tipo;
  return conejo;
}
```

{{index "new operator", this, "return keyword", [object, creation]}}

JavaScript proporciona una manera de hacer que la definición de este tipo de
funciones sea más fácil. Si colocas la palabra clave `new` ("new") delante de
una llamada de función, la función sera tratada como un constructor. Esto
significa que un objeto con el prototipo adecuado es creado automáticamente,
vinculado a `this` en la función, y retornado al final de la función.

{{index "prototype property"}}

El objeto prototipo utilizado al construir objetos se encuentra al tomar
la propiedad `prototype` de la función constructora.

{{index "rabbit example"}}

```
function Conejo(tipo) {
  this.tipo = tipo;
}
Conejo.prototype.hablar = function(linea) {
  console.log(`El conejo ${this.tipo} dice '${linea}'`);
};

let conejoRaro = new Conejo("raro");
```

{{index constructor}}

Los constructores (todas las funciones, de hecho) automáticamente obtienen
una propiedad llamada `prototype`, que por defecto contiene un objeto simple
y vacío, que deriva de `Object.prototype`. Puedes sobrescribirlo con un nuevo
objeto si asi quieres. O puedes agregar propiedades al objeto ya existente,
como lo hace el ejemplo.

{{index capitalization}}

Por convención, los nombres de los constructores tienen la primera letra en
mayúscula para que se puedan distinguir fácilmente de otras funciones.

{{index "prototype property", "getPrototypeOf function"}}

Es importante entender la distinción entre la forma en que un prototipo
está asociado con un constructor (a través de su propiedad `prototype`)
y la forma en que los objetos _tienen_ un prototipo (que se puede
encontrar con `Object.getPrototypeOf`). El prototipo real de un constructor
es `Function.prototype`, ya que los constructores son funciones. Su
_propiedad_ `prototype` contiene el prototipo utilizado para las
instancias creadas a traves de el.

```
console.log(Object.getPrototypeOf(Conejo) ==
            Function.prototype);
// → true
console.log(Object.getPrototypeOf(conejoRaro) ==
            Conejo.prototype);
// → true
```

## Notación de clase

Entonces, las ((clase))es en JavaScript son funciones ((constructoras)) con una
propiedad ((prototipo)). Así es como funcionan, y hasta 2015, esa
era la manera en como tenías que escribirlas. Estos días, tenemos una
notación menos incómoda.

```{includeCode: true}
class Conejo {
  constructor(tipo) {
    this.tipo = tipo;
  }
  hablar(linea) {
    console.log(`El conejo ${this.tipo} dice '${linea}'`);
  }
}

let conejoAsesino = new Conejo("asesino");
let conejoNegro = new Conejo("negro");
```

{{index "rabbit example"}}

La palabra clave `class` ("clase") comienza una ((declaración de clase)),
que nos permite definir un constructor y un conjunto de métodos, todo en un
solo lugar. Cualquier número de métodos se pueden escribir dentro de las llaves
de la declaración. El metodo llamado `constructor` es tratado de una manera
especial. Este proporciona la función constructora real, que estará vinculada al
nombre `Conejo`. Los otros metodos estaran empacados en el prototipo de ese
constructor. Por lo tanto, la declaración de clase anterior es equivalente a la
definición de constructor en la sección anterior. Solo que se ve mejor.

{{index ["class declaration", properties]}}

Actualmente las declaraciones de clase solo permiten que los _metodos_—propiedades
que contengan funciones—puedan ser agregados al ((prototipo)). Esto puede ser
algo inconveniente para cuando quieras guardar un valor no-funcional allí.
La próxima versión del lenguaje probablemente mejore esto. Por ahora, tú
puedes crear tales propiedades al manipular directamente el
prototipo después de haber definido la clase.

Al igual que `function`, `class` se puede usar tanto en posiciones de
declaración como de expresión. Cuando se usa como una expresión, no define una
vinculación, pero solo produce el constructor como un valor. Tienes permitido
omitir el nombre de clase en una expresión de clase.

```
let objeto = new class { obtenerPalabra() { return "hola"; } };
console.log(objeto.obtenerPalabra());
// → hola
```

## Sobreescribiendo propiedades derivadas

{{index "shared property", overriding}}

Cuando le agregas una ((propiedad)) a un objeto, ya sea que esté presente en
el prototipo o no, la propiedad es agregada al objeto _en si mismo_.
Si ya había una propiedad con el mismo nombre en el prototipo, esta propiedad
ya no afectará al objeto, ya que ahora está oculta detrás de la propiedad del
propio objeto.

```
Rabbit.prototype.dientes = "pequeños";
console.log(conejoAsesino.dientes);
// → pequeños
conejoAsesino.dientes = "largos, filosos, y sangrientos";
console.log(conejoAsesino.dientes);
// → largos, filosos, y sangrientos
console.log(conejoNegro.dientes);
// → pequeños
console.log(Rabbit.prototype.dientes);
// → pequeños
```

{{index [prototype, diagram]}}

El siguiente diagrama esboza la situación después de que este código ha sido
ejecutado. Los ((prototipo))s de `Conejo` y `Object` se encuentran detrás de
`conejoAsesino` como una especie de telón de fondo, donde las propiedades
que no se encuentren en el objeto en sí mismo puedan ser buscadas.

{{figure {url: "img/rabbits.svg", alt: "Rabbit object prototype schema",width: "8cm"}}}

{{index "shared property"}}

Sobreescribir propiedades que existen en un prototipo puede ser algo útil
que hacer. Como muestra el ejemplo de los dientes de conejo, esto se
puede usar para expresar propiedades excepcionales en instancias de una clase
más genérica de objetos, dejando que los objetos no-excepcionales tomen un
valor estándar desde su prototipo.

{{index "toString method", "Array prototype", "Function prototype"}}

También puedes sobreescribir para darle a los prototipos estándar de función y
array un método diferente `toString` al del objeto prototipo básico.

```
console.log(Array.prototype.toString ==
            Object.prototype.toString);
// → false
console.log([1, 2].toString());
// → 1,2
```

{{index "toString method", "join method", "call method"}}

Llamar a `toString` en un array da un resultado similar al de una llamada
`.join(",")` en él—pone comas entre los valores del array.
Llamar directamente a `Object.prototype.toString` con un array produce un
string diferente. Esa función no sabe acerca de los arrays, por lo que
simplemente pone la palabra _object_ y el nombre del tipo entre corchetes.

```
console.log(Object.prototype.toString.call([1, 2]));
// → [object Array]
```

## Mapas

{{index "map method"}}

Vimos a la palabra _map_ usada en el [capítulo anterior](orden_superior#map)
para una operación que transforma una estructura de datos al aplicar una
función en sus elementos.

{{index "map (data structure)", "ages example", "data structure"}}

Un _mapa_ (sustantivo) es una estructura de datos que asocia valores (las llaves)
con otros valores. Por ejemplo, es posible que desees mapear nombres a edades.
Es posible usar objetos para esto.

```
let edades = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};

console.log(`Júlia tiene ${edades["Júlia"]}`);
// → Júlia tiene 62
console.log("Se conoce la edad de Jack?", "Jack" in edades);
// → Se conoce la edad de Jack? false
console.log("Se conoce la edad de toString?", "toString" in edades);
// → Se conoce la edad de toString? true
```

{{index "Object.prototype", "toString method"}}

Aquí, los nombres de las propiedades del objeto son los nombres de las
personas, y los valores de las propiedades sus edades. Pero ciertamente no
incluimos a nadie llamado toString en nuestro mapa. Sin embargo, debido a
que los  objetos simples se derivan de `Object.prototype`, parece que
la propiedad está ahí.

{{index "Object.create function", prototype}}

Como tal, usar objetos simples como mapas es peligroso. Hay varias
formas posibles de evitar este problema. Primero, es posible crear
objetos sin _ningun_ prototipo. Si pasas `null` a `Object.create`,
el objeto resultante no se derivará de `Object.prototype` y podra ser
usado de forma segura como un mapa.

```
console.log("toString" in Object.create(null));
// → false
```

Los nombres de las ((propiedades)) de los objetos deben ser strings.
Si necesitas un mapa cuyas claves no puedan ser convertidas fácilmente a
strings—como objetos—no puedes usar un objeto como tu mapa.

{{index "Map class"}}

Afortunadamente, JavaScript viene con una clase llamada `Map` que esta
escrita para este propósito exacto. Esta almacena un mapeo y permite cualquier
tipo de llaves.

```
let edades = new Map();
edades.set("Boris", 39);
edades.set("Liang", 22);
edades.set("Júlia", 62);

console.log(`Júlia tiene ${edades.get("Júlia")}`);
// → Júlia tiene 62
console.log("Se conoce la edad de Jack?", edades.has("Jack"));
// → Se conoce la edad de Jack? false
console.log(edades.has("toString"));
// → false
```

{{index interface, "set method", "get method", "has method", encapsulation}}

Los métodos `set` ("establecer"),` get` ("obtener"), y `has` ("tiene")
son parte de la interfaz del objeto `Map`.
Escribir una estructura de datos que pueda actualizarse rápidamente y
buscar en un gran conjunto de valores no es fácil, pero no tenemos que
preocuparnos acerca de eso. Alguien más lo hizo por nosotros, y podemos
utilizar esta simple interfaz para usar su trabajo.

{{index "hasOwnProperty method", "in operator"}}

Si tienes un objeto simple que necesitas tratar como un mapa por alguna
razón, es útil saber que `Object.keys` solo retorna las llaves propias del
objeto, no las que estan en el prototipo. Como alternativa al operador `in`,
puedes usar el método` hasOwnProperty` ("tienePropiaPropiedad"), el cual
ignora el prototipo del objeto.

```
console.log({x: 1}.hasOwnProperty("x"));
// → true
console.log({x: 1}.hasOwnProperty("toString"));
// → false
```

## Polimorfismo

{{index "toString method", "String function", polymorphism, overriding, "object-oriented programming"}}

Cuando llamas a la función `String` (que convierte un valor a un
string) en un objeto, llamará al método `toString` en ese
objeto para tratar de crear un string significativo a partir de el. Mencioné que
algunos de los prototipos estándar definen su propia versión de `toString`
para que puedan crear un string que contenga información más útil que
`"[object Object]"`. También puedes hacer eso tú mismo.

```{includeCode: "top_lines: 3"}
Conejo.prototype.toString = function() {
  return `un conejo ${this.tipo}`;
};

console.log(String(conejoNegro));
// → un conejo negro
```

{{index "object-oriented programming"}}

Esta es una instancia simple de una idea poderosa. Cuando un pedazo de código es
escrito para funcionar con objetos que tienen una cierta ((interfaz))—en este
caso, un método `toString`—cualquier tipo de objeto que soporte
esta interfaz se puede conectar al código, y simplemente funcionará.

Esta técnica se llama _polimorfismo_. El código polimórfico puede funcionar
con valores de diferentes formas, siempre y cuando soporten la interfaz
que este espera.

{{index "for/of loop", "iterator interface"}}

Mencioné en el [Capítulo 4](datos#for_of_loop) que un ciclo `for`/`of`
puede recorrer varios tipos de estructuras de datos. Este es otro caso
de polimorfismo—tales ciclos esperan que la estructura de datos exponga una
interfaz específica, lo que hacen los arrays y strings. Y también puedes agregar
esta interfaz a tus propios objetos! Pero antes de que podamos hacer eso,
necesitamos saber qué son los símbolos.

## Símbolos

Es posible que múltiples interfaces usen el mismo nombre de propiedad
para diferentes cosas. Por ejemplo, podría definir una interfaz en la que
se suponga que el método `toString` convierte el objeto a una pieza
de hilo. No sería posible para un objeto ajustarse a
esa interfaz y al uso estándar de `toString`.

Esa sería una mala idea, y este problema no es muy común. La mayoria de
los programadores de JavaScript simplemente no piensan en eso.
Pero los diseñadores del lenguaje, cuyo _trabajo_ es pensar acerca de estas
cosas, nos han proporcionado una solución de todos modos.

{{index "Symbol function", property}}

Cuando afirmé que los nombres de propiedad son strings, eso no fue del todo
preciso. Usualmente lo son, pero también pueden ser _((símbolo))s_.
Los símbolos son valores creados con la función `Symbol`. A diferencia de los
strings, los símbolos recién creados son únicos—no puedes crear el mismo símbolo
dos veces.

```
let simbolo = Symbol("nombre");
console.log(simbolo == Symbol("nombre"));
// → false
Conejo.prototype[simbolo] = 55;
console.log(conejoNegro[simbolo]);
// → 55
```

El string que pases a `Symbol` es incluido cuando lo conviertas a
string, y puede hacer que sea más fácil reconocer un símbolo cuando, por
ejemplo, lo muestres en la consola. Pero no tiene sentido más allá de
eso—múltiples símbolos pueden tener el mismo nombre.

Al ser únicos y utilizables como nombres de propiedad, los símbolos son adecuados
para definir interfaces que pueden vivir pacíficamente junto a otras
propiedades, sin importar cuáles sean sus nombres.

```{includeCode: "top_lines: 1"}
const simboloToString = Symbol("toString");
Array.prototype[simboloToString] = function() {
  return `${this.length} cm de hilo azul`;
};

console.log([1, 2].toString());
// → 1,2
console.log([1, 2][simboloToString]());
// → 2 cm de hilo azul
```

Es posible incluir propiedades de símbolos en expresiones de objetos y
clases usando ((corchete))s alrededor del nombre de la ((propiedad)).
Eso hace que se evalúe el nombre de la propiedad, al igual que la
notación de corchetes para acceder propiedades, lo cual
nos permite hacer referencia a una vinculación que contiene el símbolo.

```
let objetoString = {
  [simboloToString]() { return "una cuerda de cañamo"; }
};
console.log(objetoString[simboloToString]());
// → una cuerda de cañamo
```

## The iterator interface

{{index "iterable interface", "Symbol.iterator symbol", "for/of loop"}}

The object given to a `for`/`of` loop is expected to be _iterable_.
This means that it has a method named with the `Symbol.iterator`
symbol (a symbol value defined by the language, stored as a property
of the `Symbol` function).

{{index "iterator interface", "next method"}}

When called, that method should return an object that provides a
second interface, _iterator_. This is the actual thing that iterates.
It has a `next` method that returns the next result. That result
should be an object with a `value` property, providing the next value,
if there is one, and a `done` property which should be true when there
are no more results and false otherwise.

Note that the `next`, `value`, and `done` property names are plain
strings, not symbols. Only `Symbol.iterator`, which is likely to be
added to a _lot_ of different objects, is an actual symbol.

We can directly use this interface ourselves.

```
let okIterator = "OK"[Symbol.iterator]();
console.log(okIterator.next());
// → {value: "O", done: false}
console.log(okIterator.next());
// → {value: "K", done: false}
console.log(okIterator.next());
// → {value: undefined, done: true}
```

{{index "matrix example", "Matrix class", array}}

{{id matrix}}

Let's implement an iterable data structure. We'll build a _matrix_
class, acting as a two-dimensional array.

```{includeCode: true}
class Matrix {
  constructor(width, height, element = (x, y) => undefined) {
    this.width = width;
    this.height = height;
    this.content = [];

    for (let y = 0; y < height; y++) {
      for (let x = 0; x < width; x++) {
        this.content[y * width + x] = element(x, y);
      }
    }
  }

  get(x, y) {
    return this.content[y * this.width + x];
  }
  set(x, y, value) {
    this.content[y * this.width + x] = value;
  }
}
```

The class stores its content in a single array of _width_ × _height_
elements. The elements are stored row-by-row, so, for example, the third
element in the fifth row is (using zero-based indexing) stored at
position 4 × _width_ + 2.

The constructor function takes a width, height, and an optional
content function that will be used to fill in the initial values.
There are `get` and `set` methods to retrieve and update elements in
the matrix.

When looping over a matrix, you are usually interested in the position
of the elements as well as the elements themselves, so we'll have our
iterator produce objects with `x`, `y`, and `value` properties.

{{index "MatrixIterator class"}}

```{includeCode: true}
class MatrixIterator {
  constructor(matrix) {
    this.x = 0;
    this.y = 0;
    this.matrix = matrix;
  }

  next() {
    if (this.y == this.matrix.height) return {done: true};

    let value = {x: this.x,
                 y: this.y,
                 value: this.matrix.get(this.x, this.y)};
    this.x++;
    if (this.x == this.matrix.width) {
      this.x = 0;
      this.y++;
    }
    return {value, done: false};
  }
}
```

The class tracks the progress of iterating over a matrix in its `x`
and `y` properties. The `next` method starts by checking whether the
bottom of the matrix has been reached. If it hasn't, it _first_
creates the object holding the current value and _then_ updates its
position, moving to the next row if necessary.

Let us set up the `Matrix` class to be iterable. Throughout this book,
I'll occasionally use after-the-fact prototype manipulation to add
methods to classes, so that the individual pieces of code remain small
and self-contained. In a regular program, where there is no need to
split the code into small pieces, you'd declare these methods directly
in the class instead.

```{includeCode: true}
Matrix.prototype[Symbol.iterator] = function() {
  return new MatrixIterator(this);
};
```

{{index "for/of loop"}}

We can now loop over a matrix with `for`/`of`.

```
let matrix = new Matrix(2, 2, (x, y) => `value ${x},${y}`);
for (let {x, y, value} of matrix) {
  console.log(x, y, value);
}
// → 0 0 value 0,0
// → 1 0 value 1,0
// → 0 1 value 0,1
// → 1 1 value 1,1
```

## Getters, setters, and statics

{{index interface, property, "Map class"}}

Interfaces often consist mostly of methods, but it is also okay to
include properties that hold non-function values. For example, `Map`
objects have a `size` property that tells you how many keys are stored
in them.

It is not even necessary for such an object to compute and store such
a property directly in the instance. Even properties that are accessed
directly may hide a method call. Such methods are called
_((getter))s_, and they are defined by writing `get` in front of the
method name in an object expression or class declaration.

```{test: no}
let varyingSize = {
  get size() {
    return Math.floor(Math.random() * 100);
  }
};

console.log(varyingSize.size);
// → 73
console.log(varyingSize.size);
// → 49
```

{{index "temperature example"}}

Whenever someone reads from this object's `size` property, the
associated method is called. You can do a similar thing when a
property is written to, using a _((setter))_.

```{test: no, startCode: true}
class Temperature {
  constructor(celsius) {
    this.celsius = celsius;
  }
  get fahrenheit() {
    return this.celsius * 1.8 + 32;
  }
  set fahrenheit(value) {
    this.celsius = (value - 32) / 1.8;
  }

  static fromFahrenheit(value) {
    return new Temperature((value - 32) / 1.8);
  }
}

let temp = new Temperature(22);
console.log(temp.fahrenheit);
// → 71.6
temp.fahrenheit = 86;
console.log(temp.celsius);
// → 30
```

The `Temperature` class allows you to read and write the temperature
in either degrees ((Celsius)) or degrees ((Fahrenheit)), but
internally only stores Celsius, and automatically converts to Celsius
in the `fahrenheit` getter and setter.

{{index "static method"}}

Sometimes you want to attach some properties directly to your
constructor function, rather than to the prototype. Such methods won't
have access to a class instance but can, for example, be used to
provide additional ways to create instances.

Inside a class declaration, methods that have `static` written before
their name are stored on the constructor. So the `Temperature` class
allows you to write `Temperature.fromFahrenheit(100)` to create a
temperature using degrees Fahrenheit.

## Inheritance

{{index inheritance, "matrix example", "object-oriented programming", "SymmetricMatrix class"}}

Some matrices are known to be _symmetric_. If you mirror a symmetric
matrix around its top-left-to-bottom-right diagonal, it stays the
same. In other words, the value stored at _x_,_y_ is always the same
as that at _y_,_x_.

Imagine we need a data structure like `Matrix` but one that enforces
the fact that the matrix is and remains symmetrical. We could write it
from scratch, but that would involve repeating some code very similar
to what we already wrote.

{{index overriding, prototype}}

JavaScript's prototype system makes it possible to create a _new_
class, much like the old class, but with new definitions for some of
its properties. The prototype for the new class derives from the old
prototype but adds a new definition for, say, the `set` method.

In object-oriented programming terms, this is called
_((inheritance))_. The new class inherits properties and behavior from
the old class.

```{includeCode: "top_lines: 17"}
class SymmetricMatrix extends Matrix {
  constructor(size, element = (x, y) => undefined) {
    super(size, size, (x, y) => {
      if (x < y) return element(y, x);
      else return element(x, y);
    });
  }

  set(x, y, value) {
    super.set(x, y, value);
    if (x != y) {
      super.set(y, x, value);
    }
  }
}

let matrix = new SymmetricMatrix(5, (x, y) => `${x},${y}`);
console.log(matrix.get(2, 3));
// → 3,2
```

The use of the word `extends` indicates that this class shouldn't be
directly based on the default `Object` prototype, but on some other class. This
is called the _((superclass))_. The derived class is the
_((subclass))_.

To initialize a `SymmetricMatrix` instance, the constructor calls its
superclass' constructor through the `super` keyword. This is necessary
because if this new object is to behave (roughly) like a `Matrix`, it
is going to need the instance properties that matrices have. In order
to ensure the matrix is symmetrical, the constructor wraps the
`content` method to swap the coordinates for values below the
diagonal.

The `set` method again uses `super`, but this time not to call the
constructor, but to call a specific method from the superclass' set of
methods. We are redefining `set` but do want to use the original
behavior. Because `this.set` refers to the _new_ `set` method, calling
that wouldn't work. Inside class methods, `super` provides a way to
call methods as they were defined in the superclass.

Inheritance allows us to build slightly different data types from
existing data types with relatively little work. It is a fundamental
part of the object-oriented tradition, alongside encapsulation and
polymorphism. But while the latter two are now generally regarded as
wonderful ideas, inheritance is more controversial.

{{index complexity, reuse, "class hierarchy"}}

Whereas ((encapsulation)) and polymorphism can be used to _separate_
pieces of code from each other, reducing the tangledness of the
overall program, ((inheritance)) fundamentally ties classes together,
creating _more_ tangle. When inheriting from a class, you usually have
to know more about how it works than when simply using it. Inheritance
can be a useful tool, and I use it now and then in my own programs,
but it shouldn't be the first tool you reach for, and you probably
shouldn't actively go looking for opportunities to construct class
hierarchies (family trees of classes).

## The instanceof operator

{{index type, "instanceof operator", constructor, object}}

It is occasionally useful to know whether an object was derived from a
specific class. For this, JavaScript provides a binary operator called
`instanceof`.

```
console.log(
  new SymmetricMatrix(2) instanceof SymmetricMatrix);
// → true
console.log(new SymmetricMatrix(2) instanceof Matrix);
// → true
console.log(new Matrix(2, 2) instanceof SymmetricMatrix);
// → false
console.log([1] instanceof Array);
// → true
```

{{index inheritance}}

The operator will see through inherited types, so a `SymmetricMatrix`
is an instance of `Matrix`. The operator can also be applied to
standard constructors like `Array`. Almost every object is an instance
of `Object`.

## Summary

So objects do more than just hold their own properties. They have
prototypes, which are other objects. They'll act as if they have
properties they don't have as long as their prototype has that
property. Simple objects have `Object.prototype` as their prototype.

Constructors, which are functions whose names usually start with a
capital letter, can be used with the `new` operator to create new
objects. The new object's prototype will be the object found in the
`prototype` property of the constructor. You can make good use of this
by putting the properties that all values of a given type share into
their prototype. There's a `class` notation that provides a clear way
to define a constructor and its prototype.

You can define getters and setters to secretly call methods every time
an object's property is accessed. Static methods are methods stored in
a class' constructor, rather than its prototype.

The `instanceof` operator can, given an object and a constructor, tell
you whether that object is an instance of that constructor.

One useful thing to do with objects is to specify an interface for
them and tell everybody that they are supposed to talk to your object
only through that interface. The rest of the details that make up your
object are now _encapsulated_, hidden behind the interface.

More than one type may implement the same interface. Code written to
use an interface automatically knows how to work with any number of
different objects that provide the interface. This is called
_polymorphism_.

When implementing multiple classes that differ in only some details,
it can be helpful to write the new classes as _subclasses_ of an
existing class, _inheriting_ part of its behavior.

## Exercises

{{id exercise_vector}}

### A vector type

{{index dimensions, "Vec class", coordinates, "vector (exercise)"}}

Write a ((class)) `Vec` that represents a vector in two-dimensional
space. It takes `x` and `y` parameters (numbers), which it should save
to properties of the same name.

{{index addition, subtraction}}

Give the `Vec` prototype two methods, `plus` and `minus`, that take
another vector as a parameter and return a new vector that has the sum
or difference of the two vectors' (`this` and the parameter) _x_ and
_y_ values.

Add a ((getter)) property `length` to the prototype that computes the
length of the vector—that is, the distance of the point (_x_, _y_) from
the origin (0, 0).

{{if interactive

```{test: no}
// Your code here.

console.log(new Vec(1, 2).plus(new Vec(2, 3)));
// → Vec{x: 3, y: 5}
console.log(new Vec(1, 2).minus(new Vec(2, 3)));
// → Vec{x: -1, y: -1}
console.log(new Vec(3, 4).length);
// → 5
```
if}}

{{hint

{{index "vector (exercise)"}}

Look back to the `Rabbit` class example if you're unsure how `class`
declarations look.

{{index Pythagoras, "defineProperty function", "square root", "Math.sqrt function"}}

Adding a getter property to the constructor can be done by putting the
word `get` before the method name. To compute the distance from (0, 0)
to (x, y), you can use the Pythagorean theorem, which says that the
square of the distance we are looking for is equal to the square of
the x-coordinate plus the square of the y-coordinate. Thus, [√(x^2^ +
y^2^)]{if html}[[$\sqrt{x^2 + y^2}$]{latex}]{if tex} is the number you
want, and `Math.sqrt` is the way you compute a square root in
JavaScript.

hint}}

### Groups

{{index "groups (exercise)", "Set class", "Group class", "set (data structure)"}}

{{id groups}}

The standard JavaScript environment provides another data structure
called `Set`. Like an instance of `Map`, a set holds a collection of
values. Unlike `Map`, it does not associate other values with those—it
just tracks which values are part of the set. A value can only be part
of a set once—adding it again doesn't have any effect.

{{index "add method", "delete method", "has method"}}

Write a class called `Group` (since `Set` is already taken). Like
`Set`, it has `add`, `delete`, and `has` methods. Its constructor
creates an empty group, `add` adds a value to the group (but only if
it isn't already a member), `delete` removes its argument from the
group (if it was a member), and `has` returns a Boolean value
indicating whether its argument is a member of the group.

{{index "=== operator", "indexOf method"}}

Use the `===` operator, or something equivalent such as `indexOf`, to
determine whether two values are the same.

{{index "static method"}}

Give the class a static `from` method that takes an iteratable object
as argument and creates a group that contains all the values produced
by iterating over it.

{{if interactive

```{test: no}
class Group {
  // Your code here.
}

let group = Group.from([10, 20]);
console.log(group.has(10));
// → true
console.log(group.has(30));
// → false
group.add(10);
group.delete(10);
console.log(group.has(10));
// → false
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "indexOf method", "includes method"}}

The easiest way to do this is to store an ((array)) of group members
in an instance property. The `includes` or `indexOf` methods can be
used to check whether a given value is in the array.

{{index "push method"}}

Your class' ((constructor)) can set the member collection to an empty
array. When `add` is called, it must check whether the given value is
in the array, and add it, for example with `push`, otherwise.

{{index "filter method"}}

Deleting an element from an array, in `delete`, is less
straightforward, but you can use `filter` to create a new array
without the value. Don't forget to overwrite the property holding the
members with the newly filtered version of the array.

{{index "for/of loop", "iterable interface"}}

The `from` method can use a `for`/`of` loop to get the values out of
the iterable object and call `add` to put them into a newly created
group.

hint}}

### Iterable groups

{{index "groups (exercise)", interface, "iterator interface", "Group class"}}

{{id group_iterator}}

Make the `Group` class from the previous exercise iterable. Refer back
to the section about the iterator interface earlier in the chapter if
you aren't clear on the exact form of the interface anymore.

If you used an array to represent the group's members, don't just
return the iterator created by calling the `Symbol.iterator` method on
the array. That would work, but it defeats the purpose of this exercise.

It is okay if your iterator behaves strangely when the group is
modified during iteration.

{{if interactive

```{test: no}
// Your code here (and the code from the previous exercise)

for (let value of Group.from(["a", "b", "c"])) {
  console.log(value);
}
// → a
// → b
// → c
```

if}}

{{hint

{{index "groups (exercise)", "Group class", "next method"}}

It is probably worthwhile to define a new class `GroupIterator`.
Iterator instances should have a property that tracks the current
position in the group. Every time `next` is called, it checks whether
it is done, and if not, moves past the current value and returns it.

The `Group` class itself gets a method named by `Symbol.iterator`
that, when called, returns a new instance of the iterator class for
that group.

hint}}

### Borrowing a method

Earlier in the chapter I mentioned that an object's `hasOwnProperty`
can be used as a more robust alternative to the `in` operator when you
want to ignore the prototype's properties. But what if your map needs
to include the word `"hasOwnProperty"`? You won't be able to call that
method anymore, because the object's own property hides the method
value.

Can you think of a way to call `hasOwnProperty` on an object that has
its own property by that name?

{{if interactive

```{test: no}
let map = {one: true, two: true, hasOwnProperty: true};

// Fix this call
console.log(map.hasOwnProperty("one"));
// → true
```

if}}

{{hint

Remember that methods that exist on plain objects come from
`Object.prototype`.

And that you can call a function with a specific `this` binding by
using its `call` method.

hint}}
