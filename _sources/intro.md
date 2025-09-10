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

```{code-cell} ipython3
from IPython.display import Image
Image(filename='imagenes/GEE.png', width=336, height=48)
```

# Prólogo

Estimado lector,
Bienvenido a un libro que, de una manera inusual y fascinante, refleja un recorrido que probablemente ya hayas transitado de otra forma. Este libro surge, en esencia, como una traducción a palabras escritas de una serie de videos que muchos de ustedes ya han visto en el canal de IDERA. Si has sido espectador de esos videos, encontrarás aquí los mismos capítulos transformados en textos, con el objetivo de ofrecerte una experiencia de lectura que te resulte familiar y, a la vez, enriquecedora.

Es poco común que un libro nazca después de que sus capítulos hayan sido difundidos primero en formato audiovisual. Sin embargo, este texto busca precisamente acompañar esa transición: el paso de ver y escuchar a leer y reflexionar. En cada página, te encontrarás con un terreno ya conocido, pero también con la oportunidad de profundizar en conceptos que antes viste en pantalla.

Además, este libro es testigo de una época de cambio y adaptación tecnológica, en la que nos aventuramos a integrar la inteligencia artificial aplicada a información geoespacial, en particular a imágenes satelitales. Lo que leerás aquí no es solo un compendio de técnicas y algoritmos, sino también un reflejo de nuestros primeros pasos en la exploración de estas nuevas fronteras digitales. La intención es acompañarte en este viaje de transformación digital, donde las infraestructuras de datos espaciales son un pilar fundamental para construir un verdadero conocimiento territorial.

Esperamos que esta lectura te resulte no solo instructiva, sino también inspiradora, y que encuentres en estas páginas un puente entre lo que ya has visto y lo que ahora podrás revisitar con una mirada renovada.

Cabe resaltar que este libro no solo es un reflejo de un contenido ya visualizado, sino también una invitación a explorar la enorme riqueza de la información geoespacial. Las técnicas de inteligencia artificial que aquí se presentan enriquecen enormemente nuestra capacidad de comprender el territorio en sus múltiples dimensiones. Al trasladar estos conceptos del video a la palabra escrita, queremos subrayar cómo el uso de datos geoespaciales y su análisis con IA no solo permite mapear y clasificar, sino también generar predicciones y nuevas formas de interpretar el territorio.

En este sentido, la lectura te permitirá apreciar cómo la inteligencia artificial se convierte en una herramienta clave para construir un verdadero modelo digital del territorio, uno que revele las múltiples capas de información y permita a todas las disciplinas beneficiarse de estas técnicas. Desde la planificación urbana hasta la gestión ambiental, el uso de datos geoespaciales potenciados por IA abre un abanico de posibilidades para el desarrollo de capacidades y la toma de decisiones fundamentadas.

Al incorporar la inteligencia artificial en el análisis de estos datos, estamos enriqueciendo nuestra capacidad de interpretar y utilizar esa información. Lo que tienes en tus manos es, por lo tanto, más que un simple texto: es una guía para adentrarte en un territorio de conocimiento donde la geoespacialidad y la inteligencia artificial se entrelazan.

Esperamos que disfrutes de esta lectura y que te sirva como un puente entre lo que ya has visto y lo que ahora podrás redescubrir con una mirada más profunda. La información geoespacial, como bien señala la CEPAL, es un cimiento vital de nuestra era, y este libro te acompañará en ese camino de comprensión y aprendizaje.

# Dedicación

Este libro esta dedicado a todos los integrantes de IDERA que han comenzado a transitar un nuevo camino de aprendizaje sobre tecnicas de inteligencia artificial. En particular está dedicado a los miembros del grupo técnico de trabajo de Ciencia de Datos geoespaciales y a los alumnos de la Escuela de Primavera del Instituto Gullich donde tendré la oportunidad de brindar una capacitación utilizando este material.

# Agradecimientos



# Sobre este libro

El objetivo de este libro digital es impulsar el desarrollo de capacidades sobre cómo emplear tecnicas de geoIA con información geoespacial, principalmente empleando imágenes satelitales disponibles en Google Earth Engine.

Este libro interactivo digital está siendo desarrollado con [Jupyter Book documentation](https://jupyterbook.org) y tendrá ISBN tramitado por IDERA-IGN.

