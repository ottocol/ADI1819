# Introducción a Vue


## Conceptos básicos

- [Incluir Vue](https://vuejs.org/v2/guide/#Getting-Started) en la página con el `<script src="">`

```html
<script src="https://unpkg.com/vue"></script>
```

- App: plantilla  + Instancia de Vue `new Vue({el:_, data:_})`


- plantillas
    + interpolación con {{}}
    + vinculación con v-bind o `:`
    + condicionales
    + bucles
    
- reactividad
    + Cambiar las propiedades de `data` y ver cómo cambia lo mostrado
    + límites de la reactividad: 
        + Nuevas propiedades (podemos usar `Vue.set` o `this.$set`), 
        + Cambiar datos en arrays (podemos usar el `splice` de JS, o el `set` de antes)
- propiedades computadas y *watchers*
- Interactividad:
    + Two-way data binding
    + Eventos

## Componentes

- componente: `Vue.Component(nombre, {data, template, ...})`. Son instancias de `Vue` reusables, con nombre y un template entre las propiedades.
    + Diferencia importante: `data` debe ser una función que devuelva un objeto con los datos, así conseguimos que cada instancia del componente tenga sus propios datos    
    + En HTML se representan como *tags* con ese nombre
- La *app* principal será una instancia de `Vue` como hasta ahora.


## Vue CLI

```bash
npx @vue/cli create mi-proyecto
npm run serve
```



