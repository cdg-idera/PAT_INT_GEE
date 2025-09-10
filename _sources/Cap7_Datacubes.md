# Cubos de Imágenes Satelitales

# Introducción

🎬 ¡Seguimos explorando las Series Temporales aplicadas a la Información Geoespacial! 🌍📊
En este nuevo episodio, damos un paso más: introducimos los cubos de datos 🧊🛰️ como estructura clave para organizar, analizar y visualizar imágenes satelitales a lo largo del tiempo.

🚀 A partir de la misión Sentinel-3 de la Agencia Espacial Europea, repasamos conceptos esenciales como bandas espectrales, índices y, sobre todo, definimos qué es un cubo de datos geoespaciales, cuáles son sus dimensiones (espacio, tiempo, espectro, categorías) y por qué resultan fundamentales para el análisis ambiental y territorial.

💡 Para ilustrar su utilidad, presentamos tres casos prácticos donde aplicamos cubos de datos a variables reales:

🔷 Ejemplo 1: Cubo de temperatura superficial MODIS 🌡️
Creamos un cubo de datos mensual para Bahía Blanca con imágenes MODIS (2024-2025). Calculamos promedios mensuales, generamos una serie temporal y exportamos los resultados en CSV para analizarlos en Colab. Detectamos tendencias, estacionalidad y anomalías térmicas —como la registrada en marzo de 2025.
📓 Notebook: Lab_007_ST_BahiaBlanca.ipynb

🔷 Ejemplo 2: Cubo de precipitación con CHIRPS 🌧️📉
Construimos un cubo de datos de precipitación mensual para la misma región, utilizando la colección CHIRPS. Visualizamos capas en el mapa, graficamos valores mensuales y detectamos eventos extremos, como lluvias anómalas en marzo de 2025. Gráficos y tablas complementan el análisis en consola.

🔷 Ejemplo 3: Cubo de clasificación de suelo con Sentinel-2 y Random Forest 🌾🏙️
Generamos un cubo categórico bimestral de tipos de suelo (agua, urbano, cultivos, etc.) para 2024. A partir de imágenes Sentinel-2 y un modelo Random Forest, construimos una serie temporal clasificada y mostramos su evolución en gráficos apilados. Incluimos métricas de validación, porcentajes y recuentos por clase para interpretar cambios en el paisaje, como expansión urbana o variación en áreas verdes.


# De imágenes satelitales a Cubos de Imágenes

Al observar una imagen tomada desde un satélite, es tentador pensar que estamos viendo una simple foto de la Tierra como si fuera una fotografía aérea en alta resolución, pero eso sería una visión limitada, sería reducirla a lo que nuestros ojos alcanzan a ver, cuando en realidad hay mucho más que eso. 
En verdad, lo que capturan los satélites no es una simple imagen como una fotografía convencional, sino un conjunto de bandas espectrales, cada una correspondiente a una distinta longitud de onda del espectro electromagnético
Estas bandas, combinadas, forman una representación detallada y multidimensional de lo que ocurre en la superficie terrestre. Pero la información no termina acá, las bandas pueden ser utilizadas individualmente o combinadas mediante índices espectrales, que son fórmulas matemáticas diseñadas para resaltar ciertos fenómenos, como la vegetación, el agua, los suelos y las áreas urbanas, en función de su comportamiento espectral. El catálogo absam spectral indecis, reúne más de 250 de estos índices organizados por dominio de aplicación, lo que lo convierte en una herramienta clave para interpretar y extraer información significativa de las imágenes satelitales. El cumulo de cientos de índices de este catálogo puede ser accedido desde distintos paquetes en lenguajes d eprogramacion como javascript, Python, R y julia.
Un excelente ejemplo de esta riqueza espectral es la misión Sentinel-2, operada por la Agencia Espacial Europea. Esta misión no toma simplemente una foto del planeta, sino que registra 13 bandas espectrales distintas, distribuidas en diferentes partes del espectro electromagnético y con distintas resoluciones espaciales 10, 20 y 60 metros, cada una con un propósito específico.

Estas bandas incluyen banda 1 aerosol captura información en la región del violeta, útil para la corrección atmosférica y detección de aerosoles, resolución 60 metros, 
banda 2 azul, banda 3 verde y banda 4 rojo, bandas visibles al ojo humano que permiten generar imágenes en color natural, resolución 10 metros, 
banda 5, 6 y 7, conocidas como red edge, estas bandas cubren la transición entre el rojo y el infrarrojo cercano, son fundamentales para estudios avanzados de vegetación y agricultura de precisión, resolución 20 metros, 
banda 8 y banda  8a, bandas en el infrarrojo cercano, claves para evaluar la salud de la vegetación, calcular índices como el NDVI y detectar cambios en la biomasa, resolución 10 metros, banda 8 y 20 metros en banda 8a,  
banda 9, vapor de agua, útil para corregir efectos atmosféricos asociados al vapor de agua, resolución 60 metros, 
banda 10, cirrus, diseñada específicamente para la detección de nubes cirrus que pueden interferir en el análisis de otras bandas, resolución 60 metros, 
banda 11 y banda 12, SWIR 1 y SWIR 2, bandas en el infrarrojo de onda corta, útiles para analizar contenido de  humedad del suelo, nieve, hielo o detectar áreas afectadas por incendios, resolución 20 metros, 
Cada una de estas bandas aporta una capa distinta de información. Y al combinar esta riqueza espectral con series temporales, índices matemáticos e inteligencia artificial, podemos transformar simples imágenes en verdaderos análisis dinámicos del territorio.
Entonces, lo que vemos como una imagen, en realidad, es una colección multidimensional de datos espectrales. Cada píxel tiene múltiples valores asociados, uno por cada banda, revelando propiedades biofísicas que no podríamos captar con nuestros sentidos. Es como si viéramos el mundo con “ojos científicos”.
Y esto ocurre para cada punto de la Tierra observado por la misión. Pero además, estos puntos no se observan una única vez…
🎬 Parte 2 – Revisitando la Tierra: frecuencia y volumen de datos en Sentinel 2
Sentinel 2 no solo es poderosa por la cantidad de bandas que captura…
Lo verdaderamente extraordinario es su capacidad para volver a mirar, una y otra vez, el mismo lugar del planeta con una frecuencia difícil de igualar.
Durante años, esta misión estuvo compuesta por dos satélites gemelos: Sentinel 2A y Sentinel 2B, operando en la misma órbita para cubrir la superficie terrestre con alta frecuencia. Pero en septiembre de 2024 se produjo un hito importante: el lanzamiento de Sentinel 2C, desde la Guayana Francesa a bordo del último cohete Vega de la Agencia Espacial Europea.
Aunque Sentinel 2C fue diseñado como satélite de reemplazo, durante una etapa transitoria en 2025, los tres satélites estuvieron operativos de forma simultánea, lo que llevó la frecuencia de revisita a niveles inéditos. En ciertas regiones, ahora es posible observar un mismo punto del territorio con apenas uno o dos días de diferencia entre capturas. Y eso, para quienes trabajamos con análisis multitemporal, es una oportunidad invaluable.
Este ritmo sostenido de adquisición nos permite construir series temporales detalladas: ver cómo se expande una ciudad, cómo reverdece un bosque tras un incendio, cómo evoluciona un cultivo o cómo fluctúan los cuerpos de agua. Pero claro… semejante constancia tiene un precio. No económico, sino informativo.
La constancia genera volumen. Mucho volumen.
Cada satélite Sentinel 2 recolecta y transmite más de 1.5 terabytes de datos por día, por lo que la constelación —en esta fase de tres satélites activos— puede generar más de 4.5 terabytes diarios. A lo largo del año, esto representa más de 1.6 petabytes de nuevas imágenes satelitales.
Hasta mediados de 2025, el archivo acumulado de la misión supera los 30 petabytes. Un océano de información geoespacial, que incluye productos de nivel 1C (ortorrectificados, en top-of-atmosphere) y nivel 2A (corregidos atmosféricamente, en bottom-of-atmosphere).
Y lo mejor de todo es que esta información no está bajo llave.
La Agencia Espacial Europea, a través del programa Copernicus, ha impulsado una política de ciencia abierta sin precedentes. Los datos de Sentinel 2 están disponibles para todo el mundo en repositorios como el Copernicus Data Space Ecosystem o el CEDA del Reino Unido, que ya superan los 34 petabytes de información de Observación de la Tierra, creciendo cada día en varios terabytes.
Este compromiso con la transparencia, el acceso libre y el desarrollo global de capacidades analíticas nos plantea un desafío apasionante:
Con tanto conocimiento disponible desde el espacio…
¿Estamos preparados para sacarle el máximo provecho?
¿Cómo organizamos toda esta información?
Bienvenidos al mundo de los cubos de datos, o datacubes.
El volumen y la complejidad de los datos satelitales nos obliga a pensar de una forma nueva. ¿Cómo podemos analizar millones de imágenes, cada una con múltiples bandas y fechas distintas, sin ahogarnos en un mar de archivos?
Aquí entra en juego un concepto clave en el análisis geoespacial moderno: el cubo de dato o inglés datacube dádaciubs
Según la plataforma abierta para la observación de la Tierra openEO:
“Un cubo de datos es una estructura de datos multidimensional que organiza la información, generalmente geoespacial, en ejes bien definidos como espacio, tiempo, bandas espectrales, etc.”
Y según Giuliani et al. (2016):
“Es una colección de datos geoespaciales organizados como una matriz multidimensional regular, diseñada para facilitar el acceso, consulta y análisis en tiempo real de grandes volúmenes de información.”
En otras palabras, un cubo de datos no es solo un almacenamiento de imágenes, sino una forma ordenada e inteligente de organizarlas para que podamos filtrar, combinar, analizar y visualizar los datos de manera eficiente.
¿Y cuáles son sus dimensiones? Las principales son:
•	Espacio: definido por las coordenadas x e y, donde x representa la longitud (el eje horizontal, de oeste a este) y y representa la latitud (el eje vertical, de sur a norte). Estas dos dimensiones permiten ubicar cada píxel en la superficie terrestre.
•	Tiempo: corresponde al momento en que fue adquirida cada imagen, ya sea una fecha o una hora específica. Esta dimensión es clave para el análisis de cambios en el territorio.
•	Espectro: se refiere a las bandas espectrales que capturan diferentes rangos del espectro electromagnético, como el visible, el infrarrojo cercano o el SWIR, lo que permite observar fenómenos que no son visibles a simple vista.
•	Y, en algunos casos, también la geometría o incluso el sistema de referencia espacial (CRS) como una dimensión adicional.
Esto nos permite hacer operaciones sofisticadas sin necesidad de reestructurar los datos. Por ejemplo:
•	Filtrar el cubo por fechas, bandas o región.
•	Aplicar funciones matemáticas sobre cada píxel o grupo de píxeles.
•	Calcular promedios temporales, máximos espectrales, o derivar índices como NDVI o NDBI.
•	Remuestrear los datos a resoluciones espaciales o temporales diferentes.
•	Y realizar agregaciones estadísticas por regiones geográficas, como cuencas, provincias o áreas de cultivo.
Un cubo de datos puede ser raster, cuando trabaja con pixeles organizados en grillas, o vectorial, cuando se organiza por geometrías como polígonos o puntos.
________________________________________
🟣 Aplicaciones reales y cierre
Gracias a los cubos de datos, hoy es posible:
•	Analizar la evolución mensual del NDVI en una región agrícola.
•	Detectar patrones de urbanización usando índices como NDBI.
•	Entrenar modelos de machine learning sobre cubos multitemporales.
•	Crear tableros interactivos para monitorear el ambiente a gran escala.
📢 En definitiva, los cubos de datos no son solo una forma eficiente de almacenar información, sino una herramienta poderosa para transformar datos satelitales en conocimiento accionable.
🧠 Comprender cómo se estructuran, cómo se consultan y cómo se transforman es clave para aprovechar todo el potencial de los datos de observación de la Tierra.







“Datacubes de Imágenes Satelitales: Fundamentos y Aplicaciones”
________________________________________
🟢 Introducción (1 minuto)
[Visual: Animación de un globo terráqueo con imágenes satelitales apareciendo en distintos momentos]
📢 “¿Qué pasaría si pudiéramos organizar miles de imágenes satelitales como si fueran una sola estructura ordenada, lista para ser procesada en bloque? Esa es la promesa de los datacubes: una forma poderosa de manejar datos espaciales y temporales de forma eficiente.”
________________________________________
🟡 ¿Qué es un Datacube? (2 minutos)
[Visual: Representación de cubo multibanda y luego una versión “plana” de ese cubo]
📚 Definición base (openEO):
“Un datacube es una estructura de datos multidimensional que organiza la información, generalmente geoespacial, en ejes bien definidos como espacio, tiempo, bandas espectrales, etc.”
📚 Definición académica complementaria (Giuliani et al., 2016):
“Un datacube es una colección de datos geoespaciales organizados como una matriz multidimensional regular, diseñada para facilitar el acceso, consulta y análisis en tiempo real de grandes volúmenes de información.”
🔎 Conceptos clave:
•	Multidimensionalidad: espacio (x, y), tiempo (t), bandas (espectral), geometría.
•	Arreglo ordenado: permite aplicar operaciones masivas, comparaciones, filtros y algoritmos sin necesidad de reestructurar el dataset.
•	No es literalmente un cubo: puede tener más o menos de tres dimensiones, es solo una metáfora visual.
________________________________________
🔵 Tipos de Datacubes (1 minuto)
[Visual: Comparativa de raster vs vector datacube]
•	Raster datacube: ejes x, y, t, bandas. Ejemplo clásico: Sentinel-2.
•	Vector datacube: geometrías (polígonos, puntos), t, bandas. Útil para análisis estadísticos por regiones.
•	Otros tipos: temporal puro (solo t), o con dimensión CRS (cuando hay varias zonas UTM).
📌 Ejemplo: una imagen Sentinel-2 con 4 bandas y 3 fechas → 4 dimensiones (x, y, bandas, tiempo).
________________________________________
🟣 Dimensiones: El eje del análisis (2 minutos)
[Visual: Tabla con metadatos de las dimensiones]
📌 Cada dimensión tiene propiedades clave:
•	Nombre, tipo (espacial, temporal, espectral), etiquetas, resolución, sistema de referencia.
•	Ejemplo: dimensión temporal con fechas 2020-10-01, 2020-10-13, 2020-10-25.
📏 Resoluciones:
•	Temporal: cada cuánto hay observaciones (ej. cada 5 días).
•	Espacial: tamaño de pixel (ej. 10m).
•	Espectral: cantidad y tipo de bandas.
📐 CRS como dimensión adicional: importante cuando se usan datos en diferentes zonas UTM. Se puede evitar reproyecciones costosas incluyendo CRS como una dimensión más.
________________________________________
🟤 Valores y Metadatos (1 minuto)
[Visual: Estructura de un datacube con valores y metadatos]
•	Los valores dentro del datacube son escalares: números, texto o booleanos.
•	Todo lo demás (coordenadas, bandas, fechas, geometrías) va como metadatos o etiquetas de dimensiones.
•	Precaución: evitar cambiar tipos de datos entre operaciones si el backend no lo soporta.
________________________________________
🟠 Procesos sobre Datacubes (6 minutos)
1. Filtrado (filter) – (1 minuto)
[Visual: Animación filtrando tiempo, bandas y espacio]
Permite recortar el cubo en función de:
•	Intervalos temporales (fechas).
•	Bandas específicas.
•	Área geográfica.
Ejemplo: filtrar solo la banda NIR entre octubre 15 y 27 en un polígono dado.
________________________________________
2. Aplicar Funciones (apply) – (1.5 minutos)
[Visual: Comparativa antes/después de aplicar abs() o media espacial]
•	apply: aplicar función pixel a pixel (unary) o considerando vecinos (n-ary).
•	apply_neighborhood: usa ventana de píxeles alrededor (kernel espacial o temporal).
•	apply_dimension: aplica función a todos los valores de una dimensión (ej: media temporal).
Ejemplo: suavizar una serie temporal usando media móvil de 3 pasos.
________________________________________
3. Reducción (reduce) – (1 minuto)
[Visual: Cubo antes y después de reducir una dimensión]
•	Colapsa una dimensión usando una función resumen: media, máximo, etc.
•	Ejemplo: reducir dimensión temporal → imagen con valores promedio.
________________________________________
4. Remuestreo (resample) – (1 minuto)
[Visual: Cambio de resolución espacial y temporal]
•	Cambia la disposición o resolución de una dimensión.
•	Ejemplo: pasar de imágenes cada 5 días a una imagen mensual (temporal).
•	Ejemplo: reescalar de 10m a 30m o viceversa (espacial).
________________________________________
5. Agregación (aggregate) – (1.5 minutos)
[Visual: Agregación por polígonos]
•	Agrupa datos por intervalos o geometrías, y luego reduce dentro de cada grupo.
•	Temporal: por mes, trimestre, año.
•	Espacial: por polígonos (provincias, cuencas, etc.), obteniendo una tabla con valores por banda, geometría y tiempo.
________________________________________
🟤 Aplicaciones prácticas (1 minuto)
[Visual: Dashboard o mapa de resultados con un datacube]
•	Seguimiento de NDVI mensual en zonas agrícolas.
•	Detección de cambios urbanos usando NDBI.
•	Cálculo de estadísticas climáticas por región.
•	Entrenamiento de modelos de machine learning sobre cubos temporales.
________________________________________
🔴 Cierre (1 minuto)
📢 “Los datacubes no son solo una forma de organizar datos, son una herramienta poderosa para el análisis geoespacial a escala. Su estructura permite filtrar, combinar, reducir y visualizar información de manera reproducible, precisa y escalable.”
🧠 “Entender sus dimensiones, procesos y resoluciones es clave para aprovechar su potencial en tareas de teledetección, monitoreo ambiental y geoIA.”
🎓 “Te invito a seguir explorando este tema con datasets reales y plataformas como openEO, Google Earth Engine, xcube o rasdaman.”

________________________________________
🎙️ Script detallado para video – Análisis Temporal Multisensor en Bahía Blanca (GEE)
________________________________________
🎬 Introducción
🎤 [Locución]
¡Hola! Bienvenidos a este video donde vamos a recorrer paso a paso un algoritmo desarrollado en Google Earth Engine.
¿El objetivo? Construir una serie temporal multifuente que combine imágenes ópticas de Sentinel-2, datos radar de Sentinel-1, precipitación diaria del producto CHIRPS y temperatura superficial del satélite MODIS, todo aplicado al área de Bahía Blanca, Argentina.
________________________________________
🧭 1. Configuración temporal
🎤 [Locución]
Comenzamos definiendo la frecuencia de análisis, en este caso mensual.
Creamos una secuencia de fechas desde enero de 2024 hasta julio de 2025, avanzando mes a mes.
Estas fechas serán utilizadas para iterar y obtener imágenes representativas de cada período.
javascript
CopyEdit
var frecuencia = 'mensual';
var mesesPorPaso = { 'mensual': 1, 'bimestral': 2, 'trimestral': 3, 'cuatrimestral': 4 };
var paso = mesesPorPaso[frecuencia];
🎤 [Locución]
Calculamos cuántos pasos mensuales hay entre las fechas de inicio y fin, y creamos una lista de fechas que usaremos más adelante para procesar la información.
________________________________________
🗺️ 2. Delimitación del área de estudio
🎤 [Locución]
Utilizamos la base GAUL para filtrar el polígono de Bahía Blanca, en la provincia de Buenos Aires.
Esta geometría será nuestra región de interés para recortar y analizar todas las imágenes.
javascript
CopyEdit
var bahiaBlanca = admin2.filter(ee.Filter.eq('ADM2_NAME', 'Bahia Blanca'));
var roi2 = bahiaBlanca.geometry().simplify(100);
________________________________________
🌱 3. Índices espectrales Sentinel-2
🎤 [Locución]
A continuación definimos una función llamada agregarIndices, que calcula varios índices espectrales a partir de las bandas de Sentinel-2.
Calculamos:
•	NDVI, para vegetación.
•	NDWI, para agua superficial.
•	NDMI, para humedad de vegetación.
•	NDBI, útil para identificar suelo desnudo o áreas urbanas.
•	Y el NIR, es decir, el infrarrojo cercano.
javascript
CopyEdit
function agregarIndices(img) {
  ...
  return img.addBands([ndvi, ndbi, ndwi, ndmi, nir]);
}
________________________________________
🛰️ 4. Construcción de la colección mensual óptica
🎤 [Locución]
Luego definimos la función getColeccionPorPeriodo, que por cada mes filtra imágenes Sentinel-2 que cubren nuestra región y tienen menos de un 20% de nubosidad.
Recortamos las imágenes, aplicamos los índices y calculamos una imagen promedio mensual, que guarda además la fecha y la cantidad de imágenes utilizadas.
javascript
CopyEdit
return coleccionConIndices.mean()
  .set('system:time_start', fecha.millis())
  .set('fecha', fecha.format('YYYY-MM'))
  .set('cantidad_imagenes', cantidad);
________________________________________
📊 5. Cantidad de imágenes por período
🎤 [Locución]
Con esta colección mensual generamos un resumen por consola, mostrando cuántas imágenes fueron procesadas cada mes.
Esto permite ver la cobertura temporal y posibles vacíos de datos.
________________________________________
📡 6. Procesamiento de datos Sentinel-1 radar
🎤 [Locución]
Ahora incorporamos datos de radar Sentinel-1, filtrando aquellas imágenes que tienen polarización VV y, si está disponible, también VH.
Calculamos además el índice Ratio, que compara VV y VH.
Esto es muy útil para detectar humedad o inundaciones, ya que el radar penetra nubes y capta señales de superficie.
javascript
CopyEdit
var ratio = ee.Algorithms.If(hasVH,
  vv.divide(ee.Image(vh)).rename('Ratio'),
  null
);
________________________________________
🔀 7. Combinación óptico + radar
🎤 [Locución]
En este paso unimos, para cada mes, la imagen óptica con la imagen radar correspondiente.
Si no hay radar para ese período, la fecha se omite.
El resultado es una colección integrada multisensorial, lista para análisis más completos.
________________________________________
🌧️ 8. Precipitación con CHIRPS
🎤 [Locución]
Incorporamos el componente climático con datos diarios de precipitación del producto CHIRPS.
Sumamos los valores diarios para cada mes, generando una serie mensual de lluvia acumulada.
javascript
CopyEdit
var suma = chirps.filterDate(inicio, fin).sum();
________________________________________
🌡️ 9. Temperatura superficial con MODIS
🎤 [Locución]
También sumamos información térmica, utilizando el producto MODIS LST.
Este dataset nos da la temperatura superficial terrestre cada 8 días, que escalamos y convertimos de Kelvin a grados Celsius.
javascript
CopyEdit
.multiply(0.02).subtract(273.15)
________________________________________
📈 10. Visualización de resultados
🎤 [Locución]
Finalmente, generamos cuatro gráficos de series temporales:
•	Uno para la precipitación mensual (CHIRPS).
•	Otro con los índices ópticos y radar como NDVI, NDWI, NDMI, NDBI, NIR y el Ratio.
•	Un tercero para VV y VH de radar Sentinel-1.
•	Y uno adicional para la temperatura superficial (LST) de MODIS.
🎤 [Locución]
Todos estos gráficos se integran en un panel de visualización dentro del entorno de Google Earth Engine.
Esto permite explorar fácilmente la evolución mensual de distintas variables ambientales sobre el área de Bahía Blanca.
________________________________________
🧭 Cierre
🎤 [Locución]
Como vimos, Google Earth Engine permite combinar diferentes fuentes de datos satelitales y meteorológicos para construir análisis temporales multivariados.
Esta metodología puede adaptarse a otras regiones, otras frecuencias o incluso extenderse con algoritmos de clasificación y detección de cambios.
¡Gracias por acompañarnos!
 
🎬 Guion de Video: Serie Temporal de Clases de Suelo con Clasificador Entrenado
________________________________________
🧭 Introducción al Bloque
“En este video vamos a aplicar el clasificador supervisado que entrenamos previamente sobre datos Sentinel-2 de la ciudad de Rosario. Nuestro objetivo es generar una serie temporal que nos permita observar cómo evolucionan los distintos tipos de cobertura del suelo a lo largo del tiempo, distinguiendo clases como agua, zonas urbanas, cultivos, vegetación natural y terreno desnudo.”
________________________________________
🔧 BLOQUE 1: Configuración del período temporal
javascript
CopyEdit
var frecuencia = "bimestral";
Definimos la frecuencia temporal con la que vamos a construir nuestra serie de tiempo. En este caso elegimos bimestral, lo que implica que cada punto de la serie va a representar un promedio de dos meses.
javascript
CopyEdit
var mesesPorPaso = {
  "mensual": 1,
  "bimestral": 2,
  "trimestral": 3,
  "cuatrimestral": 4
};
Creamos un diccionario que traduce cada opción de frecuencia en una cantidad de meses. Esto nos permitirá usar la misma estructura para distintas resoluciones temporales.
javascript
CopyEdit
var paso = mesesPorPaso[frecuencia];
Extraemos el valor del paso correspondiente a la frecuencia elegida (en este caso 2 meses).
javascript
CopyEdit
var fechas = ee.List.sequence(0, 12, paso).map(function(mes) {
  return ee.Date('2024-01-01').advance(mes, 'month');
});
Generamos una lista de fechas que representan los inicios de cada período bimestral, a partir de enero de 2024. Cada valor es un objeto ee.Date.
________________________________________
🏷️ BLOQUE 2: Definición de etiquetas de clases
javascript
CopyEdit
var etiquetas = ee.Dictionary({
  '0': 'Agua',
  '1': 'Urbano',
  '2': 'Cultivos',
  '3': 'Bosque-Area arbustiva',
  '4': 'Terreno desnudo'
});
Creamos un diccionario que asocia cada código de clase (usado por el clasificador) con un nombre legible. Esto nos permitirá mostrar las clases en gráficos con nombres descriptivos.
________________________________________
🧠 BLOQUE 3: Aplicación del clasificador por período
javascript
CopyEdit
var coleccionClasificada = ee.FeatureCollection(fechas.map(function(fechaInicio) {
Usamos map() para iterar sobre la lista de fechas y aplicar la misma lógica de clasificación a cada período bimestral.
javascript
CopyEdit
  var fechaFin = ee.Date(fechaInicio).advance(paso, 'month');
Calculamos la fecha final del período sumando el paso (dos meses en este caso) a la fecha de inicio.
javascript
CopyEdit
  var s2 = ee.ImageCollection("COPERNICUS/S2_SR_HARMONIZED")
    .filterDate(fechaInicio, fechaFin)
    .filterBounds(roi)
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 30))
    .select('B.*');
Descargamos las imágenes Sentinel-2 del período actual:
•	Filtramos por fechas, región de interés (roi) y porcentaje de nubes.
•	Seleccionamos todas las bandas ópticas.
javascript
CopyEdit
  var imagen = s2.median().clip(roi);
Calculamos la mediana de las imágenes en el período y la recortamos al área de estudio.
javascript
CopyEdit
  var clasificada = imagen.classify(classifier);
Aplicamos el clasificador previamente entrenado a la imagen compuesta para generar un raster de clases.
________________________________________
📊 BLOQUE 4: Conteo de píxeles por clase
javascript
CopyEdit
  var histograma = clasificada.reduceRegion({
    reducer: ee.Reducer.frequencyHistogram(),
    geometry: ee.FeatureCollection(roi).geometry(),
    scale: 30,
    maxPixels: 1e13
  }).get('classification');
Reducimos la imagen clasificada a una tabla que indica cuántos píxeles hay de cada clase en ese período, usando un histograma.
javascript
CopyEdit
  var dict = ee.Dictionary(histograma);
  var keys = dict.keys();
  var valores = keys.map(function(k) { return dict.get(k); });
Obtenemos las claves del diccionario (0, 1, etc.) y los valores asociados (cantidad de píxeles).
javascript
CopyEdit
  var nuevasClaves = keys.map(function(k) {
    return etiquetas.get(k);
  });
Traducimos las claves numéricas por sus nombres: agua, urbano, etc.
javascript
CopyEdit
  var propsEtiquetadas = ee.Dictionary.fromLists(nuevasClaves, valores);
Construimos un nuevo diccionario con claves descriptivas y valores numéricos.
javascript
CopyEdit
  var ft = ee.Feature(null, propsEtiquetadas)
    .set('fecha', ee.Date(fechaInicio).format('YYYY-MM-dd'));
    
  return ft;
}));
Creamos un Feature con los datos de ese período y la fecha correspondiente, y lo devolvemos. Al final, coleccionClasificada es una colección de Features, uno por período.
________________________________________
📊 BLOQUE 5: Visualización absoluta
javascript
CopyEdit
var grafico = ui.Chart.feature.byFeature({
  features: coleccionClasificada,
  xProperty: 'fecha'
}).setChartType('ColumnChart').setOptions({
  title: 'Serie temporal de clases de suelo - ' + frecuencia,
  hAxis: {title: 'Fecha'},
  vAxis: {title: 'Cantidad de píxeles'},
  isStacked: true
});
Creamos un gráfico de barras apiladas que muestra la cantidad absoluta de píxeles por clase en cada período. Esto permite ver por ejemplo si aumentaron los cultivos o disminuyó el área urbana en un bimestre determinado.
________________________________________
📐 BLOQUE 6: Conversión a porcentajes
javascript
CopyEdit
var coleccionPorcentual = coleccionClasificada.map(function(feat) {
Iteramos sobre cada Feature de la colección anterior para convertir los valores absolutos en porcentajes.
javascript
CopyEdit
  var props = feat.toDictionary();
  var clases = props.keys().remove('fecha');
Convertimos cada Feature en un diccionario y extraemos las clases (omitimos la fecha).
javascript
CopyEdit
  var total = ee.Number(
    clases.map(function(k) {
      return ee.Number(props.get(k));
    }).reduce(ee.Reducer.sum())
  );
Calculamos el total de píxeles clasificados en ese período.
javascript
CopyEdit
  var nuevasProps = ee.Dictionary(
    clases.iterate(function(k, acc) {
      ...
    }, ee.Dictionary({}))
  );
Para cada clase, calculamos su porcentaje con respecto al total y lo guardamos en un nuevo diccionario.
javascript
CopyEdit
  return ee.Feature(null, nuevasProps)
    .set('fecha', props.get('fecha'));
});
Creamos un nuevo Feature para cada período, ahora con porcentajes. coleccionPorcentual es una colección con los datos relativos.
________________________________________
📈 BLOQUE 7: Visualización porcentual
javascript
CopyEdit
var graficoPorcentual = ui.Chart.feature.byFeature({
  features: coleccionPorcentual,
  xProperty: 'fecha'
}).setChartType('LineChart').setOptions({
  title: 'Serie temporal porcentual de clases de suelo - ' + frecuencia,
  hAxis: {title: 'Fecha'},
  vAxis: {
    title: 'Porcentaje (%)',
    minValue: 0,
    maxValue: 100
  },
  isStacked: false
});
print(graficoPorcentual);
Mostramos un gráfico de líneas con la evolución relativa de cada clase. A diferencia del gráfico anterior, este permite detectar tendencias aunque cambie el número total de píxeles disponibles (por ejemplo, por nubosidad).
________________________________________
🧪 Conclusión
“Con este bloque de código logramos aplicar automáticamente el clasificador de suelos para Rosario a cada período bimestral. A partir de ahí, generamos una serie de tiempo de clases del suelo que nos permite observar tanto los cambios absolutos como los relativos. Este enfoque es ideal para detectar procesos de urbanización, expansión agrícola, deforestación o cambios hidrológicos en el territorio.”
________________________________________
