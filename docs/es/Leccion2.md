---
title: Lección 2 - Java Script para GEE
linktitle: Lección 2 
toc: true
type: docs
date: "2020-01-05T00:00:00+01:00"
draft: false
menu:
  gee_cai2019:
    name: Lección 2 - Java Script para GEE
    weight: 4

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---


## Conceptos básicos

### Tipos de datos básicos de Java Scripts

  Una variable es un espacio en la memoria de la computadora al cual se le asigna un numbre y puede contener datos. El valor de una variable puede cambiar después que se la define. Para definir una variable es necesario anteponer la palabra `var`.  Existen diferentes tipos de variables dependiendo del tipo de dato que van a contener:

**Strings:**  variables de tipo textos o cadenas de caracteres, se utilizan las comillas simple o dobles para definir una cadena. 


```{js} 
// Usamos comillas simples o dobles para crear un string
var tipoStrings = 'Esta variable contiene un texto ';
print( tipoStrings );
``` 

**Números:** Variable que almacena números. En Java no se diferencias los enteros de los decimales, todos los números se guardan como decimales.

```{js} 
// Asignamos un número a una variable
var numero = 38; // se usa '.' (punto) para coma decimal
print('El valor es:', numero);
```

**Listas:** Las listas las definimos con corchetes [ ].  Las listas pueden contener más de un valor.

```{js}
// Lista de números
var listDeNumeros = [0, 1, 1, 2, 3, 5];
print('Lista de Números:', listDeNumeros);
```

Las listas también pueden almacenar strings u otros objetos.

```{js}
// Lista de strings.
var listDeStrings = ['BLUE', 'GREEN', 'RED', 'NIR', 'SWIR1','SWIR2'];
print('Lista de strings:', listDeStrings);
```

**Objetos:** En JavaScript los objetos son diccionarios de pares _clave: valor_. Para crear un objeto (o diccionario) se utilizan las llaves { }. Este diccionario podrá contener valores diferentes tipos de datos.

```{js}
// Usamos las llaves {} para hacer un diccionario de pares clave:valor
var object = {
  unString: 'Taller de GEE', 
  unNumero: 99,
  unaLista: ['BLUE', 'NIR', 'GREEN']
};

print('Diccionario:', object);

// Para acceder al valor de un diccionario se utiliza la clave y los corchetes [ ].
print('Imprime la clave unString:', object['unString']);

// También se puede acceder utilizando la notación con punto.
print('Print unaLista:', object.unaLista);
```

### Funciones

Las funciones son otra forma de mejorar la legibilidad y reutilización del código mediante el agrupamiento de conjuntos de operaciones.

*	Para definir una función se utiliza la palabra clave _function_.
*	Los nombres de las funciones _comienzan con una letra y tienen un par de paréntesis al final_.
*	Las funciones a menudo toman _parámetros_ o _argumentos_ que le proveen información a la función qué se utiliza junto con las intrucciones que definen que tiene que hacer. Estos parámetros van dentro de los paréntesis ().
*	El conjunto de declaraciones que componen la función van dentro de las llaves { }. La palabra clave _return_ indica cuál es la salida de la función.

Una de las formas de declarar una función

```{js}
// La función debe declararse antes de ser invocada
//declaración:

var probando_Funcion = function(argumento) {
  var mensaje = ' Esto es una ' + argumento;
  return mensaje;
};

// aqui la llamamos o invocamos a la funcion con el parametro.

print('Llamamos a la funcion', probando_Funcion('prueba'));
```

### Objetos de GEE

Son primitivas y objetos JavaScript en contenedores de Earth Engine para enviarlos al servidor y procesarlos en Google.  En el siguiente código de ejemplo _"Donde esta el string?"_ es una cadena de texto en el _cliente_ mientras que _"Esta en el server"_ es una cadena que fue enviada a los servidores de GEE para ser evaluada y luego devuelta.

```{js}
// Defino una cadena en el servidor.
var serverString = ee.String('Está en el server.');
print('¿Donde está el String?', serverString);
```

Los métodos _ee.String()_ y _ee.Number()_ son constructores. 

Estos contructores tiene sus argumentos que ponemos en un contenedor y que al restornar como un objeto EE podemos manipularlo en nuestro código. Un constructor comienza siempre con _ee_ y retorna un _objeto Earth Engine_ (EE).
Para operar con un objeto de EE vamos a utilizar los métodos que son provistos según la clase de objeto con la que trabajamos. Para el ejemplo anterior vamos a convertir en mayúsculas la cadena contenida en _serverString_.

```{js}
var metodo= serverString.toUpperCase();
print (metodo);
```
**Listas** 

Podemos hacer una lista de JavaScript como un objeto _ee.List_ en el servidor.  Podemos recuperar un elemento de la lista con el operador _get_. Recuerde que esta lista solo existe en el servidor, por lo que EE nos permite acceder a esos objetos sólo a través de sus métodos.

```{js}
// Ejemplos de listas,  
var miObjetoList = ee.List(['Un Texto', 22888555, 45, 4249898, 5]);

// Usamos un método de ee.List para recuperar un valor
var recupero= miObjetoList.get(0);

print (recupero)
```

**Casting**

Para operar con los números almacenados en un objeto debo “castear” el resultado ya que GEE no sabe el tipo de objeto que es retornado por un método.

Si al código anterior agregamos:

```{js}
var valor1=   eeList.get(2)
var valor2=   eeList.get(4)
var sumatoria =  valor1.add(valor2)
```

tendremos error:  `valor1.add is not a function`

Esto es común con la función _get()_, que puede devolver todo tipo de objetos de Earth Engine. Para corregirlo, utilice el constructor _ee.Number_ para "castear" el resultado:

```{js}
var valor1=   eeList.get(2);
var valor2=   eeList.get(4);

var cast_valor1= ee.Number(valor1);
var cast_valor2= ee.Number(valor2);

var sumatoria =  cast_valor1.add(cast_valor2);
print (sumatoria);
```