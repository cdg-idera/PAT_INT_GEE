# Capítulo 5 · Random Forest para detectar agua

## Introducción

En este capítulo vamos a aplicar aprendizaje automático a imágenes satelitales para predecir superficies de agua. Utilizaremos datos satelitales de Sentinel-2 para procesar una colección de imágenes correspondientes a una región de estudio y aplicaremos un modelo de Random Forest para clasificar los píxeles en dos categorías: agua y no agua. Es decir, emplearemos una clasificación binaria.

En Google Earth Engine (GEE) podes explorar imagenes Sentinel haciendo click en DATASET para acceder al catálogo de datos y luego hacer en la opción de menú Sentinel y encontrarás todos los productos satelitales Sentinel. 

Sentinel-2 es propiedad de la Agencia Espacial Europea {cite:p}`esa_sentinel2`.  En Google Earth Engine tenemos acceso a colecciones de imágenes satelitales Sentinel-1, Sentinel-2, Sentinel-3 y Sentinel-5 {cite:p}`gee_sentinel`. Nos vamos a centrar en Sentinel-2, cuyos datos son muy populares. 

Se aplican en diversas áreas de monitoreo de la superficie terrestre, incluida la *gestión de desastres*, la *gestión de emergencias*, la *cuantificación de la tendencia de urbanización* y la *cobertura del suelo*, entre otras.

```{figure} imagenes/PosIT-1.png
:name: fig-PosIT-1
:width: 60%

Algunas aplicaciones de Sentinel-2
```

Bien creamos un nuevo script denominado *A001_RandomForest_CuerposDeAguaComahue*. Nuestra área de estudio será la **región del Comahue** principalmente la provincia del *Neuquén* y la de *Rio Negro*. Vamos a crear nuestra región de estudio, nuestraregión de interés o ROI. 

```{figure} imagenes/km.png
:name: fig-km
:width: 20%

Sobre el area de estudio
```

```{figure} imagenes/p_3.png
:name: fig-p_3
:width: 50%

Etapa: Clasificar la imagen
```

Como no estamos interesados en los límites provinciales precisos de la provincia, debido a que queremos abarcar los rios que componen los limites naturales provinciales, no utilizaremos los límites administrativos exactos disponibles en la IDE de la provincia de Neuquén y Rio Negro, o en el **Instituto Geográfico Nacional**  {cite:p}`IGN:CapasSIG`, sino que utilizaremos el conjunto de datos de Capas de **Unidades Administrativas Globales** {cite:p}`UN:GAUL:DataBasis` de Google Earth Engine.

Esto nos permitirá aprender un poco más sobre este conjunto de datos de Unidades Administrativas Globales: 

En el buscador de dataset buscamos *FAO GAUL: Global Administrative Unit Layers 2015, First-Level Administrative Units*

```{figure} imagenes/PosIT-2.png
:name: fig-PosIT-2
:width: 60%

Sobre Global Administrative Unit Layers
```
Las Capas de Unidades Administrativas Globales (GAUL) recopilan y difunden la mejor información disponible sobre unidades administrativas para todos los países del mundo, contribuyendo a la estandarización del conjunto de datos espaciales que representan las unidades administrativas.

Llamando a esta colección de imágenes, tendremos los límites de administración para todos los países.

```javascript
var roi = ee.FeatureCollection("FAO/GAUL/2015/level1");
```

Si visualizamos el esquema de tabla (ver fig. {numref}`fig-FAO`) de este conjunto de datos, existen algunos atributos que utilizaremos para filtrar:

* **ADM0_CODE** tiene el código de pais, y **ADM0_NAME** tiene el nombre de pais,
* **ADM1_CODE** tiene el código de unidades administrativas de primer nivel y **ADM1_NAME** tiene el nombre de unidades administrativas de primer nivel.

```{figure} imagenes/FAOSchema.png
:name: fig-FAO
:width: 100%

El esquema de Global Administrative Unit Layers
```

Utilizando **ADM0_NAME** filtraremos *Argentina* y utilizando y **ADM1_NAME** filtraremos la unidad administrativa de la provincia del neuquen y la Provincia de Rio Negro: 

Primero mostramos como ADM0_NAME nos permite filtrar por nombre de país en este caso, Argentina. Luego mostramos como utilizando ADM1_NAME podemos filtrar las provincia de Rio Negro y Neuquen. Cuando agregamos esta capa al mapa la llamamos *Comahue*.

En ambos casos se inspeccionan datos utilizando el inspector de Google Earth Engine para ver como están escritos los nombres de provincias, por ejemplo Neuquén no esta con acento.


```javascript
// Definir roi (region de interés o estudio): Prov. Neuquén y Rio Negro
var roi = ee.FeatureCollection("FAO/GAUL/2015/level1")
  .filter(ee.Filter.eq('ADM0_NAME', 'Argentina'))
  .filter(ee.Filter.inList('ADM1_NAME', ['Neuquen', 'Rio Negro']));
```

### Center Object

Centramos nuestra zona de estudio utilizando la función *centerObject*, haciendo variar el nivel de zoom.

La funcion *centerObjet*  es una función que te ayuda a cambiar la etiqueta de zoom. Según la documentación el nivel de zoom es un valor entre 0 y 24,
Podes ajustar el nivel de zoom. Si no ajustás el parametro de zoom, no se ampliará el área que te interesa.

```javascript
Map.addLayer(roi, {}, 'Comahue');
Map.centerObject(roi,5);
```

### Buffer

Sin embargo antes de mostrar la región de estudio al mapa aplicaremos un **buffer de 5 km** para incluir los límites naturales completos que comprenden rios que separan provincias.

Se puede crear un buffer para un *FeatureCollection* usando el método *.map()* para aplicar una operación a cada Feature en el FeatureCollection de unidades administrativas.

```javascript
// Crear un buffer de 5 km (5.000 metros)
var roi = roi.map(function(feature){
  return feature.buffer(5000);
});
Map.addLayer(roi, {}, 'Comahue');
Map.centerObject(roi,5);
```

Esta es nuestra **área de estudio o ROI**.

### Imágenes Sentinel-2

A continuación buscamos Sentinel-2 y seleccionamos las colecciones de imágenes **Harmonized sentinel-2 MSI (MultiSpectralInstrument) level 1-c**

Copiamos su identificación que utilizarmeos en la defición de una colección de imágenes sentinel denominada **image**

Filtraremos imágenes sentinel del año 2024. Utilizaré datos de todo el año 2024, es decir, fecha de comienzo: 1 de enero de 2024 y fecha de finalización el 1 de enero de 2025 (esta ultima fecha se excluye) como podemos comprobar en la documentación de **FilterData**, un método que se aplica a un FeatureColection (**start date** es *inclusive* pero **end date** es *exclusive*).

```javascript
// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01');
```

### Filtro de nubes

Vamos a usar un filtro de nubes aquí. Cuando se utilizan datos satelitales, generalmente hay *contaminación por nubes*. Deseamos utilizar la mayor cantidad posible de imágenes libres de nubes. Entonces, aquí vamos a aplicar un filtro para seleccionar imagens con menos de un 10% de nubes.

Esto se logra utilizando metadatos de la imagen satelital, si consultmaos las *propiedades* de la imagen, existe un atributo: **CLOUDY_PIXEL_PERCENTAGE** 
que representa el Porcentaje de píxeles nublados específicos

Esto nos permite utilizar una imagen de nubes en la que la contaminación de las nubes es menor al 10 %. Podes ajustar este parámetro en función del área de estudio según tu criterio de análisis.

```javascript
// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10));
```

### FilterBounds()

filterBounds(roi) tiene un propósito: Filtrar las imágenes en la colección para incluir solo aquellas que intersectan con la región de interés (roi).
Limita la cantidad de imágenes en la colección para que solo contenga aquellas que cubren total o parcialmente la región definida por roi. Esto reduce el número de imágenes procesadas y mejora la eficiencia.

```javascript
// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10))
  .filterBounds(roi):

```

Lo que necesitamos es una sola imagen. Por lo tanto, necesitamos agregar todas las imágenes mediante un reductor o una agregación estadística. En este caso, vamos a utilizar la mediana. Se suele recomendar la mediana como mínimo mejor que la media. Se evitará incluir algunos valores atípicos, por lo que se utilizará el valor de la mediana. De esta manera, se evita incluir valores atípicos en el proceso de agregación estadística. En este caso, utilizamos la mediana. 


```javascript
// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10))
  .filterBounds(roi)
  .median();

```

Luego recortamos la imagen en función la región de estudio CON clipToCollection(roi). El propósito es recortar la imagen resultante al contorno exacto de la colección o región (roi). Tras combinar las imágenes (en este caso, usando .median()), recorta la salida final para que coincida exactamente con el contorno de roi.

```javascript
// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10))
  .filterBounds(roi)
  .median()
  .clipToCollection(roi);
```
Ahora tenemos una imagen Sentinel más limpia, que está lista para nuestra clasificación de aprendizaje automático.

Vamos a utilizar esta función en la colección de Imágenes Sentinel:

Vamos a aplicar algunos parámetros de filtrado a la colección de imágenes Sentinel.

Para ello utilizaremos la funcion Map que ejecuta la función que definimos anteriormente agregarNDVI a todo feature  del featureColetcion en otras palabaras a toda imagen de la colección de imágenes. 
En otras palabras MAP permite aplicar la función agregarNDVI a cada imagen de la colección de imágenes. 


```javascript

// función para calcular NDVI
var agregarNDVI = function(imagen){
  var ndvi = imagen.normalizedDifference(['B8','B4']).rename('NDVI');
  return imagen.addBands(ndvi);
};

// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10))
  .filterBounds(roi)
  .map(agregarNDVI)
  .median()
  .clipToCollection(roi);
```


```javascript

// función para calcular NDVI
var agregarNDVI = function(imagen){
  var ndvi = imagen.normalizedDifference(['B8','B4']).rename('NDVI');
  return imagen.addBands(ndvi);
};

// Cargar imagen Sentinel
var image = ee.ImageCollection("COPERNICUS/S2_HARMONIZED")
  .filterDate('2024-01-01','2025-01-01')
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 10))
  .filterBounds(roi)
  .map(agregarNDVI)
  .median()
  .clipToCollection(roi);

var visParams = {bands: ['B4','B3','B2'], min:0, max: 3000};
  
Map.addLayer(image, visParams, "Sentinel 2024");

// Map.addLayer(image, {bands: ['B5','B4','B3'], min:0, max: 3000}, 'False Color (543)');

```



Cargamos nuestra imagen, a nuestro mapa con addLayer, si la imagen sentinel no cuenta con parámetros de visualización se verá negra. Ppodemos importar los parámetros de visualización , utilizando la configuración de la capa, seleccionando las 3 bandas RGB, bandas B4, B3 y B2 y un stretch de 98% que permite visualizar los valores de estas tres bandas.

Aplicamos e importamos esta configuración. Hacemos una prueba con estos parámetros y ya visualizamos la colección de imágenes.

En su defecto podemos definir nuestros propiso parámetros con una variable *visParams*, para ello, creamos un parámetro de visualización, 

Aamos a utilizar una composición de colores rojo, verde, azul (RGB). En este caso, vamos a proporcionar las bandas que vamos a utilizar aquí, banda B4, banda B3, banda B2. No las estamos utilizando para nuestro modelo, sino solo para visualizar la imagen en el mapa. 

Y luego un valor mínimo y máximo en función de los datos de reflectancia de la superficie PARA ESTAS BANDAS

```javascript
var visParams = {bands: ['B4','B3','B2'], min: 0 , max: 3000};
Map.addLayer(image, visParams, "Sentinel 2024")
```

## Función para calcular NDVI

En nuestra clasificación aplicando aprendizaje automático utilizaremos valores de reflectancia de ciertas bandas de la imagen Sentinel en conjunto con el índice NDVI. 
Utilizaremos filtros de nubes, y filtros temporales

El NDVI es el índice de vegetación diferencial normalizado que ayuda a monitorear la salud de la vegetación. 
Por lo tanto, un NDVI más alto generalmente indica una cobertura vegetal densa o una vegetación más verde.
Valores negativos son comunes en cuerpos de agua abiertos.
mientras que Valores cercanos a 0 pueden observarse en agua con sedimentos o cubierta parcial por vegetación flotante.




```{figure} imagenes/PosIT-NDVIbis.png
:name: fig-PosIT-NDVIbis
:width: 100%

Indice Espectral NDVI
```

Rangos típicos del NDVI:

Agua: -1 a ~0
    - Valores negativos son comunes en cuerpos de agua abiertos.
    - Valores cercanos a 0 pueden observarse en agua con sedimentos o cubierta parcial por vegetación flotante.
Suelo desnudo: 0 a 0.2
Vegetación escasa: 0.2 a 0.5
Vegetación densa: 0.5 a 1

```{figure} imagenes/valoresNDVI.png
:name: fig-valoresNDVI
:width: 100%

Valores típicos del NDVI
```

Lo primero que debemos hacer es escribir una función que calcule el NDVI. 
Vamos a crear una función denominada agregarNDVI para calcular el NDVI, que vamos a utilizar en nuestra clasificación de aprendizaje automático.

También podríamos utilizar otros índices como:

* NDWI (Índice de Diferencia Normalizada del Agua)
* MNDWI (Índice de Diferencia Normalizada del Agua Modificado)
* AWEI (Índice de Extracción Automática del Agua)
* LSWI (Índice del Agua Basado en Tierra)
* WI (Índice del Agua)


```{figure} imagenes/IndicesAguaBis.png
:name: fig-IndicesAguaBis
:width: 100%

Algunos Indices espectrales en el dominio de aplicación Agua
```

Nuestra función agregarNDVI, tiene un parámetro de entrada imagen. Podemos llamarlo imagen o algún otro nombre significativo.

La primera instrucción es definir la variable NDVI. que es igual a la diferencia normalizada de la banda ocho, la banda infrarroja cercana, y la banda cuatro, la banda roja de la imagen Sentinel. 
Si consultamos la documentación del método normalizedDifference, este metodo se aplica a una imagen. Aca se utiliza la notación orientada a objetos objeto.metodo() el objeto es 1 imagen y el método es normalizedDifference.

consultando la documentaicon de los métodos que se aplican a un objeto, la función normalizedDifference, una función incorporada a Google Earth Engine, calcula la diferencia normalizada entre dos bandas.

Es necesario proporcionar las dos bandas a este método para obtener el índice ndvi calculado.

El método no tiene parámetros obligatorios, cuando los parámetros en la documentación están en letra itálica esto signfiica que los parámetros son opcionales.
Si no se especifican las bandas a utilizar, es decir no se proporcionan parámetros, el método utiliza las dos primeras bandas. 
La diferencia normalizada se calcula como (primero − segundo) / (primero + segundo)

para nuestro ejemplo:
```javascript
(first − second) / (first + second)  = (B8 − B4) / (B8 + B4)
```
es decir 
```javascript
NDVI= (NIR−RED) / (NIR+RED)
```

Asi de simple, es calcular un índice para una imagen satelital. Este acercamiento nos permite definir otros índices como: NDWI, MNDWI, y otros mostrados en la Figura a continuación:


```{figure} imagenes/IndicesAgua.png
:name: fig-IndicesAgua
:width: 100%

Indices Espectrales con el mismo patron de fórmula matemática 
```


Luego, finalmente, la funcion devuelve nuestra imagen, a la cual le agregaremos estas banda calculada con el NDVI. 



Esta función se define para una imagen, luego calculará técnicamente el NDVI para todas las imágenes dentro de la colección de imágenes con la operación MAP. 

Antes de ejecutar este código comprobamos que la colección de imágenes no cuenta inicialmente con este índice NDVI, y que solo luego de ejecutar el código es posible ver esta nuevo dato en cada imagen


```{figure} imagenes/FalsoColor.png
:name: fig-FalsoColor
:width: 50%

Falso Color: Descripción
```

Generaremos una imagen de **falso color** utilizando las bandas B5 (NIR), B4 (Red), y B3 (Green) de Sentinel-2, que nos permitirá detectar agua y generar mas facilmente los puntos de entrenamiento. Esto permite detectar cuerpos de agua de manera efectiva debido a cómo el agua interactúa con la luz en estas longitudes de onda
En un mapa con este esquema de **falso color**:

- Ríos, lagos y océanos: Oscuros o negros.
- Vegetación: Rojo brillante.
- Suelo desnudo o urbano: Varía entre tonos de gris y verde tenue.

Estos son los datos de Sentinel que vamos a utilizar para nuestro aprendizaje automático, es decir, la clasificación supervisada Random forest. 



```javascript
Map.addLayer(image, {bands: ['B5','B4','B3'], min:0, max: 3000}, 'False Color (543)');
```


## Workflow

Para aplicar la tecnica supervisada de random forest tendremos en cuenta el flujo de trabajo


```{figure} imagenes/Workflow.png
:name: fig-Workflow
:width: 100%

Workflow para aplicar ML a imágenes satelitales
```


###  Entrenamiento 

Ahora crearemos datos de entrenamiento y luego ejecutaremos nuestro aprendizaje automático. 

```{figure} imagenes/p_1.png
:name: fig-p_1
:width: 50%

Etapa: Seleccionar las muestras de entrenamiento
```

Vamos a crear los datos de entrenamiento:

Para crear los datos de entrenamiento, utilizando la herramienta de dibujo del panel de Earth Engine. 

Crearemos una nueva capa, denominada agua, de tipo featureColeccion, y defiNImos una propiedad denomninada categoriaAgua, con valor 1.

También crearé otro feature collection denominado NO-AGUA, con una propiedad denominada categoriaAgua, con valor 0.

De esta forma tenemos dos feature collection Agua y NoAgua, con cero features. 

vamos a comenzar a recopilar los datos, primero para el featureCollection agua, vamos a resaltar aquella capa para la cual deseamos capturar datos de entrenamiento.

En este caso, se resaltamos la capa agua. Lo que haremos será recopilar la mayor cantidad posible de pixeles que representen agua y asegurarnos de que estén en nuestro region de estudio.

Vamos a capturar tantos puntos como sea posible aquí y luego lo vamos a cambiar un poco de zonas para obtener muestras espacialmente dispersas y representativas.

Continuamos capturando más puntos:

-----
Esto debería ser suficiente para nuestro feature collection de agua.

-----

Continuamos con nuestro featureCollectoin de no-agua.

podemos caputrar puntos que no sean agua, esto puede ser pixeles de una zona urbana, un cultivo o un bosque. 

Esta es un área agrícola de cultivo marcaeremos algunos putnos.

Podemos ir a alguna ciudad importante solo para capturar pixels de la zona urbana.

Podemos capturar puntos de cobertura vegetal tambien.

Capturar puntos de una zona industrial. . Y estamos a punto de terminar nuestra captura de muestras de no agua.

Estos puntos de control en el terreno, deberían ser suficientes para nuestro análisis. 

Si realmente estás haciendo un análisis científico real o tu proyecto, necesitamos capturar varios puntos de datos representativos y asegurarnos de que la calidad de los puntos de datos sea buena.

Nuestro próximo paso es combinar ambos featureCollection en un solo featureCollection, esto  lo hace la función MERGE.
fusiona AMBAS CAPAS: AGUA Y NO AGUA EN UNA SOLA COLECCION denominada training

La fusión o merge, combinó estos dos colecciones en una, la cual contiene tiene aproximadamente xxxx puntos de datos de entrenamiento, PUNTOS DE clase agua y DE clase no agua.

-------------------------------------------------------------------------------
Muy bien. Ahora  estamos listos para nuestra clasificación de aprendizaje automático

La etiqueta es CategoriaAgua y representa nuestra etiqueta para la clasificación de aprendizaje automático o la clasificación supervisada. 
Recorda que la definimos cuando creamos los dos featureCollection de datos de entrenamiento agua y no-agua. 
Esa será la etiqueta para nuestra clasificación.

Vamos a utilizar para el entrenamiento las bandas dos, tres, cuatro, ocho y NDVI de la imagen del satélite Sentinel.
Extraemos las bandas Sentinel para estos datos de entrenamiento que hemos capturado.


```{figure} imagenes/Tabla_Comahue.png
:name: fig-Tabla_Comahue
:width: 100%

Variables Independientes y Dependientes
```


Para obtener estos datos de la imagen utilizamos la función sampleRegions
vamos a utilizar la resolución de 10 metros, la resolución más alta posible de los datos de Sentinel-2. 

SampleRegions permite definir la tabla que entrena el modelo de clasificación supervisada.
los parámetro de esta función son un diccionario de JavaScript que incluye:

En collection especificamos la coleccion de imagenes Sentinel con las bandas seleccionadas 
En properties indicamos la etiqeuta que deseamos predecir, en este caso categoriaAgua, que representa la propiedad o etiqueta para nuestro entrenamiento.
En scale indicamos la escala es la resolución espacial, que es de 10 metros, porque estamos usando las bandas Sentinel con una resolución de 10 metros. 

Podemos imprimir la tabla de entrenamiento que vamos a utilizar para la clasificación de aprendizaje automático. 
Contamos con el valor de las bandas Sentinel seleccionadas, la banda NDVI y la etiqueta CategoriaAgua.

necesitamos separar algunos de los datos para entrenar el modelo y también algunos de los datos para evaluarlo. 

Por lo tanto, dividiremos esos datos: crearemos una columna aleatoria y luego solo el 70 % de los datos se utilizarán para entrenar nuestro modelo.
 Y luego, el conjunto de prueba aquí será la evaluación, que es el 30 % de datos restantes.


### Entrenamiento

```{figure} imagenes/p_2.png
:name: fig-p_2
:width: 50%

Etapa Entrenamiento del Clasificador 
```

En este paso entrenemos el modelo para luego aplicarlo a la imagen con el fin de generar un mapa de masa de agua. 

El paso de entrenar el modelo consiste en aplicar la clasificación supervisada de random forest con los datos de entremiento

Tendrás features, que son datos de entrenamiento, que es el conjunto de entrenamiento, 
luego una etiqueta, necesita una etiqueta para la clasificación. 
y luego, finalmente, las bandas, que usamos para hacer la predicción, que son las bandas Sentinel.

Entonces, cuando ejecutes esto, entrenarás el modelo de aprendizaje automático. 
Cuando lo haga, el modelo de aprendizaje automático se entrenará en función de nuestros datos de entrenamiento en las bandas Sentinel. 


```javascript
// Crear los datos de entrenamiento
var training = agua.merge(noAgua);
print('FeatureCollection Training:', training);
print('size of Training:', training.size());

var label = 'categoriaAgua';
var bands = ['B2', 'B3', 'B4', 'B8','NDVI'];
var input = image.select(bands);

// Superponer los puntos sobre la imagen para obtener datos completos de entrenamiento

var trainImage = input.sampleRegions({
  collection: training,
  properties: [label],
  scale: 10
});


// separar de los datos de entrenamiento
// 80% para entrenar el modelo y 20% para test

var trainingData = trainImage.randomColumn();
var trainSet = trainingData.filter(ee.Filter.lessThan('random', 0.8));
var testSet = trainingData.filter(ee.Filter.greaterThanOrEquals('random', 0.8));

// print('size of trainingData', trainingData.size());
// print('size of trainSet', trainSet.size());
// print('size of testSet', testSet.size());

// Modelo de Clasificación
var classifier = ee.Classifier.smileRandomForest(30)
   .train({
     features: trainSet,
     classProperty: label,
     inputProperties: bands
   });
   
// Classify the Image
var classifiedImage = input.classify(classifier);

```


```{figure} imagenes/Tabla_1.png
:name: fig-Tabla_1
:width: 100%

Tabla de variables independientes y dependientes
```


### Clasificar la imagen

```{figure} imagenes/p_3.png
:name: fig-p_3
:width: 50%

Etapa Clasificar la imagen
```
El siguiente paso es aplicar este modelo, que es el clasificador, a la imagen, la imagen Sentinel.
Simplemente se va a aplicar el modelo para generar una clasificación.

Ahora, podemos ver la imagen, que es una clasificación.
El siguiente paso es ver nuestra imagen clasificada en una pantalla aquí. 

```javascript
// Modelo de Clasificación
var classifier = ee.Classifier.smileRandomForest(30)
   .train({
     features: trainSet,
     classProperty: label,
     inputProperties: bands
   });
   
// Classify the Image
var classifiedImage = input.classify(classifier);

```

### MASCARA:
en el comando: var water = classifiedImage.updateMask(classifiedImage);

La función updateMask utiliza los valores de classified como una máscara. Los píxeles con valor 1 (agua) permanecen visibles, mientras que los píxeles con valor 0 (no agua) se enmascaran (ocultan).

Esto se traduce en:

Si un píxel tiene valor 1 → Se mantiene visible en la nueva variable water.
Si un píxel tiene valor 0 → Se vuelve invisible en water.


```javascript
var water = classifiedImage.updateMask(classifiedImage);
```


Crear un parámetro de visualización: cuyo parámetros son el mínimo y el máximo. Por lo tanto, se trata simplemente de un binario de cero y uno.
El azul es para la clase de agua y el blanco para la clase de no agua y luego agregaremos la capa al mapa.

Agregaremos la capa al mapa, pasaremos la imagen clasificada aquí, los parámetros de visualización y la llamaremos agua. 
Entonces, simplemente ejecutemos eso. Y cuando lo ejecutemos, veremos nuestra clase de agua.

Esta es nuestra clase de agua, que se superpone a la imagen de Sentinel. Y esa es nuestra clase de agua basada en el aprendizaje automático en la imagen de Sentinel.
Esta es nuestra clasificación. Se trata de una clasificación predicha por un modelo basado en el aprendizaje automático, 
específicamente en un algoritmo de random forest o bosque aleatorio en la plataforma Earth Engine que utiliza datos satelitales Sentinel2 de 10 metros.

```javascript

var visParams = {min: 0, max: 1, palette: ['white','blue']};
Map.addLayer(water, visParams, 'Water');
```

### Acurracy Assessment


Como hemos mencionado en anteriores videos, hay algunos pasos después de este posprocesamiento y tenemos que calcular evaluaciones de precisión de estos datos, 

Utilizaremos la función de matriz de confusión.
Entonces, vamos a utilizar el conjunto de prueba y luego el clasificador. Este es nuestro modelo, el modelo de entrenamiento. 
Utilizamos los datos del conjunto de prueba, que están configurados para la evaluación.

El primero es el valor real, que es el valor observado en función de este conjunto de pruebas, Y el siguiente es el valor previsto, que es la clasificación. 
Una vez que se proporcionan estos parámetros, se va a crear una evaluación de precisión, es decir, valores. 

```{figure} imagenes/p_4.png
:name: fig-p_4
:width: 50%

Etapa Evaluación de Precisión
```

```javascript
// Accuracy Assessment
// Obtener la matriz de confusión
// Accuracy Assessment
// Obtener la matriz de confusión

var confusionMatrix = ee.ConfusionMatrix(testSet.classify(classifier)
   .errorMatrix({
     actual: 'categoriaAgua',
     predicted: 'classification'
   }));
   
print('Confusion Matrix:', confusionMatrix);
// Obtener otras métricas 
print('Overall Accuracy:', confusionMatrix.accuracy());
print('Producers Accuracy:', confusionMatrix.producersAccuracy());
print('Consumers Accuracy:', confusionMatrix.consumersAccuracy());

// Exportación de la clasificación

Export.image.toDrive({
  image: water,
  description: 'Proceso_de_Exportacion',
  scale: 10,
  region: roi, //aca podemos cambiar la region de exportación
  maxPixels: 1e13, // Aumenta el límite al máximo
  fileFormat: 'GeoTIFF' //GeoTIFF es el predeterminado
});

```


Algunos de estos valores incluyen una matriz de confusión, una tabla y también la precisión general, es decir, la precisión del productor y la precisión del consumidor.

Básicamente, nos limitaremos a observar la precisión general y ejecutaremos la prueba basándonos en estos XXX puntos de datos aproximadamente. 
Veamos cuál es nuestra precisión. 
Parece que es 100 %, bastante buena, pero creo que si capturamos más puntos de datos, la razón por la que esto es 100 % cierto es que:

* A, el agua es bastante fácil de predecir, 
* B, hemos utilizado la resolución de 10 metros, que también es otro factor.

### Exportación

La última parte a considerar, es que si deseas usar esta clasificación fuera de Google Earth Engine, digamos un software GIS estándar, como QGIS o ArcGIS 
vas a usar la función Export.image.toDrive para exportar, ya sabes, tu mapa de agua.

Cuando lo ejecutes, lo verás en tu Google Drive. Una vez que hayas completado esto, verás los datos o la imagen en tu Google Drive. 
Luego, lo descargarás desde Google Drive a tu equipo local y lo abrirás en un software de GIS estándar.

```javascript

// Exportación de la clasificación
Export.image.toDrive({
  image: water,
  description: 'Proceso_de_Exportacion',
  scale: 10,
  region: roi, //aca podemos cambiar la region de exportación
  maxPixels: 1e13, // Aumenta el límite al máximo
  fileFormat: 'GeoTIFF' //GeoTIFF es el predeterminado
});

```


Así es como se aplica la clasificación de aprendizaje automático, los datos satelitales en este caso Sentinel en la API de Google Earth Engine Cloud y se realiza con estos pasos generales:

* captura tus propios datos de entrenamiento, 
* desarrolla un modelo de aprendizaje automático, 
* aplicas el modelo para clasificar la imagen de la region de estudio
* estudias el rendimiento de tu modelo y 
* también exporta tu clasificación final a tu Google Drive. 

## Mostrar el árobol de decisión luego de aplicar CART

Se puede aplicar el método CART en lugar de Random Forest para obtener el árbol de decisión en formato dot. El siguiente código muestra el árbol de decisión obtenido:

```python
!apt-get install graphviz
!pip install graphviz

# Instalar dependencias adicionales para la conversión de PDF a imagen
!apt-get -qq install -y poppler-utils
!pip install pdf2image


# Importar las librerías necesarias
import graphviz
from google.colab import files
from pdf2image import convert_from_path
import matplotlib.pyplot as plt

# Definir las reglas del árbol de decisión en formato DOT
dot_data = """
digraph DecisionTree {
 node [shape=box, style="filled, rounded", color="black", fontname=helvetica];
 edge [fontname=helvetica];
 0 [label=<B8 &le; 969.0000<br/>score = 0.4649>, fillcolor="#00000000"];
 1 [label=<class = 1>, fillcolor="#00000000", shape=ellipse];
 0 -> 1 [labeldistance=2.5, labelangle=45, headlabel="True"];
 2 [label=<class = 0>, fillcolor="#00000000", shape=ellipse];
 0 -> 2 [labeldistance=2.5, labelangle=-45, headlabel="False"];
}
"""

# Crear el gráfico a partir del DOT
graph = graphviz.Source(dot_data)

# Renderizar el árbol de decisión en formato PDF
graph.render('decision_tree', format='pdf', cleanup=False)

# Convertir el PDF a imagen
images = convert_from_path('decision_tree.pdf')

# Mostrar la imagen del árbol de decisión
plt.imshow(images[0])
plt.axis('off')  # Desactivar los ejes para que solo se vea la imagen
plt.show()
```

## Video del capítulo

Podes mirar el video asociado a este capítulo en el canal de youtube de IDERA: https://www.youtube.com/watch?v=-DMd6LXIEkE&t=610s


