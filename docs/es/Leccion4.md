---
title: Lección 4 -Features y Colecciones de Features
linktitle: Lección 4 
toc: true
type: docs
date: "2020-01-05T00:00:00+01:00"
draft: false
menu:
  gee_cai2019:
    name: Lección 4 - Features y Colecciones de Features
    weight: 5

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 5
---


## Features 

Un Feature de Earth Engine se define como un GeoJSON Feature. Es un objeto con una propiedad de tipo Geometry y una propiedad de properties que almacena un diccionario de otras propiedades.
Entonces, necesitamos un objeto Geometry y opcionalmente un diccionario con los atributos de ese Feature.

```{js}
// La geometría
var poligono = ee.Geometry.Polygon(
       [[[-63.33892822265625, -25.150878651548442],
         [-63.33824157714844, -25.17791290009134],
         [-63.31043243408203, -25.17760219565173],
         [-63.31043243408203, -25.15025710411473]]]);
//La declaración del Feature:
var miFeature = ee.Feature(poligono,
    {variable_1: 100,
     variable_2: 'Hola'});

print(miFeature);
Map.addLayer(miFeature, {}, 'Mi Feature!');
Map.centerObject(miFeature, 12);   
```

Los Features tienen las mismas funcionalidades para gestionar sus geometrías que los objetos Geometry. Además, poseen otros métodos setters & getters para el manejo de las propiedades.

```{js}
var feature_ejemplo = ee.Feature(
  	ee.Geometry.Point([-64.4913, -25.2314]))
    	.set('Nombre', 'El Tunal')
    	.set('Area_ha', 3800);
   	 
// Recupero una propiedad del feature
var nombre = feature_ejemplo.get('Nombre');
print(nombre);

print(feature_ejemplo);
// Asigno una nueva propiedad
feature_ejemplo = feature_ejemplo.set('Altura_max', 41);

// Sobreescribo un nuevo diccionario
var newDict = {Nombre: 'Embalse El Tunal', Area_ha: 3290};
var feature_ejemplo = feature_ejemplo.set(newDict);

print(feature_ejemplo);

// Se muestran los resultados
Map.addLayer(feature_ejemplo, {'color':'CC0011'}, 'Ejemplo 2');  	
```

## Colecciones de Features

Los grupos de features relacionados se pueden combinar en una _FeatureCollection_ (FC), para permitir operaciones adicionales en todo el conjunto tales como: filtrado, clasificación y renderizado. Además de simples features (geometría + propiedades), FeatureCollection también puede contener otras colecciones.

### FeatureCollection a partir de lista de Features

Podemos construir un FeatureCollection a partir de una lista de features, donde estos pueden tener o no el mismo tipo de geometría.

```{js}
var features = [
	ee.Feature(ee.Geometry.Point(-65.4027, -24.7154), {Localidad:'Vaqueros'}),
	ee.Feature(ee.Geometry.Point(-65.0511, -24.6605), {Localidad:'Guemes'}),
	ee.Feature(ee.Geometry.Point(-65.48832, -24.8961 ), {Localidad: 'Cerrillos'}),
	ee.Feature(ee.Geometry.Point(-65.656, -24.9061), {Localidad: 'Campo Quijano'})];

var fc_desdeUnaLista = ee.FeatureCollection(features);

print(fc_desdeUnaLista);
Map.addLayer(fc_desdeUnaLista, {}, 'FC_Puntos');
Map.centerObject(fc_desdeUnaLista,8);
```

### FeatureCollection a partir de Google Fusion Table

También podemos incorporar un FeatureCollection a partir de un Google Fusion Table. Google Fusion Tables (GFT) es un servicio de Google que permite manejar tablas. Esto habilita la importación de archivos vectoriales a la plataforma GEE a través de archivos vectoriales en formato KML.

Estas tablas las gestionamos desde Google Drive, Debemos conectar a la Aplicación Tablas Dinámicas y crear una tabla dinámica de Google.

Para la creación del Fusion Table debemos importar  desde el sistema de archivos un documento .kml.

Para poder importar este archivo a GEE necesitamos el ID de la tabla, para ello vamos a File y seleccionamos About this table. Ahí encontraremos el identificador (Id).

Ej. *1BqbNEHsacJn5xWbOwznChvc77xJ66tutV3u7axq_*

Para cargar una FeatureCollection desde una Fusion Table, proporcione al constructor (ee.FeatureCollection) el ID de tabla agregado a ft:. Por ejemplo:

```{js}
var desdeFT = ee.FeatureCollection('ft:1BqbNEHsacJn5xWbOwznChvc77xJ66tutV3u7axq_');
print(desdeFT);
Map.addLayer(desdeFT, {}, 'Región Chaqueña');
Map.centerObject(desdeFT);
```

### Creación de un FeatureCollection a partir de Tablas

Es posible instanciar un FC utilizando tablas que tenemos almacenadas en un Assets. Para crear esta nueva tabla podemos utilizar un Shapefile o un .zip que contenga todos los archivos que componen el Shapefile. El tamaño máximo permitido es 10GB.

{{< figure src="CargarAssets.png" title="Secuencia de pantallas para cargar un shape file a GEE" >}}

La carga del archivo (upload) no es instantáneo y puede demorar algunos minutos dependiendo de la congestión de la plataforma y el tamaño que tenga el archivo. Podemos verificar el progreso desde la solapa _Task_:
Podemos incluir este FC de dos formas. La primera es desde la solapa Assest. Importar el FC. Automáticamente se incorpora al código en la sección import.:

{{< figure src="Import.png" title="Secuencia de pantallas para cargar un shape file a GEE" >}}

La segunda forma es:

```{js}
var segunda_forma = 'users/hernanelena/Santa_maria_este_cuenca';
var cuenca = ee.FeatureCollection(segunda_forma);
print(cuenca);
Map.addLayer(cuenca, {color:'fec44f'}, 'Cuenca');
```

### Creación de FeaturesCollection desde el Mapa

Al igual que mostramos anteriormente como dibujar geometrías, es posible definir FeaturesCollection dibujando desde el mapa.

{{< figure src="GenerarGeometriaMapa.png" >}}

{{< figure src="GenerarGeometriaMapa2.png" >}}
