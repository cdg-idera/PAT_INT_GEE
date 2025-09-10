# Predecir Superficies de Agua

## Introducción

En este capítulo vamos a aplicar aprendizaje automático a imágenes satelitales para predecir superficies de agua. Utilizaremos datos satelitales de Sentinel-2 para procesar una colección de imágenes correspondientes a una región de estudio y aplicaremos un modelo de Random Forest para clasificar los píxeles en dos categorías: agua y no agua.

En google Earth Engine, podes explorar imagenes SENTINEL haciendo click en DATASET para acceder al catálogo de datos y luego hacer en la opción de menú Sentinel y encontrarás todos los productos satelitales Sentinel. 

Sentinel-2 es propiedad de la Agencia Europea de Satélites ( European Space Agency ESA), en Google eart Engen tenemos varios conjuntos de imágenes satelitales Sentinel: Sentinel-1, Sentinel-2, Sentinel-3 y Sentinel-5.

Nos vamos a centrar en Sentinel-2, cuyos datos son muy populares. 

Se aplican en diversas áreas de monitoreo de la superficie terrestre, incluida la gestión de desastres, la gestión de emergencias, la cuantificación de la tendencia de urbanización y la cobertura del suelo, entre otras.

-------------------------------------------------------------------------------
Bien CREAMOS UN NUEVO SCRIPT denominado A001_RandomForest_CuerposDeAguaComahue

Nuestra área de estudio será la región del Comahue principalmente la provincia del Neuquén y la de Rio negro
vamos a crear nuestra región de estudio, nuestraregión de interés o ROI. 

Como no estamos interesados en los límites provinciales precisos de la provincia, debido a que queremos abarcar los rios que componen los limites naturales provinciales, 
no utilizaremos los límites administrativos exactos disponibles en la IDE de la provincia de RN y Nqn, o en el Instituto Geográfico Nacional
sino que utilizaremos el conjunto de datos de Capas de Unidades Administrativas Globales de Google eart engen

Esto nos permitirá aprender un poco más sobre este conjunto de datos de Unidades Administrativas Globales: 

<<buscar: Global Administrative Unit Layers>>
FAO GAUL: Global Administrative Unit Layers 2015, First-Level Administrative Units

Las Capas de Unidades Administrativas Globales (GAUL) recopilan y difunden la mejor información disponible sobre unidades administrativas para todos los países del mundo, contribuyendo a la estandarización del conjunto de datos espaciales que representan las unidades administrativas.

Llamando a esta colección de imágenes, tendremos los límites de administración para todos los países.

<<ee.FeatureCollection("FAO/GAUL/2015/level1")>>

Si visualizamos el esquema de tabla de este conjunto de datos, existen algunos atributos que utilizaremos para filtrar:
ADM0_CODE tiene el codigo de pais, y ADM0_NAME tiene el nombre de pais,
ADM1_CODE tiene el code of unidades administrativas de primer nivel y ADM1_NAME tiene Name of unidades administrative de primer nivel

Utilizando ADM0_NAME  filtraremos Argentina y utilizando y ADM1_NAME filtraremos la unidad administrativa de la provincia del neuquen: 

Primero mostramos como ADM0_NAME nos permite filtrar por nombre de país en este caso, Argentina

Luego mostramos como utilizando ADM1_NAME podemos filtrar las provincia de Rio Negro y Neuquen.
Cuando agregamos esta capa al mapa la llamamos Comahue

En ambos casos se inspeccionan datos utilizando el inspector de Google Earth Engen para ver
como están escritos los nombres de provincias, por ejemplo Neuquén no esta con acento.

-------------------------------------------------------------------------------
CENTER OBJECT

Centramos nuestra zona de estudio utilizando la función centerObject, haciendo variar el nivel de zoom.

La funcion centerObjet  es una función que te ayuda a cambiar la etiqueta de zoom.
Segun la documentacion el nivel de zoom es un valro entre 0 y 24,
Podes ajustar el nivel de zoom.
Si no ajustás el parametro de zoom, no se ampliará el área que te interesa.

var roi = ee.FeatureCollection("FAO/GAUL/2015/level1")
  .filter(ee.Filter.eq('ADM0_NAME', 'Argentina'))  // Filtrar por país
  .filter(ee.Filter.inList('ADM1_NAME', [
    'Neuquen']));


-------------------------------------------------------------------------------

BUFFER:

Aplicaremos un buffer de 5 km para incluir los límites naturales completos que comprenden rios que separan provincias.

Se puede crear un buffer para un FeatureCollection usando el método .map() para aplicar una operación a cada Feature en el FeatureCollection de unidades administrativas.

// Crear un buffer de 10 km (10,000 metros)
var bufferedRoi = roi.map(function(feature) {
  return feature.buffer(10000); // Agregar buffer de 10 km
});


Esta es nuestra área de estudio.
-------------------------------------------------------------------------------

IMAGENES SENTINEL 2:

A continuación buscamos Sentinel-2 y seleccionamos las colecciones de imágenes 
Harmonized sentinel-2 MSI (MultiSpectralInstrument) level 1-c

Copiamos su identificación que utilizarmeos en la deficion de una colección de imágenes sentinel denominada IMAGE

-------------------------------------------------------------------------------
Filtraremos imágenes sentinel del año 2024, utilizaré datos de todo el año 2024, es decir, fecha de comienzo: 1 de enero de 2024 y fecha de finalización el 1 de enero de 2025 
(esta ultima fecha se excluye) como podemos comprobar en la documentación de FilterData
un método que se aplica a un FeatureColection (start date es inclusive pero end date es exclusive).

-------------------------------------------------------------------------------
Vamos a usar un filtro de nubes aquí. Cuando se utilizan datos satelitales, generalmente hay contaminación por nubes. 
Deseamos utilizar la mayor cantidad posible de imágenes libres de nubes. Entonces, aquí vamos a aplicar un filtro para seleccionar imagens con menos de un 10% de nubes.

Esto se logra utilizando metadatos de la imagen satelital , si consultmaos las propiedadesde la imagen, existe un atributo: CLOUDY_PIXEL_PERCENTAGE 
que representa el Porcentaje de píxeles nublados específicos

esto  nos permite utilizar una imagen de nubes en la que la contaminación de las nubes es menor al 10 %. Podes ajustar este parámetro en función del área de estudio según tu criterio de análisis.

-------------------------------------------------------------------------------
filterBounds(roi) tiene un propósito: Filtrar las imágenes en la colección para incluir solo aquellas que intersectan con la región de interés (roi).
Limita la cantidad de imágenes en la colección para que solo contenga aquellas que cubren total o parcialmente la región definida por roi. Esto reduce el número de imágenes procesadas y mejora la eficiencia.

-------------------------------------------------------------------------------

Lo que necesitamos es una sola imagen. Por lo tanto, necesitamos agregar todas las imágenes mediante un reductor o una agregación estadística. En este caso, vamos a utilizar la mediana.
Se suele recomendar la mediana como mínimo mejor que la media. Se evitará incluir algunos valores atípicos, por lo que se utilizará el valor de la mediana.
De esta manera, se evita incluir valores atípicos en el proceso de agregación estadística. En este caso, utilizamos la mediana. 

-------------------------------------------------------------------------------
Luego recortamos la imagen en función la región de estudio CON clipToCollection(roi)
eL Propósito ES RecortaR la imagen resultante al contorno exacto de la colección o región (roi).
Tras combinar las imágenes (en este caso, usando .median()), recorta la salida final para que coincida exactamente con el contorno de roi.

-------------------------------------------------------------------------------
Ahora tenemos una imagen Sentinel más limpia, que está lista para nuestra clasificación de aprendizaje automático.
aHORA VAMOS A UTILIZAR ESTA FUNCIÓN EN LA COLECCION DE IMAGENES sENTINEL


Vamos a aplicar algunos parámetros de filtrado a la colección de imágenes Sentinel.

PARA ELLO utilizarEMOS la funcion Map que ejecuta la función que definimos anteriormente agregarNDVI a todo feature  del featureColetcion en otras palabaras a toda imagen de la colección de imágenes. 
En otras palabras MAP permite aplicar la función agregarNDVI a cada imagen de la colección de imágenes. 


-------------------------------------------------------------------------------


CargaMOS nuestra imagen, a nuestro mapa con addLayer, si la imagen sentinel no cuenta con parámetros de visualización se verá negra, 
podemos importar los parámetros de visualización , utilizando la configuración de la capa, seleccionando las 3 bandas RGB, bandas 4,3 y2 y un stretch de 98% 
que permite visualizar los valores de estas tres bandas,

Aplicamos e importamos esta configuración.
hacemos una prueba con estos parámetros y ya visualizamos la colección de imágenes.

En su defecto definir nuestros propiso parámetros con una variable visParams
para ello, creamos un parámetro de visualización, 

vamos a utilizar una composición de colores rojo, verde, azul (RGB). 
En este caso, vamos a proporcionar las bandas que vamos a utilizar aquí, banda 4, banda 3, banda 2. 
No las estamos utilizando para nuestro modelo, sino solo para visualizar la imagen en el mapa. 
Y luego un valor mínimo y máximo en función de los datos de reflectancia de la superficie PARA ESTAS BANDAS

var visParams = {bands: ['B4','B3','B2'], min: 0 , max: 3000};
Map.addLayer(image, visParams, "Sentinel 2024")

-------------------------------------------------------------------------------

-------------------------------------------------------------------------------

FUNCIÓN PARA CALCULAR NDVI

En nuestra clasificación aplicando aprendizaje automático utilizaremos valores de reflectancia de ciertas bandas de la imagen Sentinel en conjunto con el índice NDVI. 
Utilizaremos filtros de nubes, y filtros temporales

El NDVI es el índice de vegetación diferencial normalizado que ayuda a monitorear la salud de la vegetación. 
Por lo tanto, un NDVI más alto generalmente indica una cobertura vegetal densa o una vegetación más verde.
Valores negativos son comunes en cuerpos de agua abiertos.
mientras que Valores cercanos a 0 pueden observarse en agua con sedimentos o cubierta parcial por vegetación flotante.


Rangos típicos del NDVI:

Agua: -1 a ~0
    - Valores negativos son comunes en cuerpos de agua abiertos.
    - Valores cercanos a 0 pueden observarse en agua con sedimentos o cubierta parcial por vegetación flotante.
Suelo desnudo: 0 a 0.2
Vegetación escasa: 0.2 a 0.5
Vegetación densa: 0.5 a 1

Lo primero que debemos hacer es escribir una función que calcule el NDVI. 
Vamos a crear una función denominada agregarNDVI para calcular el NDVI, que vamos a utilizar en nuestra clasificación de aprendizaje automático.

También podríamos utilizar otros índices como:
NDWI (Índice de Diferencia Normalizada del Agua)
MNDWI (Índice de Diferencia Normalizada del Agua Modificado)
AWEI (Índice de Extracción Automática del Agua)
LSWI (Índice del Agua Basado en Tierra)
WI (Índice del Agua)

Nuestra función agregarNDVI, tiene un parámetro de entrada imagen. Podemos llamarlo imagen o algún otro nombre significativo.

La primera instrucción es definir la variable NDVI. que es igual a la diferencia normalizada de la banda ocho, la banda infrarroja cercana, y la banda cuatro, la banda roja de la imagen Sentinel. 
Si consultamos la documentación del método normalizedDifference, este metodo se aplica a una imagen. Aca se utiliza la notación orientada a objetos objeto.metodo() el objeto es 1 imagen y el método es normalizedDifference.

consultando la documentaicon de los métodos que se aplican a un objeto, la función normalizedDifference, una función incorporada a Google eart engen, calcula la diferencia normalizada entre dos bandas.

Es necesario proporcionar las dos bandas a este método para obtener el índice ndvi calculado.

El método no tiene parámetros obligatorios, cuando los parámetros en la documentación están en letra itálica esto signfiica que los parámetros son opcionales.
Si no se especifican las bandas a utilizar, es decir no se proporcionan parámetros, el método utiliza las dos primeras bandas. 
La diferencia normalizada se calcula como (primero − segundo) / (primero + segundo)

para nuestro ejemplo:
(first − second) / (first + second)  = (B8 − B4) / (B8 + B4)
es decir 
NDVI= (NIR−RED) / (NIR+RED)

Asi de simple, es calcular un índice para una imagen satelital. Este acercamiento nos permite definir otros índices como: NDWI, MNDWI, LSWI

Luego, finalmente, la funcion devuelve nuestra imagen, a la cual le agregaremos estas banda calculada con el NDVI. 


-------------------------------------------------------------------------------

Esta función se define para una imagen, luego calculará técnicamente el NDVI para todas las imágenes dentro de la colección de imágenes con la operación MAP. 

Antes de ejecutar este código comprobamos que la colección de imágenes no cuenta inicialmente con este índice NDVI, y que solo luego de ejecutar el código es posible ver esta nuevo dato en cada imagen

-------------------------------------------------------------------------------

Generaremos una imagen de FALSO COLOR ,  utilizando las bandas B5 (NIR), B4 (Red), y B3 (Green) del Sentinel-2 ,  que nos permitirá detectar agua y generar mas facilmente los puntos de entrenamiento

Estoi permite detectar cuerpos de agua de manera efectiva debido a cómo el agua interactúa con la luz en estas longitudes de onda
En un mapa con este esquema de falso color:

- Ríos, lagos y océanos: Oscuros o negros.
- Vegetación: Rojo brillante.
- Suelo desnudo o urbano: Varía entre tonos de gris y verde tenue.


Estos son los datos de Sentinel que vamos a utilizar para nuestro aprendizaje automático, es decir, la clasificación supervisada Random forest. 

-------------------------------------------------------------------------------
WORKFLOW:
Para aplicar la tecnica supervisada de random forest tendremos en cuenta el flujo de trabajo

-------------------------------------------------------------------------------
ENTRENAMIENTO:

Ahora crearemos datos de entrenamiento y luego ejecutaremos nuestro aprendizaje automático. 

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

-------------------------------------------------------------------------------
ENTRENAMIENTO:

En este paso entrenemos el modelo para luego aplicarlo a la imagen con el fin de generar un mapa de masa de agua. 

El paso de entrenar el modelo consiste en aplicar la clasificación supervisada de random forest con los datos de entremiento

Tendrás features, que son datos de entrenamiento, que es el conjunto de entrenamiento, 
luego una etiqueta, necesita una etiqueta para la clasificación. 
y luego, finalmente, las bandas, que usamos para hacer la predicción, que son las bandas Sentinel.

Entonces, cuando ejecutes esto, entrenarás el modelo de aprendizaje automático. 
Cuando lo haga, el modelo de aprendizaje automático se entrenará en función de nuestros datos de entrenamiento en las bandas Sentinel. 

-------------------------------------------------------------------------------
Clasificar la imagen:

El siguiente paso es aplicar este modelo, que es el clasificador, a la imagen, la imagen Sentinel.
Simplemente se va a aplicar el modelo para generar una clasificación.

Ahora, podemos ver la imagen, que es una clasificación.
El siguiente paso es ver nuestra imagen clasificada en una pantalla aquí. 
-------------------------------------------------------------------------------

MASCARA:
en el comando: var water = classifiedImage.updateMask(classifiedImage);

La función updateMask utiliza los valores de classified como una máscara. Los píxeles con valor 1 (agua) permanecen visibles, mientras que los píxeles con valor 0 (no agua) se enmascaran (ocultan).

Esto se traduce en:

Si un píxel tiene valor 1 → Se mantiene visible en la nueva variable water.
Si un píxel tiene valor 0 → Se vuelve invisible en water.


crear un parámetro de visualización: cuyo parámetros son el mínimo y el máximo. Por lo tanto, se trata simplemente de un binario de cero y uno.
El azul es para la clase de agua y el blanco para la clase de no agua y luego agregaremos la capa al mapa.

Agregaremos la capa al mapa, pasaremos la imagen clasificada aquí, los parámetros de visualización y la llamaremos agua. 
Entonces, simplemente ejecutemos eso. Y cuando lo ejecutemos, veremos nuestra clase de agua.

Esta es nuestra clase de agua, que se superpone a la imagen de Sentinel. Y esa es nuestra clase de agua basada en el aprendizaje automático en la imagen de Sentinel.
Esta es nuestra clasificación. Se trata de una clasificación predicha por un modelo basado en el aprendizaje automático, 
específicamente en un algoritmo de random forest o bosque aleatorio en la plataforma Earth Engine que utiliza datos satelitales Sentinel2 de 10 metros.

-------------------------------------------------------------------------------

ACCURRACY ASSESSMENT

Como hemos mencionado en anteriores videos, hay algunos pasos después de este posprocesamiento y tenemos que calcular evaluaciones de precisión de estos datos, 

Utilizaremos la función de matriz de confusión.
Entonces, vamos a utilizar el conjunto de prueba y luego el clasificador. Este es nuestro modelo, el modelo de entrenamiento. 
Utilizamos los datos del conjunto de prueba, que están configurados para la evaluación.

El primero es el valor real, que es el valor observado en función de este conjunto de pruebas, Y el siguiente es el valor previsto, que es la clasificación. 
Una vez que se proporcionan estos parámetros, se va a crear una evaluación de precisión, es decir, valores. 

Algunos de estos valores incluyen una matriz de confusión, una tabla y también la precisión general, es decir, la precisión del productor y la precisión del consumidor.

Básicamente, nos limitaremos a observar la precisión general y ejecutaremos la prueba basándonos en estos XXX puntos de datos aproximadamente. 
Veamos cuál es nuestra precisión. 
Parece que es 100 %, bastante buena, pero creo que si capturamos más puntos de datos, la razón por la que esto es 100 % cierto es que:

- A, el agua es bastante fácil de predecir, 
- B, hemos utilizado la resolución de 10 metros, que también es otro factor.

-------------------------------------------------------------------------------

EXPORTACION

La última parte a considerar, es que si deseas usar esta clasificación fuera de Google eart engen , digamos un software GIS estándar, como QGIS o ArcGIS 
vas a usar la función Export.image.toDrive para exportar, ya sabes, tu mapa de agua.

Cuando lo ejecutes, lo verás en tu Google Drive. Una vez que hayas completado esto, verás los datos o la imagen en tu Google Drive. 
Luego, lo descargarás desde Google Drive a tu equipo local y lo abrirás en un software de GIS estándar.

Así es como se aplica la clasificación de aprendizaje automático, los datos satelitales en este caso Sentinel en la API de Google Earth Engine Cloud y se realiza, ya sabes, 
captura tus propios datos de entrenamiento, 
desarrolla un modelo de aprendizaje automático, 
aplicas el modelo para clasificar la imagen de la region de estudio
estudias el rendimiento de tu modelo y 
también exporta tu clasificación final a tu Google Drive. 

-------------------------------------------------------------------------------
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