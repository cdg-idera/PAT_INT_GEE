# Capítulo 1 · Teledetección y Aprendizaje Automático


Bienvenidos en este capítulo exploraremos cómo el aprendizaje automático está revolucionando la teledetección y el trabajo en la nube y mostraremos un ejemplo práctico de aprendizaje automático supervisado con Random Forest. El capítulo se estructura en tres partes en la primera parte comenzaremos haciendo una breve reseña sobre teledetección luego en la segunda parte abordaremos técnicas de aprendizaje automático o Machine learning aplicadas a imágenes satelitales, en la tercera parte mostraremos un ejemplo concreto de la técnica Random Forest a una composición de imágenes sobre un área de estudio. Comenzaremos haciendo una pequeña reseña sobre GeoAI, teledetección y Aprendizaje Automático.


## ¿Qué es la geoAI?

> Inteligencia Artificial Geoespacial (GeoAI) es la integración de los estudios geoespaciales con la inteligencia artificial (aprendizaje automático y profundo) y las tecnologías de grafos de conocimiento {cite:p}`gao_hu_li_2023_handbook_geoai`. 

> "GeoAI es la integración de la inteligencia artificial (IA) con los datos espaciales, la ciencia y la tecnología geoespacial para potenciar el conocimiento y solucionar problemas espaciales. GeoAI incluye la aplicación de técnicas de IA tradicionales para generar datos espaciales por medio de la extracción, la clasificación y la detección de información de datos estructurados y no estructurados. GeoAI es el uso de técnicas de IA expresamente espaciales cuya finalidad es resolver los problemas espaciales mediante el análisis de datos espaciales, e incluye técnicas de detección de patrones, predicción, previsión espaciotemporal, etc." {cite:p}`esri2021geoai`

## Teledetección

> La teledetección es la ciencia de obtener información sobre objetos o áreas a distancia mediante el uso de satélites aeronaves o drones.

> Jensen, John R. en su libro Remote Sensing of the Environment {cite:t}`jensen2007remote`: An Earth Resource Perspective (2007) define la teledetección como: "La ciencia y el arte de obtener información sobre un objeto, área o fenómeno mediante el análisis de datos adquiridos por un dispositivo que no está en contacto con el objeto, área o fenómeno en estudio." 

Otra definición es:

> "La teledetección es el proceso de adquirir información sobre las propiedades de los objetos en la superficie terrestre sin estar en contacto físico con ellos. Esto se logra detectando y analizando la radiación reflejada o emitida por esos objetos, utilizando sensores montados en plataformas remotas, como satélites o aeronaves." {cite:t}`lillesand2015remote`

```{figure} imagenes/areasIA2.png
:name: fig-areas
:width: 90%

Teledetección: Áreas de Aplicación.
```


Esta disciplina se ha convertido en una herramienta esencial para entender nuestro entorno y tomar decisiones fundamentadas en múltiples campos. Entre sus principales propósitos se encuentran (ver fig. {numref}`fig-areas`):

* La Generación de mapas de uso del suelo y cobertura del suelo (LULC, por sus siglas en inglés), una práctica clave para monitorear el crecimiento urbano, la planificación urbana sostenible y la identificación de cambios en el paisaje.

* La Detección de cambios ambientales, como la expansión urbana, las variaciones en la vegetación, los cultivos, y los cuerpos de agua.

* El Control del impacto de la urbanización, especialmente en áreas preurbanas y sensibles al cambio ambiental.

* Con la democratización del acceso a imágenes satelitales, plataformas como Google Earth Engine y hubs como Copernicus ofrecen recursos gratuitos y accesibles que eliminan las barreras de entrada para realizar análisis geoespaciales. Ya no hay excusas: hoy en día, cualquier persona con conocimientos básicos puede acceder a datos satelitales para abordar problemas ambientales, monitorear el crecimiento urbano o evaluar la salud de los ecosistemas.

Además, gracias a las capacidades de procesamiento en la nube, podemos llevar a cabo estudios a escalas regionales y nacionales sin la necesidad de infraestructuras complejas. Esto abre un abanico de posibilidades para investigadores, profesionales y tomadores de decisiones que buscan soluciones sostenibles basadas en datos confiables y actualizados.

Como indicamos anteriormente este capítulo se enfocará en el uso del aprendizaje automático en teledetección y mostraremos un ejemplo en el cual aplicamos aprendizaje supervisado con árboles aleatorios o Random Forest. 

El aprendizaje automático se ha consolidado como una herramienta ideal para resolver problemas complejos en teledetección, principalmente porque aborda de manera eficiente la clasificación y detección de objetos o materiales en imágenes satelitales. Por ejemplo detectar vegetación, agua, edificios, carreteras, etc. (ver fig. {numref}`fig-tele2`). Estos problemas, como identificar tipos de cobertura terrestre o distinguir entre características específicas, presentan desafíos significativos cuando se intentan resolver mediante métodos analíticos tradicionales.

```{figure} imagenes/tele2.png
:name: fig-tele2
:width: 100%

Teledetección: Gráfico Simbólico
```

Existen algunos desafíos de los Datos de Observación Terrestre (ver fig. {numref}`fig-slide3`):

```{figure} imagenes/atmosfera.png
:name: fig-atmosfera
:width: 100%

Algunas Interferencias atmosféricas
```

* Los datos satelitales, aunque ricos en información, están **inherentemente sujetos a ruido y variabilidad**:

* **Ruido atmosférico y del sensor:** Los satélites operan a cientos de kilómetros de altitud, capturando datos a través de múltiples interferencias atmosféricas (ver fig. {numref}`fig-atmosfera`). Este entorno introduce desviaciones en las mediciones de reflectancia.

* **Variabilidad temporal y espacial:** Las mismas condiciones en diferentes días pueden generar valores de reflectancia ligeramente distintos debido a factores ambientales y limitaciones instrumentales.

* **Dimensionalidad de los datos:** Las imágenes de teledetección suelen incluir múltiples bandas espectrales, lo que genera un espacio de características de alta dimensionalidad que dificulta la creación de reglas manuales para clasificar objetos con precisión.

```{figure} imagenes/Slide3.png
:name: fig-slide3
:width: 100%

Teledetección: Desafíos de la Observación Terrestre
```


## Ventajas del Aprendizaje Automático:

El aprendizaje automático supera estos desafíos al modelar relaciones complejas entre las bandas espectrales y los objetos de interés, sin requerir que el usuario formule reglas explícitas. En su lugar, utiliza datos etiquetados de campo  para entrenar modelos, a estos datos etiqeutados de campo se los suele denominar "verdad terrestre" (ver fig. {numref}`fig-etiquetaA` y fig. {numref}`fig-etiquetaB`). 

```{figure} imagenes/EtiquetaA.png
:name: fig-etiquetaA
:width: 100%

Etiquetas o muestras de Entrenamiento de tipos de Suelo
```

```{figure} imagenes/EtiquetaB.png
:name: fig-etiquetaB
:width: 100%

Etiquetas o muestras de Entrenamiento de Agua-No Agua.
```

A partir de estas etiquetas los algoritmos de aprendizaje automático (ver fig. {numref}`fig-Slide4`):

* Aprenden *patrones espectrales*: es decir, Identifican combinaciones óptimas de bandas espectrales para clasificar materiales específicos, como agua o vegetación, a pesar del ruido.

* *Se adaptan a la variabilidad*: Los modelos pueden generalizar patrones, tolerando pequeñas variaciones en los datos de entrada sin comprometer la precisión.

```{figure} imagenes/Slide4.png
:name: fig-Slide4
:width: 100%

Ventajas del Aprendizaje Automático.
```


Un Enfoque Práctico:

Por ejemplo, si el objetivo es detectar agua, sería ideal medir directamente las propiedades espectrales del agua en el campo. Sin embargo, debido al ruido y la complejidad inherente de los datos satelitales, un modelo de aprendizaje automático entrenado con datos de referencia puede inferir las reglas necesarias para identificar agua con alta precisión, incluso cuando las condiciones no son ideales.

*Conclusión:* En el contexto de teledetección, el aprendizaje automático no solo simplifica el manejo de datos complejos y ruidosos, sino que también proporciona una robustez y adaptabilidad que los métodos tradicionales no pueden igualar. Este enfoque representa un cambio de paradigma: en lugar de tratar de definir manualmente las reglas de clasificación, delegamos esta tarea a algoritmos capaces de extraer patrones directamente de los datos.

## Clasificación y aprendizaje automático en teledetección 

El aprendizaje automático (en inglés *ML ó Machine learning*) ofrece dos enfoques principales para abordar problemas en teledetección: aprendizaje supervisado y no supervisado. Cada uno tiene aplicaciones específicas y ventajas según el caso de uso: Aprendizaje no supervisado y Aprendizaje supervisado (ver fig. {numref}`fig-Slide6`). 


```{figure} imagenes/Slide6.png
:name: fig-Slide6
:width: 100%

Principales tipos de Aprendizaje Automático.
```

### Aprendizaje no supervisado 

Este método implica proporcionar los datos al algoritmo sin etiquetas previas. Los algoritmos no supervisados agrupan los datos en categorías basadas en similitudes internas, como los valores de reflectancia en imágenes satelitales. Por ejemplo, algoritmos como k-means clustering o SNCC identifican agrupaciones de píxeles con características espectrales similares, pero no asignan significado alguno a esos grupos; esta tarea recae en el analista o en otro modelo.

Aunque el aprendizaje no supervisado es útil para encontrar patrones en datos complejos, tiene limitaciones. La interpretación y etiquetado posterior de los grupos requieren un esfuerzo significativo, lo que lo convierte más en una herramienta inicial o complementaria que en una solución completa. En teledetección, se utiliza comúnmente para la clasificación basada en objetos, donde se combinan agrupaciones no supervisadas con métodos supervisados.

### Aprendizaje supervisado
 
El aprendizaje supervisado, por otro lado, requiere datos previamente etiquetados. Por ejemplo, para clasificar agua en una imagen satelital, es necesario identificar y etiquetar píxeles de muestra representativos de agua y no agua. Estos datos de entrenamiento alimentan al algoritmo, que aprende a generalizar y clasificar el resto de la imagen. Algoritmos como regresión logística, bosques aleatorios (random forest) y otros modelos de clasificación supervisada son ampliamente utilizados.

En la práctica, la clasificación supervisada es la técnica predominante, ya que permite asignar etiquetas claras a cada píxel, crucial para cuantificaciones precisas y análisis detallados. Su flujo de trabajo típico incluye (ver fig. {numref}`fig-workflow`):

* Selección de de datos de entrenamiento 
* Entrenamiento de un clasificador 
* Clasificación de la imagen 
* Evaluación de precisión 
* Refinamiento iterativo mediante ajustes de parámetros o mejora de los datos de entrada 


```{figure} imagenes/Workflow.png
:name: fig-workflow
:width: 100%

Workflow de trabajo para aplicar ML.
```


Este enfoque, según {cite:p}`reynoso2025flujo`, permite integrar algoritmos de Machine Learning en flujos de trabajo reproducibles e integra las partes principales que deberán ser consideradas en cada aplicación de algoritmos de machine learning (ML) a informacion geoespacial. El flujo será aplicado en distintos capítulos del presente libro.


### Consideraciones prácticas

Aunque el aprendizaje automático simplifica el manejo de datos complejos, no siempre es la mejor opción. Métodos tradicionales como los sistemas expertos, que emplean fórmulas derivadas de las propiedades físicas del terreno, son preferibles en ciertos casos, especialmente cuando se trabaja con pocos parámetros (bandas espectrales). Por ejemplo, el conjunto de datos global Global Surface Water, basado en un sistema experto, supera a los métodos de Aprendizaje automático en la detección de agua en imágenes Landsat a escala global (ver fig. {numref}`fig-Slide7`).

```{figure} imagenes/Slide7.png
:name: fig-Slide7
:width: 100%

Sistemas Expertos ó Aprendizaje Automático.
```


La elección entre un sistema experto y Aprendizaje automático depende del problema en cuestión. Los sistemas expertos destacan por su transparencia y simplicidad en problemas bien definidos, mientras que el Aprendizaje automático se adapta mejor a datos complejos y ruidosos, descubriendo patrones ocultos sin necesidad de reglas explícitas.

### Ejemplo práctico: Clasificación binaria

Para ilustrar el proceso de aprendizaje supervisado, consideremos una clasificación binaria: detectar agua frente a no agua. Supongamos que seleccionamos píxeles representativos (50 de agua y 50 de no agua) y extraemos sus valores de reflectancia en dos bandas (verde e infrarrojo). Estos datos se organizan en una tabla de entrenamiento, donde cada fila representa un píxel y contiene sus valores espectrales y etiqueta correspondiente.

Un modelo entrenado con estos datos aprenderá a predecir la clase de nuevos píxeles basándose en sus reflectancias. Este proceso es escalable a problemas más complejos, como clasificaciones multiclase (ej., vegetación, suelo desnudo, agua) y análisis multiespectral.

### Conclusión de la sección


La mayoría de las aplicaciones de teledetección implican algún tipo de clasificación supervisada, ya que permiten convertir imágenes en mapas clasificados, útiles para responder preguntas clave como (ver fig. {numref}`fig-Slide11`): ¿Qué porcentaje de la ciudad está cubierto por árboles? o ¿Cuál es el área urbanizada en crecimiento?

```{figure} imagenes/Slide11.png
:name: fig-Slide11
:width: 100%

Sistemas Expertos ó Aprendizaje Automático.
```

Aunque ambos enfoques (supervisado y no supervisado) tienen un lugar en el análisis de teledetección, comprender sus fortalezas y limitaciones es esencial para elegir la estrategia adecuada en cada caso. Al final, la combinación de ambos métodos, junto con sistemas expertos, puede ofrecer resultados más robustos y confiables.

El aprendizaje automático se ha convertido en una herramienta imprescindible en teledetección, particularmente para resolver problemas de clasificación. Una aplicación común es identificar qué píxeles en una imagen satelital representan cuerpos de agua y cuáles corresponden a otras superficies. Para abordar estas tareas, se han desarrollado diversos algoritmos, cada uno con fortalezas particulares (ver fig. {numref}`fig-Slide9`). Entre ellos, destacan las Máquinas de Soporte Vectorial (SVM), los árboles de decisión y el algoritmo Random Forest, que han transformado la manera en que se procesan y analizan los datos satelitales.


```{figure} imagenes/Slide9.png
:name: fig-Slide9
:width: 100%

Sistemas Expertos ó Aprendizaje Automático.
```


Durante años, las Máquinas de Soporte Vectorial se posicionaron como una de las herramientas preferidas en teledetección, especialmente en problemas de clasificación binaria. Este algoritmo, conocido por su precisión, construye un hiperplano que separa de forma óptima dos clases de datos en un espacio multidimensional. Por ejemplo, en dos dimensiones, esta separación se traduce en una línea, mientras que en tres dimensiones, se convierte en un plano. El modelo aprende a clasificar datos a partir de ejemplos etiquetados, ajustando el hiperplano para maximizar la distancia entre las dos clases. Una vez entrenado, puede determinar la clase de un nuevo píxel simplemente evaluando en qué lado del hiperplano se encuentra. Sin embargo, a pesar de su efectividad en problemas simples, las SVM enfrentan limitaciones cuando se aplican a tareas que involucran múltiples clases, lo que redujo su protagonismo a medida que surgieron enfoques más versátiles.

Con el tiempo, los árboles de decisión comenzaron a ganar terreno como una alternativa más flexible y adaptativa. A diferencia de las SVM, los árboles de decisión no se limitan a dos clases y son capaces de manejar problemas multiclase de forma natural. Este algoritmo utiliza un enfoque basado en reglas, en el que los datos son clasificados mediante un proceso de decisiones jerárquicas. Cada nodo en el árbol representa una condición, como un umbral de reflectancia, y las ramas llevan a diferentes resultados según las características de los datos. Al final, cada píxel es asignado a una clase con base en su recorrido por el árbol. Esta estructura no solo es eficaz, sino también intuitiva, ya que las reglas generadas por el modelo pueden interpretarse con facilidad. Además, su capacidad para realizar regresiones amplía su utilidad a problemas más complejos, como la estimación de rendimientos agrícolas. Sin embargo, su principal desafío radica en el sobreajuste: cuando un árbol se adapta demasiado a los datos de entrenamiento, pierde capacidad para generalizar en nuevos contextos.

Para superar estas limitaciones, surgió Random Forest, un algoritmo que revolucionó el uso de los árboles de decisión mediante un enfoque de aprendizaje en conjunto. En lugar de construir un único árbol, Random Forest genera múltiples árboles independientes, cada uno entrenado con un subconjunto aleatorio de los datos. Si bien cada árbol puede ser propenso al sobreajuste, el algoritmo combina sus predicciones mediante un proceso de votación mayoritaria, logrando así un modelo final más robusto y preciso (ver fig. {numref}`fig-Slide14`). Este enfoque, inspirado en la "sabiduría de las multitudes", ha demostrado ser particularmente eficaz en teledetección, donde la diversidad de los datos suele complicar el análisis. Además de su capacidad para manejar ruido y datos complejos, Random Forest se adapta fácilmente a problemas multiclase, consolidándose como una herramienta versátil y confiable.


```{figure} imagenes/Slide14.png
:name: fig-Slide14
:width: 100%

Algunas Características de RF.
```

A lo largo de los años, la evolución de los algoritmos en teledetección ha reflejado un cambio significativo en las preferencias y necesidades de la comunidad científica. Aunque las SVM continúan siendo útiles para problemas específicos, como las clasificaciones binarias, Random Forest se ha establecido como el estándar para tareas más complejas y de mayor escala. Este algoritmo no solo garantiza precisión y robustez, sino que también permite ajustes personalizados y evaluaciones de rendimiento, adaptándose a los requisitos de cada proyecto. Su integración en plataformas como Google Earth Engine lo ha convertido en una opción accesible y eficaz para usuarios de diferentes niveles de experiencia, marcando el punto de partida ideal para explorar técnicas más avanzadas.

Para clasificar píxeles en una imagen satelital, supongamos que tenemos un conjunto de datos inicial compuesto por 100 píxeles. Cada píxel tiene dos valores asociados, correspondientes a las reflectancias en las bandas verde e infrarroja cercana (NIR). Además, sabemos qué píxeles representan agua (etiquetados como "agua") y cuáles no (etiquetados como "no agua").

El objetivo es entrenar un modelo que, con base en las reflectancias, pueda clasificar todos los píxeles de la imagen en una de estas dos categorías. 

## Maquina de Soporte Vectorial (SVM)

A continuación, exploraremos cómo funciona la tecnica maquinas de soporte vectorial o SVM

### Entrenamiento:

SVM es un modelo adecuado para problemas con dos clases (en este caso, agua y no agua). Durante el entrenamiento, SVM busca una línea (o un plano en dimensiones superiores) que separe los píxeles etiquetados como "agua" de los etiquetados como "no agua".
* En un gráfico 2D, con la reflectancia de la banda verde en el eje X y la reflectancia de la banda NIR en el eje Y, SVM encuentra la línea que mejor separa los dos grupos.
* En un grafico 3D en lugar de linea empleamos un plano.

### Predicción:

Una vez entrenado, el modelo usa esta línea para clasificar nuevos píxeles (ver fig. {numref}`fig-Slide12`):

* Si un píxel tiene valores que caen por debajo de la línea, se clasifica como "agua".
* Si está por encima, se clasifica como "no agua".
* SVM es eficaz, simple y rápido para este tipo de problemas binarios.

```{figure} imagenes/Slide12.png
:name: fig-Slide12
:width: 100%

Algunas Características de las SMV.
```


Ahora mostraremos de manera esquemática como se emplea Arboles de decisión a nuestro ejemplo sencillo de clasificación binaria (agua /no agua):

## Árboles de Decisión

### Entrenamiento:
Los Árboles de Decisión dividen los datos en pasos secuenciales basados en umbrales en los valores de las bandas. Por ejemplo, el modelo puede establecer que un píxel es "agua" si su reflectancia en la banda verde es menor que un valor X y su reflectancia en la banda infrarroja cercana es menor que un valor Y. Estas reglas se organizan en un flujo lógico (un diagrama de árbol), donde cada bifurcación representa una decisión basada en un umbral.

### Predicción:

Para clasificar un píxel, se sigue el flujo del árbol:
Se verifican las condiciones establecidas en cada nodo.
Al final, se llega a una hoja que indica la clase del píxel (agua o no agua).

### Ventajas y limitaciones:
Los Árboles de Decisión son fáciles de interpretar porque el modelo resulta en un diagrama explicativo (ver fig. {numref}`fig-Slide13`).
Sin embargo, tienen un problema conocido como sobreajuste: si los datos de entrenamiento contienen ruido, el árbol puede volverse excesivamente complejo al intentar ajustarse a datos irrelevantes.


```{figure} imagenes/Slide13.png
:name: fig-Slide13
:width: 100%

Algunas Características de los Árboles de Decisión.
```

Para mitigar el sobreajuste, se pueden podar ramas del árbol, limitar la profundidad máxima o emplear métodos como Random Forest, que combinan múltiples árboles para mejorar la generalización.



## Video del capítulo

Podes mirar el video asociado a este capítulo en el canal de youtube de IDERA: https://www.youtube.com/watch?v=fk6atugR6ss


