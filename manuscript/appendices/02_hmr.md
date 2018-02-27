# Reemplazo del Módulo Caliente

**Hot Module Replacement** (HMR) se basa en el WDS. Habilita una interfaz que hace posible el intercambio de  módulos en vivo. Por ejemplo, *style-loader* puede actualizar tu CSS sin forzar una actualización. Como CSS es apatrida por diseño, implementar HMR para eso es lo ideal.

HMR es posible también con JavaScript, pero debido a la aplicación de estado, es más complicado. Vue y [vue-hot-reload-api](https://www.npmjs.com/package/vue-hot-reload-api) es un buen ejemplo.

## Habilitando HMR

Para habilitar HMR, las siguientes cosas tienen que pasar:

1. WDS tiene que ejecutarse en modo caliente para exponer la interfaz del reemplazo del módulo caliente al cliente.
2. Webpack tiene que proporcionar actualizaciones calientes para el servidor. Esto se logra usando `webpack.HotModuleReplacementPlugin`.
3. El cliente tiene que ejecutar secuencias de comando específicas proporcionadas por el WDS. Son inyectadas automáticamente pero pueden ser habilitadas explícitamente a través de la configuración de entrada.
4. El cliente debe implementar la interface HMR a través de  `module.hot.accept`.

Usar `webpack-dev-server --hot` resuelve los primeros dos problemas. En este caso tienes que controlar sólo el último tu mismo, si quieres reparar el código de aplicación de JavaScript. Omitir el indicador `--hot` e ir a través de la configuración de webpack da más flexibilidad.

{pagebreak}

La siguiente lista continiene las partes más importantes relacionadas a este enfoque. Tendrás que adaptarte desde aquí para que coincida con tu estilo de configuración:

```javascript
{
  devServer: {
    // Don't refresh if hot loading fails. Good while
    // implementing the client interface.
    hotOnly: true,

    // If you want to refresh on errors too, set
    // hot: true,
  },
  plugins: [
    // Enable the plugin to let webpack communicate changes
    // to WDS. --hot sets this automatically!
    new webpack.HotModuleReplacementPlugin(),
  ],
}
```

Si implementas la configuración como arriba sin implementar la interfaz del cliente, probablemente termines con un error:

![No refresh](images/no-refresh2.png)

El mensaje dice que aunque la interfaz de HMR notificó la parte del cliente del código de una actualización caliente, no se hizo nada al respecto. Esto es algo para solucionar después.

T> La configuración asume que has habilitado `NamedModulesPlugin`. Lee el capítulo *Adding Hashes to Filenames* para más detalles.

W> *webpack-dev-server* puede ser exigente sobre los caminos. Webpack [issue #675](https://github.com/webpack/webpack/issues/675) discute el problema con más detalle.

W> Deberías **not** habilitar HMR para tu configuración de producción. Es probable que funcione, pero hace a tus paquetes más grandes de lo que deberían ser.

W> Si usas Babel, configuralo para que webpack controle el módulo de generación, ya que, de lo contrario, la lógica de HMR no funcionará!

## Implementar la interfaz de HMR

Webpack empone la interfaz de HMR a través de una variable global: `module.hot`. Proporciona actualizaciones a través de la función `module.hot.accept(<path to watch>, <handler>)` y necesitas reparar la aplicación ahí.

{pagebreak}

La siguiente implementación ilustra la idea contra el tutorial de aplicación:

**app/index.js**

```javascript
import component from "./component";

let demoComponent = component();

document.body.appendChild(demoComponent);

// HMR interface
if (module.hot) {
  // Capture hot update
  module.hot.accept("./component", () => {
    const nextComponent = component();

    // Replace old content with the hot loaded one
    document.body.replaceChild(nextComponent, demoComponent);

    demoComponent = nextComponent;
  });
}
```

Si actualizas el buscador, trata de modificar *app/component.js* después de este cambio, y altera el texto a algo más, deberíar notar que el buscador no se actualiza del todo. En cambio, debería reemplazar el nodo DOM mientras conserva el resto de la aplicación como está.

{pagebreak}

La imagen de abajo muestra los posibles resultados:

![Patched a module successfully through HMR](images/hmr.png)

La idea es la misma con el estilo, React, Redux, y otras tecnologías. A veces, no tienes que implementar la interfaz tu mismo, aunque las herramientas disponibles se encarguen de eso por ti.

T> Para comprobar que HMR conserva el estado de aplicación, configura un componenta basado en [a checkbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/checkbox) al lado del original. El código `module.hot.accept` tiene que evolucionar para captar los cambios hechos en el también.

T> El bloque `if(module.hot)` es completamente eliminado de la construcción de producción cuando el minifier lo recoge. El capítulo *Minifying* profundiza en este tema.

{pagebreak}

## Establecer manalmente los puntos de entrada de WDS

En la configuración anterior, las entradas relacionadas con WDS fueron inyectadas automáticamente. Asumiendo que estás usando WDS a través del nodo, tendrías que configurarlos tu mismo ya que el nodo de API no soporta la inyección. El siguiente ejemplo muestra como lograrlo:
```javascript
entry: {
  hmr: [
    // Include the client code. Note host/post.
    "webpack-dev-server/client?http://localhost:8080",

    // Hot reload only when compiled successfully
    "webpack/hot/only-dev-server",

    // Alternative with refresh on failure
    // "webpack/hot/dev-server",
  ],
  ...
},
```

## Conclusión

HMR es uno de esos aspectos de webpack que lo hace atractivo para desarrolladores y webpack ha llevado lejos su implementación. Para trabajar, HMR requiere el soporte tanto del cliente como del servidor. Para este propósito, webpack-dev-server proporciona ambos. Generalmente, tienes que implementar la interfaz del lado del cliente, aunque cargadores como *style-loader* lo implementen por ti.
