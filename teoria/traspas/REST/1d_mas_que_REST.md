<!-- .slide: class="titulo" -->

# Tema 1
## Introducción a los servicios web REST
# Parte 4
## Más allá de REST

![](img_1d/everywhere.jpg)

---

## Contenidos

1. RPC
2. GraphQL
2. APIs en tiempo real

---

<!-- .slide: class="titulo" -->

# 1. RPC 

---

## Recordar: apps web "clásicas"

- Las URL representan **operaciones** 
- Los datos de entrada se envían como **parámetros HTTP**
- Las peticiones solo pueden ser **GET** o **POST**

```http
//obviamos la autenticación para simplificar
http://uaclouddementira.ua.es/obtenerAlumno?dni=11222333F
```

Esta URL devolvería una **página HTML** con los datos del alumno 

---

Las apps webs clásicas no son **APIs**, ya que devuelven *páginas completas HTML* (==interfaz), no *datos*... pero podemos "APIficarlo" **devolviendo JSON/XML/otros**. Opcionalmente podemos enviar JSON/XML/etc en vez de usar parámetros HTTP 

Petición

```http
http://uaclouddementira.ua.es/obtenerAlumno?dni=11222333F
```

Respuesta

```javascript
{
   "dni":"11222333F"
   "nombre": "Ernesto"
   "apellidos": "Rito Bravo"
   ...  
}
```

---

## APIs RPC

Los APIs de este estilo se denominan **RPC** (Remote Procedure Call) ya que la petición HTTP es muy similar conceptualmente a la llamada a un método o función convencional

```java
Alumno obtenerAlumno(String dni) {
    ...
}

obtenerAlumno("11222333F")
```

RPC sobre HTTP

```http
http://uaclouddementira.ua.es/api/obtenerAlumno?dni=11222333F
```

---

## Recordad el API de Flickr

No es que sea un mal API, simplemente no es REST ¡¡en realidad es **RPC**!! ya que la interfaz es una lista de **operaciones** y no de **recursos**.   

![](img_1d/ops_flickr.png)

*(¿pero por qué se empeñan en llamarlo REST?)*

---

## Recordad el API de Flickr

La única diferencia de sintaxis es que la operación en lugar de estar en la URL es un parámetro HTTP (**`method`**)

```http
https://api.flickr.com/services/rest/?method=flickr.test.echo&name=value
```


---

RPC no es implícitamente inferior (ni superior) a REST

- RPC puede resultar más intuitivo cuando un API se exprese mejor como un conjunto de operaciones/procesos más que de recursos
- REST es el más popular (actualmente) en APIs públicos

---

## Elecciones de diseño en un API RPC

- ¿Qué **protocolo** de transporte se usa?: HTTP, protocolos propios
- ¿Cómo se especifica la **operación**?: URL, parámetro HTTP, dato (JSON/XML/...) en el cuerpo de la petición
- ¿Cómo se pasan los **datos de entrada**?: parámetros HTTP, datos (JSON/XML,...) en el cuerpo de la petición

---

Algunos *frameworks* para desarrollar APIs RPC van un paso más allá y **hacen transparente la llamada remota**. En el código no hacemos peticiones HTTP sino en apariencia solo llamadas a **métodos/funciones**. Por ejemplo, [Apache Thrift](http://thrift.apache.org)

```javascript
var transport = new Thrift.TXHRTransport("http://localhost:8585/hello");
var protocol  = new Thrift.TJSONProtocol(transport);
var client = new HelloSvcClient(protocol);
var msg = client.hello_func();
```

<span class="caption">[HMTML completo (ejemplo de cliente Thrift desde el navegador)](https://github.com/apache/thrift/blob/master/lib/nodejs/examples/hello.html)</span>

---

## Estándares y protocolos abiertos en APIs RPC

- Estándares
    * [**JSON-RPC**](http://www.jsonrpc.org): JSON sobre HTTP
    * [**SOAP**](https://www.w3.org/TR/soap/)(*): XML sobre (generalmente) HTTP
- Abiertos
    * [**gRPC**](https://grpc.io)(*) (Google): datos binarios sobre HTTP/2
    * [**Apache Thrift**](https://thrift.apache.org)(*): JSON,XML,texto,binario sobre HTTP

(*) Hace transparentes las llamadas remotas

---

## JSON-RPC

Campos JSON estándar: `jsonrpc` (versión JSONRPC), `method` (operación), `id` (id del cliente), `params` (objeto con parámetros)

- Ejemplo: el *media center* [Kodi](https://kodi.tv) lo usa para permitir el control remoto

```bash
# URL
http://<your-ip>:<your-port>/jsonrpc
```

```javascript
// Cuerpo de la petición
// Pausar (o play si está pausado) el reproductor número 0
{
 "jsonrpc": "2.0",
 "method": "Player.PlayPause", 
 "params": { "playerid": 0 }, 
 "id": 1
}
```

<span class="caption">[http://kodi.wiki/view/JSON-RPC_API/Examples](http://kodi.wiki/view/JSON-RPC_API/Examples)</span>

---

## Otro ejemplo: el API Web de Slack

No sigue ningún estándar RPC, como [se dice en la documentación](https://api.slack.com/web#basics) simplemente es ***"una colección de métodos HTTP al estilo RPC"***

- La URL siempre es de la forma `https://slack.com/api/METHOD_FAMILY.method`. Por ejemplo
  + `https://slack.com/api/channels.create`
  + `https://slack.com/api/conversations.invite`
  + `https://slack.com/api/conversations.archive`
- Usa únicamente GET y POST (por ejemplo borrar un mensaje se hace con POST)
- Según la operación se admiten los parámetros en formato `application/x-www-form-urlencoded` (o sea `nombre1=valor1&nombre2=valor2...`) o bien en JSON


Ejemplo: [documentación de cómo crear un canal](https://api.slack.com/methods/channels.create)




---

<!-- .slide: class="titulo" -->

# 2. Introducción básica a GraphQL

---

## Un problema con los APIs REST

La **granularidad** de los recursos es **fija**. En la petición no podemos indicar que queremos solo **parte del recurso** o que queremos también **recursos relacionados**


```http
http://miapirest.com/blogs/1/posts/1
```
<!-- .element class="caption" -->Queremos ver el post 1 del blog 1

El diseñador del API puede haber decidido que un post ya incluye los comentarios, o bien que no, pero es una *decisión fija*. Si a veces los necesitamos y otras no, tendremos un problema de eficiencia.

---

Ya vimos que ciertos APIs REST **extienden la sintaxis** para obtener solo algunos campos o para obtener recursos relacionados

```http
https://graph.facebook.com/JustinBieber?fields=id,name,picture
https://graph.facebook.com/me?fields=photos.limit(5),posts.limit(5)
```

Podríamos ver GraphQL como esta idea mejorada y ampliada

---

## ¿Qué es GraphQL?

Es un lenguaje para hacer consultas flexibles a **APIs orientados a recursos** en los que estos están relacionados entre sí formando un **grafo**

![](https://scontent.falc1-1.fna.fbcdn.net/v/t39.2365-6/11891339_452528061601395_1389717909_n.jpg?oh=c78fee1a5404ad86d33a19de9141bde8&oe=5A4B063C)

---

## Esquema GraphQL

Además del lenguaje de consulta hay una sintaxis para definir el **esquema** de los recursos (**estructura** del grafo + **consultas** posibles)

![](img_1d/schema.png)

<!-- .element class="caption" -->Esta sintaxis es "abstracta". La sintaxis real dependerá del lenguaje que estemos usando para implementar el servidor GraphQL


---

## Evolución de GraphQL

- Desarrollado en Facebook y usado internamente desde 2012. [Dado a conocer](https://www.youtube.com/watch?v=9sc8Pyc51uU) en 2015
- La especificación es *open source*, aunque controlada por FB: [https://github.com/facebook/graphql](https://github.com/facebook/graphql)
- Hay [multitud de implementaciones](http://graphql.org/code/) de cliente y servidor en diferentes lenguajes. Las más conocidas son [Relay](https://facebook.github.io/relay/) (de FB) y [Apollo](http://dev.apollodata.com/) (terceros)
  

---

## Ejemplo sencillo

- Tomado de [https://github.com/kadirahq/graphql-blog-schema](https://github.com/kadirahq/graphql-blog-schema). Un API para gestionar un blog, sin BD, con datos en memoria para simplificar.

- [Esquema](https://github.com/kadirahq/graphql-blog-schema/blob/master/src/schema.js)
  * Recursos: `Post`, `Category`, `Author`, `Comment` 
  * Relaciones:  `Post->Category(1:1)`, `Post->Comment(1:N)`, `Post->Author(N:1)`, `Comment->Author(N:1)`

---

## Cómo probar el ejemplo

1. Clonar el [repositorio git](https://github.com/kadirahq/graphql-blog-schema)
2. Instalar dependencias con `npm install`
3. Arrancar el servidor GraphQL con `npm run start`
4. Abrir un navegador e ir a `http://localhost:3000`. Aparecerá [GraphiQL](https://github.com/graphql/graphiql), que es un editor interactivo y con autocompletado para lanzar consultas a APIs GraphQL

También podéis probarla *online* en [https://radiant-atoll-63982.herokuapp.com/](https://radiant-atoll-63982.herokuapp.com/)

---

Podéis probar estas consultas, u otras similares:

Podemos obtener los campos que queramos, del objeto sobre el que hacemos la *query* o de los relacionados

```javascript
query {
  latestPost {
    title
    author {
      name
    }
  }
}
```
Las *queries* pueden tener parámetros

```javascript
query {
  recentPosts(count:2) {
    title
    category 
  }
}
```

---

Ejemplo de mutación

```javascript
mutation {
  createAuthor(_id:"Pepito", name:"Pepito Pérez", twitterHandle:"@pepito") {
    # la mutación devuelve el autor creado, mostramos el nombre
    # (aunque es un poco tontería porque ya lo sabíamos :))
    name
  }
```

---

## El *schema*

Define la interfaz con el API: las **queries** (consultas), las **mutaciones** (modificaciones) y la **estructura de los datos**.

```javascript
const Schema = new GraphQLSchema({
  query: Query,
  mutation: Mutation
});

//aqui se definen las queries posibles y también la estructura
const Query = new GraphQLObjectType({
  name: 'BlogSchema',
  description: 'Root of the Blog Schema',
  fields: () => ({
    posts: {
      ...
    }
    latestPost: {
      ...
    }
    ...
  })
  ...
})
```
<!-- .element class="caption" -->[Código completo](https://github.com/kadirahq/graphql-blog-schema/blob/master/src/schema.js)

---

## El *schema*

Define también la estructura de los datos

```javascript
const Author = new GraphQLObjectType({
  name: 'Author',
  description: 'Represent the type of an author of a blog post or a comment',
  fields: () => ({
    _id: {type: GraphQLString},
    name: {type: GraphQLString},
    twitterHandle: {type: GraphQLString}
  })
});
```


---

La *magia*, o el **"enganche" entre GraphQL y los datos reales** (típicamente en una BD, pero aquí simplemente en variables en memoria) se hace en la función **`resolve()`**

```javascript
latestPost: {
  type: Post,
  description: 'Latest post in the blog',
  resolve: function() {
    PostsList.sort((a, b) => {
      var bTime = new Date(b.date['$date']).getTime();
      var aTime = new Date(a.date['$date']).getTime();

      return bTime - aTime;
    });

    return PostsList[0];
  }
},
```

---

<!-- .slide: class="dim" -->
<!-- .slide: data-background-image="img_1d/minions.jpg" -->
<!-- .slide: style="color: white; text-shadow: 1px 1px 3px black" -->
## Demo con el API GraphQL de Github

- [Prueba](https://developer.github.com/v4/explorer/) (hace falta *loguearse* en Github)

- [Documentación](https://developer.github.com/v4/)

- [Otros APIs GraphQL de acceso público](https://github.com/apis-guru/graphql-apis)

---

## Más sobre GraphQL

- En un API REST hay una URL por recurso, aquí **todas las peticiones van a la misma URL**

```http
http://miservidorgraphql/api?query={...}
```

- Para los **errores no se usa el código de estado HTTP**, sino campos en el JSON de la respuesta

```javascript
#query incorrecta, ya que el campo "titulo" no existe
query {
  post(_id:"100") {
    titulo
  }
}

#respuesta del servidor
{
  "errors": [
    {
      "message": "Cannot query field \"titulo\" on type \"Post\"."
    }
  ]
}
```

---

GraphQL es una tecnología prometedora, pero como todas las nuevas tecnologías tiene un tiempo de vida incierto. **Tampoco hace falta matar ya a REST**

<iframe width="640" height="360" data-src="https://www.youtube.com/embed/cUIhcgtMvGc" frameborder="0" allowfullscreen></iframe>"</iframe>

<!-- .element: class="caption" --> [Por qué API REST está muerto y debemos usar APIs GraphQL - José María Rodríguez](https://youtu.be/cUIhcgtMvGc) (Independientemente de lo tremendista del título, una charla interesante para ilustrar cómo funciona GraphQL y diferencias con REST)

---

<!-- .slide: class="titulo" -->

# 3. APIs basados en eventos

---


- En ciertos casos queremos estar al tanto de las **actualizaciones del servidor** (p. ej. un *juego online*, un *chat*, ver los *tweets* de nuestro *timeline*, ...)
- El cliente puede hacer ***polling* periódicamente, pero es ineficiente**, es mejor **que el servidor "nos avise"** de que hay nuevos datos


---

## Petición/respuesta vs. eventos


<!-- .element class="caption" -->De [https://realtimeapi.io/hub/event-driven-apis/](https://realtimeapi.io/hub/event-driven-apis/)
![ddd](https://dzone.com/storage/temp/6520586-pushpin.jpg)


---

## Algunas tecnologías web para tiempo real/eventos

**Servidor-Cliente(Navegador)**

- **Long polling**: el cliente hace *polling* pero la conexión se mantiene abierta hasta que el servidor envía datos. Entonces hay que hacer *polling* de nuevo
- **Server Sent Events**: el cliente recibe de forma asíncrona mensajes y eventos del servidor
- **Websockets**: comunicación bidireccional asíncrona basada en eventos

**Servidor-Servidor**

- **Webhooks**: se avisa con una petición HTTP cuando hay nuevos datos (no sigue exactamente el esquema de la transparencia anterior)


---


## Polling vs Long polling vs. SSE

<!-- .element class="caption" --> De [Polling vs SSE vs WebSocket— How to choose the right one](https://codeburst.io/polling-vs-sse-vs-websocket-how-to-choose-the-right-one-1859e4e13bd9)
![](https://cdn-images-1.medium.com/max/800/1*zG7Jyeq02JRAN6Wz6gs15g.png)

---

## Server Sent Events


 + **Unidireccionales**, siempre desde el servidor al cliente
 + Mensajes de **texto**
 + Funciona sobre **HTTP**
 + El API [no está soportado](https://caniuse.com/#feat=eventsource) en **Explorer/Edge**, Opera

---

## Ejemplo de SSE

<!-- .element  class="caption"--> Ejemplo completo en [https://glitch.com/edit/#!/peridot-coin](https://glitch.com/edit/#!/peridot-coin)

```javascript
//Servidor
app.get('/sse', function(pet, resp) {
  //El servidor de eventos debe usar el tipo MIME text/event-stream
  resp.header('Content-Type', 'text/event-stream')
  //Temporizador cada dos segundos
  setInterval(function() {
     //nombre del evento
     resp.write('event: ping\n')
     //datos del evento (texto, en nuestro caso un JSON)
     resp.write(`data: {"timestamp":"${new Date()}"}`)
     //Hay que acabar el mensaje con 2 retornos de carro
     resp.write('\n\n')
  }, 2000)
})

```

```javascript
//Cliente
var evtSource = new EventSource("/sse");
evtSource.addEventListener('ping', function(evento) {
   var datos = JSON.parse(evento.data)
   console.log(datos.timestamp)
})
```

---

Facebook ofrece algunos *endpoints* SSE en su "graph API"

[https://developers.facebook.com/docs/graph-api/server-sent-events](https://developers.facebook.com/docs/graph-api/server-sent-events)

![](img_1d/sse_facebook.png)


---

## Websockets


+ **Bidireccionales**, tanto cliente como servidor pueden enviar mensajes
+ Los mensajes pueden contener **texto** o datos **binarios**
+ Usa un **protocolo propio** (no es HTTP). Podemos tener problemas para pasar algunos *firewalls* 

---

## Ejemplo de websockets

<!-- .element  class="caption"--> Ejemplo completo en [https://glitch.com/edit/#!/sugar-property](https://glitch.com/edit/#!/sugar-property)

```javascript
//SERVIDOR
var express = require('express');
var app = express();
app.use(express.static('public'));
var expressWs = require('express-ws')(app);

app.ws('/', function(ws, pet) {
  ws.on('message', function(data){
        console.log("Mensaje del cliente: " + data)
  })
  
  setInterval(
    () => ws.send(new Date().toLocaleTimeString()),
    2000
  )
}))
```

```javascript
//CLIENTE
var ws = new WebSocket('wss://' + window.location.hostname)
      
ws.onmessage = function(evento) {
 console.log("El servidor dice: " + evento.data)
}   

document.getElementById('botonMensaje').addEventListener('click', function() {
  ws.send(document.getElementById('mensaje').value)  
})
```

---

## Webhooks

- Cuando nuestro servidor accede a un servicio de un tercero y queremos que ese tercero nos avise de actualizaciones
- Unir/modificar ideas que ya conocéis de otras asignaturas
   - "Patrón de diseño" **publicar/suscribir** 
   - ***Callbacks***
- Cuando hay algún evento importante, el servidor del Webhook lanza una petición POST a una URL de nuestro servidor (*callback*)

---

## PubSubHubbub

- "Extensión" de *webhooks* en el que las suscripciones las gestiona un tercero (*hub*) 


![](img_1d/pubsubhubbub-processo.jpg)

- Añade funcionalidades como la **validación de suscripciones** (para evitar que alguien nos suscriba sin nosotros desearlo): cuando nos suscribimos, el servidor envía un GET al que hay que responder de la forma adecuada para confirmar subscripción

---

## Webhooks "ahí afuera"

- Algunos APIs REST públicos que usan *webhooks* y/o PubSubHubbub: Facebook, Instagram, Github, Paypal, Foursquare, algunos de Google (p.ej. Calendar), ... 
- Documentación y ejemplos
  - [Facebook real-time updates](https://developers.facebook.com/docs/graph-api/real-time-updates/)
  - [Github webhooks](http://developer.github.com/v3/repos/hooks/)
 

---

<!-- .slide: class="dim" -->
<!-- .slide: data-background-image="img_1d/minions.jpg" -->
<!-- .slide: style="color: white; text-shadow: 1px 1px 3px black" -->

# Demo:
# *webhooks* en Github

---

1. Necesitamos un servidor que pueda recibir peticiones del exterior. Podemos usar p.ej. [Beeceptor](https://beeceptor.com/) para obtener una URL pública *temporal* a la que hacer peticiones
2. En el API REST de github, dada la URL de un repo, sus *webhooks* están en `hooks` (`https://api.github.com/repos/:nombre_usuario/:nombre_repo/hooks`. Como queremos *crear* uno nuevo, hay que enviar un POST con datos JSON, por ejemplo

```javascript
{
  "name": "web",
  "active": true,
  "events": [
    "push",
    "pull_request"
  ],
  "config": {
    "url": URL pública a la que Github hará POST,
    "content_type": "json"
  }
}

```
Esqueleto de script CURL+JSON en [https://repl.it/repls/NervousForestgreenComputationallinguistics](https://repl.it/repls/NervousForestgreenComputationallinguistics)

---

## Limitación importante de los webhooks

El suscriptor al *webhook* debe poder responder a peticiones HTTP. **No nos vale** por ejemplo para avisar a nuestra app cliente **en el navegador**

![](https://media.giphy.com/media/rvDtLCABDMaqY/giphy.gif)




---

# ¿Alguna duda?
