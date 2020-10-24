<a href="https://es.cooltext.com"><img src="https://images.cooltext.com/5474898.png" width="863" height="82" alt="LECTURA Y EJERCICIOS ARM32" /></a>
<a href="http://es.cooltext.com" target="_top"><img src="https://cooltext.com/images/ct_pixel.gif" width="80" height="15" alt="Cool Text: Generador de Logotipos y Gráficos." border="0" /></a>

# RESUMEN
# Capitulo 1

## Lectura previa

### Caracteristicas generales de la arquitectura ARM

ARM es una arquitectura RISC (Reduced Instruction Set Computer=Ordenador con Conjunto Reducido de Instrucciones) de 32 bits, salvo la versión del core ARMv8- A que es mixta 32/64 bits (bus de 32 bits con registros de 64 bits). Se trata de una arquitectura licenciable, quiere decir que la empresa desarrolladora ARM Holdings diseña la arquitectura, pero son otras compañías las que fabrican y venden los chips, llevándose ARM Holdings un pequeño porcentaje por la licencia. El chip en concreto que lleva la Raspberry Pi es el BCM2835, se trata de un SoC (System on a Chip=Sistema en un sólo chip) que contiene además de la CPU otros elementos como un núcleo GPU (hardware acelerado OpenGL ES/OpenVG/Open EGL/OpenMAX y decodificación H.264 por hardware) y un núcleo DSP (Digital signal processing=Procesamiento digital de señales) que es un procesador más pequeño y simple que el principal, pero especializado en el procesado y representación de señales analógicas.

### Registros

La arquitectura ARMv6 presenta un conjunto de 17 registros

__Registros Generales.__ 
Su función es el almacenamiento temporal de datos. Son los 13 registros que van R0 hasta R12. 

__Registros Especiales.__ 
Son los últimos 3 registros principales: R13, R14 y R15. Como son de propósito especial, tienen nombres alternativos. 
  __SP/R13.__ Stack Pointer ó Puntero de Pila. Sirve como puntero para almacenar variables locales y registros en llamadas a funciones. 
  __LR/R14.__ Link Register ó Registro de Enlace. Almacena la dirección de retorno cuando una instrucción BL ó BLX ejecuta una llamada a una rutina. 
  __PC/R15.__ Program Counter ó Contador de Programa. Es un registro que indica la posición donde está el procesador en su secuencia de instrucciones. Se incrementa de 4 en 4 cada vez que se ejecuta una instrucción, salvo que ésta provoque un salto.

__Registro CPSR.__
Almacena las banderas condicionales y los bits de control. Los bits de control definen la habilitación de interrupciones normales (I), interrupciones rápidas (F), modo Thumb 1 (T) y el modo de operación de la CPU. Existen hasta 8 modos de operación, pero por ahora desde nuestra aplicación sólo vamos a trabajar en uno de ellos, el Modo Usuario. Los demás son modos privilegiados usados exclusivamente por el sistema operativo.

__Esquema de almacenamiento.__
El procesador es Bi-Endian, quiere decir que es configurable entre Big Endian y Little Endian. Aunque nuestro sistema operativo nos limita a Little Endian. Por tanto la regla que sigue es “el byte menos significativo ocupa la posición más baja”.

### El lenguaje ensamblador

Desarrollar programas en lenguaje ensamblador es un proceso laborioso. El procedimiento es similar al de cualquier lenguaje compilado. Un conjunto de instrucciones y/o datos forman un módulo fuente. Este módulo es la entrada del compilador, que chequea la sintaxis y lo traduce a código máquina formando un módulo objeto. Finalmente, un enlazador (montador ó linker) traduce todas las referencias relativas a direcciones absolutas y termina generando el ejecutable.

Generalmente, y dado que crear programas un poco extensos es laborioso, el ensamblador se utiliza como apoyo a otros lenguajes de alto nivel para 3 tipos de situaciones: 
- Operaciones que se repitan un número elevado de veces. 
- Cuando se requiera una gran velocidad de proceso. 
- Para utilización y aprovechamiento de dispositivos y recursos del sistema.

### El entorno

Los pasos habituales para hacer un programa (en cualquier lenguaje) son los siguientes: lo primero es escribir el programa en el lenguaje fuente mediante un editor de programas. El resultado es un fichero en un lenguaje que puede entender el usuario, pero no la máquina. Para traducirlo a lenguaje máquina hay que utilizar un programa traductor. Éste genera un fichero con la traducción de dicho programa, pero todavía no es un programa ejecutable. Un fichero ejecutable contiene el programa traducido más una serie de códigos que debe tener todo programa que vaya a ser ejecutado en una máquina determinada.

Durante el proceso de creación de un programa se suelen producir errores. Hay dos tipos de errores: los sintácticos o detectables en tiempo de traducción y los errores semánticos o detectables en tiempo de ejecución. Los errores sintácticos son, por ejemplo, escribir mal una instrucción o hacer una operación entre dos tipos de datos incompatibles. Estos errores son detectados por el traductor y se deben solucionar para poder generar un ejecutable.

En el laboratorio utilizaremos el editor nano para crear y editar los módulos fuente de nuestros programas. El traductor (que en el caso de traducir de un lenguaje ensamblador a lenguaje máquina recibe el nombre de ensamblador), el linker y el debugger son respectivamente GNU Assembler (as), GNU Compiler Collection (gcc) y GNU Debbuger (gdb). Todas estas herramientas forman parte de la GNU toolchain que viene instalada por defecto en la mayoría de las distribuciones basadas en Linux, en concreto Raspbian. Para obtener más información sobre estos comandos se puede recurrir a la ayuda del sistema con man as, man gcc y man gdb.

### Aspecto de un programa ensamblador

La principal característica de un módulo fuente en ensamblador es que existe una clara separación entre las instrucciones y los datos. La estructura más general de un módulo fuente es: 

__Sección de datos.__
Viene identificada por la directiva .data. En esta zona se definen todas las variables que utiliza el programa con el objeto de reservar memoria para contener los valores asignados. Hay que tener especial cuidado para que los datos estén alineados en palabras de 4 bytes, sobre todo después de las cadenas. Alinear significa rellenar con ceros el final de un dato para que el siguiente dato comience en una dirección múltiplo de 4 (con los dos bits menos significativos a cero). Los datos son modificables. 

__Sección de código.__
Se indica con la directiva .text, y sólo puede contener código o datos no modificables. Como todas las instrucciones son de 32 bits no hay que tener especial cuidado en que estén alineadas. Si tratamos de escribir en esta zona el ensamblador nos mostrará un mensaje de error

### Datos

Los datos se pueden representar de distintas maneras. Para representar números tenemos 4 bases. La más habitual es en su forma decimal, la cual no lleva ningún delimitador especial. Luego tenemos otra muy útil que es la representación hexadecimal, que indicaremos con el prefijo 0x. Otra interesante es la binaria, que emplea el prefijo 0b antes del número en binario. La cuarta y última base es la octal, que usaremos en raras ocasiones y se especifica con el prefijo 0. Sí, un cero a la izquierda de cualquier valor convierte en octal dicho número.

### Simbolos

Como las etiquetas se pueden ubicar tanto en la sección de datos como en la de código, la versatilidad que nos dan las mismas es enorme. En la zona de datos, las etiquetas pueden representar variables, constantes y cadenas. En la zona de código podemos usar etiquetas de salto, funciones y punteros a zona de datos. 

### Instrucciones

Cada instrucción del as se refiere a una operación que puede realizar el microprocesador. También hay pseudoinstrucciones que son tratadas por el preprocesador como si fueran macros y codifican otras instrucciones, como lsl rn, #x que codifica mov rn, rn, lsl #x, o bien push/pop que se traducen instrucciones stm/ldm más complejas y difíciles de recordar para el programador. Podemos agrupar el conjunto de instrucciones del as, según el tipo de función que realice el microprocesador, en las siguientes categorías:

Instrucciones de transferencia de datos Mueven información entre registros y posiciones de memoria. En la arquitectura ARMv6 no existen puertos ya que la E/S está mapeada en memoria. Pertenecen a este grupo las siguientes instrucciones: mov, ldr, str, ldm, stm, push, pop. 

Instrucciones aritméticas. Realizan operaciones aritméticas sobre números binarios o BCD. Son instrucciones de este grupo add, cmp, adc, sbc, mul.

Instrucciones de manejo de bits. Realizan operaciones de desplazamiento, rotación y lógicas sobre registros o posiciones de memoria. Están en este grupo las instrucciones: and, tst, eor, orr, LSL, LSR, ASR, ROR, RRX.

Instrucciones de transferencia de control. Se utilizan para controlar el flujo de ejecución de las instrucciones del programa. Tales como b, bl, bx, blx y sus variantes condicionales.

## Enunciados de la práctica

### Como empezar

Recuerda que en laboratorio las raspberries no tienen monitor ni teclado, la única conexión con el mundo real es el puerto Ethernet. En el apéndice C se explica otro mecanismo para conectar. Así que antes de nada averigua cuál es la dirección IP de la Raspberry Pi dentro de la red local. Por defecto el usuario es pi y la contraseña raspberry.

### Entero naturales

Recordemos que cuando se representa cualquier dato en memoria, éste tiene un valor explícito (el que tiene como dato guardado en binario) y un valor implícito (el que tiene interpretado como un tipo de dato determinado o como una instrucción). En este apartado queremos que veais la diferencia entre el valor explícito y el valor implícito interpretado como un natural y como un entero.

### Rotaciones y desplazamientos

En este apartado veremos el funcionamiento de las instrucciones de desplazamiento y rotación. Las instrucciones de desplazamiento pueden ser lógicas o aritméticas. Los desplazamientos lógicos desplazan los bit del registro fuente introduciendo ceros (uno o más de uno).

Las instrucciones de rotación también se desplazan, pero el bit que sale del valor se realimenta. No existe ninguna instrucción para rotar hacia la izquierda ROL, ya que puede simularse con la de rotación a la derecha POR que sí existe. 


### Instrucciones de multiplicacion

Las instrucciones de multiplicación admiten muchas posibilidades, debido a que es una operación en la cual el resultado tiene el doble de bits que cada operando.

La instrucción mul realiza una multiplicación truncada, es decir, nos quedamos con los 32 bits inferiores del resultado. Como el signo del resultado es el bit más significativo el cual no obtenemos, esta multiplicación es válida tanto para operandos naturales (sin signo) como para enteros (con signo).

Las dos siguientes multiplicaciones (umull y smull) son largas, por eso la l del final, donde el resultado es de 64 bits. Si los operandos son naturales escogemos la multiplicación sin signo (unsigned) umull. Por el contrario, si tenemos dos enteros como factores hablamos de multiplicación con signo (signed) smull.



# Capitulo 2

## Lectura previa
### Modos de direccionamiento del ARM

En la arquitectura ARM los accesos a memoria se hacen mediante instrucciones específicas ldr y str (luego veremos las variantes ldm, stm y las preprocesadas push y pop). El resto de instrucciones toman operandos desde registros o valores inmediatos, sin excepciones. En este caso la arquitectura nos fuerza a que trabajemos de un modo determinado: primero cargamos los registros desde memoria, luego procesamos el valor de estos registros con el amplio abanico de instrucciones del ARM, para finalmente volcar los resultados desde registros a memoria. Existen otras arquitecturas como la Intel x86, donde las instrucciones de procesado nos permiten leer o escribir directamente de memoria

__Direccionamiento inmediato.__
El operando fuente es una constante, formando parte de la instrucción.

__Direccionamiento inmediato con desplazamiento o rotación.__
Es una variante del anterior en la cual se permiten operaciones intermedias sobre los registros.

Estas instrucciones también se usan implicitamente para la creación de constantes, rotando o desplazando constantes más pequeñas de forma transparente al usuario. Como todas las instrucciones ocupan 32 bits, es técnicamente imposible que podamos cargar en un registro cualquier constante de 32 bits con la instrucción mov

__Direccionamiento a memoria.__
Sin actualizar registro puntero. Es la forma más sencilla y admite 4 variantes. Después del acceso a memoria ningún registro implicado en el cálculo de la dirección se modifica.


__Direccionamiento a memoria__ 
Actualizando registro puntero. En este modo de direccionamiento, el registro que genera la dirección se actualiza con la propia dirección. De esta forma podemos recorrer un array con un sólo registro sin necesidad de hacer el incremento del puntero en una instrucción aparte. Hay dos métodos de actualizar dicho registro, antes de ejecutar la instrucción (preindexado) o después de la misma (postindexado).

### Tipos de datos
__Tipos de datos básicos.__ En la siguiente tabla se recogen los diferentes tipos de datos básicos que podrán aparecer en los ejemplos, así como su tamaño y rango de representación

__Punteros.__ Un puntero siempre ocupa 32 bits y contiene una dirección de memoria. En ensamblador no tienen tanta utilidad como en C, ya que disponemos de registros de sobra y es más costoso acceder a las variables a través de los punteros que directamente.

__Vectores.__ Todos los elementos de un vector se almacenan en un único bloque de memoria a partir de una dirección determinada. Los diferentes elementos se almacenan en posiciones consecutivas, de manera que el elemento i está entre los i-1 e i+1.

__Matrices bidimensionales.__ Una matriz bidimensional de N×M elementos se almacena en un único bloque de memoria. Interpretaremos una matriz de N×M como una matriz con N filas de M elementos cada una. Si cada elemento de la matriz ocupa B bytes, la matriz ocupará un bloque de M ×N ×B bytes .


### Instrucciones de salto

Las instrucciones de salto pueden producir saltos incondicionales (b y bx) o saltos condicionales. Cuando saltamos a una etiqueta empleamos b, mientras que si queremos saltar a un registro lo hacemos con bx. La variante de registro bx la solemos usar como instrucción de retorno de subrutina, raramente tiene otros usos.
La lista completa de condiciones es ésta: 
  EQ (equal, igual). Cuando Z está activo (Z vale 1). 
  NEQ (not equal, igual). Cuando Z está inactivo (Z vale 0). MI (minus, negativo). Cuando N está activo (N vale 1). 
  PL (plus, positivo o cero). Cuando N está inactivo (N vale 0). 
  CS/HS (carry set/higher or same, carry activo/mayor o igual). Cuando C está activo (C vale 1).
  CC/LO (carry clear/lower, carry inactivo/menor). Cuando C está inactivo (C vale 0). VS (overlow set, desbordamiento activo). Cuando V está activo (V vale 1). 
  VC (overlow clear, desbordamiento inactivo). Cuando V está inactivo (V vale 0). 
  GT (greater than, mayor en complemento a dos). Cuando Z está inactivo y N=V (Z vale 0, N vale V). 
  LT (lower than, menor en complemento a dos). Cuando N!=V (N vale not V). 
  GE (greater or equal, mayor o igual en complemento a dos). Cuando N=V (N vale V). 
  LE (lower or equal, menor o igual en complemento a dos). Cuando Z está activo y N!=V (Z vale 1, N vale not V). 
  HI (higher, mayor). Cuando C está activo y Z inactivo (C vale 1, Z vale 0). 
  LS (lower or same, menor o igual). Cuando C está inactivo ó Z activo (C vale 0 ó Z vale 1).

### Estructuras de control de alto nivel

En este punto veremos cómo se traducen a ensamblador las estructuras de control de alto nivel que definen un bucle (for, while, . . . ), así como las condicionales (if-else). Las estructuras for y while se pueden ejecutar un mínimo de 0 iteraciones (si la primera vez no se cumple la condición). La traducción de las estructuras for y while se puede ver en los listados 2.1 y 2.2

### Compilación a ensamblador

Para acabar la teoría veamos cómo trabaja un compilador de C real. Normalmente los compiladores crean código compilado (archivos .o) en un único paso. En
el caso de gcc este proceso se hace en dos fases: en una primera se pasa de C a
ensamblador, y en una segunda de ensambladador a código compilado (código máquina). 

## Enunciados de la practica
### Suma de elementos de un vector

En este primer apartado, estudiaremos un bucle que calcula la suma de todos los elementos de un vector. El vector se denomina vector y tiene 5 elementos de tipo int (entero de 32 bits). Los algoritmos que realizan la suma de sus elementos, tanto en C como en ensamblador, se pueden encontrar en los listados 2.8 y 2.9

El código del listado 2.9 está en el fichero tipos4.s. Compila y monta el programa con el as y el gcc. Ahora ejecuta el algoritmo con el gdb. Recuerda empezar con start. Para ver su funcionamiento, podemos ejecutar un par de iteraciones con si y ver cómo los valores de los registros van cambiando i r r0 r1 r2 r3 (de vez en cuando ejecuta disas para saber por dónde vas). Si ejecutamos un par de iteraciones con si veremos que el hecho de ejecutar instrucción a instrucción resulta poco útil. Para acelerar el proceso, podemos utilizar puntos de parada o breakpoints.




