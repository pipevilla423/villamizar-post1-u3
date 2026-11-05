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


### Checkpoint 1 — Tabla de traza del programa de suma

| Instrucción ejecutada | AX | BX | CX | IP siguiente | ZF | CF | SF |
|---|---|---|---|---|---|---|---|
| MOV AX,000A | 000A | 0000 | 0000 | 0103 | 0 | 0 | 0 |
| MOV BX,0005 | 000A | 0005 | 0000 | 0106 | 0 | 0 | 0 |
| MOV CX,0003 | 000A | 0005 | 0003 | 0109 | 0 | 0 | 0 |
| ADD AX,BX | 000F | 0005 | 0003 | 010B | 0 | 0 | 0 |
| ADD AX,CX | 0012 | 0005 | 0003 | 010D | 0 | 0 | 0 |

**Verificación:** El registro AX contiene `0012h`, equivalente a 18 en decimal, después de ejecutar las dos instrucciones ADD.


### Checkpoint 2 — Tabla de traza del programa con bucle LOOP

| Iteración | Instrucción | AX después | CX después | IP siguiente | ¿LOOP salta? |
|---|---|---|---|---|---|
| Inicialización | MOV AX,0000 | 0000 | Valor previo | 0103 | No aplica |
| Inicialización | MOV CX,0004 | 0000 | 0004 | 0106 | No aplica |
| 1 | ADD AX,0002 | 0002 | 0004 | 0109 | No aplica |
| 1 | LOOP 0106 | 0002 | 0003 | 0106 | Sí |
| 2 | ADD AX,0002 | 0004 | 0003 | 0109 | No aplica |
| 2 | LOOP 0106 | 0004 | 0002 | 0106 | Sí |
| 3 | ADD AX,0002 | 0006 | 0002 | 0109 | No aplica |
| 3 | LOOP 0106 | 0006 | 0001 | 0106 | Sí |
| 4 | ADD AX,0002 | 0008 | 0001 | 0109 | No aplica |
| 4 | LOOP 0106 | 0008 | 0000 | 010B | No |

**Análisis:** El programa utiliza el registro CX como contador para controlar cuatro iteraciones del bucle. En cada iteración, la instrucción `ADD AX,0002` incrementa el acumulador AX en dos unidades, mientras que `LOOP 0106` decrementa CX en una unidad y regresa a la dirección `0106` siempre que CX sea diferente de cero.

Durante las tres primeras iteraciones, el salto se realiza porque CX conserva un valor distinto de cero. En la cuarta iteración, CX llega a `0000`, por lo que la instrucción LOOP deja de saltar y continúa hacia `INT 20`.

**Resultado final:** AX = `0008h` (8 decimal) y CX = `0000h`. Se ejecutaron 10 instrucciones antes de alcanzar la instrucción de terminación, demostrando el funcionamiento correcto del bucle LOOP.

