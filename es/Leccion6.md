---
title: Lección 6 - Manejo de datos rasters
linktitle: Lección 6 
toc: true
type: docs
date: "2020-01-05T00:00:00+01:00"
draft: false
menu:
  gee_cai2019:
    name: Lección 6 - Manejo de datos rasters
    weight: 7

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 7
---

## Manejo de datos rasters

En la plataforma existe una gran cantidad de fuentes de información entre las que se incluye tanto información de base como imágenes satelitales, bases de datos meteorológicas, como productos generados: Modelos Digitales de Elevación (DEMs), Máscaras de cuerpos de agua y áreas urbanas, etc.

En este caso vamos a seleccionar un producto y lo vamos a filtrar (acotar) a las necesidades particulares (intervalo de tiempo y área de interés), ya que generalmente el alcance es global y se disponen largas series temporales.

### Explorando catálogo de datos

Una de las características más interesantes de la plataforma GEE es la capacidad de analizar catálogos completos de imágenes satelitales o productos cartográficos sin la necesidad de descargarlos. Para buscar y acceder a los catálogos disponibles, basta con explorar en el cuadro de búsqueda que se encuentra sobre el editor.

Entre los productos de sensores remotos más interesantes se puede mencionar la colección completa de imágenes **MODIS, Landsat (4, 5,7 y 8), Sentinel 1 y 2, ASTER**, además de _productos_ como el mapa de **cambios forestales global de Hansen (Hansen Global Forest Change), FIRMS: Fire Information for Resource Management System, el mapa global de cultivos y fuentes de riego (Global Cropland Extent and Watering Source), el mapa global de aguas superficiales (JRC Global Surface Water Mapping Layers)**, entre otros.

Cada producto tiene un código asociado (_ImageCollection ID_) y una nomenclatura de bandas. El buscador del _Code Editor_ permite ver las colecciones disponibles, el ID y las bandas, presenta una breve explicación del producto y el origen del mismo.

{{< figure src="Buscador.png" title="Busqueda de un recurso e importación en nuestro script" >}}


Para agregarla a nuestro script basta con pulsar el botón de _Import_, pero para efectos de este ejercicio lo agregaremos de forma manual copiando el ImagenCollection ID, en este caso _LANDSAT/LC8_, y copiando el siguiente comando, donde la función objeto _ee.ImageCollection_ indica que el elemento a ser añadido.

```{js}
// Seleccionado un producto de la colección. En este caso Landsat 8 Tier1 TOA Reflectance
var landsat8 = ee.ImageCollection('LANDSAT/LC08/C01/T1_RT_TOA');

```
### Realizando operaciones en las imágenes

Cada elemento de la colección instanciada en la variable Landsat8 es a su vez un objeto de tipo [ee.Image](https://developers.google.com/earth-engine/api_docs).
A partir de aquí comenzaremos a utilizar filtros que permitan limitar la cantidad de imágenes dentro del área y el periodo que deseamos analizar. Para ello haremos uso de las funciones _.filterDate_ y _.filterBounds_. Donde _.filterDate_ indica la temporalidad de las imágenes y _.filterBounds_ indica los límites geográficos de las imágenes que deseamos analizar.

Para tener en cuenta:
*	Los filtros aplican sobre metadatos, valores de la imagen o utilizando geometrías.
*	Las selecciones aplican sobre las bandas.

Para filtrar primero definimos una región. Lo podemos hacer usando polígonos creados con las herramientas de GEE o invocando una que este por ejemplo en el ASSEST.  Agregar las siguientes líneas al código:

```{js}
// área de estudio
var area_estudio = ee.FeatureCollection('users/hernanelena/yungas');

Map.addLayer(area_estudio)
```

Ahora Aplicaremos filtros y observaremos la consola.

```{js}
//Filtrado de la colección Landsat8
var landsat8_filtrada = landsat8
                 // filtro por el área de estudio
                .filterBounds(area_estudio)
                // filtro por rango de fechas
                .filterDate('2016-01-01', '2016-12-31')
                //filtro por mes                      	                     .filter(ee.Filter.calendarRange(6,10,"month"))
                //filtro por cobertura nubosa. En este caso menor al 20%
                .filterMetadata('CLOUD_COVER','less_than', 20);
                // filtro por path row
                //.filterMetadata('default:WRS_PATH','equals', 231)
                //.filterMetadata('default:WRS_ROW','equals', 86);
                       	 
// selección
print  ("Colección filtrada: ",landsat8_filtrada);
```

Seleccionar bandas de interés:

```{js}
// Selección de bandas
var bandas = ['B2','B3','B4','B5','B6','B7']

landsat8_filtrada = landsat8_filtrada.select(bandas);

print  ("Colección filtrada con bandas filtradas: ",landsat8_filtrada);
```

### Realizando operaciones en una colección de imágenes

Si nuestro deseo es realizar un _apilamiento de imagenes L5, L7 y L8_ para una futura clasificación, _es conveniente normalizar los nombres de las bandas_. Pues _la B1 de Landsat 5 no es la misma que la B1 de Landsat 8_. Para ello podemos hacer una función que cambie el nombre de las bandas. Esto se aplicará a las diferentes colecciones si fuera necesario:

```{js}
//Funciones para cambiar nombre de bandas
var changeBandNameL5L7 = function(image) {
  return image.select(
  ['B1','B2', 'B3', 'B4', 'B5', 'B7'],  
  ['BLUE', 'GREEN', 'RED', 'NIR', 'SWIR1','SWIR2'])};

var changeBandNameL8 = function(image) {
  return image.select(
  ['B2', 'B3', 'B4', 'B5', 'B6', 'B7'],  
  ['BLUE', 'GREEN', 'RED', 'NIR', 'SWIR1','SWIR2'])};
  
var  l8= changeBandNameL8(landsat8_filtrada);
print (l8);
```

El objeto _ee.ImageCollection_ implica un catálogo, un grupo de imágenes. Para poder generar nuevas bandas, o exportar se requiere convertirla al objeto _ee.Image_. Esto se puede hacer creando una imagen a partir de bandas de la colección o aplicando algoritmos de reducción a la colección (e.g:mediana, promedio o valor máximo de pixels). En este caso, vamos a obtener como resultado una única imágen para cada banda (ahora objeto ee.Image), la cual puede ser posteriormente exportada y permite generar índices a partir de sus bandas.

```{js}
// Aplicar reducción de mediana 
// con .clip  recorto la imagen por el area de estudio
var stackL8 = l8.median().clip(area_estudio);

// ver imagen en mapa:
Map.addLayer( stackL8, {bands: ['NIR', 'SWIR1', 'GREEN'], min: [0,0,0], max:[1,1,1] } , "Landsat 8 " );
print ('Stack: ',stackL8);
```

```StackL8``` será una imagen de 6 bandas donde cada pixel es la mediana de los pixeles de la colección que habíamos filtrado (imágenes de 2016).

Supongamos que deseamos posteriormente realizar una clasificación supervisada para determinar desmontes ocurridos entre periodos 2010 y 2016.

Para esto deberemos realizar un mosaico único, como en el ejemplo anterior, de 2010 y apilarla  con la de 2016.

Agregamos

```{js}
// Seleccionado un producto de la colección. En este caso Landsat 5 Tier1 TOA Reflectance
var landsat5 = ee.ImageCollection('LANDSAT/LT05/C01/T1_TOA');

var landsat5_filtrada=  landsat5
                        	.filterBounds(area_estudio)
                        	.filterDate('2010-01-01', '2010-12-31')
                        	.filter(ee.Filter.calendarRange(6,10,"month"))
                        	.filterMetadata('CLOUD_COVER','less_than', 20);
var  l5= changeBandNameL5L7(landsat5_filtrada);
var stackL5 = l5.median().clip(area_estudio);
Map.addLayer( stackL5, {bands: ['NIR', 'SWIR1', 'GREEN'], min: [0,0,0], max:[1,1,1] } , "Landsat 5 2010 " );
```

###○ Generación de índices

Siguiendo con la idea anterior, El stack final, que será utilizado para clasificar, además de contener la bandas seleccionadas de los mosaicos de 2010 y 2016 podría también contener índices calculados a partir de bandas.
Hay varias maneras de realizar el cálculo:

```{js}
// cálculo NDVI - Por medio de una función
var ndviL8 = stackL8.normalizedDifference(['NIR', 'RED']);

// cálculo NDVI - operaciones sobre la imagen
var ndviL5 = stackL5.select('NIR').subtract(stackL5.select('RED'))
	.divide(stackL5.select('NIR').add(stackL5.select('RED')));

// ver imagen en mapa:
var ndvi_paleta_colores =
'FFFFFF, CE7E45, DF923D, F1B555, FCD163, 99B718, 74A901, 66A000, 529400,' +
'3E8601, 207401, 056201, 004C00, 023B01, 012E01, 011D01, 011301';

Map.addLayer(ndviL8, { min: [-1], max: [1], palette: ndvi_paleta_colores }, "NDVI L8 2016" );
Map.addLayer(ndviL5, { min: [-1], max: [1], palette: ndvi_paleta_colores }, "NDVI L5 2010" );
```

Otra forma de hacer una paleta de colores es usar Style Layer Description - SLD
 
```{js}
var sld_intervals =
  '<RasterSymbolizer>' +
	'<ColorMap  type="intervals" extended="false" >' +
  	'<ColorMapEntry color="#0000ff" quantity="0" label="0"/>' +
  	'<ColorMapEntry color="#00ff00" quantity="0.1" label="0 - 0.1" />' +
  	'<ColorMapEntry color="#007f30" quantity="0.2" label="0.1 - 0.2" />' +
  	'<ColorMapEntry color="#30b855" quantity="0.3" label="0.2 - 0.3" />' +
  	'<ColorMapEntry color="#ff0000" quantity="0.4" label="0.3 - 0.4" />' +
  	'<ColorMapEntry color="#ffff00" quantity="1.0" label="0.4 - 1.0" />' +
	'</ColorMap>' +
  '</RasterSymbolizer>';

Map.addLayer (ndviL5.sldStyle(sld_intervals), {}, "NDVI l5 con SLD" );
```

Ahora bien. Ya tenemos una imagen 2016 (stack de bandas), 2010 y los NDVI correspondientes a los mismo periodos, ¿cómo se hace un stack.?

```{js}
var stack_completo = stackL8.addBands(stackL5).addBands(ndviL8.rename('NDVIL8').addBands(ndviL5.rename('NDVIL5')));

print ('Stack completo', stack_completo)
```

### Extraer información del raster

Earth Engine permite extraer rápidamente información de las imágenes seleccionadas, exportarla o analizarla desde la plataforma.

Para extraer información de nuestra imagen vamos a utilizar geometría de puntos la cual contendrá:

*	Clase 1: desmontes ocurridos en el periodo 2010-2016
*	Clase 2: Cultivos existentes en 2010  y 2016
*	Clase 3: Bosque en 2010 y 2016

Copiar el [siguiente código](https://github.com/yabellini/GEE_course_code/blob/master/extraccion_info_CAI.js) y pegarlo en el _Scripts_

Esto generará _FeatureCollections_ con información de los puntos que son cargadas en el código al inicio.
Al pegar esta sección de código y apoyar el mouse sobre este, preguntará si queremos convertir a registros importados, a lo que le indicamos _Convert_. De esta manera ubica el código de los puntos agregados en una sección diferente (al inicio, en _imports_):

{{< figure src="convertImport.png" >}}
 
Para extraer la información primero debemos unificar todas las clases en un solo FC

```{js}
// Extracción de información
// Unir muestras por clase en un único FeatureCollection    
// podemos hacerlo de varias formas Ej:

var puntos = ee.FeatureCollection([Cambios1016,Cultivos,BosqueEstable]).flatten();
print ('Puntos',puntos)

// Otra forma de unificar
//var puntos2 = Cambios1016.merge(Cultivos).merge(BosqueEstable);
//print ('puntos2',puntos2);
```

Luego utilizaremos sampleregion para extraer información. Hay que indicar la imágen a utilizar (“stack_completo”), los atributos del vector (FeatureCollection) que se desean mantener (atributo “clase”) y la escala (resolución de la imagen):

```{js}
// extraer información:
var training = stack_completo.sampleRegions({
  collection: puntos,
  properties: ['clase'],
  scale: 30
});

// Los puntos ahora tendran la informacion de cada banda de la imagen stack
print (' puntos de muestras',training);

// exportar tabla con información
Export.table.toDrive({
	'collection': training,
	'description': 'muestrasCAI',
	'fileNamePrefix': 'muestrasCAI',
	'fileFormat': 'CSV'}
);
```

{{< figure src="EjemploTablaExportada.png" >}}
