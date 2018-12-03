## Estructura de una *app* modular con Vue

Aunque como vimos la semana pasada se puede usar Vue simplemente incluyéndolo en la página con un `<script src=""></script`, no es lo habitual en aplicaciones de tamaño mediano o grande, ya que esto no da soporte a aplicaciones modulares.

Podemos usar también Vue en conjunción con un *bundler* y con Babel. En las aplicaciones Vue de este tipo:

- Podemos usar módulos de ES6 como hicimos en la práctica anterior
- Podemos guardar los componentes en archivos `.vue`, de modo que en el mismo archivo tendremos el HTML, el JS y el CSS del componente. Esto en Vue se llaman *single file components*

En la documentación de Vue se explica más o menos cómo funcionan estos [*single file components*](https://vuejs.org/v2/guide/single-file-components.html). Se recomienda que le echéis un vistazo a este ejemplo de [*app* de tareas pendientes](https://codesandbox.io/s/o29j95wx9) hecho en este estilo.

Para crear estas aplicaciones lo más sencillo es usar una herramienta en línea de comandos llamada `@vue/cli`

 > Para probar esto en el laboratorio antes tenéis que instalar una versión de node que no requiera permisos de superusuario para instalar paquetes en modo global (`-g`). Recordemos que se podía hacer con `curl -L https://git.io/n-install | bash`.


```
#instalar la herramienta
npm install -g @vue/cli
#crear un proyecto
vue create practica3
cd practica3
#Ejecutar un servidor local (similar al servidor de parcel de la práctica anterior)
npm run serve
```





