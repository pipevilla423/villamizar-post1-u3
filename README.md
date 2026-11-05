# villamizar-post1-u3
Laboratorio Unidad 3 - Manejo del DEBUG en DOSBox - William Felipe Villamizar Soler - 1152498 - Arquitectura de Computadores - UFPS

### Checkpoint 2 — Volcado de memoria con el comando D

El comando `D` (Dump) de DEBUG permite visualizar el contenido de una región de memoria en formato hexadecimal y ASCII sin modificar sus valores. La primera columna muestra la dirección de memoria en formato segmento:desplazamiento (segmento:offset), indicando dónde comienza cada fila de 16 bytes. Las columnas centrales presentan los valores almacenados en memoria expresados en hexadecimal, organizados en dos grupos de ocho bytes para facilitar su lectura. La última columna muestra la representación de esos bytes como caracteres, utilizando puntos (`.`) cuando no son imprimibles. Al ejecutar `D 200 L40`, se observa el patrón repetitivo `AB CD EF` previamente escrito con el comando `F`, lo que permite verificar que los 64 bytes fueron almacenados correctamente.
