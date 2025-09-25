# Capítulo 6 · Workflow ML en GEE

Este capítulo se enfoca en el Flujo de trabajo para clasificación supervisada de imágenes satelitales mediante algoritmos de Machine Learning (ML) y Google Earth Engine (GEE)

Este estudio especifica un flujo de trabajo fundamentado en técnicas de aprendizaje automatizado (Machine Learning) para la clasificación supervisada de imágenes satelitales en Google Earth Engine (GEE). Se implementaron y compararon los algoritmos Random Forest, Support Vector Machine (SVM) y Árboles de Decisión en dos aplicaciones
principales: clasificación (multiclase) de suelos y clasificación (binaria)  agua/no-agua. El flujo de trabajo comprende varias etapas fundamentales: a) Colección de Imágenes de Entrada, que representa la región de estudio a clasificar; b) Recolectar Muestras de Entrenamiento, que abarca la generación de datos mediante puntos de referencia
clasificados; c) Entrenar un Clasificador, en la que los algoritmos de aprendizaje supervisado son entrenados con los datos recolectados; d) Clasificar la Imagen, donde se asignan etiquetas a cada píxel según el modelo entrenado; e) Evaluación de Precisión, con métricas como la matriz de confusión, precisión global, precisión del productor y consumidor, coeficiente de Kappa y F-score; f) Análisis de Decisiones, que incluye ajustes iterativos en los hiperparámetros del modelo y la recolección de datos de entrenamiento; y (g) Producir Estadísticas de la clasificación, ejemplo: contabilizar píxeles por clase. Para la validación, se aplicó el flujo de trabajo a: (1) región del Comahue, clasificando cuerpos de agua, y (b) región metropolitana de Rosario. En ambas regiones de estudio se utilizaron imágenes Sentinel-2 de resolución espacial de 10 metros. Los resultados demostraron la eficacia de Random Forest y SVM para identificar patrones espaciales con alta precisión, mientras que los Árboles de Decisión facilitaron la interpretación de las reglas de clasificación. Como parte del proyecto, se desarrollaron scripts y videos explicativos difundidos en IDERA para fortalecer la economía del conocimiento y el desarrollo de capacidades en estas técnicas de Inteligencia Artificial. Este enfoque proporciona un marco replicable y robusto para el análisis geoespacial, y la democratización de la información satelital.


## INTRODUCCIÓN

La clasificación supervisada de imágenes satelitales es una de las técnicas más utilizadas en la teledetección para la extracción de información geoespacial. Gracias al avance en la tecnología satelital y al desarrollo de algoritmos de aprendizaje automático (Machine Learning, ML), se han logrado mejoras significativas en la precisión y eficiencia de estos procedimientos. La clasificación supervisada permite etiquetar cada píxel de una imagen en función de un conjunto de datos de entrenamiento previamente definidos, proporcionando una herramienta poderosa para el análisis de cambios en la cobertura terrestre, detección de cuerpos de agua, monitoreo ambiental y planificación urbana.

El uso de algoritmos de aprendizaje automático en la teledetección ha sido ampliamente estudiado, destacando su aplicación en la clasificación de imágenes de alta resolución (Jensen, 2007; Lillesand, Kiefer & Chipman, 2015). Su uso en aplicaciones geoespaciales se ha incrementado debido a la creciente necesidad de datos actualizados y precisos para la
toma de decisiones en diferentes sectores. Desde la agricultura de precisión hasta la gestión de riesgos naturales, la capacidad de analizar grandes volúmenes de datos en tiempo real ha cambiado la forma en que se estudian y comprenden los ecosistemas terrestres. Además, la combinación de sensores multiespectrales y el desarrollo de técnicas de procesamiento avanzadas han permitido identificar patrones de uso del suelo con un alto grado de precisión.

En este contexto, herramientas como Google Earth Engine (GEE) han revolucionado la forma en que se manejan grandes volúmenes de datos satelitales, al proporcionar una plataforma de cómputo en la nube que facilita el acceso, procesamiento y análisis de imágenes sin la necesidad de infraestructura local costosa. Al integrar GEE con algoritmos de clasificación basados en aprendizaje automático, se abre un abanico de posibilidades para la optimización del análisis geoespacial. Este enfoque permite una escalabilidad sin precedentes, ya
que facilita el procesamiento de grandes conjuntos de datos de manera eficiente y accesible.

##  OBJETIVOS

### Objetivo general

Desarrollar un flujo de trabajo optimizado para la clasificación supervisada de imágenes satelitales utilizando los algoritmos más conocidos de Machine Learning en Google Earth Engine.

### Objetivos específicos

-   Establecer una metodología replicable que permita optimizar la clasificación de imágenes satelitales en futuras investigaciones.

-   Generar recursos didácticos y repositorios públicos para facilitar la aplicación de los algoritmos de Machine Learning más utilizados.

-   Aplicar el flujo de trabajo aplicando los algoritmos de machine learning: Random Forest, SVM y Árboles de Decisión en la clasificación de imágenes Sentinel-2.

## METODOLOGÍA

Para establecer un flujo de trabajo genérico en la clasificación supervisada de imágenes satelitales con Machine Learning en Google Earth Engine (GEE), se siguió un enfoque basado en la aplicación, validación y documentación de cada etapa del proceso en distintos contextos geoespaciales.

El primer paso consistió en aplicar la clasificación supervisada en diferentes regiones de estudio (ROI), con el objetivo de identificar patrones comunes y definir criterios  generales para cada etapa del flujo de trabajo. Se utilizaron diversas imágenes satelitales y se implementaron algoritmos de Machine Learning como Random Forest, Support
Vector Machine (SVM) y Árboles de Decisión, analizando su desempeño en distintos escenarios.

Además, se generaron videos explicativos que describen cada etapa esencial del proceso, facilitando la comprensión y replicabilidad del flujo de trabajo. Estos recursos permiten documentar la aplicación práctica de los algoritmos en GEE y sirven como material de referencia para futuras investigaciones.

Cada etapa descrita en la sección de desarrollo fue aplicada y ajustada en función de los resultados obtenidos en diferentes regiones de estudio. Esto permitió abstraer los elementos fundamentales de un flujo de trabajo genérico, estableciendo un esquema replicable para futuras aplicaciones. Se definieron pasos clave como la selección y preprocesamiento de imágenes, la recolección de muestras de entrenamiento y validación, el ajuste de hiperparámetros y la evaluación de precisión mediante métricas estándar.

Este enfoque permitió la construcción de una metodología estructurada y flexible, adaptable a diversos escenarios y necesidades, garantizando la
validez y aplicabilidad del flujo de trabajo propuesto en múltiples contextos geoespaciales.

## DESARROLLO

Para profundizar en la aplicación de las técnicas de clasificación supervisada en Google Earth Engine (GEE), se desarrollaron tres videos
publicados en el canal de YouTube de la Infraestructura de Datos Espaciales de la República Argentina (IDERA), los cuales explican
detalladamente el uso de Random Forest, Support Vector Machine (SVM) y Árboles de Decisión CART. Estos videos han alcanzado cientos de
visualizaciones, lo que demuestra el interés y la relevancia del tema en la comunidad de análisis geoespacial y teledetección.

El código GEE de cada video está disponible en un repositorio público con acceso libre:
<https://code.earthengine.google.com/?accept_repo=users/cdg-idera/gee>
Cualquier usuario de Google Earth Engine que desee utilizar este código en otra área de estudio solo necesita hacer una copia y cambiar las
muestras de entrenamiento. Es decir, el código funciona como una plantilla reutilizable que facilita la adaptación a distintas regiones y
necesidades de clasificación.

La aplicación de estas técnicas requiere un trabajo detallado y pormenorizado, donde cada paso es fundamental para garantizar la
precisión y confiabilidad de los resultados. Desde la selección de los datos de entrenamiento hasta la evaluación del modelo, cada fase implica
la optimización de parámetros y la validación cruzada para evitar sesgos y sobreajustes.

Los videos desarrollados son:

-   Machine Learning con Imágenes Satelitales - Aplicación de Random Forest. Serie Laboratorio 2 (Reynoso, 2025a). En este video introduce la aplicación de machine learning en teledetección y explora la aplicación del algoritmo Random Forest en imágenes satelitales en el área metropolitana de Rosario, mostrando su implementación en GEE y su capacidad para clasificar diferentes categorías de suelo.

-   Aplicación de Random Forest en Imágenes Satelitales para detectar agua. Serie Agua 1 (Reynoso, 2025b). Se demuestra cómo Random Forest puede ser utilizado para la detección de cuerpos de agua en imágenes satelitales, destacando su precisión.

-   Clasificación Supervisada Multiclase con SVM y Árboles de Decisión. Serie Laboratorio 3-4 (Reynoso, 2025c). Se presentan las metodologías para la clasificación supervisada multiclase mediante Support Vector Machine y Árboles de Decisión CART, analizando su desempeño y sus aplicaciones prácticas.

Estos videos proporcionan un recurso educativo valioso para quienes buscan implementar técnicas de aprendizaje automático en la clasificación de imágenes satelitales. A través de estas explicaciones detalladas, se facilita la comprensión de los procesos y se promueve el uso de herramientas avanzadas para el análisis geoespacial.

## ETAPAS DEL FLUJO DE TRABAJO PARA CLASIFICACION SUPERVISADA**

El objetivo del estudio realizado se materializa en un flujo de trabajo mostrado en Figura 1. A continuación describiremos sus partes principales.

### COLECCIÓN DE IMÁGENES DE ENTRADA (INPUT IMAGE)**

En esta etapa del flujo de trabajo se define la colección de imágenes a utilizar, asegurando que la resolución espacial y temporal sea adecuada para el objetivo del análisis geoespacial. Es fundamental seleccionar imágenes con la mejor calidad posible y filtrar aquellas que puedan afectar la precisión del modelo, como las que contienen un alto porcentaje de nubosidad. También se deben considerar otros criterios, como el ángulo del sensor, la calidad radiométrica y la consistencia temporal de las imágenes.

Para mejorar la representatividad de los datos, se pueden aplicar reductores estadísticos como la mediana o percentiles a la colección de imágenes, obteniendo una imagen compuesta que minimice anomalías y maximice la calidad del análisis. La visualización de la imagen compuesta puede realizarse mediante distintas combinaciones de bandas espectrales según el propósito del estudio.

La etapa de adquisición y preprocesamiento de la imagen satelital es crucial para garantizar la calidad del análisis posterior. Para nuestros ejemplos de aplicación: se empleó la colección de imágenes Sentinel-2 corregidas atmosféricamente (COPERNICUS/ S2_SR_HARMONI-ZED). Para mejorar la calidad de la imagen de entrada, se aplicaron filtros que
eliminan imágenes con más del 30% de cobertura nubosa, se seleccionó el rango de fechas entre 2024 y 2025 y se restringió el área de estudio. Posteriormente, se calculó la mediana de la colección filtrada para obtener una imagen compuesta sin nubes y de alta calidad. Finalmente, la imagen se visualizó utilizando los valores espectrales de las bandas rojo, verde y azul (B4, B3, B2).

### RECOLECTAR MUESTRAS DE ENTRENAMIENTO (SAMPLE COLLECTION)

La recolección de muestras de entrenamiento es un paso fundamental en la clasificación supervisada. Para garantizar la representatividad de los datos, se seleccionaron puntos de referencia bien distribuidos a lo largo del área de estudio, asegurando que cada clase de cobertura terrestre esté adecuadamente representada. Otro aspecto clave en esta
etapa es la partición de los datos en conjuntos de entrenamiento y validación. Un porcentaje de las muestras debe utilizarse para entrenar el clasificador, mientras que otro porcentaje se reserva para evaluar la precisión del modelo y garantizar su capacidad de generalización. La correcta distribución de estas muestras es esencial para evitar sesgos y mejorar la robustez del modelo. Se utilizó un muestreo estratificado, y se dividieron los datos en dos conjuntos: el 70%/80% para el entrenamiento del modelo y el 30%/20% restante para la validación. Es fundamental evitar el sobreajuste asegurando que las muestras de validación sean independientes de las de entrenamiento.

### ENTRENAR UN CLASIFICADOR (TRAIN A CLASSIFIER)

El entrenamiento del clasificador es el proceso mediante el cual el algoritmo aprende a diferenciar las distintas clases a partir de los datos de entrenamiento. Se implementaron tres algoritmos de clasificación supervisada: Random Forest, Support Vector Machine (SVM) y Árboles de Decisión CART.

**Random Forest:** Modelo basado en múltiples árboles de decisión, ideal para manejar grandes volúmenes de datos con alta dimensionalidad. El algoritmo Random Forest ha sido ampliamente utilizado debido a su capacidad para manejar datos de alta dimensionalidad y mitigar el sobreajuste (Breiman, 2021; Barreñada et al., 2024; Cavalheiro et al.,
2023).. Este modelo genera múltiples árboles de decisión y combina sus resultados para obtener una clasificación más precisa y estable. Para su implementación en GEE, se utilizó el método:

```javascript
var classifier = ee.Classifier.smileRandomForest(100).train({ features:
training, classProperty: \'landcover\', inputProperties:
composite.bandNames()});
```

donde 100 representa el número de árboles en el bosque aleatorio.

Se exportaron los resultados y se evaluó la exactitud utilizando la matriz de confusión, obteniendo una precisión del 98.5% en la clasificación de cuerpos de agua en el área de estudio.

**SVM:** Algoritmo que busca encontrar un hiperplano óptimo que maximice la separación entre clases. SVM busca encontrar un hiperplano óptimo que
separe los datos en clases. Es especialmente útil en escenarios donde las clases no están claramente separadas linealmente. SVM ha sido
utilizados en múltiples estudios para la clasificación de imágenes satelitales, mostrando resultados satisfactorios en términos de
precisión y regularización del modelo (Scholkopf & Smola, 2022). SVM se implementó en GEE utilizando:

```javascript
var classifier = ee.Classifier.libsvm().train({ features: training,
classProperty: \'landcover\', inputProperties: composite.bandNames() });
```

Se realizó una evaluación de exactitud con coeficiente de Kappa, obteniendo un valor de 1, lo que indica una alta fiabilidad (100%).

**Árboles de Decisión CART:** Modelo jerárquico basado en reglas de decisión, útil para interpretabilidad (Blanquero et al., 2021) y análisis de características importantes. Los árboles de decisión clasifican datos en función de preguntas lógicas basadas en umbrales de las bandas espectrales. Se implementaron en GEE con:

```javascript
var classifier = ee.Classifier.smileCart().train({ features: training,
classProperty: \'landcover\', inputProperties: composite.bandNames() });
```

El árbol de decisión obtenido en la región de estudio contó con 7 nodos de decisión y 8 hojas, con una precisión general del 98.02%. Se generaron gráficos con Google Colab para visualizar el árbol de decisión y analizar sus reglas, facilitando la interpretabilidad del modelo. Se provee acceso al código Python utilizado para visualizar las reglas del árbol de decisión en la descripción del video.

### CLASIFICAR LA IMAGEN (CLASSIFY THE IMAGE)

Una vez entrenado el clasificador, este debe aplicarse a la imagen preprocesada para generar un mapa de clasificación. En esta etapa, cada píxel de la imagen satelital es etiquetado según la clase más probable según el modelo entrenado. La correcta aplicación del clasificador es esencial para garantizar la coherencia espacial de los resultados y minimizar errores de clasificación.

### EVALUACION DE PRECISIÓN (ACCURACY ASSESSMENT)

Para evaluar la calidad del modelo, se deben calcular métricas de desempeño como la precisión general, la precisión del productor y la precisión del consumidor (entre otras).

-   **Precisión general:** Representa el porcentaje de píxeles correctamente clasificados respecto al total de muestras.

-   **Precisión del productor:** Indica la probabilidad de que un píxel de una clase real haya sido correctamente clasificado.

-   **Precisión del consumidor:** Representa la probabilidad de que un píxel clasificado en una categoría pertenezca efectivamente a dicha clase. Estas métricas se calcularon mediante la matriz de confusión.

-   **Otras métricas:** También es posible utilizar técnicas como el coeficiente de Kappa y F-Score, entre otras.

### ANÁLISIS DE DECISIÓN

Una vez realizada la evaluación de precisión y a partir de las métricas obtenidos es necesario realizar un análisis de decisión, para determinar si es necesario ajustar las muestras de entrenamiento o realizar optimización de hiperparámetros. En el caso de la aplicación de Random Forest, se identificó que ciertas clases presentaban baja precisión del productor o del consumidor, lo que llevó a ajustar los parámetros del modelo para mejorar su desempeño, aplicando 100 árboles en lugar de 50 lo permitió mejora. Esta etapa iterativa es fundamental para optimizar la clasificación y asegurar resultados más precisos y confiables. 

### PRODUCIR ESTADÍSTICAS DE CLASIFICACIÓN. 

Una vez aceptado el modelo final, contabilizar píxeles por clase (ver fig. {numref}`fig-workflow`).

```{figure} imagenes/Workflow.png
:name: fig-workflow
:width: 100%

Flujo de Trabajo de Aprendizaje Supervisado con Machine Learning
```

## CONCLUSIONES

La generación de un flujo de trabajo estructurado es esencial para la clasificación supervisada de imágenes satelitales con Machine Learning en Google Earth Engine. La estandarización de las etapas permite mejorar la reproducibilidad, escalabilidad y eficiencia del proceso de clasificación, facilitando su aplicación en distintos contextos
geoespaciales. 

Definir claramente cada fase del flujo de trabajo---desde la selección de imágenes hasta la evaluación del modelo---permite optimizar los recursos y reducir errores en la clasificación. La identificación de criterios clave, como la calidad de las imágenes, la partición adecuada de los datos en entrenamiento y validación, y la aplicación de métricas de precisión, garantiza resultados más confiables y adaptables a diferentes estudios.

Además, la implementación de algoritmos como Random Forest y SVM ha demostrado ser eficaz en términos de precisión y estabilidad, mientras que herramientas como GEE ofrecen una  plataforma accesible y escalable para el procesamiento y análisis de imágenes satelitales. La automatización y replicabilidad de este flujo de trabajo facilitan la 
democratización del acceso a datos geoespaciales y potencian el desarrollo de capacidades en el ámbito de la teledetección y la inteligencia artificial aplicada.

En conclusión, el establecimiento de un flujo de trabajo bien definido y replicable es clave para la clasificación supervisada de imágenes satelitales, promoviendo metodologías estandarizadas y mejorando la eficiencia en la toma de decisiones basadas en datos geoespaciales. Los resultados del estudio respaldan la aplicación de metodologías basadas
en Google Earth Engine y aprendizaje automático para la clasificación supervisada de imágenes satelitales. Se concluye que:

1.  La metodología propuesta es replicable y escalable, permitiendo su aplicación en diferentes contextos geográficos.

2.  GEE facilita la democratización del acceso a datos satelitales, eliminando barreras técnicas y económicas.

3.  Futuras investigaciones pueden centrarse en la integración de redes neuronales profundas para la detección de patrones espaciales complejos y el uso de aprendizaje no supervisado para la clasificación automática de grandes volúmenes de datos satelitales.