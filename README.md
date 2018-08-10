# OptimizarMemoriaPHP
***Consejos y ejemplos sobre optimizar memoria y procesos en codigo php***

## Tabla de contenidos
- [Usar HTML ante PHP](#usar-html-ante-php).
- [Variables](#variables).
- [Uso de unset y el GC de PHP](#uso-de-unset-y-el-gc-de-php).
- [Bucles](#bucles).
- [Inclusiones](#inclusiones).
- [Supresion de errores](#supresion-de-errores).
- [Random](#random).
- [Uso de comillas](#uso-de-comillas).
- [Comprobar existencia de variables](#comprobar-existencia-de-variables).
- [Operadores eficientes](#operadores-eficientes).
- [Comprobar uso de memoria y proceso](#comprobar-uso-de-memoria-y-proceso).
- [Comparaciones](#comparaciones).
- [Incrementos](#incrementos).
- [Impresion de textos](#impresion-de-textos).
- [Evitar concatenaciones](#evitar-concatenaciones).
- [Substituir texto](#substituir-texto).
- [Ordenar resultados de BD](#ordenar-resultados-de-bd).
- [Usar el cache alternativo de PHP (APC)](#usar-el-cache-alternativo-de-php).
- [Usar un MPM en apache](#usar-mpm-en-apache).

### Usar HTML ante PHP

Si necesitas escribir HTML hazlo directamente sin utilizar echo o print . Usa PHP para imprimir información sólo cuando sea necesario. 

### Variables

Favorece siempre que puedas el uso de variables estáticas. No utilices variables que no necesites (ocupan memoria). Evita las variables globales en la medida de lo posible. Usa constantes. 
- Este ejemplo es mejor
```

<?php
function test()
{
    static $a = 0;
    echo $a;
    $a++;
}
?>

```
- Que este otro
```

<?php
function test()
{
    $a = 0;
    echo $a;
    $a++;
}
?>

```

### Uso de unset y el GC de PHP

Utiliza la función unset para destruir variables y liberar memoria, sobre todo con arrays o variables extensas. PHP usa un Garbage Collector, pero en mitad de un script puedes usarlo para maximizar la memoria disponible (útil en servidores limitados).
- unset

```

unset($variable); // elimina una variable de la memoria principal

unset($lista[$i]); // elimina el elemento $i de la lista

unset($lista); // elimina toda la lista

unset($GLOBALS['variable']); // elimina una variable global

unset($var1,$var2,$var3); // elimina varias variables

```

## Gargbage Collector (GC)

Otra buena idea es activar el GC de PHP, este ya viene activado por defecto pero hay formas de llamarlo dentro de un script.

```

gc_enable(); // activa el GC de php

gc_collect_cycles(); // fuerza la recoleccion de ciclos

```

### Bucles

Revisa bien los bucles en tus programas, si no es necesario un bucle, evitalo. Si puedes ahorrarte ciclos, hazlo. Comprueba la condición de parada y nunca uses funciones en ella (vuelca en una variable antes del bucle). En términos de velocidad un do..while es más rápido que un while, que a su vez este es más rápido que un for.

```
// Top de rapidez de bucles

// #1  (El mas rapido)

foreach($aHash as $val){
..
..
..
}


// #2
do{
...
...
...
}while(true);

// #3 
while(true){
..
..
..
}

// #4
for ($i = 1; $i <= 10; $i++){
..
..
..
}


```
- Depende mucho del ambito donde se use sera mas conveniente uno u otro

### Inclusiones

Organiza bien tu código y evita en lo posible el uso de funciones como include_once() y require_once(). Estas funciones son muy utiles para comprobar si un script ya ha sido procesado, pero son muy costosas. En su lugar utiliza include() y require().

```

// Estas son mejor y mas rapidas
include();
require();

// Que estas
include_once();
require_once();

```

### Supresion de errores

Al colocar una @ antes de una función evitamos que se muestre un posible mensaje de error. Muy útil, pero muy costoso. Es preferible utilizar un funcion() or ..

### Random

Si queremos generar valores aleatorios con la función rand(), es recomendable utilizar la familia de funciones mt_rand(). Esta función utiliza un algoritmo de Mersenne Twister mucho más eficiente y rápido. 
```
mt_rand(); // este es mejor

rand(); // Es util pero no es la mejor opcion
```

### Uso de comillas

 Las comillas simples interpretan literales, sin embargo, las comillas dobles además interpolan el valor de variables. Da siempre preferencia a las comillas simples y nunca escribas símbolos de dolar sin escapar ( $ ) en comillas dobles, ralentiza mucho la ejecución. 
 ```
 
 'soy una literal $' //esta es la mejor opcion si no se agregan variables dentro
 
 "Puedo ser o no una literal $variable" //Esta solo es buena si se desea leer tambien una variable ($)
 
 ```
 
### Comprobar existencia de variables
 
Siempre se debería utilizar una función para comprobar si existe una variable. Entre las funciones isset(), empty() y is_array(), la primera es la más rápida y eficiente. 
 ```
 
 if (empty($cadena)) echo “vacía”; // Más rápida
 if (strlen($cadena) == 0) echo “vacía”; // Practica comun pero mas lenta
 
 ```
 
 ### Operadores eficientes
 
En las comparaciones, la diferencia del operador === con el operador == es que este último hace una comprobación de tipos de variables antes. Si estás seguro de que son del mismo tipo, utiliza el primero. 
 
 ### Comprobar uso de memoria y proceso

Utiliza memory_get_usage() y microtime() para comprobar la velocidad y la memoria que consume tu código y poder optimizarlo mejor.
 ```
 memory_get_usage(); // Retorno la cantidad de memoria asignada a PHP
 microtime(); // Devuelve el tiempo actual del servidor util para medir la velocidad
 
 ```

### Comparaciones

Los if / else son siempre más rápidos que los switch / case.
Las siguientes funciones son alias de las de su derecha. Utilizar la función de la izquierda es mucho más lento que usar la de la derecha:
   - lentos | rapidos
   - chop -> rtrim
   - close -> closedir
   - die -> exit
   - dir -> getdir
   - diskfreespace -> disk_free_space
   - fputs -> fwrite
   - ini_alter -> ini_set
   - is_writeable -> is_writable
   - join -> implode
   - pos -> current
   - rewind -> rewinddir
   - strchr -> strstr
   - sizeof -> count
   
### Incrementos

Cuando incrementamos una variable del modo $i++ es más lento que si lo hacemos ++$i . La diferencia es que la primera forma primero usa su valor y luego lo incrementa, en cambio, la segunda primero la incrementa y luego la usa. La segunda forma es más rápida, puesto que utiliza 3 opcodes, en lugar de los 4 opcodes de la primera. Usarla cuando sea posible. 

```

++$i; // Mas rapido
$i++; // mas lento

```

### Impresion de textos

La función echo es más rápida que la función print, además de otras diferencias. 

```

echo "Impresion de texto"; // Esta es mas rapida

print("Impresion de texto"); // Esta es mas lenta y retorna valor

```
 ### Evitar concatenaciones
 
 Es mejor imprimir usando varios echo que concatenar texto y generar la salida
 
```

echo “Bienvenido”; echo $usuario; // Más rápido
echo “Bienvenido”.$usuario; // Este es mas lento

```

### Substituir texto

La manera mas rapida de substituir texto es con strtr y no con str_replace o preg_replace
```

$salida = strtr("abd","d","c"); // Más rápida
$salida = str_replace("d","c","abd");
$salida = preg_replace("/d/","c","abd");

```


### Ordenar resultados de BD

Cuando se termine de hacer operaciones pesadas con una base de datos del tipo SQL es mejor ordenar los resultados haciendo uso de la funcion usort() que por medio del gestor SQL. 
 
### Usar el cache alternativo de PHP

El APC, o caché alternativo de PHP (por sus siglas en inglés de Alternative PHP Cache), es un código de operación de caché libre y abierto para PHP. Su objetivo es el de proporcionar un marco robusto, libre y abierto para optimizar código de PHP intermedio mediante el almacenamiento en caché. Hay un manual completo sobre como se puede usar este recurso valioso [aqui](https://secure.php.net/manual/es/book.apc.php)

### Usar un MPM distinto al prefork en apache

Se puede configurar un MPM en el servidor para tener un manejo de hilos(worker, event) en vez de procesos (prefork). Es conveniente crear hilos ya que los procesos se les asigna una cantidad mayor de memoria cada que se crea uno y los hilos comparten memoria consiguiendo asi un mayor ahorro y rendimiento en servicions con varios usuarios conectados de manera concurrente.

#### MPM prefork

Este es el mas estable y el que viene por defecto en los servidores apaches, hace uso de procesos y es facil de configurar la desventaja es que ocupa mas recursos. No requiere ninguna configuracion previa en el PHP para poder correr con este modo.

#### MPM Worker

Este modo pretende mejorar el rendimiento de prefork. hace uso de procesos y de hilos a la vez por lo que cada proceso genera un numero definido de hilos hijos que pueden manejar cada peticion de una manera mas eficiente. Las desventajas de este modo es que se requiere compilar PHP con la bandera ``--enable-maintainer-zts`` o configurarlo con php-fpm otra desventaja es que su configuracion es mas complicada y su baja tolerancia a fallos.

#### MPM Event

Este modo es una mejora del worker por lo que su instalacion es similar solo que este solo admite ser configurado bajo php-fpm.

Para configurarlos se necesita saber que modo se tiene activo para esto se corre el comando ``httpd -V | grep MPM`` que mostrara el nombre de la configuracion activa. El siguiente paso es instalar php-fpm ``yum install php-fpm``
Para que el servidor apache lo reconozca hay que agregar esta configuracion en ``/etc/httpd/conf.d/php-fpm.conf`` si no existe el archivo hay que crearlo.
Dentro de php-fpm.conf se agregan las siguientes lineas.
```
Action fcgi-php-fpm /fcgi-php-fpm virtual
Alias /fcgi-php-fpm /fcgi-php-fpm
```
Una vez que tenemos esto se procede a modificar ``etc/httpd/conf.modules.d/00-mpm.conf``. solo se debe descomentar la linea donde aparece el modulo MPM que deseas tener activo. Ojo solo se puede tener uno activo a la vez.

```
# Select the MPM module which should be used by uncommenting exactly
# one of the following LoadModule lines:

# prefork MPM: Implements a non-threaded, pre-forking web server
# See: http://httpd.apache.org/docs/2.4/mod/prefork.html
#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so

# worker MPM: Multi-Processing Module implementing a hybrid
# multi-threaded multi-process web server
# See: http://httpd.apache.org/docs/2.4/mod/worker.html
#
LoadModule mpm_worker_module modules/mod_mpm_worker.so

# event MPM: A variant of the worker MPM with the goal of consuming
# threads only for connections with active processing
# See: http://httpd.apache.org/docs/2.4/mod/event.html
#
#LoadModule mpm_event_module modules/mod_mpm_event.so
```

Ahora se tiene que crear el archivo de configuracion del mpm donde se decidira cuantos hilos se van a crear con cada peticion entre otras cosas. En el caso de worker seria ``/etc/httpd/conf.modules.d/10-worker.conf`` y dentro de el se crea algo asi.

```
<IfModule mpm_worker_module>
    ServerLimit              16
    StartServers              4
    MinSpareThreads          64
    MaxSpareThreads         256
    ThreadsPerChild          64
    MaxClients              512
    MaxRequestsPerChild    1024
</IfModule>
```
Se puede consultar en la documentacion oficial de apache para saber que hace cada configuracion.
Tambien se puede configurar la configuracion del php-fpm donde se definen los hilos que va a manejar, el archivo se encuentra en 
``/etc/php-fpm.d/www.conf ``.
