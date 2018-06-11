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

## Promises

Working with abstract concepts is often easier when those concepts can
be represented by ((value))s. In the case of asynchronous actions you
could, instead of arranging for a function to be called at some point
in the future, return an object that represents this future event.

{{index "Promise class", "asynchronous programming"}}

This is what the standard class `Promise` is for. A _promise_ is an
asynchronous action that may complete at some point and produce a
value. It is able to notify anyone who is interested when its value is
available.

{{index "Promise.resolve function", "resolving (a promise)"}}

The easiest way to create a promise is by calling `Promise.resolve`.
This function ensures that the value you give it is wrapped in a
promise. If it's already a promise, it is simply returned—otherwise,
you get a new promise that immediately finishes with your
value as its result.

```
let fifteen = Promise.resolve(15);
fifteen.then(value => console.log(`Got ${value}`));
// → Got 15
```

{{index "then method"}}

To get the result of a promise, you can use its `then` method. This
registers a ((callback function)) to be called when the promise
resolves and produces a value. You can add multiple callbacks to a
single promise, and they will be called, even if you add them after
the promise has already _resolved_ (finished).

But that's not all the `then` method does. It returns another promise,
which resolves to the value that the handler function returns or, if
that returns a promise, waits for that promise and then resolves to
its result.

It is useful to think of promises as a device to move values into an
asynchronous reality. A normal value is simply there. A promised value
is a value that _might_ already be there or might appear at some point
in the future. Computations defined in terms of promises act on such
wrapped values and are executed asynchronously as the values become
available.

{{index "Promise class"}}

To create a promise, you can use `Promise` as a constructor. It has a
somewhat odd interface—the constructor expects a function as argument,
which it immediately calls, passing it a function that it can use to
resolve the promise. It works this way, instead of for example with a
`resolve` method, so that only the code that created the promise can
resolve it.

{{index "storage function"}}

This is how you'd create a promise-based interface for the
`leerAlmacenamiento` function.

```{includeCode: "top_lines: 5"}
function storage(nest, name) {
  return new Promise(resolve => {
    nest.leerAlmacenamiento(name, result => resolve(result));
  });
}

storage(granRoble, "enemies")
  .then(value => console.log("Got", value));
```

This asynchronous function returns a meaningful value. This is the
main advantage of promises—they simplify the use of asynchronous
functions. Instead of having to pass around callbacks, promise-based
functions look similar to regular ones: they take input as arguments
and return their output. The only difference is that the output may
not be available yet.

## Failure

{{index "exception handling"}}

Regular JavaScript computations can fail by throwing an exception.
Asynchronous computations often need something like that. A network
request may fail, or some code that is as part of the asynchronous
computation may throw an exception.

{{index "callback function", error}}

One of the most pressing problems with the callback style of
asynchronous programming is that it makes it extremely difficult to
make sure failures are properly reported to the callbacks.

A widely used convention is that the first argument to the callback is
used to indicate that the action failed, and the second contains the
value produced by the action when it was successful. Such callback
functions must always check whether they received an exception, and
make sure that any problems they cause, including exceptions thrown by
functions they call, are caught and given to the right function.

{{index "rejecting (a promise)", "resolving (a promise)", "then method"}}

Promises make this easier. They can be either resolved (the action
finished successfully) or rejected (it failed). Resolve handlers (as
registered with `then`) are only called when the action is successful,
and rejections are automatically propagated to the new promise that is
returned by `then`. And when a handler throws an exception, this
automatically causes the promise produced by its `then` call to be
rejected. So if any element in a chain of asynchronous actions fails,
the outcome of the whole chain is marked as rejected, and no regular
handlers are called beyond the point where it failed.

{{index "Promise.reject function", "Promise class"}}

Much like resolving a promise provides a value, rejecting one also
provides one, usually called the _reason_ of the rejection. When an
exception in a handler function causes the rejection, the exception
value is used as the reason. Similarly, when a handler returns a
promise that is rejected, that rejection flows into the next promise.
There's a `Promise.reject` function that creates a new,
immediately rejected promise.

{{index "catch method"}}

To explicitly handle such rejections, promises have a `catch` method
that registers a handler to be called when the promise is rejected,
similar to how `then` handlers handle normal resolution. It's also very
much like `then` in that it returns a new promise, which resolves to
the original promise's value if it resolves normally, and to the
result of the `catch` handler otherwise. If a `catch` handler throws
an error, the new promise is also rejected.

{{index "then method"}}

As a shorthand, `then` also accepts a rejection handler as second
argument, so you can install both types of handlers in a single method
call.

A function passed to the `Promise` constructor receives a second
argument, alongside the resolve function, which it can use to reject
the new promise.

The chains of promise values created by calls to `then` and `catch`
can be seen as a pipeline through which asynchronous values or
failures move. Since such chains are created by registering handlers,
each link has a success handler or a rejection handler (or both)
associated with it. Handlers that don't match the type of outcome
(success or failure) are ignored. But those that do match are called,
and their outcome determines what kind of value comes next—success
when it returns a non-promise value, rejection when it throws an
exception, and the outcome of a promise when it returns one of those.

{{index "uncaught exception", "exception handling"}}

Much like an uncaught exception is handled by the environment,
JavaScript environments can detect when a promise rejection isn't
handled, and will report this as an error.

## Networks are hard

{{index network}}

Occasionally, there isn't enough light for the ((crow))s' mirror
systems to transmit a signal, or something is blocking the path of the
signal. It is possible for a signal to be sent, but never received.

{{index "send method", error, timeout}}

As it is, that will just cause the callback given to `send` to never
be called, which will probably cause the program to stop without even
noticing there is a problem. It would be nice if, after a given period
of not getting a response, a request would _time out_ and report
failure.

Often, transmission failures are random accidents, like a car's
headlight interfering with the light signals, and simply retrying the
request may cause it to succeed. So while we're at it, let's make our
request function automatically retry the sending of the request a few
times before it gives up.

{{index "Promise class", "callback function", interface}}

And, since we've established that promises are a good thing, we'll
also make our request function return a promise. In terms of what they
can express, callbacks and promises are equivalent. Callback-based
functions can be wrapped to expose a promise-based interface, and
vice versa.

Even when a ((request)) and its ((response)) are successfully
delivered, the response may indicate failure—for example, if the
request tries to use a request type that hasn't been defined or the
handler throws an error. To support this, `send` and
`definirTipoSolicitud` follow the convention mentioned before, where the
first argument passed to callbacks is the failure reason, if any, and
the second is the actual result.

These can be translated to promise resolution and rejection by our
wrapper.

{{index "Timeout class", "request function", retry}}

```{includeCode: true}
class Timeout extends Error {}

function request(nest, target, type, contenido) {
  return new Promise((resolve, reject) => {
    let done = false;
    function attempt(n) {
      nest.send(target, type, content, (failed, value) => {
        done = true;
        if (failed) reject(failed);
        else resolve(value);
      });
      setTimeout(() => {
        if (done) return;
        else if (n < 3) attempt(n + 1);
        else reject(new Timeout("Timed out"));
      }, 250);
    }
    attempt(1);
  });
}
```

{{index "Promise class", "resolving (a promise)", "rejecting (a promise)"}}

Because promises can only be resolved (or rejected) once, this will
work. The first time `resolve` or `reject` is called determines the
outcome of the promise, and any further calls, such as the timeout
arriving after the request finishes, or a request coming back after
another request finished, are ignored.

{{index recursion}}

To build an asynchronous ((loop)), for the retries, we need to use a
recursive function—a regular loop doesn't allow us to stop and wait
for an asynchronous action. The `attempt` function makes a single
attempt to send a request. It also sets a timeout that, if no response
has come back after 250 milliseconds, either starts the next attempt
or, if this was the fourth attempt, rejects the promise with an
instance of `Timeout` as the reason.

{{index idempotence}}

Retrying every quarter second and giving up when no response has come
in after a second is definitely somewhat arbitrary. It is even
possible, if the request did come through but the handler is just
taking a bit longer, for requests to be delivered multiple times.
We'll write our handlers with that problem in mind—duplicate messages
should be harmless.

In general, we will not be building a world-class, robust network
today. But that's okay—crows don't have very high expectations yet
when it comes to computing.

{{index "definirTipoSolicitud function", "requestType function"}}

To isolate ourselves from callbacks altogether, we'll go ahead and
also define a wrapper for `definirTipoSolicitud` that allows the handler
function to return a promise or plain value, and wires that up to the
callback for us.

```{includeCode: true}
function requestType(name, handler) {
  definirTipoSolicitud(name, (nest, content, fuente,
                           callback) => {
    try {
      Promise.resolve(handler(nest, content, source))
        .then(response => callback(null, response),
              failure => callback(failure));
    } catch (exception) {
      callback(exception);
    }
  });
}
```

{{index "Promise.resolve function"}}

`Promise.resolve` is used to convert the value returned by `handler`
to a promise if it isn't already.

{{index "try keyword", "callback function"}}

Note that the call to `handler` had to be wrapped in a `try` block, to
make sure any exception it raises directly is given to the callback.
This nicely illustrates the difficulty of properly handling errors
with raw callbacks—it is very easy to forget to properly route
exceptions like that, and if you don't do it, failures won't get
reported to the right callback. Promises make this mostly automatic,
and thus less error-prone.

## Collections of promises

{{index "neighbors property", "ping request"}}

Each nest computer keeps an array of other nests within transmission
distance in its `neighbors` property. To check which of those are
currently reachable, you could write a function that tries to send a
`"ping"` request (a request that simply asks for a response) to each
of them, and see which ones come back.

{{index "Promise.all function"}}

When working with collections of promises running at the same time,
the `Promise.all` function can be useful. It returns a promise that
waits for all of the promises in the array to resolve, and then
resolves to an array of the values that these promises produced (in
the same order as the original array). If any promise is rejected, the
result of `Promise.all` is itself rejected.

```{includeCode: true}
requestType("ping", () => "pong");

function availableNeighbors(nest) {
  let requests = nest.neighbors.map(neighbor => {
    return request(nest, neighbor, "ping")
      .then(() => true, () => false);
  });
  return Promise.all(requests).then(result => {
    return nest.neighbors.filter((_, i) => result[i]);
  });
}
```

{{index "then method"}}

When a neighbor isn't available, we don't want the entire combined
promise to fail, since then we still wouldn't know anything. So the
function that is mapped over the set of neighbors to turn them into
request promises attaches handlers that make successful requests
produce `true` and rejected ones produce `false`.

{{index "filter method", "map method", "some method"}}

In the handler for the combined promise, `filter` is used to remove
those elements from the `neighbors` array whose corresponding value is
false. This makes use of the fact that `filter` passes the array index
of the current element as a second argument to its filtering function
(`map`, `some`, and similar higher-order array methods do the same).

## Network flooding

The fact that nests can only talk to their neighbors greatly inhibits
the usefulness of this network.

For broadcasting information to the whole network, one solution is to
set up a type of request that is automatically forwarded to neighbors.
These neighbors then in turn forward it to their neighbors, until the
whole network has received the message.

{{index "sendGossip function"}}

```{includeCode: true}
import {everywhere} from "./crow-tech";

everywhere(nest => {
  nest.state.gossip = [];
});

function sendGossip(nest, message, exceptFor = null) {
  nest.state.gossip.push(message);
  for (let neighbor of nest.neighbors) {
    if (neighbor == exceptFor) continue;
    request(nest, neighbor, "gossip", message);
  }
}

requestType("gossip", (nest, message, source) => {
  if (nest.state.gossip.includes(message)) return;
  console.log(`${nest.name} received gossip '${
               message}' from ${source}`);
  sendGossip(nest, message, source);
});
```

{{index "everywhere function", "gossip property"}}

To avoid sending the same message around the network forever, each
nest keeps an array of gossip strings that it has already seen. To
define this array, we use the `everywhere` function—which runs code on
every nest—to add a property to the nest's `state` object, which is
where we'll keep nest-local ((state)).

When a nest receives a duplicate gossip message, which is very likely
to happen with everybody blindly resending these, it ignores it. But
when it receives a new message, it excitedly tells all its neighbors
except for the one who sent it the message.

This will cause a new piece of gossip to spread through the network
like an ink stain in water. Even when some connections aren't
currently working, if there is an alternative route to a given nest,
the gossip will reach it through there.

{{index "flooding"}}

This style of network communication is called _flooding_—it floods the
network with a piece of information until all nodes have it.

{{if interactive

We can call `sendGossip` to see a message flow through the village.

```
sendGossip(granRoble, "Kids with airgun in the park");
```

if}}

## Message routing

{{index efficiency}}

If a given node wants to talk to a single other node, flooding is not
a very efficient approach. Especially when the network is big, that
would lead to a lot of useless data transfers.

{{index "routing"}}

An alternative approach is to set up a way for messages to hop from
node to node, until they reach their destination. The difficulty with
that is that it requires knowledge about the layout of the network. To
send a request in the direction of a faraway nest, it is necessary to
know which neighboring nest gets it closer to its destination. Sending
it in the wrong direction will not do much good.

Since each nest only knows about its direct neighbors, it doesn't have
the information it needs to compute a route. We must somehow spread
the information about these connections to all nests. Preferably in a
way that allows it to change over time, when nests are abandoned or
new nests are built.

{{index flooding}}

We can use flooding again, but instead of checking whether a given
message has already been received, we now check whether the new set of
neighbors for a given nest matches the current set we have for it.

{{index "broadcastConnections function", "connections binding"}}

```{includeCode: true}
requestType("connections", (nest, {name, neighbors},
                            source) => {
  let connections = nest.state.connections;
  if (JSON.stringify(connections.get(name)) ==
      JSON.stringify(neighbors)) return;
  connections.set(name, neighbors);
  broadcastConnections(nest, name, source);
});

function broadcastConnections(nest, name, exceptFor = null) {
  for (let neighbor of nest.neighbors) {
    if (neighbor == exceptFor) continue;
    request(nest, neighbor, "connections", {
      name,
      neighbors: nest.state.connections.get(name)
    });
  }
}

everywhere(nest => {
  nest.state.connections = new Map;
  nest.state.connections.set(nest.name, nest.neighbors);
  broadcastConnections(nest, nest.name);
});
```

{{index JSON, "== operator"}}

The comparison uses `JSON.stringify` because `==`, on objects or
arrays, will only return true when the two are the exact same value,
which is not what we need here. Comparing the JSON strings is a crude
but effective way to compare their content.

The nodes immediately start broadcasting their connections, which
should, unless some nests are completely unreachable, quickly give
every nest a map of the current network ((graph)).

{{index pathfinding}}

A thing you can do with graphs is find routes in them, as we saw in
[Chapter ?](robot). If we have a route towards a message's
destination, we know which direction to send it in.

{{index "findRoute function"}}

This `findRoute` function, which greatly resembles the `findRoute`
from [Chapter ?](robot#findRoute), searches for a way to reach a given
node in the network. But instead of returning the whole route, it just
returns the next step. That next nest will itself, using its current
information about the network, decide where _it_ sends the message.

```{includeCode: true}
function findRoute(from, to, connections) {
  let work = [{at: from, via: null}];
  for (let i = 0; i < work.length; i++) {
    let {at, via} = work[i];
    for (let next of connections.get(at) || []) {
      if (next == to) return via;
      if (!work.some(w => w.at == next)) {
        work.push({at: next, via: via || next});
      }
    }
  }
  return null;
}
```

Now we can build a function that can send long-distance messages. If
the message is addressed to a direct neighbor, it is delivered as
usual. If not, it is packaged in an object and sent to a neighbor that
is closer to the target, using the `"route"` request type, which will
cause that neighbor to repeat the same behavior.

{{index "routeRequest function"}}

```{includeCode: true}
function routeRequest(nest, target, type, content) {
  if (nest.neighbors.includes(target)) {
    return request(nest, target, type, content);
  } else {
    let via = findRoute(nest.name, target,
                        nest.state.connections);
    if (!via) throw new Error(`No route to ${target}`);
    return request(nest, via, "route",
                   {target, type, content});
  }
}

requestType("route", (nest, {target, type, content}) => {
  return routeRequest(nest, target, type, content);
});
```

{{if interactive

We can now send a message to the nest in the church tower, which is
four network hops removed.

```
routeRequest(granRoble, "Church Tower", "note",
             "Incoming jackdaws!");
```

if}}

{{index "[network, stack]"}}

We've constructed several ((layer))s of functionality on top of a
primitive communication system in order to make it convenient to use.
This is a nice (though simplified) model of how real computer networks
work.

{{index error}}

A distinguishing property of computer networks is that they aren't
reliable—abstractions built on top of them can help, but you can't
abstract away network failure. So network programming is typically
very much about anticipating and dealing with failures.

## Async functions

To store important information, ((crow))s are known to duplicate it
across nests. That way, when a hawk destroys a nest, the information
isn't lost.

To retrieve a given piece of information that it doesn't have in its
own storage bulb, a nest computer might consult random other nests in
the network until it finds one that has it.

{{index "findInStorage function", "network function"}}

```{includeCode: true}
requestType("storage", (nest, name) => storage(nest, name));

function findInStorage(nest, name) {
  return storage(nest, name).then(found => {
    if (found != null) return found;
    else return findInRemoteStorage(nest, name);
  });
}

function network(nest) {
  return Array.from(nest.state.connections.keys());
}

function findInRemoteStorage(nest, name) {
  let sources = network(nest).filter(n => n != nest.name);
  function next() {
    if (sources.length == 0) {
      return Promise.reject(new Error("Not found"));
    } else {
      let source = sources[Math.floor(Math.random() *
                                      sources.length)];
      sources = sources.filter(n => n != source);
      return routeRequest(nest, source, "storage", name)
        .then(value => value != null ? value : next(),
              next);
    }
  }
  return next();
}
```

{{index "Map class", "Object.keys function", "Array.from function"}}

Because `connections` is a `Map`, `Object.keys` doesn't work on it. It
has a `keys` _method_, but that returns an iterator rather than an
array. An iterator (or iterable value) can be converted to an array
with the `Array.from` function.

{{index "Promise class", recursion}}

Even with promises this is some rather awkward code. Multiple
asynchronous actions are chained together in non-obvious ways. We
again need a recursive function (`next`) to model looping through the
nests.

{{index "synchronous programming", "asynchronous programming"}}

And the thing the code actually does is completely linear—it always
waits for the previous action to complete before starting the next
one. In a synchronous programming model, it'd be simpler to express.

{{index "async function", "await keyword"}}

The good news is that JavaScript allows you write pseudo-synchronous
code. An `async` function is a function that implicitly returns a
promise and that can, in its body, `await` other promises in a way
that _looks_ synchronous.

{{index "findInStorage function"}}

We can rewrite `findInStorage` like this:

```
async function findInStorage(nest, name) {
  let local = await storage(nest, name);
  if (local != null) return local;

  let sources = network(nest).filter(n => n != nest.name);
  while (sources.length > 0) {
    let source = sources[Math.floor(Math.random() *
                                    sources.length)];
    sources = sources.filter(n => n != source);
    try {
      let found = await routeRequest(nest, source, "storage",
                                     name);
      if (found != null) return found;
    } catch (_) {}
  }
  throw new Error("Not found");
}
```

{{index "async function", "return keyword", "exception handling"}}

An `async` function is marked by the word `async` before the
`function` keyword. Methods can also be made `async` by writing
`async` before their name. When such a function or method is called,
it returns a promise. As soon as the body returns something, that
promise is resolved. If it throws an exception, the promise is
rejected.

{{if interactive

```{startCode: true}
findInStorage(granRoble, "events on 2017-12-21")
  .then(console.log);
```

if}}

{{index "await keyword", "control flow"}}

Inside an `async` function, the word `await` can be put in front of an
expression to wait for a promise to resolve, and only then continue
the execution of the function.

Such a function no longer, like a regular JavaScript function, runs
from start to completion in one go. Instead, it can be _frozen_ at any
point that has an `await`, and resumed at a later time.

For non-trivial asynchronous code, this notation is usually more
convenient than directly using promises. Even if you need to do
something that doesn't fit the synchronous model, such as perform
multiple actions at the same time, it is easy to combine `await` with
direct use of promises.

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
