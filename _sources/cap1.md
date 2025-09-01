---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# GEE

```{code-cell} ipython3
from IPython.display import Image
Image(filename='imagenes/GEE.png', width=300, height=150)
```

## Introducción


```{code-cell} ipython3
import ee
import geemap

```
## Inicializar

```{code-cell} ipython3
ee.Initialize(project='ee-luisreynoso')
```

```{code-cell} ipython3
SPC = ee.FeatureCollection("projects/ee-luisreynoso/assets/SPCH")
Map = geemap.Map()
Map
```




