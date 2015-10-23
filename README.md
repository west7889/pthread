# Principles of Parallel Programming - Chapter One
##Wilson Esteban Velez Morales
## Threads in C


Lo programas que se encuentran en este repositorio hacen parte de la actividad de hilos de la clase Fundamentos de Sistemas Distribuidos, los cuales fueron ejecutados y modificados para enteder el comportamiento de los programas que ejecutan hilos.

A continuación se plantean algunas preguntas a las cuales se les da respuesta observando el comportamiento de cada programa:

##A gentle introduction
The first example is thread_101_1.c. This program exhibits how interact with pthread methods such aspthread_create and pthread_join.
In order to compile the program run the following command gcc -pthread thread_101_1.c -o thread_101_1

##Assignment
Remove the instructions related to pthread_join and explain:
What is the program's behavior

R/ El programa realiza la invocación a la función que permite incrementar X, pero en la salida del programa el valor no se ha modificado, mientras que el de Y si se ha incrementado a cien (100).
If it is unexpected, what did you think that happen?
Lo que está ocurriendo es que no se realiza la respectiva sincronización con el hilo que está incrementando el valor de X, antes de finalizar el programa, por lo que la respuesta que se imprime no tiene el valor de X actualizado.
Try the program several times.
All the executions were equal?
Se pudo observar que la respuesta dependen de la velocidad de la máquina donde se realiza la ejecución del programa y el modo como maneja los hilos, pues al realizar muchas peticiones de forma simultanea se obtienen dos respuesta diferentes y una de esas es respuestas es que el valor de X si se ha incrementado a cien (100) al igual que el valor de Y.
Another example of using threads
In chapter one of the book "Principles of Parallel Programming", a trivial example using threads is presented. The problem is to count the number of times that the number "3" is present in a given array.
Example if the input array is [10, 3, 15, 4, 10, 3, 2, -1] the program should return 2.
In this directory you find different C programs to present the code that should help you on understanding the section "Parallelism Using Multiple Instruction Streams" in the aforementioned chapter. As follows a description of every program is given.
Important Note
To compile all programs in this directory run
make clean && make all
Note: It is very important to read chapter one of "Principles of Parallel Programming" before to continue with this reading.
Note: These programs were run in a computer having 8 GB of RAM

##3s-00.c
This program presents a sequential solution of the problem stated in chapter one. The code has two principal parts:
Initialize and populate a large vector with random numbers.
Count in the initialized vector the number of 3s in it.

###Assignment
Add the proper code sentences that allow you to determine how much time the program spends during:
Vector initialization.

Se reduce el valor en la variable global VECTOR_SIZE, pues con el valor inicial de 100000000000 mi máquina se colgó en memoria (máquina de 4 GB de memoria), por lo que reduje el valor a 100000000 obteniendo una rápida respuesta.
Counting the number of 3s in a given vector.
Los resultados para la primera ejecución son: para la variable count que es donde se almacena la información de los 3s que se están encontrando en el arreglo es 5001928 y para double_count es 5001928, esta variable permite controlar el resultado, pues en ella se almacena el numero de 3s que se ingresan en el vector al momento de inicializarlo, el tiempo de ejecución fue de  730.937012 . Para una siguiente ejecución es count es 5000688 y double_count es 5000688, estos valores diferentes entre una ejecución y otra se dan porque en la inicialización del vector, los valores que se registran se generan de forma aleatoria, el tiempo de ejecución fue de 731.576965. 

##3s-01.c
This program uses threads in order to divide the task of counting 3s in a given vector amongst different "working units", a.k.a. threads. Read and understand what this program does.

###Assignment
Include the code sentences used to estimate how much time takes to each thread to count the number of 3s in a given array.
Answer the following questions
How many elements the large vector has?

Los elementos del vector son los establecidos en la variable global VECTOR_SIZE, que para efectos de esta actividad se estableció en 100000000.
The program is correct? What is wrong with it? What value the program gets and what is the expected value?
El programa no es correcto, pues la información que retorna en la variable count no corresponde al generado en la variable double_count -donde se registra el numero de 3s que se ingresan en el vector al momento de inicializarlo-, esto se debe a que no se está sincronizando la variable count en cada hilo; es decir, que la sumatoria generada en cada hilo es propia del hilo y no se comparte con los demás.
El valor esperado es 5000171 y el obtenido fue 1133, el tiempo de ejecución fue de  1.277000 .

##3s-02.c
This version of the program is more accurate but it is not completely correct.

###Assignment
Include the code sentences used to estimate how much time takes to each thread to count the number of 3s in a given array.

Se observa que la ejecución de los hilos tarda un tiempo similar, pero inferior al tiempo total del programa, estos se debe a que cada hilo se ejecuta en paralelo.
Se ejecutó el programa para 8 hilos, los tiempos de cada hilo fueron:
Thread [1] Elapsed time 984.119019
Thread [0] Elapsed time 1233.363037
Thread [3] Elapsed time 1491.271973 
Thread [2] Elapsed time 1602.638062
Thread [6] Elapsed time 1615.231079
Thread [4] Elapsed time 1613.466064 
Thread [5] Elapsed time 1672.175049
Thread [7] Elapsed time 1641.837036

What is wrong with this code this time?

El error que se presenta es por el acceso que realizan todos los hilos a la variable compartida count sin ningún control, por lo que cualquier hilo que se esté modificará el valor de la variable en cualquier momento y de esta manera se altera el conteo que se realiza en los demás hilos.
El valor esperado es 5000761  y el obtenido fue 1036477 , el tiempo de ejecución fue de 1679.347046.

##3s-03.c
This time the program is doing what we expect it has to do.

###Assignment
Identify why this program is now doing right.

Se evidencia que se corrige el problema del código anterior (3s-02.c) estableciendo exclusión mutua para el acceso al segmento de código que realiza el conteo y actualiza el valor de la variable count.
Include the code sentences used to estimate how much time takes to each thread to count the number of 3s in a segment of the full array.
Se ejecutó el programa con 8 hilos, los tiempos de cada hilo fueron:

Thread [0] Elapsed time 89.183998
Thread [2] Elapsed time 181.450989
Thread [4] Elapsed time 277.765015
Thread [5] Elapsed time 370.915009
Thread [6] Elapsed time 467.828003
Thread [7] Elapsed time 565.072021
Thread [1] Elapsed time 654.521973
Thread [3] Elapsed time 720.934998

How much time took to obtain the total count of 3s in the whole array.

El valor esperado es 5002119  y el obtenido fue 5002119, el tiempo de ejecución fue de 723.299011, donde se observa una reducción de más del 40% del tiempo obtenido con el programa anterior y similar al tiempo obtenido por el programa secuencial.

##3s-04.c
The program has a little difference with the previous program.

###Assignment
What is the difference between 3s-03.c and 3s-04.c?

Se diferencian en que el programa  3s-03.c realiza la exclusión mutua en el segmento de código que calcula el valor total de la variable count para el hilo, es decir que no se libera hasta que se evalué el arreglo por completo, mientras que el programa 3s-04.c realiza la exclusión unicamente en el incremento de la variable count, liberando en ese momento la variable.

Compare elapsed time per thread during the counting process and the total time that all threads took for counting the number of 3s in the whole array. Run every program (3s-03 and 3s-04) three times and compute the average time per program. Present your results and explain them.	

Estos datos fueron obtenidos al ejecutar el programa con 8 hilos.

Programa-Ejecución-Tiempo

3s-03-1-717.578003

3s-03-2-727.191956

3s-03-3-709.753967

Promedio 718,174642

Programa-Ejecución-Tiempo

3s-04-1-10695.166992

3s-04-2-11139.542969

3s-04-3-11294.889648 

Promedio 10917,3549805

El tiempo de ejecución del promedio del programa 3s-04 es  más de 15 veces superior al tiempo del programa 3s-03.


Esta diferencia de tiempo ocurre por que se incrementó el numero de invocaciones a los métodos pthread_mutex_lock(&mutex) y pthread_mutex_unlock(&mutex) encargados de realizar la exclusión mutua, pues se realiza el llamado a ellos tantas veces como posiciones tenga el arreglo -para esta actividad se define el tamaño del vector en 100000000-, lo cual toma más tiempo que esperar que cada hilo finalice la evaluación de la porción del arreglo que le corresponda, este incremento de tiempo se da porque se está bloqueando el acceso a una variable global para todos los hilos y se debe esperar en cada  bloqueo hasta que sea liberada para continuar la ejecución del programa. Este comportamiento nos indica que implementar exclusión mutua es costoso si no se controla el numero de bloqueos que se realizarán, pues estos bloqueos serán tiempos en donde el programa se detiene en cada uno de los hilos hasta que se encuentre disponible la variable global para poder realizar la actualización de su valor.

##3s-05.c
This time this solution is correct and exhibits a better performance when it is compared with the previous programs.

###Assignment
Where the success of this program resides?

El éxito de esta implementacion es en tener una variable privada para cada hilo, sobre la cual se realizará el conteo, y sólo al final se suma el valor obtenido con la variable global count, por lo que es sólo en este momento donde se realiza la exclusión mutua.

Compare this program execution and compare its performance with previous instances and write your observations.
Estos datos fueron obtenidos al ejecutar el programa con 8 hilos.
Programa-Ejecución-Tiempo

3s-05-1-1542.734009 

3s-05-2-1528.167969 

3s-05-3-1532.466064

Promedio 1532,466064

El tiempo de ejecución mejora con respecto al programa 3s-04, pues el manejo de la exclusión mutua sólo se tiene al realizar la modificación de la variable global count, realizándose en cada hilo de forma independiente el conteo. Sin embargo no se ve una mejoría en el desempeño del programa con respecto  la solución 3s-03, por lo que al revisar el documento ParallelProgramming-Chp01.pdf, encontramos que existe un factor que interfiere con el desempeño del programa y es el protocolo de coherencia de cache que utilizan los procesadores, en donde si dos o mas procesadores se encuentran modificando reiteradamente el mismo dato, se limitará repetidamente los cachés de cada procesador, resultando una operación muy costosa.
Este fenómeno es conocido como false sharing, en el cual lógicamente distintos datos comparten una linea de cache física, lo que ocasiona que una modificación en una parte de la linea de cache sea equivalente a la modificación de la linea entera.

##Final assignment
Implement the Fourth Solution: Try 4 found in page 20 and compare the performance of your implementation with previous programs.

Para este caso modifiqué el codigo del ejemplo anterior y lo nombré como 3s-06.c

Esta solución plantea un escenario en donde se elimina el fenómeno false sharing, controlando la linea de cache, haciendo que cada contador resida en una linea de cache privada, para el ejemplo se establece 64 bytes de tamaño, el cual corresponderá a la linea de cache con que cuenta el procesador físicamente, este valor deberá ser ajustado dependiendo de la arquitectura que tenga la máquina donde se ejecute el programa.

Adicionalmente se deben tener en cuenta el numero de procesadores que tiene la máquina, pues si se establece un numero de hilos superior a ese valor, no se percibirá una mejoría en el tiempo de procesamiento con respecto al tiempo de ejecución que se obtiene cuando el número de hilos equivale al número de procesadores. 

En la realización de esta actividad, encontré que la ejecución del programa para un hilo, se comporta de  forma similar a la ejecución secuencial del primer ejemplo, sin embargo al incrementar los hilos no se nota una mejoría en la su ejecución, esto puede estar estrechamente relacionado con la forma como se implementan los multihilos en el procesador.

A continuación se muestran los tiempos de procesamiento de cada hilo para la ejecución con uno, dos, cuatro y ocho hilos.

1 Hilo.

Thread [0] Elapsed time 702.690002

El tiempo final de esta ejecución fue de 702.991028 
2 Hilos.

Thread [0] Elapsed time 728.557983
Thread [1] Elapsed time 730.556030 

El tiempo final de esta ejecución fue de 730.902039 
4 Hilos.

Thread [0] Elapsed time 726.953979
Thread [3] Elapsed time 721.256958
Thread [1] Elapsed time 801.232971
Thread [2] Elapsed time 827.144043

El tiempo final de esta ejecución fue de  827.795044
8 Hilos.

Thread [1] Elapsed time 343.239014
Thread [4] Elapsed time 601.558044
Thread [0] Elapsed time 742.717957
Thread [7] Elapsed time 720.023987
Thread [6] Elapsed time 752.419006
Thread [2] Elapsed time 804.431030
Thread [5] Elapsed time 786.660034
Thread [3] Elapsed time 807.929993

El tiempo final de esta ejecución fue de 820.671997

Con estos datos podemos observar que en la ejecución del programa con un sólo hilo tiene un tiempo de ejecución similar al que tomó el programa secuencial (3s-00.c), al ir incrementando el numero de hilos, no se observa una disminución en la ejecución de cada hilo, es decir que al incrementar los hilos se empieza a observar que cada uno tarda un tiempo similar a la ejecución secuencial, aún cuando el numero de registros que valida es menor.

Las siguientes son cifras obtenidas estableciendo un tamaño del vector de 100000000.

Estos son los datos obtenidos al ejecutarlo con 8 hilos y una linea de caché de 64 bytes.
Programa-Ejecución-Tiempo

3s-06-1-882.022034 

3s-06-2-857.138000

3s-06-3-862.338013

Promedio 859,7380065

Estos son los datos obtenidos al ejecutar el programa con 1, 2, 4 y 8 hilos y una linea de cache de 64 bytes.

Programa-Hilos-Tiempo

3s-06-1-701.317017 

3s-06-2-718.994995 

3s-06-4-996.760010

3s-06-8-986.011047 

Estos son los datos obtenidos al ejecutar el programa con 1, 2, 4 y 8 hilos y una linea de cache de 128 bytes (padding[124]).

Programa-Hilos-Tiempo

3s-06-1-691.874023

3s-06-2-738.915955

3s-06-4-999.347961 

3s-06-8-1014.145996
