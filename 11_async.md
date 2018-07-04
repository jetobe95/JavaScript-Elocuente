{{meta {load_files: ["code/crow-tech.js", "code/chapter/11_async.js"]}}}

# Programación Asincrónica

{{quote {author: "Laozi", title: "Tao Te Ching", chapter: true}

Quién puede esperar tranquilamente mientras el barro se asienta?\
Quién puede permanecer en calma hasta el momento de actuar?

quote}}

{{index "Laozi"}}

{{figure {url: "img/chapter_picture_11.jpg", alt: "Picture of two crows on a branch", chapter: framed}}}

La parte central de una computadora, la parte que lleva a cabo los
pasos individuales que componen nuestros programas, es llamada
_((procesador))_. Los programas que hemos visto hasta ahora son cosas que
mantienen al procesador ocupado hasta que hayan terminado su trabajo. La
velocidad a la que algo como un ciclo que manipule números pueda ser
ejecutado, depende casi completamente de la velocidad del procesador.

Pero muchos programas interactúan con cosas fuera del procesador. por
ejemplo, podrian comunicarse a través de una ((red)) de computadoras o
solicitar datos del ((disco duro))—lo que es mucho más lento que
obtenerlos desde la ((memoria)).

Cuando una cosa como tal este sucediendo, sería una pena dejar que
el procesador se mantenga inactivo—podría haber algún otro trabajo que
este pueda hacer en el mientras tanto. En parte, esto es manejado por tu
sistema operativo, que cambiará el procesador entre múltiples programas en
ejecución. Pero eso no ayuda cuando queremos que un _unico_ programa pueda
hacer progreso mientras este espera una solicitud de red.

## Asincronicidad

{{index "synchronous programming"}}

En un modelo de programación _sincrónico_, las cosas suceden una a la vez.
Cuando llamas a una función que realiza una acción de larga duración, solo
retorna cuando la acción ha terminado y puede retornar el resultado.
Esto detiene tu programa durante el tiempo que tome la acción.

{{index "asynchronous programming"}}

Un modelo _asincrónico_ permite que ocurran varias cosas al mismo tiempo.
Cuando comienzas una acción, tu programa continúa ejecutándose. Cuando
la acción termina, el programa es informado y tiene acceso al
resultado (por ejemplo, los datos leídos del disco).

Podemos comparar a la programación síncrona y asincrónica usando un pequeño
ejemplo: un programa que obtiene dos recursos de la ((red)) y
luego combina resultados.

{{index "synchronous programming"}}

En un entorno síncrono, donde la función de solicitud solo retorna
una vez que ha hecho su trabajo, la forma más fácil de realizar esta tarea es
realizar las solicitudes una después de la otra. Esto tiene el inconveniente de
que la segunda solicitud se iniciará solo cuando la primera haya finalizado.
El tiempo total de ejecución será como minimo la suma de los dos tiempos de
respuesta.

{{index parallelism}}

La solución a este problema, en un sistema síncrono, es comenzar
((hilo))s adicionales de control. Un _hilo_ es otro programa activo
cuya ejecución puede ser intercalada con otros programas por el
sistema operativo—ya que la mayoría de las computadoras modernas contienen
múltiples procesadores, múltiples hilos pueden incluso ejecutarse al mismo
tiempo, en diferentes procesadores. Un segundo hilo podría iniciar la segunda
solicitud, y luego ambos subprocesos esperan a que los resultados vuelvan,
después de lo cual se vuelven a resincronizar para combinar sus resultados.

{{index CPU, blocking, "asynchronous programming", timeline, "callback function"}}

En el siguiente diagrama, las líneas gruesas representan el tiempo que el
programa pasa corriendo normalmente, y las líneas finas representan el tiempo
pasado esperando la red. En el modelo síncrono, el tiempo empleado por
la red es _parte_ de la línea de tiempo para un hilo de control dado.
En el modelo asincrónico, comenzar una acción de red conceptualmente
causa una _división_ en la línea del tiempo. El programa que inició
la acción continúa ejecutándose, y la acción ocurre junto a el,
notificando al programa cuando está termina.

{{figure {url: "img/control-io.svg", alt: "Control flow for synchronous and asynchronous programming",width: "8cm"}}}

{{index "control flow", "asynchronous programming", verbosity}}

Otra forma de describir la diferencia es que esperar que las acciones
terminen es _implicito_ en el modelo síncrono, mientras que es _explicito_,
bajo nuestro control, en el asincrónico.

La asincronicidad corta en ambos sentidos. Hace que expresar programas que
hagan algo no se ajuste al modelo de control lineal más fácil, pero también
puede hacer que expresar programas que siguen una línea recta sea más
incómodo. Veremos algunas formas de abordar esta incomodidad más adelante en
el capítulo.

Ambas de las plataformas de programación JavaScript importantes—((navegadore))s
y ((Node.js))—realizan operaciones que pueden tomar un tiempo asincrónicamente,
en lugar de confiar en ((hilo))s. Dado que la programación con hilos es
notoriamente difícil (entender lo que hace un programa es mucho más
difícil cuando está haciendo varias cosas a la vez), esto es generalmente
considerado una buena cosa.

## Tecnología cuervo

La mayoría de las personas son conscientes del hecho de que los ((cuervo))s
son pájaros muy inteligentes. Pueden usar herramientas, planear con anticipación,
recordar cosas e incluso comunicarse estas cosas entre ellos.

Lo que la mayoría de la gente no sabe, es que son capaces de hacer muchas cosas
que mantienen bien escondidas de nosotros. Personas de buena
reputación (un tanto excéntricas) expertas en ((córvidos)), me han dicho
que la tecnología cuervo no esta muy por detrás de la tecnología humana,
y que nos estan alcanzando.

Por ejemplo, muchas culturas cuervo tienen la capacidad de construir
dispositivos informáticos. Estos no son electrónicos, como lo son
los dispositivos informáticos humanos, pero operan a través de las acciones de
pequeños insectos, una especie estrechamente relacionada con las ((termitas)),
que ha desarrollado una ((relación simbiótica)) con los cuervos. Los pájaros
les proporcionan comida, y a cambio los insectos construyen y operan sus
complejas colonias que, con la ayuda de las criaturas vivientes dentro de ellos,
realizan computaciones.

Tales colonias generalmente se encuentran en nidos grandes de larga vida.
Las aves e insectos trabajan juntos para construir una red de estructuras
bulbosas hechas de arcilla, escondidas entre las ramitas del nido,
en el que los insectos viven y trabajan.

Para comunicarse con otros dispositivos, estas máquinas usan señales de luz.
Los cuervos incrustan piezas de material reflectante en tallos de
comunicación especial, y los insectos apuntan estos para reflejar la luz hacia
otro nido, codificando los datos como una secuencia de flashes rápidos.
Esto significa que solo los nidos que tienen una conexión visual ininterrumpida
pueden comunicarse entre ellos.

Nuestro amigo, el experto en córvidos, ha mapeado la red de nidos de cuervo en
el pueblo de ((Hières-sur-Amby)), a orillas del río Ródano.
Este mapa muestra los nidos y sus conexiones.

{{figure {url: "img/Hieres-sur-Amby.png", alt: "A network of crow nests in a small village"}}}

En un ejemplo asombroso de ((evolución convergente)), las computadoras cuervo
ejecutan JavaScript. En este capítulo vamos a escribir algunas funciones de
redes básicas para ellos.

## Devolución de llamadas

{{indexsee [function, callback], "callback function"}}

Un enfoque para la ((programación asincrónica)) es hacer que las funciones que
realizan una acción lenta, tomen un argumento adicional, una _((función de
devolución de llamada))_. La acción se inicia y, cuando esta finaliza,
la función de devolución es llamada con el resultado.

{{index "setTimeout function", waiting}}

Como ejemplo, la función `setTimeout`, disponible tanto en Node.js
como en navegadores, espera una cantidad determinada de milisegundos
(un segundo son mil milisegundos) y luego llama una función.

```{test: no}
setTimeout(() => console.log("Tick"), 500);
```

Esperar no es generalmente un tipo de trabajo muy importante, pero puede ser
útil cuando se hace algo como actualizar una animación o verificar si
algo está tardando más que una cantidad dada de ((tiempo)).

La realización de múltiples acciones asíncronas en una fila utilizando
devoluciones de llamada significa que debes seguir pasando nuevas funciones para
manejar la ((continuación)) de la computación después de las acciones.

{{index "hard disk"}}

La mayoría de las computadoras en los nidos de los cuervos tienen un
bulbo de almacenamiento de datos a largo plazo, donde las piezas de información
se graban en ramitas para que estas puedan ser recuperadas más tarde.
Grabar o encontrar un fragmento de información requiere un momento, por lo que
la interfaz para el almacenamiento a largo plazo es asíncrona y utiliza
funciones de devolución de llamada.

Los bulbos de almacenamiento almacenan piezas de ((JSON))-datos codificables
bajo nombres. Un ((cuervo)) podría almacenar información sobre los lugares
donde hay comida escondida bajo el nombre `"caches de alimentos"`, que podría
contener un array de nombres que apuntan a otros datos, que describen el caché
real. Para buscar un ((caché)) de alimento en los bulbos de almacenamiento del
nido _Gran Roble_, un cuervo podría ejecutar código como este:

{{index "leerAlmacenamiento function"}}

```{includeCode: "top_lines: 1"}
import {granRoble} from "./tecnologia-cuervo";

granRoble.leerAlmacenamiento("caches de alimentos", caches => {
  let primerCache = caches[0];
  granRoble.leerAlmacenamiento(primerCache, informacion => {
    console.log(informacion);
  });
});
```

(Todos los nombres de las vinculaciones y los strings se han traducido del
lenguaje cuervo a Español.)

Este estilo de programación es viable, pero el nivel de indentación
aumenta con cada acción asincrónica, ya que terminas en otra
función. Hacer cosas más complicadas, como ejecutar múltiples acciones
al mismo tiempo, puede ser un poco incómodo.

Las computadoras cuervo están construidas para comunicarse usando pares de
((solicitud))-((respuesta)). Eso significa que un nido envía un mensaje a
otro nido, el cual inmediatamente envía un mensaje de vuelta, confirmando el
recibo y, posiblemente, incluyendo una respuesta a una pregunta formulada
en el mensaje.

Cada mensaje está etiquetado con un _tipo_, que determina cómo este es
manejado. Nuestro código puede definir manejadores para tipos de solicitud
específicos, y cuando se recibe una solicitud de este tipo, se llama al
controlador para que este produzca una respuesta.

{{index "crow-tech module", "send method"}}

La interfaz exportada por el módulo `"./tecnologia-cuervo"` proporciona
funciones de devolución de llamada para la comunicación. Los nidos tienen un
método `enviar` que envía una solicitud. Este espera el nombre del nido
objetivo, el tipo de solicitud y el contenido de la solicitud como sus
primeros tres argumentos, y una función a llamar cuando llega una respuesta
como su cuarto y último argumento.

```
granRoble.send("Pastura de Vacas", "nota", "Vamos a graznar fuerte a las 7PM",
            () => console.log("Nota entregada."));
```

Pero para hacer nidos capaces de recibir esa solicitud, primero tenemos que
definir un ((tipo de solicitud)) llamado `"nota"`. El código que maneja
las solicitudes debe ejecutarse no solo en este nido-computadora, sino en
todos los nidos que puedan recibir mensajes de este tipo. Asumiremos que un
cuervo sobrevuela e instala nuestro código controlador en todos los nidos.

{{index "definirTipoSolicitud function"}}

```{includeCode: true}
import {definirTipoSolicitud} from "./tecnologia-cuervo";

definirTipoSolicitud("nota", (nido, contenido, fuente, listo) => {
  console.log(`${nido.nombre} recibio nota: ${contenido}`);
  listo();
});
```

La función `definirTipoSolicitud` define un nuevo tipo de solicitud. El
ejemplo agrega soporte para solicitudes de tipo `"nota"`, que simplemente envían
una nota a un nido dado. Nuestra implementación llama a `console.log` para que
podamos verificar que la solicitud llegó. Los nidos tienen una propiedad
`nombre` que contiene su nombre.

{{index "asynchronous programming"}}

El cuarto argumento dado al controlador, `listo`, es una función de
devolución de llamada que debe ser llamada cuando se finaliza con la solicitud.
Si hubiesemos utilizado el ((valor de retorno)) del controlador como el valor
de respuesta, eso significaria que un controlador de solicitud no puede realizar
acciones  asincrónicas por sí mismo. Una función que realiza trabajos asíncronos
normalmente retorna antes de que el trabajo este hecho, habiendo arreglado que se
llame una devolución de llamada cuando este completada. Entonces, necesitamos algún
mecanismo asíncrono, en este caso, otra ((función de devolución de
llamada))—para indicar cuándo hay una respuesta disponible.

En cierto modo, la asincronía es _contagiosa_. Cualquier función que llame a una
función que funcione asincrónicamente debe ser asíncrona en si misma, utilizando
una devolución de llamada o algun mecanismo similar para entregar su resultado.
Llamar devoluciones de llamada es algo más involucrado y propenso a errores que
simplemente retornar un valor, por lo que necesitar estructurar grandes
partes de tu programa de esa manera no es algo muy bueno.

## Promesas

Trabajar con conceptos abstractos es a menudo más fácil cuando esos conceptos
pueden ser representados por ((valores)). En el caso de acciones asíncronas,
podrías, en lugar de organizar a una función para que esta sea llamada
en algún momento en el futuro, retornar un objeto que represente este
evento en el futuro.

{{index "Promise class", "asynchronous programming"}}

Esto es para lo que es la clase estándar `Promise` ("Promesa").
Una _promesa_ es una acción asíncrona que puede completarse en algún punto
y producir un valor. Esta puede notificar a cualquier persona que esté
interesada cuando su valor este disponible.

{{index "Promise.resolve function", "resolving (a promise)"}}

La forma más fácil de crear una promesa es llamando a `Promise.resolve` ("Promesa.resolver").
Esta función se asegura de que el valor que le des, sea envuelto en una
promesa. Si ya es una promesa, simplemente es retornada—de lo contrario,
obtienes una nueva promesa que termina de inmediato con tu
valor como su resultado.

```
let quince = Promise.resolve(15);
quince.then(valor => console.log(`Obtuve ${valor}`));
// → Obtuve 15
```

{{index "then method"}}

Para obtener el resultado de una promesa, puede usar su método `then` ("entonces").
Este registra una (función de devolución de llamada) para que sea llamada cuando la
promesa resuelva y produzca un valor. Puedes agregar múltiples devoluciones de
llamada a una única promesa, y serán llamadas, incluso si las agregas después
de que la promesa ya haya sido _resuelta_ (terminada).

Pero eso no es todo lo que hace el método `then`. Este retorna otra promesa,
que resuelve al valor que retorna la función del controlador o, si
esa retorna una promesa, espera por esa promesa y luego resuelve
su resultado.

Es útil pensar acerca de las promesas como dispositivos para mover valores a una
realidad asincrónica. Un valor normal simplemente esta allí. Un valor prometido
es un valor que _podría_ ya estar allí o podría aparecer en algún momento
en el futuro. Las computaciones definidas en términos de promesas actúan en
tales valores envueltos y se ejecutan de forma asíncrona a medida los
valores se vuelven disponibles.

{{index "Promise class"}}

Para crear una promesa, puedes usar `Promise` como un constructor. Tiene una
interfaz algo extraña—el constructor espera una función como argumento, a la
cual llama inmediatamente, pasando una función que puede usar para
resolver la promesa. Funciona de esta manera, en lugar de, por ejemplo, con un
método `resolve`, de modo que solo el código que creó la promesa pueda
resolverla.

{{index "storage function"}}

Así es como crearía una interfaz basada en promesas para la función
`leerAlmacenamiento`.

```{includeCode: "top_lines: 5"}
function almacenamiento(nido, nombre) {
  return new Promise(resolve => {
    nido.leerAlmacenamiento(nombre, resultado => resolve(resultado));
  });
}

almacenamiento(granRoble, "enemigos")
  .then(valor => console.log("Obtuve", valor));
```

Esta función asíncrona retorna un valor significativo. Esta es la
principal ventaja de las promesas—simplifican el uso de funciones asincrónicas.
En lugar de tener que pasar devoluciones de llamadas, las funciones basadas en
promesas son similares a las normales: toman entradas como
argumentos y retornan su resultado. La única diferencia es que la salida
puede que no este disponible inmediatamente.

## Fracaso

{{index "exception handling"}}

Las computaciones regulares en JavaScript pueden fallar lanzando una excepción.
Las computaciones asincrónicas a menudo necesitan algo así. Una solicitud de
red puede fallar, o algún código que sea parte de la computación asincrónica
puede arrojar una excepción.

{{index "callback function", error}}

Uno de los problemas más urgentes con el estilo de devolución de llamadas
en la programación asíncrona es que hace que sea extremadamente difícil
asegurarte de que las fallas sean reportadas correctamente a las devoluciones de
llamada.

Una convención ampliamente utilizada es que el primer argumento para la
devolución de llamada es usado para indicar que la acción falló, y el segundo
contiene el valor producido por la acción cuando tuvo éxito. Tales funciones
de devolución de llamadas siempre deben verificar si recibieron una excepción, y
asegurarse de que cualquier problema que causen, incluidas las excepciones
lanzadas por las funciones que estas llaman, sean atrapadas y entregadas a la
función correcta.

{{index "rejecting (a promise)", "resolving (a promise)", "then method"}}

Las promesas hacen esto más fácil. Estas pueden ser resueltas (la acción
termino con éxito) o rechazadas (esta falló). Los controladores de resolución
(registrados con `then`) solo se llaman cuando la acción es exitosa,
y los rechazos se propagan automáticamente a la nueva promesa que es
retornada por `then`. Y cuando un controlador arroje una excepción, esto
automáticamente hace que la promesa producida por su llamada `then` sea
rechazada. Entonces, si cualquier elemento en una cadena de acciones
asíncronas falla, el resultado de toda la cadena se marca como rechazado, y no
se llaman más manejadores despues del punto en donde falló.

{{index "Promise.reject function", "Promise class"}}

Al igual que resolver una promesa proporciona un valor, rechazar una también
proporciona uno, generalmente llamado la _razón_ el rechazo. Cuando una
excepción en una función de controlador provoca el rechazo, el valor de
la excepción se usa como la razón. Del mismo modo, cuando un controlador retorna
una promesa que es rechazada, ese rechazo fluye hacia la próxima promesa.
Hay una función `Promise.reject` que crea una nueva promesa
inmediatamente rechazada.

{{index "catch method"}}

Para manejar explícitamente tales rechazos, las promesas tienen un método
`catch` ("atraoar") que registra un controlador para que sea llamado cuando se rechaze la
promesa, similar a cómo los manejadores `then` manejan la resolución normal.
También es muy parecido a `then` en que retorna una nueva promesa, que se
resuelve en el valor de la promesa original si esta se resuelve normalmente, y al
resultado del controlador `catch` de lo contrario. Si un controlador `catch`
lanza un error, la nueva promesa también es rechazada.

{{index "then method"}}

Como una abreviatura, `then` también acepta un manejador de rechazo como
segundo argumento, por lo que puedes instalar ambos tipos de controladores en
un solo método de llamada.

Una función que se pasa al constructor `Promise` recibe un segundo
argumento, junto con la función de resolución, que puede usar para rechazar
la nueva promesa.

Las cadenas de promesas creadas por llamadas a `then` y `catch`
puede verse como una tubería a través de la cual los valores asíncronicos o
las fallas se mueven. Dado que tales cadenas se crean mediante el registro de
controladores, cada enlace tiene un controlador de éxito o un controlador de
rechazo (o ambos) asociados a ello. Controladores que no coinciden con ese tipo
de resultados (éxito o fracaso) son ignorados. Pero los que sí coinciden son
llamados, y su resultado determina qué tipo de valor viene después—éxito
cuando retorna un valor que no es una promesa, rechazo cuando arroja una
excepción, y el resultado de una promesa cuando retorna una de esas.

{{index "uncaught exception", "exception handling"}}

Al igual que una excepción no detectada es manejada por el entorno,
Los entornos de JavaScript pueden detectar cuándo una promesa rechazada no es
manejada, y reportará esto como un error.

## Las redes son difíciles

{{index network}}

Ocasionalmente, no hay suficiente luz para los sistemas de espejos de los
cuervos para transmitir una señal, o algo bloquea el camino de la
señal. Es posible que se envíe una señal, pero que nunca se reciba.

{{index "send method", error, timeout}}

Tal y como es, eso solo causará que la devolución de llamada dada a `send` nunca
sea llamada, lo que probablemente hará que el programa se detenga sin siquiera
notar que hay un problema. Sería bueno si, después de un determinado período
de no obtener una respuesta, una solicitud _expirará_ e informara de un
fracaso.

A menudo, las fallas de transmisión son accidentes aleatorios, como la
luz del faro de un auto interfieriendo con las señales de luz, y simplemente
volver a intentar la solicitud puede hacer que esta tenga éxito. Entonces,
mientras estamos en eso, hagamos que nuestra función de solicitud
automáticamente reintente el envío de la solicitud momentos antes de que se de
por vencida.

{{index "Promise class", "callback function", interface}}

Y, como hemos establecido que las promesas son algo bueno, tambien haremos
que nuestra función de solicitud retorne una promesa. En
términos de lo que pueden expresar, las devoluciones de llamada y las promesas
son equivalentes. Las funciones basadas en devoluciones de llamadas se pueden
envolver para exponer una interfaz basada en promesas, y viceversa.

Incluso cuando una ((solicitud)) y su ((respuesta)) sean entregadas
exitosamente, la respuesta puede indicar un error—por ejemplo, si la
solicitud intenta utilizar un tipo de solicitud que no haya sido definida
o si el controlador genera un error. Para soportar esto, `send` y
`definirTipoSolicitud` siguen la convención mencionada anteriormente, donde
el primer argumento pasado a las devoluciones de llamada es el motivo del
fallo, si lo hay, y el segundo es el resultado real.

Estos pueden ser traducidos para prometer resolución y rechazo por parte de
nuestra envoltura.

{{index "Timeout class", "request function", retry}}

```{includeCode: true}
class TiempoDeEspera extends Error {}

function request(nido, objetivo, tipo, contenido) {
  return new Promise((resolve, reject) => {
    let listo = false;
    function intentar(n) {
      nido.send(objetivo, tipo, contenido, (fallo, value) => {
        listo = true;
        if (fallo) reject(fallo);
        else resolve(value);
      });
      setTimeout(() => {
        if (listo) return;
        else if (n < 3) intentar(n + 1);
        else reject(new TiempoDeEspera("Tiempo de espera agotado"));
      }, 250);
    }
    intentar(1);
  });
}
```

{{index "Promise class", "resolving (a promise)", "rejecting (a promise)"}}

Debido a que las promesas solo se pueden resolver (o rechazar) una vez, esto
funcionara. La primera vez que se llame a `resolve` o `reject` se determinara el
resultado de la promesa y cualquier llamada subsecuente, como el tiempo de espera
que llega después de que finaliza la solicitud, o una solicitud que regresa
después de que otra solicitud es finalizada, es ignorada.

{{index recursion}}

Para construir un ((ciclo)) asincrónico, para los reintentos, necesitamos usar
un función recursiva—un ciclo regular no nos permite detenernos y esperar
por una acción asincrónica. La función `intentar` hace un solo
intento de enviar una solicitud. También establece un tiempo de espera que, si
no ha regresado una respuesta después de 250 milisegundos, comienza el
próximo intento o, si este es el cuarto intento, rechaza la promesa con una
instancia de `TiempoDeEspera` como la razón.

{{index idempotence}}

Volver a intentar cada cuarto de segundo y rendirse cuando no ha
llegado ninguna respuesta después de un segundo es algo definitivamente
arbitrario. Es incluso posible, si la solicitud llegó pero el controlador se
esta tardando un poco más, que las solicitudes se entreguen varias veces.
Escribiremos nuestros manejadores con ese problema en mente—los mensajes
duplicados deberían de ser inofensivos.

En general, no construiremos una red robusta de clase mundial
hoy. Pero eso esta bien—los cuervos no tienen expectativas muy altas todavía
cuando se trata de la computación.

{{index "definirTipoSolicitud function", "requestType function"}}

Para aislarnos por completo de las devoluciones de llamadas, seguiremos
adelante y también definiremos un contenedor para `definirTipoSolicitud` que
permite que la función controlador pueda retornar una promesa o valor normal,
y envia eso hasta la devolución de llamada para nosotros.

```{includeCode: true}
function tipoSolicitud(nombre, manejador) {
  definirTipoSolicitud(nombre, (nido, contenido, fuente,
                           devolucionDeLlamada) => {
    try {
      Promise.resolve(manejador(nido, contenido, fuente))
        .then(response => devolucionDeLlamada(null, response),
              failure => devolucionDeLlamada(failure));
    } catch (exception) {
      devolucionDeLlamada(exception);
    }
  });
}
```

{{index "Promise.resolve function"}}

`Promise.resolve` se usa para convertir el valor retornado por `manejador`
a una promesa si no es una ya.

{{index "try keyword", "callback function"}}

Ten en cuenta que la llamada a `manejador` tenía que estar envuelta en un
bloque `try`, para asegurarse de que cualquier excepción que aparezca  
directamente se le dé a la devolución de llamada. Esto ilustra muy bien la
dificultad de manejar adecuadamente los errores con devoluciones de llamada
crudas—es muy fácil olvidarse de encaminar correctamente excepciones como esa,
y si no lo haces, las fallas no se seran informadas a la devolución de
llamada correcta. Las promesas hacen esto casi automático, y por lo
tanto, son menos propensas a errores.

## Colecciones de promesas

{{index "neighbors property", "ping request"}}

Cada computadora nido mantiene un array de otros nidos dentro de la distancia
de transmisión en su propiedad `vecinos`. Para verificar cuáles de
esos son actualmente accesibles, puede escribir una función que intente enviar
un solicitud `"ping"` (una solicitud que simplemente pregunta por una respuesta)
para cada de ellos, y ver cuáles regresan.

{{index "Promise.all function"}}

Al trabajar con colecciones de promesas que se ejecutan al mismo tiempo,
la función `Promise.all` puede ser útil. Esta retorna una promesa que
espera a que se resuelvan todas las promesas del array, y luego
resuelve un array de los valores que estas promesas produjeron (en
el mismo orden que en el array original). Si alguna promesa es rechazada, el
el resultado de `Promise.all` es en sí mismo rechazado.

```{includeCode: true}
tipoSolicitud("ping", () => "pong");

function vecinosDisponibles(nido) {
  let solicitudes = nido.vecinos.map(vecino => {
    return request(nido, vecino, "ping")
      .then(() => true, () => false);
  });
  return Promise.all(solicitudes).then(resultado => {
    return nido.vecinos.filter((_, i) => resultado[i]);
  });
}
```

{{index "then method"}}

Cuando un vecino no este disponible, no queremos que todo la promesa combinada
falle, dado que entonces no sabríamos nada. Entonces la función
que es mappeada en el conjunto de vecinos para convertirlos en
promesas de solicitud vincula a los controladores que hacen las
solicitudes exitosas produzcan `true` y las rechazadas produzcan `false`.

{{index "filter method", "map method", "some method"}}

En el controlador de la promesa combinada, `filter` se usa para eliminar
esos elementos de la matriz `vecinos` cuyo valor correspondiente es
falso. Esto hace uso del hecho de que `filter` pasa el índice de matriz
del elemento actual como segundo argumento para su función de filtrado
(`map`,` some`, y métodos similares de orden superior de arrays hacen lo
mismo).

## Inundación de red

El hecho de que los nidos solo pueden hablar con sus vecinos inhibe en gran
cantidad la utilidad de esta red.

Para transmitir información a toda la red, una solución es configurar un tipo
de solicitud que sea reenviada automáticamente a los vecinos. Estos vecinos
luego la envían a sus vecinos, hasta que toda la red ha recibido el mensaje.

{{index "sendGossip function"}}

```{includeCode: true}
import {todosLados} from "./tecnologia-cuervo";

todosLados(nido => {
  nido.estado.chismorreo = [];
});

function enviarChismorreo(nido, mensaje, exceptoPor = null) {
  nido.estado.chismorreo.push(mensaje);
  for (let vecino of nido.vecinos) {
    if (vecino == exceptoPor) continue;
    request(nido, vecino, "chismorreo", mensaje);
  }
}

requestType("chismorreo", (nido, mensaje, fuente) => {
  if (nido.estado.chismorreo.includes(mensaje)) return;
  console.log(`${nido.nombre} recibio chismorreo '${
               mensaje}' de ${fuente}`);
  enviarChismorreo(nido, mensaje, fuente);
});
```

{{index "everywhere function", "gossip property"}}

Para evitar enviar el mismo mensaje a traves de la red por siempre, cada
nido mantiene un array de strings de chismorreos que ya ha visto. Para
definir este array, usaremos la función `todosLados`—que ejecuta
código en todos los nidos—para añadir una propiedad al objeto `estado` del nido,
que es donde mantendremos ((estado)) local del nido.

Cuando un nido recibe un mensaje de chisme duplicado, lo cual es muy probable
que suceda con todo el mundo reenviando estos a ciegas, lo ignora. Pero
cuando recibe un mensaje nuevo, emocionadamente le dice a todos sus vecinos
a excepción de quien le envió el mensaje.

Esto provocará que una nueva pieza de chismes se propague a través de la red
como una mancha de tinta en agua. Incluso cuando algunas conexiones no estan
trabajando actualmente, si hay una ruta alternativa a un nido dado,
el chisme llegará hasta allí.

{{index "flooding"}}

Este estilo de comunicación de red se llama _inundamiento_-inunda la
red con una pieza de información hasta que todos los nodos la tengan.

{{if interactive

Podemos llamar a `enviarChismorreo` para ver un mensaje fluir a través del
pueblo.

```
enviarChismorreo(granRoble, "Niños con una pistola de aire en el parque");
```

if}}

## Enrutamiento de mensajes

{{index efficiency}}

Si un nodo determinado quiere hablar unicamente con otro nodo, la inundación no
es un enfoque muy eficiente. Especialmente cuando la red es grande,
daría lugar a una gran cantidad de transferencias de datos inútiles.

{{index "routing"}}

Un enfoque alternativo es configurar una manera en que los mensajes salten de
nodo a nodo, hasta que lleguen a su destino. La dificultad con eso
es que requiere de conocimiento sobre el diseño de la red. Para
enviar una solicitud hacia la dirección de un nido lejano, es necesario
saber qué nido vecino lo acerca más a su destino. Enviar la solicitud
en la dirección equivocada no servirá de mucho.

Dado que cada nido solo conoce a sus vecinos directos, no tiene
la información que necesita para calcular una ruta. De alguna manera debemos
extender la información acerca de estas conexiones a todos los nidos.
Preferiblemente en una manera que permita ser cambiada con el tiempo, cuando
los nidos son abandonados o nuevos nidos son construidos.

{{index flooding}}

Podemos usar la inundación de nuevo, pero en lugar de verificar si un
determinado mensaje ya ha sido recibido, ahora verificamos si el nuevo conjunto
de vecinos de un nido determinado coinciden con el conjunto actual que
tenemos para él.

{{index "broadcastConnections function", "connections binding"}}

```{includeCode: true}
tipoSolicitud("conexiones", (nido, {nombre, vecinos},
                            fuente) => {
  let conexiones = nido.estado.conexiones;
  if (JSON.stringify(conexiones.get(nombre)) ==
      JSON.stringify(vecinos)) return;
  conexiones.set(nombre, vecinos);
  difundirConexiones(nido, nombre, fuente);
});

function difundirConexiones(nido, nombre, exceptoPor = null) {
  for (let vecino of nido.vecinos) {
    if (vecino == exceptoPor) continue;
    solicitud(nido, vecino, "conexiones", {
      nombre,
      vecinos: nido.estado.conexiones.get(nombre)
    });
  }
}

todosLados(nido => {
  nido.estado.conexiones = new Map;
  nido.estado.conexiones.set(nido.nombre, nido.vecinos);
  difundirConexiones(nido, nido.nombre);
});
```

{{index JSON, "== operator"}}

La comparación usa `JSON.stringify` porque `==`, en objetos o
arrays, solo retornara true cuando los dos tengan exactamente el mismo valor,
lo cual no es lo que necesitamos aquí. Comparar los strings JSON es una
cruda pero efectiva manera de comparar su contenido.

Los nodos comienzan inmediatamente a transmitir sus conexiones, lo que
debería, a menos que algunos nidos sean completamente inalcanzables, dar
rápidamente cada nido un mapa del ((grafo)) de la red actual.

{{index pathfinding}}

Una cosa que puedes hacer con grafos es encontrar rutas en ellos, como vimos
en el [Capítulo 7](Robot). Si tenemos una ruta hacia el destino de un mensaje,
sabemos en qué dirección enviarlo.

{{index "findRoute function"}}

Esta función `encontrarRuta`, que se parece mucho a `encontrarRuta`
del [Capítulo 7](robot#findRoute), busca por una forma de llegar a un determinado
nodo en la red. Pero en lugar de devolver toda la ruta, simplemente
retorna el siguiente paso. Ese próximo nido en si mismo, usando su información
actual sobre la red, decididira _hacia_ dónde enviar el mensaje.

```{includeCode: true}
function encontrarRuta(desde, hasta, conexiones) {
  let trabajo = [{donde: desde, via: null}];
  for (let i = 0; i < trabajo.length; i++) {
    let {donde, via} = trabajo[i];
    for (let siguiente of conexiones.get(donde) || []) {
      if (siguiente == hasta) return via;
      if (!trabajo.some(w => w.donde == siguiente)) {
        trabajo.push({donde: siguiente, via: via || siguiente});
      }
    }
  }
  return null;
}
```

Ahora podemos construir una función que pueda enviar mensajes de larga distancia.
Si el mensaje está dirigido a un vecino directo, se entrega normalmente.
Si no, se empaqueta en un objeto y se envía a un vecino que
este más cerca del objetivo, usando el tipo de solicitud `"ruta"`, que
hace que ese vecino repita el mismo comportamiento.

{{index "routeRequest function"}}

```{includeCode: true}
function solicitudRuta(nido, objetivo, tipo, contenido) {
  if (nido.vecinos.includes(objetivo)) {
    return solicitud(nido, objetivo, tipo, contenido);
  } else {
    let via = encontrarRuta(nido.nombre, objetivo,
                        nido.estado.conexiones);
    if (!via) throw new Error(`No hay rutas disponibles hacia ${objetivo}`);
    return solicitud(nido, via, "ruta",
                   {objetivo, tipo, contenido});
  }
}

tipoSolicitud("ruta", (nido, {objetivo, tipo, contenido}) => {
  return solicitudRuta(nido, objetivo, tipo, contenido);
});
```

{{if interactive

Ahora podemos enviar un mensaje al nido en la torre de la iglesia, que esta
a cuatro saltos de red de distancia.

```
solicitudRuta(granRoble, "Torre de la Iglesia", "nota",
             "Cuidado con las Palomas!");
```

if}}

{{index "[network, stack]"}}

Hemos construido varias ((capas)) de funcionalidad sobre un
sistema de comunicación primitivo para que sea conveniente de usarlo.
Este es un buen (aunque simplificado) modelo de cómo las redes de computadoras
reales trabajan.

{{index error}}

Una propiedad distintiva de las redes de computadoras es que no son confiables—las
abstracciones construidas encima de ellas pueden ayudar, pero no se puede
abstraer la falla de una falla de red. Entonces la programación de redes es
típicamente mucho acerca de anticipar y lidiar con fallas.

## Funciones asíncronas

Para almacenar información importante, se sabe que los ((cuervo))s la duplican
a través de los nidos. De esta forma, cuando un halcón destruye un nido, la
información no se pierde.

Para obtener una pieza de información dada que no este en su propia bulbo de
almacenamiento, una computadora nido puede consultar otros nidos al azar en
la red hasta que encuentre uno que la tenga.

{{index "findInStorage function", "network function"}}

```{includeCode: true}
tipoSolicitud("almacenamiento", (nido, nombre) => almacenamiento(nido, nombre));

function encontrarEnAlmacenamiento(nido, nombre) {
  return almacenamiento(nido, nombre).then(encontrado => {
    if (encontrado != null) return encontrado;
    else return encontrarEnAlmacenamientoRemoto(nido, nombre);
  });
}

function red(nido) {
  return Array.from(nido.estado.conexiones.keys());
}

function encontrarEnAlmacenamientoRemoto(nido, nombre) {
  let fuentes = red(nido).filter(n => n != nido.nombre);
  function siguiente() {
    if (fuentes.length == 0) {
      return Promise.reject(new Error("No encontrado"));
    } else {
      let fuente = fuentes[Math.floor(Math.random() *
                                      fuentes.length)];
      fuentes = fuentes.filter(n => n != fuente);
      return solicitudRuta(nido, fuente, "almacenamiento", nombre)
        .then(valor => valor != null ? valor : siguiente(),
              siguiente);
    }
  }
  return siguiente();
}
```

{{index "Map class", "Object.keys function", "Array.from function"}}

Como `conexiones` es un `Map`, `Object.keys` no funciona en él. Este
tiene un _metódo_ `keys`, pero que retorna un iterador en lugar de un
array. Un iterador (o valor iterable) se puede convertir a un array
con la función `Array.from`.

{{index "Promise class", recursion}}

Incluso con promesas, este es un código bastante incómodo. Múltiples
acciones asincrónicas están encadenadas juntas de maneras no-obvias. Nosotros
de nuevo necesitamos una función recursiva (`siguiente`) para modelar ciclos
a través de nidos.

{{index "synchronous programming", "asynchronous programming"}}

Y lo que el código realmente hace es completamente lineal—siempre
espera a que se complete la acción anterior antes de comenzar la siguiente.
En un modelo de programación sincrónica, sería más simple de expresar.

{{index "async function", "await keyword"}}

La buena noticia es que JavaScript te permite escribir código pseudo-sincrónico.
Una función `async` es una función que retorna implícitamente una
promesa y que puede, en su cuerpo, `await` ("esperar") otras promesas de una
manera que _se ve_ sincrónica.

{{index "findInStorage function"}}

Podemos reescribir `encontrarEnAlmacenamiento` de esta manera:

```
async function encontrarEnAlmacenamiento(nido, nombre) {
  let local = await almacenamiento(nido, nombre);
  if (local != null) return local;

  let fuentes = red(nido).filter(n => n != nido.nombre);
  while (fuentes.length > 0) {
    let fuente = fuentes[Math.floor(Math.random() *
                                    fuentes.length)];
    fuentes = fuentes.filter(n => n != fuente);
    try {
      let encontrado = await solicitudRuta(nido, fuente, "almacenamiento",
                                     nombre);
      if (encontrado != null) return encontrado;
    } catch (_) {}
  }
  throw new Error("No encontrado");
}
```

{{index "async function", "return keyword", "exception handling"}}

Una función `async` está marcada por la palabra `async` antes de la
palabra clave `function`. Los métodos también pueden hacerse `async`
al escribir `async` antes de su nombre. Cuando se llame a dicha función o método,
este retorna una promesa. Tan pronto como el cuerpo retorne algo, esa
promesa es resuelta Si arroja una excepción, la promesa es rechazada.

{{if interactive

```{startCode: true}
encontrarEnAlmacenamiento(granRoble, "eventos del 2017-12-21")
  .then(console.log);
```

if}}

{{index "await keyword", "control flow"}}

Dentro de una función `async`, la palabra `await` se puede poner delante de una
expresión para esperar a que se resuelva una promesa, y solo entonces continua
la ejecución de la función.

Tal función ya no se ejecuta, como una función regular de JavaScript
de principio a fin de una sola vez. En su lugar, puede ser _congelada_ en
cualquier punto que tenga un `await`, y se reanuda en un momento posterior.

Para código asincrónico no-trivial, esta notación suele ser más
conveniente que usar promesas directamente. Incluso si necesitas hacer
algo que no se ajuste al modelo síncrono, como realizar
múltiples acciones al mismo tiempo, es fácil combinar `await` con el
uso directo de promesas.

## Generators

{{index "async function"}}

This ability of functions to be paused and then resumed again is not
exclusive to `async` functions. JavaScript also has a feature called
_((generator))_ functions. These are similar, but without the
promises.

When you define a function with `function*` (placing as asterisk after
the word `function`), it becomes a generator. When you call a
generator, it returns an ((iterator)), which we already saw in
[Chapter ?](object).

```
function* powers(n) {
  for (let current = n;; current *= n) {
    yield current;
  }
}

for (let power of powers(3)) {
  if (power > 50) break;
  console.log(power);
}
// → 3
// → 9
// → 27
```

{{index "next method", "yield keyword"}}

Initially, when you call `powers`, the function is frozen at its
start. Every time you call `next` on the iterator, the function runs
until it hits a `yield` expression, which pauses it and causes the
yielded value to become the next value produced by the iterator. When
the function returns (the one in the example never does), the iterator
is done.

Writing iterators is often much easier when you use generator
functions. The iterator for the group class (from the exercise in
[Chapter ?](object#group_iterator)) can be written with this
generator:

{{index "Group class"}}

```
Group.prototype[Symbol.iterator] = function*() {
  for (let i = 0; i < this.members.length; i++) {
    yield this.members[i];
  }
};
```

```{hidden: true, includeCode: true}
class Group {
  constructor() { this.members = []; }
  add(m) { this.members.add(m); }
}
```

There's no longer a need to create an object to hold the iteration
((state))—generators automatically save their local state every time
they yield.

Such `yield` expressions may only occur directly in the generator
function itself and not in an inner function you define inside of it.
The state a generator saves, when yielding, is only its _local_
environment and the position where it yielded.

{{index "await keyword"}}

An `async` function is a special type of generator. It produces a
promise when called, which is resolved when it returns (finishes) and
rejected when it throws an exception. Whenever it yields (awaits) a
promise, the result of that promise (value or thrown exception) is the
result of the `await` expression.

## The event loop

{{index "asynchronous programming", scheduling, "event loop", timeline}}

Asynchronous programs are executed piece by piece. Each piece may
start some actions and schedule code to be executed when the action
finishes or fails. In between these pieces, the program sits idle,
waiting for the next action.

{{index "setTimeout function"}}

So callbacks are not directly called by the code that scheduled them.
If I call `setTimeout` from within a function, that function will have
returned by the time the callback function is called. And when the
callback returns, control does not go back to the function that
scheduled it.

{{index "Promise class", "catch keyword", "exception handling"}}

Asynchronous behavior happens on its own empty function ((call
stack)). This is one of the reasons that, without promises, managing
exceptions across asynchronous code is hard. Since each callback
starts with a mostly empty stack, your `catch` handlers won't be on
the stack when they throw an exception.

```
try {
  setTimeout(() => {
    throw new Error("Woosh");
  }, 20);
} catch (_) {
  // This will not run
  console.log("Caught!");
}
```

{{index thread, queue}}

No matter how closely together events—such as timeouts or incoming
requests—happen, a JavaScript environment will only run one program at
a time. You can think of this as it running a big loop _around_ your
program, called the _event loop_. When there's nothing to be done,
that loop is stopped. But as events come in, they are added to a queue,
and their code is executed one after the other. Because no two things
run at the same time, slow-running code might delay the handling of
other events.

This example sets a timeout, but then dallies until after the
timeout's intended point of time, causing the timeout to be late.

```
let start = Date.now();
setTimeout(() => {
  console.log("Timeout ran at", Date.now() - start);
}, 20);
while (Date.now() < start + 50) {}
console.log("Wasted time until", Date.now() - start);
// → Wasted time until 50
// → Timeout ran at 55
```

{{index "resolving (a promise)", "rejecting (a promise)", "Promise class"}}

Promises always resolve or reject as a new event. Even if a promise is
already resolved, waiting for it will cause your callback to run after
the current script finishes, rather than right away.

```
Promise.resolve("Done").then(console.log);
console.log("Me first!");
// → Me first!
// → Done
```

In later chapters we'll see various other types of events that run on
the event loop.

## Asynchronous bugs

{{index "asynchronous programming"}}

When your program runs synchronously, in a single go, there are no
((state)) changes happening except those that the program itself
makes. For asynchronous programs this is different—they may have
_gaps_ in their execution during which other code can run.

Let's look at an example. One of the hobbies of our crows is to count
the number of chicks that hatch throughout the village every year.
Nests store this count in their storage bulbs. The following code tries to
enumerate the counts from all the nests for a given year.

{{index "anyStorage function", "chicks function"}}

```{includeCode: true}
function anyStorage(nest, source, name) {
  if (source == nest.name) return storage(nest, name);
  else return routeRequest(nest, source, "storage", name);
}

async function chicks(nest, year) {
  let list = "";
  await Promise.all(network(nest).map(async name => {
    list += `${name}: ${
      await anyStorage(nest, name, `chicks in ${year}`)
    }\n`;
  }));
  return list;
}
```

{{index "async function"}}

The `async name =>` part shows that ((arrow function))s can also be
made `async` by putting the word `async` in front of them.

{{index "Promise.all function"}}

The code doesn't immediately look suspicious... it maps the `async`
arrow function over the set of nests, creating an array of promises,
and then uses `Promise.all` to wait for all of these before returning
the list they build up.

But it is seriously broken. It'll always return only a single line of
output, listing the nest that was slowest to respond.

{{if interactive

```
chicks(granRoble, 2017).then(console.log);
```

if}}

Can you work out why?

{{index "+= operator"}}

The problem lies in the `+=` operator, which takes the _current_ value
of `list` at the time where the statement starts executing, and then,
when the `await` finishes, sets the `list` binding to be that value
plus the added string.

{{index "await keyword"}}

But between the time where the statement starts executing and the time
where it finishes there's an asynchronous gap. The `map` expression
runs before anything has been added to the list, so each of the `+=`
operators starts from an empty string and ends up, when its storage
retrieval finishes, setting `list` to a single-line list—the result of
adding its line to the empty string.

{{index "side effect"}}

This could have easily been avoided by returning the lines from the
mapped promises and calling `join` on the result of `Promise.all`,
instead of building up the list by changing a binding. As usual,
computing new values is less error-prone than changing existing
values.

{{index "chicks function"}}

```
async function chicks(nest, year) {
  let lines = network(nest).map(async name => {
    return name + ": " +
      await anyStorage(nest, name, `chicks in ${year}`);
  });
  return (await Promise.all(lines)).join("\n");
}
```

Mistakes like this are easy to make, especially when using `await`,
and you should be aware of where the gaps in your code occur. An
advantage of JavaScript's _explicit_ asynchronicity (whether through
callbacks, promises, or `await`) is that spotting these gaps is
relatively easy.

## Summary

Asynchronous programming makes it possible to express waiting for
long-running actions without freezing the program during these
actions. JavaScript environments typically implement this style of
programming using callbacks, functions that are called when the
actions complete. An event loop schedules such callbacks to be called
when appropriate, one after the other, so that their execution does
not overlap.

Programming asynchronously is made easier by promises, objects that
represent actions that might complete in the future, and `async`
functions, which allow you to write an asynchronous program as if it
is synchronous.

## Exercises

### Tracking the scalpel

{{index "scalpel (exercise)"}}

The village crows own an old scalpel that they occasionally use on
special missions—say, to cut through screen doors or packaging. To be
able to quickly track it down, every time the scalpel is moved to
another nest, an entry is added to the storage of both the nest that
had it and the nest that took it, under the name `"scalpel"`, with its
new location as value.

This means that finding the scalpel is a matter of following the
breadcrumb trail of storage entries, until you find a nest where that
points at the nest itself.

{{index "anyStorage function", "async function"}}

Write an `async` function `locateScalpel` that does this, starting at
the nest on which it runs. You can use the `anyStorage` function
defined earlier to access storage in arbitrary nests. The scalpel has
been going around long enough that you may assume that every nest has
a `"scalpel"` entry in its data storage.

Next, write the same function again without using `async` and `await`.

{{index "exception handling"}}

Do request failures properly show up as rejections of the returned
promise in both versions? How?

{{if interactive

```{test: no}
async function locateScalpel(nest) {
  // Your code here.
}

function locateScalpel2(nest) {
  // Your code here.
}

locateScalpel(granRoble).then(console.log);
// → Butcher Shop
```

if}}

{{hint

{{index "scalpel (exercise)"}}

This can be done with a single loop that searches through the nests,
moving forward to the next when it finds a value that doesn't match
the current nest's name, and returning the name when it finds a
matching value. In the `async` function, a regular `for` or `while`
loop can be used.

{{index recursion}}

To do the same in a plain function, you will have to build your loop
using a recursive function. The easiest way to do this is to have that
function return a promise by calling `then` on the promise that
retrieves the storage value. Depending on whether that value matches
the name of the current nest, the handler returns that value or a
further promise created by calling the loop function again.

Don't forget to start the loop by calling the recursive function once
from the main function.

{{index "exception handling"}}

In the `async` function, rejected promises are converted to exceptions
by `await`. When an `async` function throws an exception, its promise
is rejected. So that works.

If you implemented the non-`async` function as outlined above, the way
`then` works also automatically causes a failure to end up in the
returned promise. If a request fails, the handler passed to `then`
isn't called, and the promise it returns is rejected with the same
reason.

hint}}

### Building Promise.all

{{index "Promise class", "Promise.all function", "building Promise.all (exercise)"}}

Given an array of ((promise))s, `Promise.all` returns a promise that
waits for all of the promises in the array to finish. It then
succeeds, yielding an array of result values. If a promise
in the array fails, the promise returned by `all` fails too, with the
failure reason from the failing promise.

Implement something like this yourself as a regular function
called `Promise_all`.

Remember that after a promise has succeeded or failed, it can't
succeed or fail again, and further calls to the functions that resolve
it are ignored. This can simplify the way you handle failure of your
promise.

{{if interactive

```{test: no}
function Promise_all(promises) {
  return new Promise((resolve, reject) => {
    // Your code here.
  });
}

// Test code.
Promise_all([]).then(array => {
  console.log("This should be []:", array);
});
function soon(val) {
  return new Promise(resolve => {
    setTimeout(() => resolve(val), Math.random() * 500);
  });
}
Promise_all([soon(1), soon(2), soon(3)]).then(array => {
  console.log("This should be [1, 2, 3]:", array);
});
Promise_all([soon(1), Promise.reject("X"), soon(3)])
  .then(array => {
    console.log("We should not get here");
  })
  .catch(error => {
    if (error != "X") {
      console.log("Unexpected failure:", error);
    }
  });
```

if}}

{{hint

{{index "Promise.all function", "Promise class", "then method", "building Promise.all (exercise)"}}

The function passed to the `Promise` constructor will have to call
`then` on each of the promises in the given array. When one of them
succeeds, two things need to happen. The resulting value needs to be
stored in the correct position of a result array, and we must check
whether this was the last pending ((promise)) and finish our own
promise if it was.

{{index "counter variable"}}

The latter can be done with a counter that is initialized to the
length of the input array and from which we subtract 1 every time a
promise succeeds. When it reaches 0, we are done. Make sure you take
into account the situation where the input array is empty (and thus no
promise will ever resolve).

Handling failure requires some thought but turns out to be extremely
simple. Just pass the `reject` function of the wrapping promise to
each of the promises in the array as a `catch` handler or as second
argument to `then` so that a failure in one of them triggers the
rejection of the whole wrapper promise.

hint}}
