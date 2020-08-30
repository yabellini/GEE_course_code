---
title: Lección 1 - Google Earth Engine
linktitle: Lección 1 
toc: true
type: docs
date: "2020-01-05T00:00:00+01:00"
draft: false
menu:
  gee_cai2019:
    name: Lección 1 - Google Earth Engine
    weight: 4

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---


## Google Earth Engine

### ¿Qué es Google Earth Engine?

Google Earth Engine (GEE) es una plataforma informática que permite a sus usuarios realizar análisis geoespaciales utilizando la potencia de la infraestructura de Google.

GEE combina un catálogo de imágenes de satélite y conjuntos de datos geoespaciales de varios petabytes con capacidades de análisis a escala planetaria utilizando algortimos de aprendizaje automático y lo pone a disposición de científicos, investigadores y desarrolladores para detectar cambios, trazar tendencias y cuantificar diferencias en la superficie de la Tierra.

En [esta presentación](https://yabellini.netlify.app/es/courses/gee_cai2019/leccion1/Google_Earth_Engine_TallerCAI2019.pdf) hay mas detalles sobre su funcionamiento, la información disponible y que tipo de productos y análisis se pueden realizar.

Hay varias maneras de interactuar con la plataforma:

*	_Code Editor (editor de código)_ :es una Interfaz de Desarrollo (IDE) basada en la web (se utliza a través de un explorador de Internet como FireFox o Chrome) para escribir y ejecutar secuencias de comandos.
*	[Explorer](https://developers.google.com/earth-engine/datasets/) es una aplicación web que permite explorar el catálogo de datos y realizar análisis sencillos.
*	[Client Libraries(librerías de cliente)](https://developers.google.com/earth-engine/) proporcionan _wrappers_ de Python y JavaScript entorno a las _API_ (Interfaz de programación de aplicaciones, en inglés _Application programming interface_) web de Google.


En este taller nos dedicaremos al editor de codigos.

### Code Editor

Para ingresar al editor de código es necesario iniciar sesión con una cuenta de Google habilitada para el acceso a Earth Engine y utilizamos la siguiente url: https://code.earthengine.google.com . Las características del editor de código están diseñadas para hacer que el desarrollo de flujos de trabajo geoespaciales complejos sea rápido y fácil. El editor de código tiene los siguientes elementos:

{{< figure src="CodeEditor.png" title="Partes del editor de código" >}}


La sección de la izquierda de la pantalla nos presentará código (script), documentación y recursos (asset) propios como por ejemplo, archivos vectoriales con la zona de estudio a trabajar.

La sección del medio de la pantalla es el lugar donde vamos a escribir y ejecutar nuestro código, generalmente utilizando el lenguaje Java.

La sección de la derecha de la pantalla corresponde a los paneles de inspección, la consola y el gestor de tareas.  En el panel de inspección se presenta información de localizaciones, valores de píxeles y objetos en el mapa.  En la consola se nos mostrará resultados y estados de ejecuciones de código, en el gestor de tareas, se realizarán trabajos en la plataforma (como por ejemplo grabar una imagen GeoTIFF del resultado de un modelo).

Finalmente, la sección inferior, es el panel de Mapas donde se muestran los resultados en el mapa de nuestros algoritmos. También se muestran otros objetvos que hayamos incorporado al análisis como mapas de polígonos o puntos.

### Hola mundo en GEE

Es bastante común queel primer ejemplo de un pograma en un lenguaje de programación nuevo sea el "Hola mundo", asi que vamos a hacer nuestro _Hola Mundo_ para ver como se realiza en GEE utilizando el Editor de código.

Copiar el siguiente código en el editor, luego hacer click en el botón _Run_ y el resultado se mostrará en la _consola_

` print('Hola Mundo!'); `

{{< figure src="HolaMundo.png" title="Hola Mundo con Java en GEE" >}}




