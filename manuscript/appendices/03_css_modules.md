# Módulos de CSS

Quizás el mayor reto de CSS es que las todas reglas existen dentro de **global scope**, lo que significa que dos clases con el mismo nombre colicionarán. La limitación es innherente a la especificación de CSS, pero los proyectos tienen soluciones alternativas para ese problema. [CSS Modules](https://github.com/css-modules/css-modules) introduce **local scope** para cada módulo, haciendo que cada clase sea convocada individualmente, incluyendo un hash en sus nombres que sea único para todo el módulo.

## Módulos de CSS a través de *css-loader*

El *css-loader* de Webpack soporta los Módulos de CSS. Puedes activarlos a través de una definición de carga como arriba mientras se activa el soporte:

```javascript
{
  use: {
    loader: "css-loader",
    options: {
      modules: true,
    },
  },
},
```

Luego de este cambio, tu definición de clase permanecerá local para los archivos. En caso de que quieras definiciones de clase globales, necesitas envolverlas dentro del tipo de declaraciones `:global(.redButton) { ... }`.

{pagebreak}

En este caso, la definición `import` te da las clases locales que puedes entonces atar a los elementos. Asumiento que tienes el CSS como abajo:

**app/main.css**

```css
body {
  background: cornsilk;
}

.redButton {
  background: red;
}
```

Podrías entonces atar la clase resultante a un componente:

**app/component.js**

```javascript
import styles from "./main.css";

...

// Attach the generated class name
element.className = styles.redButton;
```

`body` permanece como una declaración global aún. Es ese `redButton` que hace la diferencia. Puedes hacer estilos de componentes específicos que no se filtren a otro lugar de esta manera.

Los Módulos de CSS proveen características adicionales como composición para hacer que trabajar con tus estilos sea más fácil. También puedes combinarlo con otros cargadores siempre y cuando les apliques antes *css-loader*.

T> El comportamiento de los Módulos de CSS puede ser modificado [as discussed in the official documentation](https://www.npmjs.com/package/css-loader#local-scope). Tienes control sobre los nombres que genera, por ejemplo.

T> [eslint-plugin-css-modules](https://www.npmjs.com/package/eslint-plugin-css-modules) es útil para monitorear los problemas relacionados con los Módulos de CSS.

## Usar los Módulos de CSS con Bibliotecas de terceros y CSS

Si estás usando Módulos de CSS en tu proyecto, deberías procesar el CSS normal a través de una definición de cargador separada sin la opción `modules` de *css-loader* activada. De otra froma, todas las clases serán puestas al alcande de sus ámbitos. En el caso de las bibliotecas de terceros, esto es casi seguro lo que tú no quieres.

Puedes solucionar el problema procesando el CSS de terceros de manera diferente mediante una definición `include` en contra de *node_modules*. Alternativamente, podrías usar un extensión de archivo (`.mcss`) para apartar a los archivos que usan Módulos de CSS del resto y luego manejar esta situación en un cargador `test`.

## Conclusión

Los Módulos de CSS solucionan el problema de ámbito de CSS, haciendo por defecto el ámbito por archivo. Aún puedes tener el diseño global, pero requiere esfuerzo adicional. Webpack puede ser instalado para soportar los Módulos de CSS fácilmente cómo vimos arriba.
