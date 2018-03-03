# Glosario

Dado que webpack viene con una nomenclatura específica, los términos principales y sus explicaciones han sido agrupados abajo basados en la parte del libro donde son discutidos.

## Introducción

* **Static analysis** - Cuando una herramienta realiza un análisis estático, examina el código sin ejecutarlo. Así es cómo herramientas como ESLint o webpack operan. Los estándares estadísticamente analizables, como la definición del módulo ES2015, activan características como **tree shaking**.
* **Resolving** es el procesoo que sucede cuando webpack encuentra un módulo o un cargador. Cuando esto sucede, intenta resolverlo basado en las reglas de resolución dadas.

## Desarrollo


* **Entry** refers to a file used by webpack as a starting point for bundling. An application can have multiple entries and depending on configuration, each entry can result in multiple bundles. Entries are defined in webpack's `entry` configuration. Entries are **modules** at the beginning of the dependency graph
* **Module** is a general term to describe a piece of the application. In webpack, it can refer to JavaScript, a style sheet, an image or something else. **Loaders** allows webpack to support different file types and therefore different types of module. If you point to the same module from multiple places of a code base, webpack will generate a single module in the output. This enables the singleton pattern on module level.
* **Plugins** connect to webpack's event system and can inject functionality into it. They allow webpack to be extended and can be combined with loaders for maximum control. Whereas a loader works on a single file, a plugin has much broader access and is capable of more global control.
* **Linting** relates to the process in which code is statically analyzed for a series of user-defined issues. These issues can range from discovering syntax errors to enforcing code-style. Whilst linting is by definition limited in its capabilities, a linter is invaluable for helping with early error discovery and enforcing code consistency.
=======
* **Entry** se refiere a un archivo usado por webpack como un punto de partida para empaquetar. Una aplicación puede tener múltiples entradas dependiendo de la configuración, cada entrada puede resultar en múltiples paquetes. Las entradas son definidas en la configuración `entry` de webpack. Las entradas son **modules** al inicio del gráfico de dependencia.
* **Module** es un término general para describir una parte de la aplicación. En webpack, se puede referir a JavaScript, una hoja de estilo, una imagen o algo más. Los **Loaders** le permiten a webpack soportar diferentes tipos de archivo y por lo tanto diferentes tipos de módulos. Si apuntas al mismo módulo desde diferentes partes de un código base, webpack generará un solo módulo en la salida. Esto activa el patrón singleton a nivel modular.
* **Plugins** conecta al sistema de eventos de webpack y puede introducir funcionalidades en él. Ellos le permiten a webpack extenderse y combinarse con cargadores para un control máximo. Mientras que un cargador trabaja en un solo archivo, un plugin tiene un acceso más amplio y es capaz de un control más global.
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
