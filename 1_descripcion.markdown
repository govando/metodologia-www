---
layout: page
title: Descripción
permalink: /descripcion/
resource: true
categories: [Indice]
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
## Introducción
Los estudios de planificación de capacidad (capacity planning) son adecuados para respaldar la 
toma de decisiones en la gestión y el funcionamiento de aplicaciones que tienen una gran demanda
 de utilización y requerimientos que deben ser atendidos y resueltos en el menor tiempo posible,
 sobre todo en el caso de desastres de origen natural. Estas aplicaciones deben ser desplegadas
 en grandes grupos de procesadores distribuidos. El planeamiento de capacidad permite
 garantizar que se suministre oportunamente una cantidad suficiente de recursos 
 computacionales para tratar de manera eficiente los flujos siempre cambiantes de las 
 aplicaciones y los requerimientos de los usuarios sobre estas.

Un estudio de planificación de la capacidad es una tarea compleja y que requiere mucho tiempo 
cuando la colección de servicios y componentes que forman las aplicaciones sociales se 
implementan y ejecutan en grupos de procesadores heterogéneos y distribuidos en una cloud. 
Los servicios se alojan en componentes front-end y back-end, son desplegados con componentes 
tecnológicos como Dockers y Kubernetes que permiten satisfacer ciertas características como 
tolerancia a fallas, escalabilidad, elasticidad entre otras,  para lograr el objetivo de 
mantener los tiempos de respuesta de las peticiones de usuarios y la utilización de los 
recursos computacionales por debajo de los límites superiores predefinidos, y mantener la tasa
de requerimientos totalmente procesadas por unidad de tiempo (rendimiento) al mismo nivel que 
la tasa de llegada. 

La configuración de los *N* diferentes componentes de las aplicaciones se puede representar 
como una tupla *N*-dimensional donde cada dimensión representa el número de réplicas o 
particiones para cada componente. Debido a que los recursos (servidores) son heterogéneos, 
el rendimiento de cada componente varía entre los *M* tipos de servidores disponibles, 
aumentando así la complejidad del problema. Más aun, debido a que el despliegue se realiza 
en *O* tipos de máquinas virtuales, el problema se complejiza aun más. De este modo, las 
tuplas n-dimensional de un sistema homogéneo, se transforma en una tupla 
*N*x*M*x*O*-dimensional.


[comment]: <> (\(E=mc^2\)，$$x_{1,2} = \frac{-b \pm \sqrt{b^2-4ac}}{2b}.$$)

La selección de la configuración adecuada implica evaluar el rendimiento del sistema mediante 
todas las configuraciones posibles en la búsqueda de una configuración tal que satisfaga objetivos
como utilizar el menor número de procesadores y mantener el rendimiento de las consultas y los 
límites del tiempo de respuesta.

La metodología de planeamiento de capacidad desarrollada permite determinar el número de réplicas
de cada componente de distintas aplicaciones, junto al respectivo despliegue eficiente y distribuido
en instancias virtuales elásticas sobre un *cloud* nacional de clusters de servidores, con
la finalidad de que las aplicaciones puedan escalar a medida que aumenta el número de usuarios,
acotando los tiempos de respuesta, entregar tolerancia a fallas, y utilizar los recursos computacionales 
eficientemente evitando la saturación de los mismos. 

La metodologı́a se obtuvo desarrollando un marco experimental que integró trazas reales de usuarios, 
obtenidas por el pilotaje de aplicaciones sociales, combinadas con modelos basados en operaciones
combinatoriales junto a fórmulas de análisis operacional y metaheurı́stica, tales que permiten estimar
el rendimiento computacional de las aplicaciones por medio de las caracterı́sticas relevantes que influyen
en el costo de ejecución.


## Contexto del trabajo

El contexto de este trabajo consiste de un grupo de aplicaciones heterogéneas (Figura 1), tanto desde el 
punto de vista de su diseño, requerimiento de recursos computacionales, como cuellos de botella  
que tiene cada una así como los flujos de procesamiento que genera cada tarea ejecutada en las 
aplicaciones. 

{% figure caption:"Figura 1: Aplicaciones sociales de soporte para desastres de origen
 natural utilizadas para validar el sistema" %}
![figura_1](/assets/descripcion_apps.png)
{% endfigure %}

La Figura 2 muestra un ejemplo del contexto planteado en este trabajo. Donde diferentes aplicaciones 
desplegadas en componentes Front-end y Back-end tienen diferentes requerimientos de tiempo de 
procesamiento en diferentes recursos tales como CPU, disco, red y memoria. Las aplicaciones son 
desplegadas en máquinas virtuales heterogéneas las cuales a su vez son ejecutadas en servidores o 
computadores con diferentes características de procesamiento. 

{% figure caption:"Figura 2: Contexto de infraestructura heterogénea e implicación en el
rendimiento de la aplicación Rimay"  class:"mis_figuras" %}
![figura_2](/assets/descripcion_contexto.png)
{% endfigure %}


Algunas limitaciones del trabajo se listan a continuación:

   1. Geografía: distinguimos 3 zonas, separadas por miles de kilómetros: Norte, centro y sur de Chile
   2. Cada zona cuenta con Clusters que alojan Servidores. Se conoce la Disponibilidad (Tiempo de 
   Disponibilidad)
   3. Los DC se interconectan mediante la Red Universitaria Nacional (REUNA)
   4. Se conocen las ubicaciones y especificaciones técnicas de cada Servidor (CPUs, RAM, Disco)
   5. Se definieron 3 tipos de VMs → chica, mediana, grande (limitación al problema)
   6. Cada Componente se encuentra aislado en una VM

Por medio de Benchmarks específicos por cada Componente de Aplicación, 
se conocen los Tiempos de Servicio de cada Tipo de Petición 
(en cada tipo de servidor y tipo de máquina virtual).



