# Capítulo 3 · RF en GEE y Colab


La mayor parte de los capítulos —y sus videos— se centran en el flujo con **Google Earth Engine (GEE)** en su *Code Editor* (JavaScript), porque ofrece un entorno inmediato para explorar datos, depurar en la consola y visualizar resultados en el mapa con muy poca fricción. **Sin embargo, todo ese flujo puede reproducirse en Python** trabajando en **Google Colab** mediante la *Python API* de Earth Engine, conservando el mismo modelo de cómputo **server-side** y beneficiándose del ecosistema científico de Python (por ejemplo, para graficar, registrar experimentos o complementar análisis).

En este capítulo presentamos ambos enfoques **en paralelo** y paso a paso, usando imágenes **Sentinel-2** y un caso de estudio real (el **conurbano de Rosario**). Verás cómo cada operación del flujo de **clasificación supervisada con Random Forest** —carga y filtrado de imágenes, generación del *composite* por mediana, preparación y partición de muestras, entrenamiento, clasificación y evaluación— se expresa de forma equivalente en **JS/GEE** y en **Python/Colab**. La comparación te permitirá elegir el entorno más conveniente según tus objetivos: exploración rápida y cartografía interactiva en el *Code Editor*, o integración con bibliotecas de Python y cuadernos reproducibles en Colab.

El flujo que muestra el capítulo incluye:

1. Carga y filtrado de imágenes.
2. Creación de un composite (mediana).
3. Preparación de datos de entrenamiento.
4. División entrenamiento/validación.
5. Entrenamiento del clasificador Random Forest.
6. Clasificación del composite.
7. Evaluación de exactitud con métricas estándar.


## 1. Carga de imágenes Sentinel-2

Primero se crea una `ImageCollection` de Sentinel-2, se filtra por porcentaje de nubes, por fecha y por región de interés (ROI).

> **GEE (JavaScript)**
```javascript
var s2 = ee.ImageCollection("COPERNICUS/S2_SR_HARMONIZED")
  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 30))
  .filter(ee.Filter.date('2024-01-01', '2025-01-01'))
  .filter(ee.Filter.bounds(roi))
  .select('B.*');
```

> **Colab (Python)**
```python
s2 = (ee.ImageCollection("COPERNICUS/S2_SR_HARMONIZED")
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 30))
    .filterDate('2024-01-01', '2025-01-01')
    .filterBounds(roi)
    .select('B.*'))
```

---

## 2. Creación del composite

Se reduce la colección mediante la **mediana** y se recorta al ROI. Este composite elimina la mayor parte de nubes y anomalías.

> **GEE**
```javascript
var composite = s2.median().clip(roi);
Map.addLayer(composite, {min:0, max:3000, bands:['B4','B3','B2']}, 'Composite');
```

> **Colab**
```python
composite = s2.median().clip(roi)

Map = geemap.Map()
Map.centerObject(roi, 10)
rgbVis = {'min':0, 'max':3000, 'bands':['B4','B3','B2']}
Map.addLayer(composite, rgbVis, 'Composite')
Map
```

---

## 3. Datos de entrenamiento

Cada clase de cobertura (agua, urbano, cultivos, bosque, terreno desnudo) se representa con muestras (`FeatureCollection`) que tienen una propiedad `landcover` con valores entre 0 y 4.

> **GEE**
```javascript
var gcps = urbano.merge(agua).merge(cultivos).merge(terrenoDesnudo).merge(bosque);
```

> **Colab**
```python
gcps = ee.FeatureCollection('users/cdg-idera/gcps_landcover_2024')
```

---

## 4. División entrenamiento/validación

Se divide aleatoriamente el 60% de las muestras para entrenamiento y el 40% para validación.

> **GEE**
```javascript
var gcp = gcps.randomColumn();
var trainingGCP = gcp.filter(ee.Filter.lt('random', 0.6));
var validationGCP = gcp.filter(ee.Filter.gte('random', 0.6));
```

> **Colab**
```python
gcp = gcps.randomColumn()
trainingGCP   = gcp.filter(ee.Filter.lt('random', 0.6))
validationGCP = gcp.filter(ee.Filter.gte('random', 0.6))
```

---

## 5. Extracción de valores espectrales

Se muestrean los valores de reflectancia de las bandas Sentinel-2 en cada punto de entrenamiento.

> **GEE**
```javascript
var training = composite.sampleRegions({
  collection: trainingGCP,
  properties: ['landcover'],
  scale: 10,
  tileScale: 16
});
```

> **Colab**
```python
training = composite.sampleRegions(
    collection=trainingGCP,
    properties=['landcover'],
    scale=10,
    tileScale=16
)
```

---

## 6. Entrenamiento del clasificador

Se utiliza el algoritmo **Random Forest** con 100 árboles.

> **GEE**
```javascript
var classifier = ee.Classifier.smileRandomForest(100).train({
  features: training,
  classProperty: 'landcover',
  inputProperties: composite.bandNames()
});
```

> **Colab**
```python
classifier = ee.Classifier.smileRandomForest(100).train(
    features=training,
    classProperty='landcover',
    inputProperties=composite.bandNames()
)
```

---

## 7. Clasificación

Se clasifica el composite completo y se visualiza con una paleta de colores.

> **GEE**
```javascript
var classified = composite.classify(classifier);
var classVis = {min:0, max:4, palette:['blue','gray','green','violet','orange']};
Map.addLayer(classified.clip(roi), classVis, 'Imagen Clasificada');
```

> **Colab**
```python
classified = composite.classify(classifier)

classVis = {'min':0, 'max':4, 'palette':['blue','gray','green','violet','orange']}
Map2 = geemap.Map()
Map2.centerObject(roi, 10)
Map2.addLayer(classified.clip(roi), classVis, 'Imagen Clasificada')
Map2
```

---

## 8. Validación y métricas

Se evalúa el modelo con las muestras de validación. Se generan métricas de exactitud, matriz de confusión, Kappa y F1.

> **GEE**
```javascript
var test = classified.sampleRegions({
  collection: validationGCP,
  properties: ['landcover'],
  scale: 10
});
var testConfusionMatrix = test.errorMatrix('landcover', 'classification');
print('Confusion Matrix', testConfusionMatrix);
print('Test Accuracy', testConfusionMatrix.accuracy());
print('Producers Accuracy:', testConfusionMatrix.producersAccuracy());
print('Consumers Accuracy:', testConfusionMatrix.consumersAccuracy());
print('Kappa:', testConfusionMatrix.kappa());
print('F-Score:', testConfusionMatrix.fscore(1));
```

> **Colab**
```python
test = classified.sampleRegions(
    collection=validationGCP,
    properties=['landcover'],
    scale=10
)

cm = test.errorMatrix('landcover', 'classification')
print("Confusion Matrix:\n", cm.getInfo())
print("Overall Accuracy:", cm.accuracy().getInfo())
print("Producers Accuracy:", cm.producersAccuracy().getInfo())
print("Consumers Accuracy:", cm.consumersAccuracy().getInfo())
print("Kappa:", cm.kappa().getInfo())
print("F1 (class 1):", cm.fscore(1).getInfo())
```


## 9. Interpretación de resultados

- **Matriz de confusión**: muestra aciertos y errores por clase.  
- **Overall Accuracy**: porcentaje global de aciertos.  
- **Producers/Consumers accuracy**: exactitud desde la perspectiva del productor y del usuario.  
- **Kappa**: mide el acuerdo más allá del azar.  
- **F1-Score**: balance entre precisión y exhaustividad para la clase analizada.


## Conclusión

Este capítulo demostró que **el flujo completo de clasificación supervisada** puede implementarse de forma **equivalente** en **GEE (JavaScript)** y en **Colab (Python API)**, manteniendo el mismo modelo de cómputo *server-side* y los mismos insumos. Trabajar en paralelo en ambos entornos aporta flexibilidad y robustez metodológica.

**Qué te llevás:**
- **Paridad de pasos** entre JS y Python (carga/filtrado, *composite* por mediana, muestreo, entrenamiento RF, clasificación y evaluación).
- **Elección informada del entorno**:  
  - *Code Editor (JS)* para exploración rápida, visualización inmediata y prototipado.  
  - *Colab (Python)* para integración con librerías científicas, experimentación reproducible y documentación.
- **Reproducibilidad y trazabilidad**: notebooks con parámetros explícitos, control de semillas aleatorias y registro de resultados/métricas.
- **Escalabilidad operativa**: misma lógica de procesamiento en la nube con posibilidad de exportar activos, ajustar `tileScale` y gestionar *jobs* largos.

**Recomendaciones prácticas:**
- Prototipá en **JS** y luego porta a **Python** cuando necesites análisis complementarios (gráficos, informes, pipelines).
- Centralizá **ROI, fechas, umbrales y paletas** en variables únicas para evitar divergencias entre entornos.
- Fijá **semillas aleatorias** al particionar (*train/validation*) para comparaciones justas.
- Verificá **métricas de validación** en ambos flujos (OA, matriz de confusión, precisiones del productor/usuario, Kappa, F1) para asegurar consistencia.

**Tópicos en los que puedes investigar:**
- Incorporar **ingeniería de variables** (índices espectrales, texturas, estacionalidad).
- Ensayar **búsqueda de hiperparámetros** y validación cruzada.
- Aplicar **posprocesamiento** (suavizado espacial, reglas de negocio) y análisis de **importancia de variables** para interpretar el modelo.

En síntesis, **JS/GEE** ofrece velocidad y simplicidad para explorar y cartografiar, mientras que **Python/Colab** amplía el análisis y la comunicación científica. Usados en conjunto, facilitan un flujo de trabajo **replicable, auditable y productivo**.
