# Práctica evaluable 2: Aplicaciones web en el lado del cliente

El objetivo de esta práctica es desarrollar una web que sirva de cliente al API que se implementó en la práctica anterior. La idea es usar en lo posible Javascript estándar, por lo que **no se permitirá el uso de jQuery o librerías similares y tampoco el uso de *frameworks* JS como Angular, React o Vue** (estos serán objeto de la práctica 3). Para manipular el HTML tendréis que hacer uso de las funcionalidades que hasta ahora se han visto en clase de teoría (`getElementById`, `innerHTML`,...).

**Tendrás que hacer una pequeña modificación en el código de tu práctica anterior** para que pueda funcionar junto con la nueva web. Estos se describen en [otro documento](estructura_proyecto.html).

## Requisitos mínimos

La implementación correcta de estos requisitos es necesaria para poder aprobar la práctica. Con estos requisitos podéis optar hasta un 6 si comentáis adecuadamente el código fuente, indicando la funcionalidad de las variables, de cada clase (si las usáis), de cada método/función (descripción de lo que hace, parámetros, valor de retorno,...).

### Login/logout

Aunque en un API REST "puro" no existe formalmente la idea de "hacer login/hacer *logout*", para el usuario de la web es útil que existan las operaciones de *login* y el *logout*. De lo contrario tendría que estar introduciendo continuamente *login* y *password*. Debes implementar las siguientes funcionalidades:

- Por defecto, la aplicación mostrará en su página inicial un formulario para hacer *login*.  
- Una vez comprobado que *login* y *password* son correctos, y obtenido el *token* se guardará este último en el *local storage*
- Cada vez que se haga una petición al servidor para una operación restringida enviaréis el *token* en la cabecera `Authorization`
- La operación de *logout* simplemente borrará el *token* del *local storage*. 
- Si el *token* está en `localStorage` se debería mostrar el login del usuario y un botón/enlace para hacer *logout*. En caso contrario se debería mostrar el formulario de login.

### Listado de datos

Implementa al menos un **listado de datos**, correspondiente a un caso de uso del API de tipo GET y que devuelva una colección. El listado debe tener las siguientes funcionalidades:

-  En cada resultado aparecerá un botón o enlace "ver detalles" que al pulsarlo mostrará todos los detalles del item en cuestión.
-  En cada resultado aparecerá un botón o enlace "eliminar" para eliminar el item

### Creación de items

Implementa la funcionalidad de **crear un nuevo item**, es decir que sea un formulario que permita introducir los datos y lo cree en el servidor llamando a un caso de uso POST del API

## Requisitos adicionales

Además de los 6 puntos de los requisitos mínimos, elegir de entre los siguientes:

- hasta *1.5 puntos*: estilo visual de la web. Si usáis *frameworks* CSS como [bootstrap](http://getbootstrap.com/css/) podéis obtener hasta 1 punto. Si hacéis vuestro propio CSS hasta 1.5 puntos. En este último caso debéis usar alguna funcionalidad de posicionamiento CSS como *grid* o *flexbox*.
- hasta *1 punto*: implementar el listado de otro recurso incluyendo también "ver detalles" y "eliminar".
- hasta *1 punto*: edición de datos: cada elemento del listado debería tener un botón o enlace "editar" para cambiar su contenido. Al pulsarlo aparecerá un formulario para escribir los nuevos datos. Dónde y cómo aparezca (y desaparezca al acabar la edición) queda a vuestra elección.
- hasta *1.5 puntos*: estructurar adecuadamente vuestro código para separar en lo posible lógica, presentación (manipulación del HTML) y comunicación con el servidor (`fetch`). 

## Entrega

El plazo de entrega concluye el lunes **26 de noviembre de 2017** a las 23:59 horas. La entrega se realizará como es habitual a través de Moodle. Entregad una carpeta comprimida con el proyecto de esta práctica y el del servidor (modificado para usar CORS) en dos subcarpetas, ya que ambos son necesarios para ejecutar la aplicación. 

Entregad también un LEEME.txt donde expliquéis brevemente las partes optativas que habéis hecho y cualquier detalle que consideréis necesario.