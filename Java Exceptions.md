# Introducción

En Java existen dos tipos genéricos de Excepciones. Las comprobadas y las no comprobadas. Las comprobadas son aquellas que se
comprueban en tiempo de compilación por lo que el código no completará la compilación en caso de producirse una excepción. También
son aquellas marcadas por la IDE mientras se escribe el código.
Las no comprobadas, en cambio, son aquellas que tan solo pueden comprobarse en tiempo de ejecución y que saltarán cuando durante
este se produzca algún error (RunTimeException).
Podemos controlarlas, reaccionar a ellas y propagarlas mediante el uso de bloques try and catch. Cuando se propaga lo que se hace es
enviarla al método generador de la excepción, el padre, para que sea él quién lo gestione.

# Tips
## Evitar errores debidos a NullPointerException

El `NullPointerException` es una excepción dada en tiempo de ejecución cuando accedemos a una variable que no apunta a nada y no está
inicializada o apunta a null. Buenas prácticas para evitarlo.
Implementar métodos init() que incialicen todas las variables necesarias.
Poner siempre primero la referencia que sabemos que no será nula en una comparación con un elemento que puede ser nulo.
Asegurarse de que los objetos sobre los cuales llamamos a métodos ( por ejemplo text.length() ) no sean nulos
