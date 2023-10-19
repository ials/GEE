---
title: "Indices espectrales Sentinel-2"
teaching: 15
exercises: 45
questions:
- ¿Cómo acceder a una colección de imágenes?
- ¿Cómo filtrar por fechas y por cubrimiento?
- ¿Cómo recortar todas las imágenes de una colección?
- ¿Cómo visualizar composiciones a color?
objectives:
- Acceder a una colección de imágenes
- Filtrar la colección por fecha y por extensión espacial
- Crear una función para recortar todas las imágenes de una *image collections*
- Consultar metadatos de una imagen
- Crear composiciones a color
keypoints:
- Se puede acceder a las imágenes de una *image collection* completa y crear filtros basados en el periodo de tiempo y la ubicación del área de estudio
- Las imágenes pueden ser recortadas para cubrir la zona de interés
- Una imagen se puede visualizar en diferentes combinaciones de bandas
- La visualización de una imagen requiere ajuste de contraste y corrección gamma 
---

## IALS - 18.10.2023

# Descripción general: Bandas espectrales de las imágenes  Landsat

Las imágenes Sentinel-2 tienen algunas bandas espectrales similares a Landsat tal como se muestra en la siguiente gráfica:

<img src="../fig/04_S2Landsat.png" width="80%" height="80%" />
<sub>*USGS Landsat archive holdings as of January 1, 2015 (Wulder et al. (2016)).*</sub>

Una de las diferencias es que el sensor multiespectral de Sentinel-2 adquiere tres bandas en el borde del rojo, con centro en 705, 740 y 783 nm, que son útiles para la caracterización de la vegetación.


Una composición RGB SWIR-1 (B11), infrarrojo cercano (B8), y azul (B2) de Sentinel 2 se muestra en la siguiente figura:

<br>
<img src="../fig/04_RGB-8-11-2.png" border = "10" width="60%" height="60%">
<br><br>

Esta composición se puede usar para monitorear la salud de los cultivos. La vegetación vigorosa se muestra en color verde oscuro.

# Indices espectrales a partir de imagenes Landsat

Una imagen Sentinel-2 permite obtener una gran variedad de indices espectrales.

En [este enlace](https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/indexdb/) hay una lista extensa de indices que se pueden obtener con estas imágenes.

<img src="../fig/04_S2-NDVI.png" width="80%" height="80%" />
<sub>*Sentinel-2 NDVI*</sub>
https://www.usgs.gov/landsat-missions/landsat-surface-reflectance-derived-spectral-indices


# Ejercicio: Obtención de series de tiempo del indice de vegetacion

Aquí, usaremos GEE para calcular el NDVI de todas las imágenes Sentinel-2 de 2022 que cubran una zona de interés.

### Image Collections



### Cargar archivos vectoriales



{% highlight javascript %}
// cargar un polígono de suertes previamente importado a GEE
// que está declarado en una variable de nombre *juana*

print(juana, "Hacienda La Juana");

var estilo1 = {
  fillColor: '00ffbf',
  color: '00b386',
  width: 1.0,
};

var estilo2 = {
  fillColor: 'b5ffb4',
  color: '00909F',
  width: 1.0,
};


// establecer la vista del mapa y el zoom, y añadir la zona de interés
//Map.setCenter(-76.03, 4.59, 10);
Map.centerObject(juana,14);
Map.addLayer(juana, estilo1, 'Hacienda La Juana', false, 0.5);
Map.addLayer(suerte, estilo2, 'Suerte', false);

{% endhighlight %}

<br>
<img src="../fig/03_suertes.png" border = "10" width="50%" height="50%">
<br><br>


### Filtrar una Image Collection

Aquí, estamos seleccionando todas las imágenes en el [Landsat 8 Surface Reflectance collection](https://code.earthengine.google.com/dataset/LANDSAT/LC08/C01/T1_SR) adquirido sobre nuestra zona de interés.

*Consejo: Los ID de las Image collection se encuentran en la barra de herramientas de "Search" en la parte superior del editor de códigos o a través de la búsqueda en el [data archive](https://code.earthengine.google.com/datasets/).*

{% highlight javascript %}
// cargar todas las imágenes Landsat 8 Nivel 2 dentro de la hacienda para el año 2023
// esta coleccion de imagenes tiene correccion atmosferica absoluta 
var landsat8Collection = ee.ImageCollection('LANDSAT/LC08/C02/T1_L2')
          .filterBounds(juana)
          .filterDate('2023-01-01', '2023-10-17');

print(landsat8Collection);
{% endhighlight %}

Al imprimir nuestra colección filtrada en la consola podemos conocer cuántas imágenes han sido filtradas así como los nombres de las bandas y las propiedades de las imágenes de nuestra colección:
<br>
<img src="../fig/03_L8col.png" border = "10" width="75%" height="75%">
<br><br>

{% highlight javascript %}
// -----------------------------------------------------------------
// Obtener información de la Image Collection
// -----------------------------------------------------------------

// Obtener el número de imágenes 
var count = landsat8Collection.size();
print('Numero de imagenes: ', count);

// Obtener el rango de fechas de las imaágenes de la colección
var range = landsat8Collection.reduceColumns(ee.Reducer.minMax(), ['system:time_start'])
print('Rango de fechas: ', ee.Date(range.get('min')), ee.Date(range.get('max')))

// Obtener estadisticas de alguna propiedad de las imagenes
var sunStats = landsat8Collection.aggregate_stats('SUN_ELEVATION');
print('Estadisticas de elevacion solar: ', sunStats);

var cloudStats = landsat8Collection.aggregate_stats('CLOUD_COVER');
print('Estadisticas de nubes: ', cloudStats);

// Ordenar por la cobertura de nubes y obtener la imagen con menos
var image = ee.Image(landsat8Collection.sort('CLOUD_COVER').first());
print('Esta es la imagen con menos nubes: ', image);
{% endhighlight %}

Al correr el código anterior, en la consola podemos ver la información solicitada:

<br>
<img src="../fig/03_L8-metadatos.png" border = "10" width="70%" height="70%">
<br><br>

### Visualizacion de imagenes

Primero, visualización en color natural:

{% highlight javascript %}
// Visualización en color verdadero
// La visualización por defecto es muy oscura
Map.addLayer(image,{bands:['SR_B4','SR_B3','SR_B2']}, 'imagen menos nubosa - CRUDA', false);
// Una buena visualización requiere corrección gamma y ajuste del contraste

var p_ajuste= {bands: ["SR_B4","SR_B3","SR_B2"],
             gamma: 2.5,
             max: 20000,
             min: 7000,
             opacity: 1};


Map.addLayer(image,p_ajuste, 'imagen menos nubosa- AJUSTADA');
{% endhighlight %}

Al correr el código anterior, en el mapa podemos ver las dos visualizaciones:

<br>
<img src="../fig/03_L8-natural-color.png" border = "10" width="80%" height="80%">
<br><br>

### Rescalamiento de niveles digitales

Note que los niveles digitales de las imágenes están en un rango nominal de 16 bits. Esto significa que para obtener reflectancia hay que aplicar factores de rescalamiento que se pueden ver en el catalogo de GEE buscando **USGS Landsat 8 Level 2, Collection 2, Tier 1**.

Allí se observará que las bandas SR_B1 a SR_B7 pueden tener valores entre 0 y 65455 y que para obtener reflectancia de superficie se requiere rescalar  los niveles digitales usando los siguientes valores:

- Scale = 0.0000275	
- Offset = -0.2

Vamos ahora a realizar ese rescalamiento:

{% highlight javascript %}
// 

var escala = 0.0000275;
var inter = -0.2;

var image_SR = image.select('SR_B.|SR_B7').multiply(escala).add(inter);

print(image_SR, 'image_SR');

// Visualización por defecto
Map.addLayer(image_SR,{bands:['SR_B4','SR_B3','SR_B2']},'RGB432 cruda', false);

// Visualización ajustada
var n_ajuste= {bands: ["SR_B4","SR_B3","SR_B2"],
             gamma: 2.0,
             max: 0.12,
             min: 0.00,
             opacity: 1};

Map.addLayer(image_SR,n_ajuste,'RGB432 ajustada');

// centrar el mapa en una zona conocida
Map.centerObject(suerte, 14);
Map.addLayer(suerte, {}, 'suerte de juana');
{% endhighlight %}


Al correr el código anterior, en el mapa veremos la imagen rescalada:

<br>
<img src="../fig/03_L8-rescalada.png" border = "10" width="90%" height="90%">
<br><br>

Use la pestaña Inspector en la ventana de la derecha para indagar los valores de reflectancia en algunos sitios conocidos.
Por ejemplo si usted hace clic en el centro del lote más al oriente de La Juana obtendrá unos datos similares a los siguientes en la imagen


<br>
<img src="../fig/03_L8r-inspector.png" border = "10" width="65%" height="65%">
<br><br>

### Visualización en falso color

Cualquier combinación de bandas RGB diferente a la que se conoce como "color natural", es denominada falso color.

Intentemos una combinación que combine las bandas NIR, SWIR y Blue:

{% highlight javascript %}
// Visualización en falso color
//  RGB 572
var o_ajuste= {bands: ["SR_B5","SR_B7","SR_B2"],
             gamma: 1.5,
             max: 0.35,
             min: 0.00,
             opacity: 1};

Map.addLayer(image_SR,o_ajuste,'RGB572 ajustada');
{% endhighlight %}

El resultado es el siguiente:
<br>
<img src="../fig/03_RGB572.png" border = "10" width="90%" height="90%">
<br><br>

### Recorte de imágenes

Todas las imagenes de una colección se pueden recortar si se crea una función que recorte una imagen y, luego,
se utiliza el metodo *map* para iterar en todas las imagenes de la colección.

{% highlight javascript %}
function recortar(img) {
  return img.clip(AOI2);
}

var recorteL8col = landsat8Collection.map(recortar);

// imprimir la imagen collection recortada
print(recorteL8col, 'recorteL8col');

// visualizar una de las imagenes de la colleccion recortada
Map.centerObject(AOI2,12);
Map.addLayer(recorteL8col.sort('CLOUD_COVER').first(), p_ajuste, 'imagen coleccion recortada');
{% endhighlight %}

El resultado es el siguiente:
<br>
<img src="../fig/03_Recorte.png" border = "10" width="90%" height="90%">
<br><br>


Se puede acceder a una versión estática del script aquí: [https://code.earthengine.google.com/b82a56ebdb175d28ef65b29a0f2a7182](https://code.earthengine.google.com/b82a56ebdb175d28ef65b29a0f2a7182)