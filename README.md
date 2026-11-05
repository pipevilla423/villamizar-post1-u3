# villamizar-post1-u3
Laboratorio Unidad 3 - Manejo del DEBUG en DOSBox - William Felipe Villamizar Soler - 1152498 - Arquitectura de Computadores - UFPS

### Checkpoint 2 — Volcado de memoria con el comando D

El comando `D` (Dump) de DEBUG permite visualizar el contenido de una región de memoria en formato hexadecimal y ASCII sin modificar sus valores. La primera columna muestra la dirección de memoria en formato segmento:desplazamiento (segmento:offset), indicando dónde comienza cada fila de 16 bytes. Las columnas centrales presentan los valores almacenados en memoria expresados en hexadecimal, organizados en dos grupos de ocho bytes para facilitar su lectura. La última columna muestra la representación de esos bytes como caracteres, utilizando puntos (`.`) cuando no son imprimibles. Al ejecutar `D 200 L40`, se observa el patrón repetitivo `AB CD EF` previamente escrito con el comando `F`, lo que permite verificar que los 64 bytes fueron almacenados correctamente.


### Paso 12. Decisión técnica — Verificación no destructiva de una escritura en memoria

Para verificar que la escritura realizada en el Paso 11 se ejecutó correctamente, se determina que el comando **D (Dump)** es la opción más adecuada, debido a que permite visualizar el contenido de la memoria sin modificar los datos almacenados.

El comando `E 300`, cuando se utiliza sin especificar una lista de bytes, inicia un modo interactivo que muestra los valores existentes y permite reemplazarlos. Esto representa un riesgo, ya que una entrada accidental podría alterar la información que se pretende verificar. En cambio, `E 300 78 56` realiza una escritura explícita de los bytes indicados, por lo que tampoco constituye una operación de consulta.

Por su parte, el comando `F` no resulta apropiado porque su función consiste en rellenar un rango de memoria con un patrón determinado, sobrescribiendo los valores existentes. El comando `R` permite consultar los registros del procesador, pero no verifica directamente el contenido almacenado en una dirección de memoria.

Por estas razones, **se selecciona el comando `D 300 L10`**, ya que muestra los 16 bytes a partir de la dirección DS:0300 sin alterarlos. De esta manera, se puede comprobar que los primeros dos bytes contienen `78 56` y los restantes permanecen en `00`, garantizando una verificación no destructiva del resultado esperado.
