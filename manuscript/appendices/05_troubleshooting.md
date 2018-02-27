# Resolución de problemas Troubleshooting

Usar webpack puede guiar a una variedad de advertensias o errores de tiempo de ejecucuión. Generalmente una parte particular de la construcción falla, por una razón u otra. Un proceso básico puede ser usado para descifrar estos problemas:

1. Pasa el indicador `--display-error-details` a webpack para conseguir un error más exacto para estudiar. Ejemplo: `npm run build -- --display-error-details`.
2. Estudia cuidadosamente el origen del error. A veces, puedes deducir qué está mal por contexto. Si webpack falla en analizar un módulo, es posible que no pase por el cargador que esperas por ejemplo.
3. Trata de entender de donde proviene el error. Viene de tu código, una dependencia o de webpack?
4. Remueve el código hasta que el error desaparezca y añade el código de vuelta hasta que aparezca otra vez. Simplifica lo más que puedas para aislar el problema.
5. Si el código funcionó en otro proyecto, determina qué es diferente. Es posible que las dependencias entre los proyectos varíen, o la confuguración difiere de algún modo. En el peor caso, un paquete en el que confías ha ganado una regresión. Usar un *lockfile* es una buena idea para este motivo.
6. Estudia cuidadosamente los paquetes relacionados. A veces, mirar dentro del paquete *package.json* puede generar una idea. Es posible que el paquete que estás usando no resuelva de la manera que esperas.
7. Busca el error en linea. Tal vez alguien se ha topado con el. [Stack Overflow](https://stackoverflow.com/questions/tagged/webpack) y [the official issue tracker](https://github.com/webpack/webpack/issues) son buenos puntos para empezar.
8. Habilita `stats: "verbose"` para tener más información de webpack. El [official documentation covers more flags](https://webpack.js.org/configuration/stats/).
9. Añade un `console.log` temporal cerca del error para tener más conocimiento del problema. Una opción más pesada es [debug webpack through Chrome Dev Tools](https://medium.com/webpack/webpack-bits-learn-and-debug-webpack-with-chrome-dev-tools-da1c5b19554).
10. [Ask a question at Stack Overflow](https://stackoverflow.com/questions/tagged/webpack) o [use the official Gitter channel](https://gitter.im/webpack/webpack).
11. Si todo falla y estás convencido que hallaste un error, [report an issue at the official issue tracker](https://github.com/webpack/webpack/issues) o en otros lugares si es un problema en una dependencia. Sigue cuidadosamente el 
formato del error, y proporciona un mínimo ejemplo ejecutable mientras ayuda a resolver el problema.

A veces, es más rápido dejar caer el error en un motor de búsqueda y tener una respuesta de esa manera. Además, esta es una buena orden de depuración. Si tu configuración funcionó en el pasado, puedes considerar usar comandos como [git bisect](https://git-scm.com/docs/git-bisect) para descifrar que ha cambiado entre el estado de trabajo conocido y el actual descompuesto.

Aprenderás sobre los errores más comunes a continuación y como lidiar con ellos.

## ERROR en el módulo de Entrada no encontrado

Puedes terminar con este error si haces un punto de camino de entrada en un lugar que no existe. El mensaje de error te dice qué camino webpack falla en encontrarse.

## ERROR ... Módulo no encontrado

Puedes obtener el error de dos maneras. O bien sea rompiendo una definición del cargador para que apunte a un cargador que no existe, o rompiendo un camino de importación by breaking an import path dentro de tu código para que apunte a un módulo que no existe. El mensaje apunta qué arreglar.

## Error en el análisis de Módulo

Aunque webpack puede resolver tus módulos bien, puede igual fallar para construirlos. Este caso puede pasar si estás usando sintaxis que tus cargadores no entienden. Puedes estar perdiendo algo en tu pase de procesamiento.

## Cargador no encontrado

Hay otro error relacionado con el cargador sutil. Sin un paquete coincide con un nombre de cargador que no implemeta la interfaz del cargador, existe, webpack coincide con eso y da un error de tiempo de ejecución que dice que el paquete no es un cargador.

Este error puede producirse por escribir `loader: "eslint"` en lugar de `loader: "eslint-loader"`. Si el cargador no existe en lo absoluto, el error `Module not found` se generará.

## Error en la compilación del módulo: Palabra desconocida

Este error encaja en la misma catergoría. El aálisis del archivo tuvo exito, pero estaba la sintaxis desconocida. Lo más probable es que el problema sea un error de tipeo, pero este error puede ocurrir también cuando Webpack ha seguido una importación y ha encontrado una sintaxis que no entiende. Probablemente esto significa que un cargador falta para ese tipo de archivo en especial.

## Error de sintaxis: Símbolo inesperado

`SyntaxError` es otro error para la misma categoría. Este error es posible si usas la sintaxis ES2015 que no ha sido transpilada junto a UglifyJS. Cuando encuentra una construcción de sintaxis que no reconoce, se generará un error.

## Conclusión

Estos son sólo ejemplos de errores. Ciertos errores ocurren en el lado de webpack, pero el resto viene de los paquetes que usa a través de cargadores y complementos. Simplificando, tu proyecto es un buen paso que hace que sea más fácil donde ocurre el error.

En la mayoría de los casos, los errores son fáciles de resolver si sabes donde buscar, pero en el peor de los casos, te has topado con un error para arreglar la herramienta. En ese caso, deberías proporcionar un reporte de alta calidad para el proyecto y ayudar a resolverlo. 
