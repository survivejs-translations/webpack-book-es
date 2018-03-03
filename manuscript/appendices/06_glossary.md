# Glossary

Given webpack comes with specific nomenclature, the main terms and their explanations have been gathered below based on the book part where they are discussed.

## Introduction

* **Static analysis** - When a tool performs static analysis, it examines the code without running it. This is how tools like ESLint or webpack operate. Statically analyzable standards, like ES2015 module definition, enable features like **tree shaking**.
* **Resolving** is the process that happens when webpack encounters a module or a loader. When that happens, it tries to resolve it based on the given resolution rules.

## Developing

* **Entry** refers to a file used by webpack as a starting point for bundling. An application can have multiple entries and depending on configuration, each entry can result in multiple bundles. Entries are defined in webpack's `entry` configuration. Entries are **modules** at the beginning of the dependency graph.
* **Module** is a general term to describe a piece of the application. In webpack, it can refer to JavaScript, a style sheet, an image or something else. **Loaders** allows webpack to support different file types and therefore different types of module. If you point to the same module from multiple places of a code base, webpack will generate a single module in the output. This enables the singleton pattern on module level.
* **Plugins** connect to webpack's event system and can inject functionality into it. They allow webpack to be extended and can be combined with loaders for maximum control. Whereas a loader works on a single file, a plugin has much broader access and is capable of more global control.
* **Hot Module Replacement (HMR)** se refiere a una técnica donde el código ejecutándose en el navegador es parcheado sobre la marcha sin necesidad de refrescar toda la página. Cuando una aplicación contiene un estado complejo, restaurarla puede ser difícil sin HMR o una solución similar.
* **Linting** se relaciona con el proceso en el cual el código es analizado estáticamente por una serie de problemas definidos por los usuarios. Estos problemas variar entre descubrir errores de sintaxis a imponer estilos de código. El Lint de Whilst es por definición limitado en sus capacidades, un linter es invaluable por ayudar con el descubrimiento de errores tempranos e imponiendo la consistencia del código.

## Carga

* **Loader** realiza una transformación que acepta una fuente y regresa una fuente transformada. Puede también saltarse procesamiento y realizar una revisión contra la entrada en su lugar. Mediante la configuración, un cargador se dirige a un subconjunto de módulos, a menudo basados en el tipo de módulo o ubicación. Un cargador sólo actúa en un solo módulo mientras que un plugin puede actuar en varios archivos.
* **Asset** es un término general para los archivos multimedia y archivos fuente de un proyecto que son la materia prima usada por webpack en la construcción de un paquete.

## Construcción

* **Source maps** describe el mapeo entre el código fuente original y el generado, permitiéndole a los navegadores proporcionar una mejor experiencia de depuración. Por ejemplo, ejecutar el código ES2015 mediante Babel genera el completamente nuevo código ES5. Sin un mapa fuente, un desarrollador perdería el enlace desde donde algo ocurrió en el código generado y desde donde ocurrió en el código fuente. Lo mismo es verdad para las hojas de estilo cuando se ejecutan a través de un pre o post-procesador.
* **Bundle** es el resultado del empaquetado. El empaquetado implica procesar el material fuente de la aplicación en un paquete final que está listo para usar. Un empaquetador puede generar más de un paquete.
* **Bundle splitting** ofrece una manera de optimizar una versión, permitiéndole a webpack generar múltiples paquetes para una sola aplicación. Como consecuencia, cada resultado puede ser aislado de los cambios que afectan a otros, reduciendo la cantidad de códigos que necesitan ser republicados y por lo tanto redescargados por el cliente y sacar provecho del caché del navegador.
* **Code splitting** produce más paquetes granulados que ruptura de paquetes. Para usarlo, el desarrollador tiene que habilitarlo mediante llamadas específicas en el código fuente. Usando un `import()` dinámico, es una forma.
* **Chunk** es un término de webpack específico que es usado internamente para manejar el proceso de empaquetamiento. Webpack compone paquetes sin pedazos, y hay varios tipos de esos.

## Optimización

* **Minifying**, o minificación, es una técnica de optimización en la cual el código es hecho en una forma más compacta sin perder significado. Ciertas trasnformaciones destructivas rompen código si no tienes cuidado.
* **Tree shaking** es el proceso de arrojar código base sin usar en el análisis estático. La definición del módulo ES2015 permite este proceso ya que es posible analizarlos de esta manera particular.
* **Hashing** se refiere al proceso de generar un hash que es anexado a la ruta activo/paquete para invalidarlo en el cliente. Ejemplo de un nombre empaquetado de hash: *app.f6f78b2fd2c38e8200d.js*.

## Salida

* **Output** se refiere a archivos emitidos por webpack. Específicamente, webpack emite **bundles** y **assets** basados en la configuración de salida.
* **Target** opción de webpack que te permite sobreescribir el objetivo por defecto de la web. Puedes usar webpack para desarrollar códigos para plataformas de JavaScript específicas.
