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
 
 "Puedo ser o no una literal $variable" //Esta solo es buena si se agrega una varibable ($) pero ocupa proceso
 
 ```
 
### Comprobar existencia de variables
 
Siempre se debería utilizar una función para comprobar si existe una variable. Entre las funciones isset(), empty() y is_array(), la primera es la más rápida y eficiente. 
 ```
 
 if (empty($cadena)) echo “vacía”; // Más rápida
 if (strlen($cadena) == 0) echo “vacía”; // Practica comun pero mas lenta
 
 ```
 
 ### Operadores eficientes
 
En las comparaciones, la diferencia del operador === con el operador == es que este último hace una comprobación de tipos de variables antes. Si estás seguro de que son del mismo tipo, utiliza el primero. 
 
