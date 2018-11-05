# Estructura del proyecto

Una vez terminada esta práctica tendréis dos servidores en funcionamiento:

- El servidor del API, que corre en el puerto 3000.
- El servidor del *frontend* que vais a implementar en esta práctica. Usaremos de momento un servidor de desarrollo que correrá por el puerto 1234.

Un usuario final se conectaría al servidor del *frontend*. El código JS que contiene el *frontend* hará peticiones REST al servidor del API.

![](images/estructura.png)

Como las peticiones que van del *frontend* al API se hacen con Javascript desde el navegador, necesitamos que el servidor del API implemente CORS para que el navegador se "salte" la política de seguridad del "mismo origen". En caso contrario el JS solo podría hacer peticiones al servidor del *frontend* pero no al del API.

## Modificación del código del servidor de API

Para añadir soporte de CORS al servidor del API lo más sencillo es usar un paquete `npm` que lo implemente, por ejemplo el paquete `cors`

1. En el proyecto de la práctica anterior instalar el paquete `cors` con 

    ```bash
    npm install cors
    ```

2. En el código principal añadir:

    ```javascript
    var cors = require('cors')
    //Suponiendo "app" la variable obtenida como app=express()
    app.use(cors())
    ```

## Creación del proyecto cliente

En el desarrollo usaremos un *bundler*, que es una herramienta que nos permitirá usar módulos ES6 aunque el navegador no los soporte. También podremos usar otras funcionalidades de JS todavía no implementadas en los navegadores actuales, ya que el *bundler* traducirá el código a la versión ES5 con la ayuda de un *transpilador* (normalmente Babel).

Además los *bundler* suelen ofrecer otras funcionalidades adicionales como importar CSS como si fueran módulos JS o minificar automáticamente el código para la versión de producción.

Actualmente el *bundler* más difundido es [Webpack](https://webpack.js.org), pero es conocido por necesitar una configuración "complicadilla", así que en nuestro caso usaremos [Parcel](https://parceljs.org) cuya configuración es especialmente sencilla.

### Un proyecto cliente de tipo "hola mundo"

Lo primero es inicializar el proyecto cliente e instalar Parcel en él:

```bash
mkdir hola_parcel
cd hola_parcel
npm init -y #creará un package.json con valores por defecto
npm install parcel-bundler --save-dev
```
Una vez hecho esto podemos empezar a probar cómo importar módulos JS con `import` gracias a Parcel. Por ejemplo, este módulo JS

```javascript
//Archivo "saludador.js"
var saludos = ["¡Hola!", "Yiiiha!", "¿Cómo estás?", "Buenaaaas"]

function saludar() {
    var pos = Math.floor(Math.random()*saludos.length)
    return saludos[pos]
}

export {saludar}
```

Lo podemos usar en nuestro código con 

```javascript
//Archivo "main.js"
import {saludar} from './saludador.js'

document.addEventListener('DOMContentLoaded', function() {
    document.getElementById('mensaje').innerHTML = saludar()
})
```

Y ahora creamos el `index.html` que cargue el `main.js`

```html
<html>
<body>
  <h1 id="mensaje"></h1>
  <script src="./main.js"></script>
</body>
</html>
```

Para probarlo podemos hacer uso del servidor de desarrollo de Parcel, que está localizado en `./node_modules/.bin` (salvo que hayamos instalado Parcel con el *switch* `-g`, en cuyo caso estará en el `PATH`). 

```bash
./node_modules/.bin/parcel index.html
```

Esto abrirá un servidor web de desarrollo por el puerto 1234. Parcel incorpora *Hot Module Reloading*, lo que quiere decir (entre otras cosas) que si cambiamos cualquier módulo el efecto se verá inmediatamente sin necesidad de recargar la página.

### Un proyecto cliente para empezar con vuestra práctica

Para vuestra práctica necesitaréis un proyecto que incluya Parcel como herramienta de desarrollo. Se recomienda usar *handlebars* como lenguaje de plantillas (aunque podéis usar otro si lo deseáis).

```bash
#en el mismo nivel que esté el proyecto del servidor
mkdir cliente_js_vanilla
cd cliente_js_vanilla
npm init -y #creará un package.json con valores por defecto
npm i parcel-bundler --save-dev
npm i handlebars
```

Como ejemplo para vuestro código os puede servir esta [lista de la compra en JS estándar](https://github.com/ottocol/vanillaJS-lista-compra).