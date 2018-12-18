# Práctica evaluable 3: Frameworks Javascript

El objetivo de esta práctica es desarrollar una web que sirva de cliente al API de la práctica 1, pero a diferencia de la práctica anterior, **en la implementación se debe usar un *framework* Javascript como pueda ser Angular, React, Vue o algún otro similar**.

> Aclaración: librerías como jQuery no son *frameworks* de este estilo ya que simplemente vienen a facilitar ciertas tareas como el AJAX o la manipulación del DOM.

## Requisitos mínimos

La implementación correcta de estos requisitos es necesaria para poder aprobar la práctica. Con estos requisitos podéis optar hasta un 6 si comentáis adecuadamente el código fuente, indicando la funcionalidad de las variables, de cada clase (si las usáis), de cada método/función (descripción de lo que hace, parámetros, valor de retorno,...).

Los requisitos mínimos serán los mismos que en la práctica anterior, recordad:

- Login/logout
- Listado de datos
- Creación de items


## Requisitos adicionales

Además de los 6 puntos de los requisitos mínimos, elegir de entre los siguientes:

- hasta *1 punto* si usáis CSS propio (con alguna funcionalidad de posicionamiento CSS como *grid* o *flexbox*) o *frameworks* de componentes visuales para Vue como [vuetify](https://vuetifyjs.com/en/) o [bootstrap-vue](https://bootstrap-vue.js.org)
- hasta *1 punto*: implementar el listado de otro recurso incluyendo también "ver detalles" y "eliminar".
- hasta *1 punto*: edición de datos: cada elemento del listado debería tener un botón o enlace "editar" para cambiar su contenido. Al pulsarlo aparecerá un formulario para escribir los nuevos datos. Dónde y cómo aparezca (y desaparezca al acabar la edición) queda a vuestra elección.
- hasta *2 puntos*: usar una librería para gestionar el estado de vuestra aplicación de manera centralizada. En Vue se usaría [Vuex](https://vuex.vuejs.org), en otros *frameworks* hay equivalentes, por ejemplo en React tenéis [Redux](http://es.redux.js.org).
- hasta *2 puntos*: usar un *router* para gestionar la navegación por la *app*. En el caso de Vue se usaría [Vue Router](https://router.vuejs.org)


## Entrega

El plazo de entrega concluye el lunes **14 de enero de 2018** a las 23:59 horas. La entrega se realizará como es habitual a través de Moodle. Entregad una carpeta comprimida con el proyecto de esta práctica y el del servidor (modificado para usar CORS) en dos subcarpetas, ya que ambos son necesarios para ejecutar la aplicación. 

Entregad también un LEEME.txt donde expliquéis brevemente las partes optativas que habéis hecho y cualquier detalle que consideréis necesario.