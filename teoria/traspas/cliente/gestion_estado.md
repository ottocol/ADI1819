<!-- .slide: class="titulo" -->

# Tema 3, parte II
# Frameworks JS en el cliente
# Gestión del estado en el cliente web

---

> "Como los requisitos en aplicaciones JavaScript de una sola página se están volviendo cada vez más complicados, nuestro código, mas que nunca, debe manejar el estado. **Este estado puede incluir respuestas del servidor y datos cacheados, así como datos creados localmente que todavía no fueron guardados en el servidor. El estado de las UI también se volvió más complejo**, al necesitar mantener la ruta activa, el tab seleccionado, si mostrar o no un spinner, si deben mostrarse los controles de paginación o no.

> **Controlar ese cambiante estado es difícil**. Si un modelo puede actualizar otro modelo, entonces una vista puede actualizar un modelo, el cual actualiza otro modelo, y esto causa que otra vista se actualice. **En cierto punto, ya no se entiende que esta pasando en la aplicación ya que perdiste control sobre el cuándo, el por qué y el cómo de su estado**. Cuando un sistema es opaco y no determinista, es difícil reproducir errores o agregar nuevas características".

<!-- .element: class="caption"-->Tomado de la documentación de Redux: "[Motivación](http://es.redux.js.org/docs/introduccion/motivacion.html)"]

---

<!-- .slide: class="titulo" -->

## 1
## Estado local vs estado centralizado

---


- Al estar la aplicación estructurada en **componentes** una idea natural es que **cada componente almacene localmente su estado**
- Problema: conforme crece el número de componentes las comunicaciones se hacen mucho más complicadas

![](images_estado/flujo_informacion_componentes.png)


---

Principios de diseño importantes:

>  Almacenar el **estado** en el componente de **nivel superior**

Beneficio: si un componente no tiene estado podemos considerar la vista como una función pura de sus `props`.

Simplifica el *testing* y el razonamiento sobre el componente

---

Principios de diseño importantes: 

> Mantener un **flujo unidireccional** de información

- Si cambia el estado: comunicación de "padres" a "hijos" con las *props*
- Si hay que cambiarlo : de "hijos" a "padres" mediante eventos

Beneficio: más fácil seguir la pista de los cambios si hay algún *bug*

---

Aún así, puede que tengamos casos en que necesitemos comunicar componentes no relacionados entre sí.

Para evitar acoplamiento entre componentes podemos usar un **event bus**

---

## Event Bus

- Es simplemente un objeto global que permite **publicar eventos y suscribirse a ellos**. Los eventos serán los mensajes entre componentes.


- En Javascript este patrón suele llamarse *event bus* o *event emitter*. Hay multitud de librerías que implementan esta idea.

---

## Event Bus en Vue

Podemos aprovechar que el propio Vue contiene una implementación de un *event emitter*, en los métodos `$emit` y `$on`

```javascript
//Event bus
export const EventBus = new Vue() 
```

```javascript
//Componente 1: emisor. Suponemos hecho el import
...
EventBus.$emit('mi-evento', '¡Hola soy otro componente!');
```

```javascript
//Componente 2: receptor. Suponemos hecho el import
//Esto se haría normalmente en el método "created" del componente
EventBus.$on('mi-evento', (datos)=>{ console.log(datos) };
```
<!-- .element: class="caption" -->[Ejemplo completo](https://codesandbox.io/s/github/ottocol/prueba-eventbus-vue)

---

Por desgracia, el *event bus* rompe la idea de *flujo unidireccional*. **¿Cómo podemos seguir manteniendo un flujo unidireccional de información en toda la aplicación?**


---

<!-- .slide: class="titulo" -->

## 2
## El patrón *Store*

---

Idea: ¿por qué no sacamos el estado fuera de todos los componentes y nos lo llevamos a un "almacén centralizado"?

De ese modo **todos los componentes** se convertirían en funcionales

---

```javascript
const sourceOfTruth = {
  usuario : {
    nombre: 'Pepe Pérez',
    login: 'pepe'
  }
}

const vmA = new Vue({
  data: sourceOfTruth
})

const vmB = new Vue({
  data: sourceOfTruth
})
```

Problema: `sourceOfTruth` está actuando como una **variable global** que podemos modificar desde cualquier componente Vue

---

## El "patrón" *store*

- *store*: almacén centralizado con el estado de la *app*
- Los componentes **no modifican directamente** el estado, las modificaciones se hacen siempre a través de **métodos del *store*** 

```javascript
var store = {
  state: {
    message: 'Hello!'
  },
  setMessageAction (newValue) {
    this.state.message = newValue
  },
  clearMessageAction () {
    this.state.message = ''
  }
}
```

---

<!-- .element: class="stretch" -->
![](images_estado/simple_store.png) 


---

<!-- .slide: class="titulo" -->

## 3
## Vuex


---

**Vuex** es el *framework* "oficial" de Vue para la gestión centralizada del estado. Es una implementación del "patrón *store*" (algo más sofisticada que lo que vimos antes)

Aunque es propio de Vue se basa en los mismos principios básicos que se aplican habitualmente en el resto de *frameworks* Javascript: React, Angular, ...


---

## Antecedentes de Vuex

- **La arquitectura Elm** (2012): Elm es un lenguaje específico para clientes web que transpila a JS
- **Flux** (2014): arquitectura propuesta por Facebook  para estructurar aplicaciones con su framework React
- **Redux** (2015): la variante de Flux de mayor éxito, transportada luego a frameworks como Angular ([NgRedux](https://github.com/angular-redux/ng-redux))

---

## El patrón *store* básico en Vuex

- El *store* es un objeto que contiene las propiedades 
  + `state`: el "árbol global" con el estado de la *app*
  + `mutations`: métodos para modificar el estado (en redux se llaman `actions`)

```javascript
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```
Los métodos en `mutations` no se llaman directamente, sino a través de `commit`

```javascript
//a commit se le pasa el nombre de la mutación
store.commit('increment')
```

---

## Componente que usa el *store*

- Tiene una propiedad *computed* a partir del *state* del *store*. Al ser Vue reactivo, un cambio el el *store* desencadena finalmente un repintado del componente.
- Llamamos a `commit` sobre el *store* para cambiar el estado

```javascript
Vue.component('contador', {
  computed: {
    count () {
      return store.state.count
    }
  },
  //Aquí falta el template, mirar el ejemplo completo
  methods: {
    increment () {
      store.commit('increment')
    },
    decrement () {
      store.commit('decrement')
    }
  }
})
```

[Ejemplo completo en JSBin](https://jsbin.com/rahoyaj/2/edit?html,js,output)



---

## Flujo unidireccional en Vue


<!-- .element: class="stretch" --> ![](images_estado/vuex_diagrama.png)


Nota: todavía no hemos visto las *actions*, son como las mutaciones pero para 
operaciones asíncronas

---

Para **aplicaciones pequeñas, Vuex/Redux/NgRedux... no son necesarios**

> **People often choose Redux before they need it**. “What if our app doesn’t scale without it?” **Later, developers frown at the indirection Redux introduced to their code**. “Why do I have to touch three files to get a simple feature working?” Why indeed!
People blame Redux, React, functional programming, immutability, and many other things for their woes, and I understand them. It is natural to compare Redux to an approach that doesn’t require “boilerplate” code to update the state, and to conclude that Redux is just complicated

<!-- .element: class="caption" --> Dan Abramov, [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)"

---

Aunque en teoría el estado debe estar **todo** en el *store* eso no significa que en la práctica no podamos tener algo de estado en algún componente, sobre todo el estado referente a la interfaz de usuario

---

## Las mutaciones pueden tener parámetros

```javascript
// En el store
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

```javascript
//En un componente que dispare mutaciones
store.commit('increment', {
  amount: 10
})
```

---

## ¿Qué pasa con los formularios?

`v-model` se usa típicamente para vincular un campo de formulario con un dato del estado. Problema: el estado del *store* no se puede cambiar directamente

```javascript
<input v-model="store.state.mensaje">. //¡Error!
```

Una solución es vincular el evento `input` o `change` del campo a un método del componente que haga `commit` de una mutación (salvando las distancias, así es como se hace en React)

---

## ¿Qué pasa con los formularios? (II)

Solución más corta: Usar una propiedad *computed* con *setter* (al cambiar la propiedad en realidad estamos llamando a un método -> `commit` de una mutación)

```html
<input v-model="mensaje">
```

```javascript
computed: {
  mensaje: {
    get () {
      return store.state.mensaje
    },
    set (valor) {
      store.commit('actualizarMensaje', valor)
    }
  }
}
```

---


## Time travel debugging


Como los cambios en el estado siempre se hacen con mutaciones, si hacemos un *log* de todas ellas, avanzando y retrocediendo por él podemos **reproducir el estado de la aplicación en cualquier momento**

<!-- .element: class="caption" -->  (de la documentación de las [Vue dev tools](https://github.com/vuejs/vue-devtools), que incluyen *time travel debugging*)

<!-- .element: class="stretch" -->![](https://raw.githubusercontent.com/vuejs/vue-devtools/master/media/demo.gif)


---

## Acciones

Así se denominan en Vue las "**mutaciones asíncronas**"

Reciben como primer parámetro el `context`, que sirve para acceder al estado del *store* (`context.store`) y despachar mutaciones (con `context.commit`).

Típicamente una acción despacha varias mutaciones, que marcan "puntos concretos" en su flujo temporal.

```javascript
//En el store
actions: {
  //Una supuesta acción para hacer checkout en una tienda
  checkout (context, productos) {
    context.commit('CHECKOUT_REQUEST')
    shop_API.comprar(productos)
      then(function(){
        context.commit('CHECKOUT_OK')
      })
      .catch(function(){
        context.commit('CHECKOUT_OK'),
      })
  }
}
```

---

# Más funcionalidades

En su mayoría son "azúcar sintáctico" para hacer más sencillas ciertas operaciones, por eso no las vemos aquí en detalle, pero son útiles. Por ejemplo:

- [Inyectar el *store*](https://vuex.vuejs.org/guide/state.html#getting-vuex-state-into-vue-components) automáticamente en todos los componentes (accesible con `this.$store`). Útil en aplicaciones modulares para no tener que andar importándolo siempre
- Asociar automáticamente una llamada a un método del componente con un commit de una acción del mismo nombre ([`mapMutations`](https://vuex.vuejs.org/guide/mutations.html#committing-mutations-in-components))
