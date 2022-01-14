---
title: Lección 3 - Datos Vectoriales
linktitle: Lección 3 
toc: true
type: docs
date: "2020-01-05T00:00:00+01:00"
draft: false
menu:
  gee_cai2019:
    name: Lección 3 - Datos Vectoriales
    weight: 5

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 5
---


## Manejo de Datos Vectoriales

Earth Engine maneja datos vectoriales con el tipo _ee.Geometry_. La especificación GeoJSON describe en detalle el tipo de geometrías soportadas por Earth Engine, incluyendo:

*	_Point_: una lista de coordenadas en alguna proyección,
*	_LineString_: una lista de puntos,
*	_LinearRing_: una _LineString_ cerrada
*	_Polygon_: una lista de _LinearRings_ donde la primera es una cáscara y los anillos subsiguientes son agujeros.

Earth Engine también soporta [MultiPoint](https://developers.google.com/earth-engine/apidocs/ee-geometry-multipoint), [MultiLineString](https://developers.google.com/earth-engine/apidocs/ee-geometry-multilinestring) y [MultiPolygon](https://developers.google.com/earth-engine/apidocs/ee-geometry-multipolygon). GeoJSON _GeometryCollection_ también es compatible, aunque tiene el nombre _MultiGeometry_ dentro de Earth Engine.

### Creación de Geometrías

Vamos a crear y mapear geometrías. Tenemos dos formas. Una a través de la escritura de sentencias y la otra forma a través del mapa.

#### Creando geometrías mediante código

```{js}
//Puntos
var point = ee.Geometry.Point([-65.409244, -24.727333]);// geometria punto
//Lineas
var lineString = ee.Geometry.LineString([[-65.404817, -24.725354],
[-65.402682, -24.725636], [-65.403004, -24.727897], [-65.403959, -24.727829 ]]); //linea de puntos

//Anillo
var linearRing = ee.Geometry.LinearRing(
	[[-65.407033, -24.726395 ],[-65.407312, -24.727828],[-65.406632, -24.727128]  ,[-65.407033, -24.726395]] );

//Rectangulos
var rectangulo = ee.Geometry.Rectangle([-65.408418, -24.728688,-65.407839, -24.729896 ]);

//Poligonos
var vertices = [ [-65.410024, -24.728817], [-65.410383, -24.728763], [-65.410357, -24.728608],[-65.41018, -24.728632],[-65.41011, -24.728603],[-65.409992, -24.728661] ];
var poligono = ee.Geometry.Polygon( vertices );

 
// Mapeo de Geometrías
Map.addLayer(point,{'color':'00FF11'} ,'Punto');
Map.addLayer(lineString,{'color':'CC0011'} ,'Linea');
Map.addLayer(linearRing,{'color':'CC0011'} ,'Anillo');
Map.addLayer(rectangulo,{'color':'00FF11'} ,'Rectangulo');
Map.addLayer(poligono,{'color':'CC0011'} ,'Poligono');
Map.centerObject(point,16);
```

[ee.Geometry.Point](https://developers.google.com/earth-engine/apidocs/ee-geometry-point) es la llamada a la API Earth Engine que recibe dos parámetros: una lista _ee.List([])_ con las coordenadas y el segundo que es opcional una proyección que puede ser especificada como código EPSG [1]. El valor predeterminado es EPSG:4326 (WGS84 Lat/Lon).

[ee.Geometry.LineString](https://developers.google.com/earth-engine/apidocs/ee-geometry-linestring) recibe una lista de puntos y parámetros opcionales.

[ee.Geometry.LinearRing](https://developers.google.com/earth-engine/apidocs/ee-geometry-linearring) recibe una lista de puntos que a diferencia de LineString comienza y termina con el mismo punto para poder cerrar el anillo. También tiene parámetros opcionales.

[ee.Geometry.Rectangle](https://developers.google.com/earth-engine/apidocs/ee-geometry-rectangle) recibe una lista con esquinas mínimas y máximas del rectángulo, como una lista de dos puntos en formato de coordenadas GeoJSON 'Point' o una lista de dos _ee.Geometry_ que describen un punto, o una lista de cuatro números en el orden _xMin, yMin , xMax, yMax_.

[ee.Geometry.Polygon](https://developers.google.com/earth-engine/apidocs/ee-geometry-polygon) recibe una lista de anillos que definen los límites del polígono. Puede ser una lista de coordenadas en el formato 'Polygon' de GeoJSON, o una lista de ee.Geometry que describe un LinearRing. El resto de los parámetros son similares al resto de las geometrías.

#### Creando geometrías mediante el mapa

Las opciones para dibujar están ubicadas en el sector superior izquierdo del mapa.  Las herramientas disponibles permiten activar el dibujado de geometrías múltiples de: puntos, líneas y polígonos.Para dejar de dibujar se hace clic en la mano de la izquierda.


{{< figure src="GeometriasDesdeMapas.png" title="Herramientas para dibujar geometrías de puntos, lineas y pokígonos usando el mapa de GEE" >}}

Las geometrías creadas se incorporarán al codigo en la sección _import_ (en el panel de Script).  Una vez que se activa la herramienta esta se habilita para poder dibujar. Se asigna un color al azar y cada figura que se trace formará parte de una geometría múltiple.  En la configuración predeterminada de la instanciación de un objeto _ee.Geometry.XXXXX_ este se crea como *EPSG:4326*, es decir, será una geometría geodésica.

### Operaciones con Geometrías

Earth Engine admite una amplia variedad de operaciones en objetos [Geometry](https://developers.google.com/earth-engine/apidocs/ee-geometry). Estos incluyen operaciones en geometrías individuales tales como calcular un buffer, centroide, bounding box, perímetro, envolvente convexa, etc.

Utilizando la definición del polígono de la anterior vamos a realizar algunas operaciones de geometrías:

```{js}
print('Área: ', poligono.area());
// Todos los valores de mediciones de distancias vienen expresados en metros.
// asi que para km2 : poligono.area().divide(1000 * 1000));
```


