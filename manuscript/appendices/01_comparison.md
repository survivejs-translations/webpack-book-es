# Comparación de herramientras de Construcción

Anteriormente, era suficiente concatenar los scripts. Sin embargo, los tiempos han cambiado, y ahora distribuir el código JavaScript puede ser un esfuerzo complicado. Este problema se ha intensificado con el aumento de las aplicaciones de página única (AsPU). Ellas suelen confiar en muchas librerias más robustas. 

Por esta razón, existen múltiples estrategias sobre cómo cargarlas. Se podrían cargar todas a la vez o considerar cargarlas a medida que vayan siendo necesarias. El paquete web apoya muchos de estos tipos de estrategias.  

La popularidad de Node y [npm](https://www.npmjs.com/), es el administrador de paquete, que provee más contexto. Antes de que npm se hiciera más popular, era difícil consumir dependencias. Hubo un periodo donde las personas desarrollaron administradores de paquetes de específicos, pero al final npm ganó. Ahora el manejo de dependencias es mucho más fácil que antes, aunque aún hay desafíos por superar.

## Corredores de Tareas y Conjuntos de Paquetes

Historicamente hablando, han existido muchas herramientas. El más conocido es tal vez *Make*, y es todavía una opción viable. *Corredores de Tareas* especializados, tales como Grunt y Gunt fueron creados particurlarmente con desarrolladores JavaScript en mente. Los complementos disponibles a través de npm hace que ambos corredores de tareas sean poderosos y extensibles. Es incluso posible usar npm `scripts` como un corredor de tareas. Eso es común, particularmente con paquete web.

Los corredores de tareas son grandes herramientas en un alto nivel. Te permiten realizar operaciones en una plataforma cruzada. El problema inicia cuando se necesita unir varios puntos fuertes y producir conjuntos de paquetes. *Empaquetadores*, como Browserify, Brunch o paquete web, existen por esta razón.

{salto de página}

Por un tiempo, [RequireJS](http://requirejs.org/) fue popular. La idea era proveer una definición de módulo asincrónico y construir sobre eso. El formato, AMD, se encuentra cubierto con mayor detalle luego en este capítulo. Afortunadamente, los estándares se han alcanzado, y RequireJS parece ahora más una curiosidad.

## Make

[Make](https://en.wikipedia.org/wiki/Make_%28software%29)  va mucho más atrás, dado que fue lanzado en 1977. Aunque es una herramienta vieja, se ha mantenido relevante. Make permite escribir tareas separadas para diferentes propósitos. Por ejemplo, se puede tener diferentes tareas para la creación de una producción de construcción, minificando el JavaScript o proceso de pruebas. Se puede encontrar la misma idea en muchas otras herramientas.

Aún cuando Makees mayormente usado con proyectos en C, no se encuentra atado a C de ninguna forma. James Coglan discute en detalle [how to use Make with JavaScript](https://blog.jcoglan.com/2014/02/05/building-javascript-projects-with-make/). Considera el código abreviado basado en la publicación de James de abajo:

**Archivo Make**

```makefile
PATH  := node_modules/.bin:$(PATH)
SHELL := /bin/bash

source_files := $(wildcard lib/*.coffee)
build_files  := $(source_files:%.coffee=build/%.js)
app_bundle   := build/app.js
spec_coffee  := $(wildcard spec/*.coffee)
spec_js      := $(spec_coffee:%.coffee=build/%.js)

libraries    := vendor/jquery.js

.PHONY: all clean test

all: $(app_bundle)

build/%.js: %.coffee
    coffee -co $(dir $@) $<

$(app_bundle): $(libraries) $(build_files)
    uglifyjs -cmo $@ $^

test: $(app_bundle) $(spec_js)
    phantomjs phantom.js

clean:
    rm -rf build
```

 Con Make, se modela las tareas usando la sintaxis específica Make- y los terminales de comando haciendo posible integrarlo con paquete web.

## RequireJS

[RequireJS](http://requirejs.org/) fue tal vez el primer cargador de script que se hizo genuinamente popular. Le dio el primer vistazo apropiado a lo que un JavaScript modular en la web podría ser. Su mayor atracción fue AMD. Introdujo una envoltura `define`:

```javascript
define(["./MyModule.js"], function (MyModule) {
  return function() {}; // Export at module root
});

// or
define(["./MyModule.js"], function (MyModule) {
  return {
    hello: function() {...}, // Export as a module function
  };
});
```

{salto de página}

Incidentemente, es posible usar `require` dentro de la envoltura: 

```javascript
define(["require"], function (require) {
  var MyModule = require("./MyModule.js");

  return function() {...};
});
```

Este último enfoque elimina una parte del desorden. Todavía se mantiene un código que se siente redundante. ES2015 y otros estándares solucionan esto.

T> Jamund Ferguson ha escrito un excelente blog sobre cómo portar desde [RequireJS to webpack](https://gist.github.com/xjamundx/b1c800e9282e16a6a18e).

## npm `scripts` como un Corredor de Tarea

Aunque npm CLI no fuese primordialmente diseñado para ser usado como un corredor de tarea, funciona como tal gracias al campo de `scripts` *package.json*. Considera el siguiente ejemplo:

**package.json**

```json
"scripts": {
  "stats": "webpack --env production --json > stats.json",
  "start": "webpack-dev-server --env development",
  "deploy": "gh-pages -d build",
  "build": "webpack --env production"
},
```

Estos scripts pueden ser enlistados usando `npm run` y luego ejecutados usando `npm run <script>`. También se puede nombrar el espacio de los scripts usando una convención como `test:watch`. El problema con este enfoque es que se encarga de mantener la plataforma cruzada.

En lugar de `rm -rf`, probablemente se quiera usar utilidades tales como [rimraf](https://www.npmjs.com/package/rimraf) y así sucesivamente. Es posible invocar otros corredores de tareas aquí para esconder el hecho de que se está usando uno. De esta forma se puede refactorizar las herramientas mientras se mantiene la interfaz como la misma.

## Grunt

![Grunt](images/grunt.png)

[Grunt](http://gruntjs.com/) Fue el primer corredor de tareas popular para desarrolladores de interfaz. Su arquitectura de complementos (plugin) contribuyó a su popularidad. Los complementos son comúnmente complejos por ellos mismos. Como resultado, cuando crece la configuración, resulta más difícil comprender qué está sucediendo.

Aquí un ejemplo de [Grunt documentation](http://gruntjs.com/sample-gruntfile). En esta configuración, se define un detector y un observador de tareas. Cuando el *observador* de tareas se ejecuta, se desencadena el *detector* de tareas también. De esta forma, mientras se ejecuta Grunt, se reciben advertencias en tiempo real en el terminal mientras se edita el código fuente.

{salto de página}

**Gruntfile.js**

```javascript
module.exports = grunt => {
  grunt.initConfig({
    lint: {
      files: ["Gruntfile.js", "src/**/*.js", "test/**/*.js"],
      options: {
        globals: {
          jQuery: true,
        },
      },
    },
    watch: {
      files: ["<%= lint.files %>"],
      tasks: ["lint"],
    },
  });

  grunt.loadNpmTasks("grunt-contrib-jshint");
  grunt.loadNpmTasks("grunt-contrib-watch");

  grunt.registerTask("default", ["lint"]);
};
```

En práctica, se tendrán varias pequeñas tareas para propósitos específicos, tales como la construcción de el proyecto. Una parte importante del poder de Grunt es que esconde mucho del cableado.

LLevado demasiado lejos, esto puede resultar problemático, puede resultar difícil de comprender lo que está sucediendo bajo el capót. Esa es la lección arquitectónica que se debe tomar de Grunt.

T> complemento [grunt-webpack](https://www.npmjs.com/package/grunt-webpack) te permite usar el paquete web en un ambiente Grunt mientras se deja la carga pesada para el paquete web.

## Gulp

![Gulp](images/gulp.png)

[Gulp](http://gulpjs.com/) toma un enfoque diferente. En lugar de confiar en la configuración por complemento, se lidia con código de verdad. Si se encuentra familiarizado con Unix y tuberías, Gulp será de su agrado. Se tiene *fuentes* para emparejar los archivos, *filtros* para operar en estas fuentes, y *sumideros* para canalizar el resultado de la construcción.

Aquí una muestra corta del *Archivo Gulp* adaptado desde el proyecto LEEME para dar una mejor idea del enfoque:

**Gulpfile.js**

```javascript
const gulp = require("gulp");
const coffee = require("gulp-coffee");
const concat = require("gulp-concat");
const uglify = require("gulp-uglify");
const sourcemaps = require("gulp-sourcemaps");
const del = require("del");

const paths = {
  scripts: ["client/js/**/*.coffee", "!client/external/**/*.coffee"],
};

// Not all tasks need to use streams.
// A gulpfile is another node program
// and you can use all packages available on npm.
gulp.task("clean", () => del(["build"]));
gulp.task("scripts", ["clean"], () =>
  // Minify and copy all JavaScript (except vendor scripts)
  // with sourcemaps all the way down.
  gulp
    .src(paths.scripts)
    // Pipeline within pipeline
    .pipe(sourcemaps.init())
    .pipe(coffee())
    .pipe(uglify())
    .pipe(concat("all.min.js"))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest("build/js"))
);
gulp.task("watch", () => gulp.watch(paths.scripts, ["scripts"]));

// The default task (called when you run `gulp` from CLI).
gulp.task("default", ["watch", "scripts"]);
```

Dado que la configuración es un código, siempre se puede piratear si te topas con problemas. Se pueden envolver los paquetes del Nodo existente como complementos de Gulp, y así sucesivamente. Comparado con Grunt, se tiene una idea más clara de lo que sucede. Aunque se mantiene escribiendo repetitivamente para tareas casuales. Ahí es donde nuevos enfoques aparecen.

T> [webpack-stream](https://www.npmjs.com/package/webpack-stream) permite usar el paquete web en un ambiente Gulp.

{salto de página}

## Browserify

![Browserify](images/browserify.png)

Lidiando con los modulos de JavaScript siempre ha sido un poco problemático. El lenguaje mismo no poseía el concepto de módulos hasta ES2015. Por lo tanto, el lenguaje estaba atascado en los '90 en lo que se refiere a ambientes de navegación. Diversas soluciones, incluyendo [AMD](http://requirejs.org/docs/whyamd.html), han sido propuestas. 

[Browserify](http://browserify.org/) es una solución el problema de módulos. Le permite a los módulos CommonJS ser empaquetados juntos. Se pueden conectar con Gulp, y se pueden encontrar herramientas de transformación más pequeñas que permitan ir más allá del uso básico. Por ejemplo, [watchify](https://www.npmjs.com/package/watchify) provee un archivo observador que crea conjuntos de paquetes para su desarrollo durante el esfuerzo de guardado.

El ecosistema de Browserify se encuentra compuesto por muchos módulos pequeños. De esta forma, Browserify se adhiere a la filosofía de Unix. Browserify es más fácil de adoptar que el paquete web, y es, de hecho, una buena alternativa para ello. 

T> [Splittable](https://www.npmjs.com/package/splittable) es una envoltura de Browserify que permite la división de código, apoya ES2015 fuera de lo establecido, hace vibrar, y más.

T> [transform-loader](https://www.npmjs.com/package/transform-loader) permite usar las transformaciones de Browserify con el paquete web.

## JSPM

![JSPM](images/jspm.png)

Usando [JSPM](http://jspm.io/) es bastante diferente a herramientas previas. Viene con una herramienta de comando lineal de sí misma que es usada para instalar nuevos paquetes para el proyecto, crear un empaquetamiento de producción, y así sucesivamente. Esto apoya al [SystemJS plugins](https://github.com/systemjs/systemjs#plugins) que permite cargar varios formatos al proyecto. 

## Brunch

![Brunch](images/brunch.png)

Comparado con Gulp, [Brunch](http://brunch.io/) opera en un nivel más alto de abstracción. Utiliza un enfoque declarativo similar al del paquete web. Para proporcionar un ejemplo, considera la siguiente configuración adaptada desde el sitio Brunch. 

```javascript
module.exports = {
  files: {
    javascripts: {
      joinTo: {
        "vendor.js": /^(?!app)/,
        "app.js": /^app/,
      },
    },
    stylesheets: {
      joinTo: "app.css",
    },
  },
  plugins: {
    babel: {
      presets: [react", "env"],
    },
    postcss: {
      processors: [require("autoprefixer")],
    },
  },
};
```

Brunch viene con comandos como `brunch new`, `brunch watch --server`, y `brunch build --production`. Contiene mucho de fuera de lo establecido y puede ser extendido usando complementos.

T> Hay un experimento [Hot Module Reloading runtime](https://www.npmjs.com/package/hmr-brunch) para Brunch.

## Paquete Web

![webpack](images/webpack.png)

Se podría decir que [webpack](https://webpack.js.org/) toma un enfoque más monolítico que el de Browserify. Mientras que Browserify consiste en múltiples herramientas pequeñas, el paquete web viene con núcleo que le provee de bastante funcionalidad fuera de lo establecido.

El núcleo de paquete Web puede ser extendido usando unas específicas *escaleras* y *complementos*. Le da el control sobre cómo *resuelve* los módulos, haciendo posible adaptar tu construcción para igualar situaciones especificas y soluciones alternativas que no funcionan correctamente fuera de lo establecido.

Comparado con las otras herramientas, el paquete web viene con una complejidad inicial, sin embargo lo enmenda a través de su amplio conjunto de características. Es una herramienta avanzada que requiere paciencia. Sin embargo, una vez que entiendes las ideas básicas detrás de ello, el paquete web se vuelve poderoso.

## Otras Opciones

Puedes encontrar más alternativas enlistadas abajo:

* [pundle](https://www.npmjs.com/package/pundle) se anuncia a si mismo como la próxima generación de empaquetamiento y denota particularmente su rendimiento.
* [Rollup](https://www.npmjs.com/package/rollup) se concentra en empaquetar el código ES2015. *hacer vibrar* es uno de sus puntos de venta. Puede usar Rollup con paquete web a través de [rollup-loader](https://www.npmjs.com/package/rollup-loader).
* [AssetGraph](https://www.npmjs.com/package/assetgraph) toma un enfoque totalmente diferente y construye sobre la semántica de HTML haciéndola ideal para [hyperlink analysis](https://www.npmjs.com/package/hyperlink) o [structural analysis](https://www.npmjs.com/package/assetviz). [webpack-assetgraph-plugin](https://www.npmjs.com/package/webpack-assetgraph-plugin) puntes de paquete web y AssetGraph juntos.
* [FuseBox](https://www.npmjs.com/package/fuse-box) ees un empaquetamiento que se concentra en la velocidad. Usa un enfoque de configuración cero y apunta a ser utilizable fuera de la establecido.
* [StealJS](https://stealjs.com/) es un cargador de dependencias y una herramienta de construcción que se ha concentrado en el rendimiento y en un fácil uso.
* [Flipbox](https://www.npmjs.com/package/flipbox) envuelve múltiples empaquetamientos destrás de una interfaz uniforme.
* [Blendid](https://www.npmjs.com/package/blendid)  es una mezcla de Gulp y empaquetamientos para formar unas tuberías de puntos fuertes.

## Conclusión

Historicamente ha habido muchas herramientas de construcción para JavaScript. Cada una ha intentado solucionar un problema en específico a su modo. Los estandares han comenzado a alcanzarse y menor es el esfuerzo requerido alrededor de la semántica básica. En su lugar, las herramientas pueden competir en un nivel mayor y presionar hacia una mejor experiencia para el usuario. Usualmente se puede usar un par de soluciones separadas de forma conjunta. 

Para recapitular:

* **Corredores de Tareas* y **empaquetadores**  resuelven diferentes problemas. Puede aspirar a resultados similares con ambos, pero usualmente es mejor usarlos juntos para complementarlos.
* Herramientas más viejas, tales como Make o RequireJS, aún tienen influcencia, inlcuso si no son tan populares en desarrollo web como lo fueron en el pasado. 
* Empaquetadores como Browserify o paquete web solucionan un importamte problema y a manejar aplicaciones web complejas.
* Un número de emergentes tecnología  se enfocan en el problema desde diferentes ángulos. Algunas veces se contruyen sobre otras herramientas y a veces pueden ser usadas juntas.
