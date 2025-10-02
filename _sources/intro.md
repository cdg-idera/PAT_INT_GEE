# Prólogo e Introducción

Estimado lector:

Bienvenido a un libro que nace de un recorrido poco habitual, pero profundamente enriquecedor. Su origen no está en el papel, sino en una serie de videos difundidos a través del [canal de IDERA](https://www.youtube.com/results?search_query=idera+argentina), que muchos de ustedes ya habrán visto. Este texto es, en esencia, la traducción de aquella experiencia audiovisual a la palabra escrita. Si alguna vez acompañaste esos capítulos en pantalla, aquí los encontrarás transformados en páginas que buscan ofrecerte una experiencia distinta: familiar por lo ya conocido, pero renovada en su profundidad y en su invitación a la reflexión.

La singularidad de este proyecto radica en su génesis inversa: primero se difundieron los contenidos de manera audiovisual y solo después se transformaron en un libro. Esta transición no es casual, sino que responde a la voluntad de tender un puente entre ver y escuchar, por un lado, y leer, pensar y volver a recorrer, por el otro. Cada página constituye un terreno ya transitado, pero también una oportunidad para detenerse, desmenuzar ideas y descubrir matices que en el formato audiovisual quizás pasaron desapercibidos.

Este libro es también testimonio de una época de cambio acelerado, marcada por la integración de la inteligencia artificial en el campo de la información geoespacial, en particular en el análisis de imágenes satelitales. Lo que aquí se presenta no es únicamente un compendio técnico de algoritmos y procedimientos, sino una muestra de los primeros pasos hacia nuevas fronteras digitales. Su propósito es claro: acompañarte en este viaje de transformación digital en el que las infraestructuras de datos espaciales se consolidan como pilares para construir un verdadero conocimiento territorial.

La riqueza de la información geoespacial merece ser destacada. Como señala Naciones Unidas {cite:p}`UN-IGIF:MarcoIntegradoInformacionGeoespacial`, esta constituye un cimiento vital de la economía del conocimiento en el siglo XXI: nos permite saber no solo dónde están las cosas, sino también en qué estado se encuentran y cómo se relacionan entre sí. Al combinar estos datos con técnicas de inteligencia artificial, se abre la posibilidad de ir más allá del mapeo y la clasificación. Hoy podemos generar predicciones, modelar escenarios y explorar nuevas formas de interpretar el territorio en todas sus dimensiones.

Este cruce entre geoespacialidad e inteligencia artificial revela un potencial inmenso para las ciencias, las políticas públicas y la sociedad en su conjunto. Desde la planificación urbana hasta la gestión ambiental, pasando por la agricultura, la energía o la infraestructura, los datos geoespaciales potenciados por IA amplían nuestras capacidades y ofrecen herramientas concretas para tomar decisiones más informadas y sostenibles. En este sentido, el libro que tenés en tus manos no es simplemente un texto derivado de videos, sino una guía para adentrarte en un territorio de conocimiento complejo y prometedor, donde la tecnología y la inteligencia colectiva se entrelazan.

Esperamos que esta lectura te resulte tanto instructiva como inspiradora. Que funcione como un puente entre lo que ya has visto y lo que ahora podrás redescubrir con una mirada más profunda, más crítica y más abierta a las posibilidades que nos ofrece la IA en la era digital.

*Luis Reynoso*
Autor del libro digital

# Dedicación

Este libro está dedicado a todos los integrantes de la Infraestructura de Datos Espaciales de la República Argentina (IDERA) que han comenzado a transitar un nuevo camino de aprendizaje sobre técnicas de inteligencia artificial. En particular está dedicado a los miembros del Grupo Técnico de Trabajo de Ciencia de Datos geoespaciales (GTT-CDG) y a los alumnos de la Escuela de Primavera 2025 del Instituto Gullich (CONAE-UNC) donde tendré la oportunidad de brindar una capacitación utilizando este material.

# Agradecimientos

Este libro y recursos didácticos han sido desarrollados en el marco del grupo de investigación **04/F023: Tecnologías de Datos Espaciales, Visualización y Realidad Virtual**, Facultad de Informática, Universidad Nacional del Comahue.


# Sobre este libro

El objetivo de este libro digital es impulsar el desarrollo de capacidades en la aplicación de técnicas de geoAI con información geoespacial, principalmente empleando imágenes satelitales disponibles en Google Earth Engine. 

````{admonition} Objetivo
:class: tip 
Propiciar la incorporación enfoques innovadores y paradigmas de ciencias de datos para maximizar la gestión, análisis y aprovechamiento de la información geoespacial, fomentando el uso de tecnologías emergentes, inteligencia artificial y modelos analíticos avanzados.

````

````{admonition} Producción del libro y de los videos
:class: note

```{admonition} Libro (Jupyter Book)
:class: tip
Este libro interactivo digital está siendo desarrollado con [**Jupyter Book**](https://jupyterbook.org) y tendrá ISBN tramitado por IDERA-IGN.
```

```{admonition} Videos
:class: tip
Los videos asociados a los distintos capítulos se produjeron con componentes de **Adobe Creative Cloud** {cite}`adobe-creative-cloud-2025` —**After Effects** {cite}`adobe-after-effects-2025`, **Illustrator** {cite}`adobe-illustrator-2025`, **Audition** {cite}`adobe-audition-2025` y **Media Encoder** {cite}`adobe-media-encoder-2025`—: **After Effects** para *animación* y *composición de gráficos en movimiento*; **Illustrator** para la *preparación de arte vectorial*; **Audition** para la *edición y mezcla de audio*; y **Media Encoder** para la *transcodificación* y *publicación de los másteres finales*. Adicionalmente, se utilizó **Adobe Stock** {cite}`adobe-stock-2025`; no obstante, una parte sustancial del trabajo consistió en la *adaptación de imágenes*, la *generación de nuevas* a partir de *material base* y la *integración de diversos recursos* durante la *edición de video*.
```

````


# Requerimientos

Para poder reproducir los ejemplos prácticos y aprovechar los contenidos de este libro, es necesario contar con una cuenta en **Google Earth Engine (GEE)**. El registro es gratuito y se realiza en línea mediante una cuenta de Google, lo que habilita el acceso inmediato a un extenso catálogo de imágenes satelitales y productos derivados, así como a la infraestructura de cómputo en la nube que distingue a esta plataforma.

Es importante señalar que el acceso **gratuito** está disponible para fines **académicos, educativos, de investigación y de desarrollo no comercial**, y resulta suficiente para todas las actividades propuestas en este libro. En este modo, estudiantes, docentes y profesionales pueden explorar datos globales, ejecutar algoritmos avanzados de análisis geoespacial y descubrir el potencial de la **GeoAI aplicada a la observación de la Tierra**, sin necesidad de equipamiento especializado.

Cuando el uso de la plataforma se orienta a fines **comerciales o productivos**, GEE requiere una **licencia empresarial de pago**, que se gestiona a través de los servicios de Google Cloud o del programa **Earth Engine for Business**. Esta modalidad ofrece soporte extendido y mayores garantías de servicio para instituciones, gobiernos y compañías que dependen de un uso intensivo en entornos de producción.

En síntesis, la **cuenta gratuita es suficiente para el aprendizaje y la investigación**, y constituye el camino recomendado para iniciarse en el mundo del análisis satelital con GEE.

Una vez que cuentes con tu cuenta en GEE, podrás utilizar el siguiente enlace para explorar el repositorio público de código en JavaScript:  
🔗 [Repositorio público de IDERA en GEE](https://code.earthengine.google.com/?accept_repo=users%2Fcdg-idera%2Fgee)

Además, para complementar la programación en JavaScript en el Code Editor de GEE, utilizaremos **Google Colab** para ejecutar código **Python**. Allí mostraremos, entre otras cosas, cómo *generar ejemplos sintéticos* y *visualizar árboles de decisión* a partir de archivos .dot exportados desde GEE, ampliando así las posibilidades de análisis y documentación de los resultados.

En síntesis, una cuenta gratuita de GEE es suficiente para el aprendizaje y la investigación, y constituye el camino recomendado para iniciarse en el mundo del análisis satelital con GEE.

# Tabla de Contenidos

```{tableofcontents}
```