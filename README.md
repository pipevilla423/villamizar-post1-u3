# villamizar-post1-u3
Laboratorio Unidad 3 - Manejo del DEBUG en DOSBox - William Felipe Villamizar Soler - 1152498 - Arquitectura de Computadores - UFPS

### Checkpoint 2 — Volcado de memoria con el comando D

El comando `D` (Dump) de DEBUG permite visualizar el contenido de una región de memoria en formato hexadecimal y ASCII sin modificar sus valores. La primera columna muestra la dirección de memoria en formato segmento:desplazamiento (segmento:offset), indicando dónde comienza cada fila de 16 bytes. Las columnas centrales presentan los valores almacenados en memoria expresados en hexadecimal, organizados en dos grupos de ocho bytes para facilitar su lectura. La última columna muestra la representación de esos bytes como caracteres, utilizando puntos (`.`) cuando no son imprimibles. Al ejecutar `D 200 L40`, se observa el patrón repetitivo `AB CD EF` previamente escrito con el comando `F`, lo que permite verificar que los 64 bytes fueron almacenados correctamente.


### Paso 12. Decisión técnica — Verificación no destructiva de una escritura en memoria

Para verificar que la escritura realizada en el Paso 11 se ejecutó correctamente, se determina que el comando **D (Dump)** es la opción más adecuada, debido a que permite visualizar el contenido de la memoria sin modificar los datos almacenados.

El comando `E 300`, cuando se utiliza sin especificar una lista de bytes, inicia un modo interactivo que muestra los valores existentes y permite reemplazarlos. Esto representa un riesgo, ya que una entrada accidental podría alterar la información que se pretende verificar. En cambio, `E 300 78 56` realiza una escritura explícita de los bytes indicados, por lo que tampoco constituye una operación de consulta.

Por su parte, el comando `F` no resulta apropiado porque su función consiste en rellenar un rango de memoria con un patrón determinado, sobrescribiendo los valores existentes. El comando `R` permite consultar los registros del procesador, pero no verifica directamente el contenido almacenado en una dirección de memoria.

Por estas razones, **se selecciona el comando `D 300 L10`**, ya que muestra los 16 bytes a partir de la dirección DS:0300 sin alterarlos. De esta manera, se puede comprobar que los primeros dos bytes contienen `78 56` y los restantes permanecen en `00`, garantizando una verificación no destructiva del resultado esperado.



### Paso 14. Decisión técnica — Direccionamiento inmediato vs. directo a memoria

Al comparar las instrucciones `MOV AX,0005` y `MOV AX,[0300]`, se determina que el direccionamiento inmediato es más eficiente cuando se trabaja con valores constantes, mientras que el direccionamiento directo a memoria resulta más adecuado para consultar datos que pueden cambiar durante la ejecución del programa.

La instrucción `MOV AX,0005`, codificada como `B8 05 00`, contiene el valor inmediato `0005` dentro de sus tres bytes. Por esta razón, el procesador obtiene el operando directamente del flujo de instrucciones, sin necesidad de realizar una lectura adicional del dato en memoria.

En cambio, `MOV AX,[0300]`, codificada como `A1 00 03`, contiene una dirección de memoria en lugar del valor que debe cargarse. Por lo tanto, el procesador debe calcular la dirección física utilizando el registro `DS` y el desplazamiento `0300`, realizando un acceso adicional al bus de memoria para recuperar el dato almacenado.

**Se selecciona el direccionamiento directo a memoria** cuando se necesita consultar información que puede cambiar durante la ejecución, como el valor `5678h` escrito previamente mediante `E 300 78 56`. Este mecanismo permite recuperar el valor actualizado sin modificar la instrucción original.

Finalmente, ambas codificaciones pueden verificarse sin ejecutar ninguna instrucción mediante el comando `U` (Unassemble). Al ejecutar `U 100 109`, se observa la codificación `B8 05 00`, correspondiente a `MOV AX,0005`. Por otro lado, `U 320 324` muestra `A1 00 03`, correspondiente a `MOV AX,[0300]`.

En conclusión, aunque ambas instrucciones ocupan tres bytes, el direccionamiento inmediato evita una lectura adicional del operando, mientras que el direccionamiento directo a memoria permite trabajar con valores variables almacenados en memoria.

