---
title: "Indices espectrales Sentinel-2"
teaching: 15
exercises: 45
questions:
- ¿Cómo crear una funcion que calcule un indice de vegetacion?
- ¿Cómo aplicar la funcion a todas las imágenes de una colección?
- ¿Cómo obtener el valor promedio de un indice para una coleccion  de objetos?
objectives:
- Acceder a una colección de imágenes y filtrar por fecha y por extensión espacial
- Recortar las imagenes
- Rescalar los niveles digitales de las imagenes recortadas
- Obtener una coleccion de indices de vegetacion
- Obtener series de tiempo de los valores promedio de los indices en una coleccion de objetos
keypoints:
- Se puede crear bandas nuevas con los indices espectrales que se deseen
- Los valores de los indices se puedean agregar en cada objeto y obtener series temporlas
- Las series temporales se pueden exportar para posterior procesamiento
---


## IALS - 18.10.2023

# Descripción general: Bandas espectrales de las imágenes  Sentinel-2

Las imágenes Sentinel-2 tienen algunas bandas espectrales similares a Landsat tal como se muestra en la siguiente gráfica:

<img src="../fig/04_S2Landsat.png" width="80%" height="80%" />
<sub>*USGS Landsat and Sentinel 2 have similar bands.*</sub>

Una de las diferencias es que el sensor multiespectral de Sentinel-2 adquiere tres bandas en el borde del rojo, con centro en 705, 740 y 783 nm, que son útiles para la caracterización de la vegetación.

Una composición RGB SWIR-1 (B11), infrarrojo cercano (B8), y azul (B2) de Sentinel 2 se muestra en la siguiente figura:

<br>
<img src="../fig/04_RGB_8-11-2.png" border = "10" width="80%" height="80%">
<br><br>

Esta composición se puede usar para monitorear la salud de los cultivos. La vegetación vigorosa se muestra en color verde oscuro.

# Indices espectrales a partir de imagenes Sentinel-2

Una imagen Sentinel-2 permite obtener una gran variedad de indices espectrales.

En [este enlace](https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/indexdb/) hay una lista extensa de indices que se pueden obtener con estas imágenes.

<img src="../fig/04_S2-NDVI.jpeg" width="80%" height="80%" />
<sub>*Sentinel-2 NDVI*</sub>
https://www.usgs.gov/landsat-missions/landsat-surface-reflectance-derived-spectral-indices


# Ejercicio: Obtención de series de tiempo de un indice de vegetacion

En este ejercicio vamos calcular el NDVI de todas las imágenes Sentinel-2 de 2022 que cubran una zona de interés.

El proceso es similar al utilizado para las imagenes Landsat-8. Aunque la banda Red de Sentinel-2 es la Banda 4, la banda NIR es la Banda 8. Eso determina que la formula para el cálculo del indice NDVI es diferente.

Uno de los resultados de este ejercicio son los indices de vegetacion NDVI de toda una colección de imagenes:
<br>
<img src="../fig/04_S2_NDVI.png" border = "10" width="90%" height="90%">
<br><br>

Note que en este caso, la colección anual comprende un número mayor de imágenes que cuando se trabaja con Landsat.

Otro resultado es la serie de tiempo de NDVI para cada una de las suertes de interés:

<br>
<img src="../fig/04_S2_NDVI_chart.png" border = "10" width="120%" height="120%">
<br><br>


Se puede acceder a una versión estática del script aquí:
[https://code.earthengine.google.com/c14ae03ea7159f27ea8423f5f8e07b48](https://code.earthengine.google.com/c14ae03ea7159f27ea8423f5f8e07b48)

