# Glosario

Dado que webpack viene con una nomenclatura específica, los términos principales y sus explicaciones han sido agrupados abajo basados en la parte del libro donde son discutidos.

## Introducción

* **Static analysis** - Cuando una herramienta realiza un análisis estático, examina el código sin ejecutarlo. Así es cómo herramientas como ESLint o webpack operan. Los estándares estadísticamente analizables, como la definición del módulo ES2015, activan características como **tree shaking**.
* **Resolving** es el procesoo que sucede cuando webpack encuentra un módulo o un cargador. Cuando esto sucede, intenta resolverlo basado en las reglas de resolución dadas.

## Desarrollo

* **Entry** se refiere a un archivo usado por webpack como un punto de partida para empaquetar. Una aplicación puede tener múltiples entradas dependiendo de la configuración, cada entrada puede resultar en múltiples paquetes. Las entradas son definidas en la configuración `entry` de webpack. Las entradas son **modules** al inicio del gráfico de dependencia.
* **Module** es un término general para describir una parte de la aplicación. En webpack, se puede referir a JavaScript, una hoja de estilo, una imagen o algo más. Los **Loaders** le permiten a webpack soportar diferentes tipos de archivo y por lo tanto diferentes tipos de módulos. Si apuntas al mismo módulo desde diferentes partes de un código base, webpack generará un solo módulo en la salida. Esto activa el patrón singleton a nivel modular.
* **Plugins** conecta al sistema de eventos de webpack y puede introducir funcionalidades en él. Ellos le permiten a webpack extenderse y combinarse con cargadores para un control máximo. Mientras que un cargador trabaja en un solo archivo, un plugin tiene un acceso más amplio y es capaz de un control más global.
* **Hot Module Replacement (HMR)** refers to a technique where code running in the browser is patched on the fly without requiring a full page refresh. When an application contains complex state, restoring it can be difficult without HMR or a similar solution.
* **Linting** relates to the process in which code is statically analyzed for a series of user-defined issues. These issues can range from discovering syntax errors to enforcing code-style. Whilst linting is by definition limited in its capabilities, a linter is invaluable for helping with early error discovery and enforcing code consistency.

## Loading

* **Loader** performs a transformation that accepts a source and returns transformed source. It can also skip processing and perform a check against the input instead. Through configuration, a loader targets a subset of modules, often based on the module type or location. A loader only acts on a single module at a time whereas a plugin can act on mulitple files.
* **Asset** is a general term for the media and source files of a project that are the raw material used by webpack in building a bundle.

## Building

* **Source maps** describe the mapping between the original source code and the generated code, allowing browsers to provide a better debugging experience. For example, running ES2015 code through Babel generates completely new ES5 code. Without a source map, a developer would lose the link from where something happens in the generated code and where it happens in the source code. The same is true for style sheets when they run through a pre or post-processor.
* **Bundle** is the result of bundling. Bundling involves processing the source material of the application into a final bundle that is ready to use. A bundler can generate more than one bundle.
* **Bundle splitting** offers one way of optimising a build, allowing webpack to generate multiple bundles for a single application. As a result, each bundle can be isolated from changes effecting others, reducing the amount of code that needs to be republished and therefore re-downloaded by the client and taking advantage of browser caching.
* **Code splitting** produces more granular bundles than bundle splitting. To use it, the developer has to enable it through specific calls in the source code. Using a dynamic `import()` is one way.
* **Chunk** is a webpack-specific term that is used internally to manage the bundling process. Webpack composes bundles out of chunks, and there are several types of those.

## Optimizing

* **Minifying**, or minification, is an optimization technique in which code is written in a more compact form without losing meaning. Certain destructive transformations break code if you are not careful.
* **Tree shaking** is the process of dropping unused code based on static analysis. ES2015 module definition allows this process as it's possible to analyze in this particular manner.
* **Hashing** refers to the process of generating a hash that is attached to the asset/bundle path to invalidate it on the client. Example of a hashed bundle name: *app.f6f78b2fd2c38e8200d.js*.

## Output

* **Output** refers to files emitted by webpack. More specifically, webpack emits **bundles** and **assets** based on the output settings.
* **Target** options of webpack allow you to override the default web target. You can use webpack to develop code for specific JavaScript platforms.
