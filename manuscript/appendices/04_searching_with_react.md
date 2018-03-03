# Buscar con React

Digamos que quieres implementar una pequeña búsqueda aproximada para una aplicación sin una puerta trasera apropiada. Podrías hacerlo mediante [lunr](http://lunrjs.com/) y generar un índice de búsqueda estática para servir.

El problema es que el índice puede ser considerable dependiendo de la cantidad del contenido. Lo bueno es que no necesitas buscar el índice directo desde el incio. Puedes hacer algo más inteligente en su lugar. Puedes empezar cargando el índice cuando el usuario seleccione un campo de búsqueda.

Hacer esto aplaza la carga y la mueve a un lugar donde es más aceptable para el rendimiento. Dado que la búsqueda inicial es más lenta que las subsecuentes, deberías desplegar un indicador de carga. Pero eso está bien desde el punto de vista del usuario. La característica *Code Splitting* de Webpack permite hacer esto.

## Implementar búsqueda con código de separación

Para implementar el código de separación, necesitas decidiar dónde el punto de quiebre, ponlo, y luego maneja el `Promise`:

```javascript
import("./asset").then(asset => ...).catch(err => ...)
```

Lo bueno es que esto da manejo de errores en caso de que algo vaya mal (la red está caída, etc.) y da una oportunidad para recuperar. Puedes usar también `Promise`, utilidades basadas como `Promise.all` para componer solicitudes más complicadas.

{pagebreak}

En este caso, necesitas detectar cuando el usuario selecciona el elemento de búsqueda, carga la información inútil que ya había sido cargada, y luego ejecutar la búsqueda lógica contra ella. Considera la implementación de React de abajo:

**App.js**

```javascript
import React from "react";

export default class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      index: null,
      value: "",
      lines: [],
      results: [],
    };
  }
  render() {
    const { results, value } = this.state;

    return (
      <div className="app-container">
        <div className="search-container">
          <label>Search against README:</label>
          <input
            type="text"
            value={value}
            onChange={e => this.onChange(e)}
          />
        </div>
        <div className="results-container">
          <Results results={results} />
        </div>
      </div>
    );
  }
  onChange({ target: { value } }) {
    const { index, lines } = this.state;

    // Set captured value to input
    this.setState(() => ({ value }));

    // Search against lines and index if they exist
    if (lines && index) {
      return this.setState(() => ({
        results: this.search(lines, index, value),
      }));
    }

    // If the index doesn't exist, it has to be set it up.
    // You could show loading indicator here as loading might
    // take a while depending on the size of the index.
    loadIndex()
      .then(({ index, lines }) => {
        // Search against the index now.
        this.setState(() => ({
          index,
          lines,
          results: this.search(lines, index, value),
        }));
      })
      .catch(err => console.error(err));
  }
  search(lines, index, query) {
    // Search against index and match README lines.
    return index
      .search(query.trim())
      .map(match => lines[match.ref]);
  }
}

const Results = ({ results }) => {
  if (results.length) {
    return (
      <ul>
        {results.map((result, i) => <li key={i}>{result}</li>)}
      </ul>
    );
  }

  return <span>No results</span>;
};

function loadIndex() {
  // Here's the magic. Set up `import` to tell Webpack
  // to split here and load our search index dynamically.
  //
  // Note that you will need to shim Promise.all for
  // older browsers and Internet Explorer!
  return Promise.all([
    import("lunr"),
    import("../search_index.json"),
  ]).then(([{ Index }, { index, lines }]) => {
    return {
      index: Index.load(index),
      lines,
    };
  });
}
```

En el ejemplo, webpack detecta el `import` estáticamente. Puede generar un paquete separado basado en este punto de quiebre. Dado que depende de un análisis estático, no puedes generalizar a `loadIndex` en este caso y pasar la ruta del índice de búsqueda como un parámetro.

{pagebreak}

## Conclusión

Más allá de la búsqueda, el acercamiento puede ser usado con los routers también. Como el usuario introduce una ruta, puedes cargar las dependencias que la vista resultante necesite. Alternativamente, puedes empezar a cargar dependencias cuando el usuario se mueva por una página y obtenga partes adyacentes con funcionalidad real. `import` provee un montón de poder y te permite mantener tu aplicación esbelta.

Puedes encontrar una [full example](https://github.com/survivejs-demos/lunr-demo) mostrando cómo van todas juntas con lunr, React, y webpack. La idea básica es la misma, pero hay ma´s configuración en su lugar.

Para recapitular:

* Si tu conjunto de datos es pequeño y estático, la búsqueda client-side es una buena opción.
* Puedes indexar tu contenido usando una solución como [lunr](http://lunrjs.com/) y luego realizar una búsqueda contra ella.
* La característica *code splitting* de Webpack es ideal para cargar un índice de búsqueda bajo demanda.
* Code splitting can be combined with a UI solution like React to implement the whole user interface.
