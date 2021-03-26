---
layout: page
title: Validación de Metodología
permalink: /validacion/
resource: true
categories: [Indice]
---

## Validación

La validación de la metodología se realizó comparando sus resultados con los datos obtenidos en pruebas
reales de las aplicaciones. En la Figura 1 se muestran los pasos que se siguieron para realizar la validación.

Se realizaron benchmarks específicos de aplicaciones, para cada una de sus tareas, mediante la instrumentación
del código, con el fin de medir los tiempos de ejecución. Cabe destacar que solo los componentes whitebox
fueron instrumentados, no así bases de datos o caches, ya que son cajas negras. Además, se obtuvieron los
datos de utilización de CPU de cada máquina virtual, registrados en archivos CSV.

Con la instrumentación se obtienen estadísticas como tiempo promedio de ejecución por petición y la desviación
estandar.

Para los componentes de caja negra, se combinaron la instrumentación con los datos de utilización. De esta
forma encontraron las estadísticas de los componentes black box.


{% figure caption:"Figura 1: Pasos de la validación" %}
![figura_1](/assets/validacion_modelo.png)
{% endfigure %}


El *Método para ajustar parámetros de Componentes BlackBox* realiza ajustes a los tiempos de servicio. 
Esto porque en algunos casos, la mayor parte del tiempo es generada por los Drivers de conexión a base de datos
por ejemplo, los cuales no son viables de ser instrumentados debido a que son cajas negras, o si son de código 
abierto se tardaría mucho tiempo en realizar completamente y correctamente su instrumentación.  
Al realizar la medición de tiempos, se obtienen resultados erróneos, ya que por ejemplo, gran parte
de los tiempos de base datos obtenidos serán parte de la ejecución del Driver (el cual es ejecutado en un BOT
o Backend).

Es por ello que a partir de la utilización de cada componente, se puede calcular la proporción de tiempo en 
cada componente. Por ejemplo, para algunos casos de aplicaciones asíncronas se encontró que el tiempo 
de ejecución en base de datos era mayor que en el componente donde se generaba la petición. 
Sin embargo, como se muestra en la Figura 2, la máquina virtual que contiene el Bot (Fig. 2 izquierda) 
presenta una mayor utilización que la base de datos (Fig. 2 derecha), donde sus peaks alcanzan el
100% y el 24% respectivamente, sin embargo los tiempos dados por la instrumentación indican que el 
bot tuvo un tiempo de 2.6 ms y la base de datos 18.2 ms. En la Figura 3 se muestra el tiempo total de ejecución 
(BOT+DB=20.84ms). En base a los datos de ambas utilizaciones, se calculó una proporcionalidad de tiempo.


En base a los datos de ambas imágenes, se calculó una proporcionalidad de tiempo


{% figure caption:"Figura 2: Utilizaciones de máquinas virtuales Bot (izquierda) y Base de datos(derecha)" %}
![figura_2](/assets/validacion_util.png)
{% endfigure %}



{% figure caption:"Figura 3: Método de ajuste de tiempos" %}
![figura_3](/assets/validacion_tiempos_hack.png)
{% endfigure %}


De esta manera, la metodología pudo generar resultados tal que la utilización teórica y la real
presentaron una correlación de pearson promedio mayor al 90%. En la Figura 4 se muestran 4 
utilizaciones reales y sus respectivas utilizaciones teóricas obtenidas con la metodología mencionada. 
Específicamente, para el caso (a) se obtuvo un Pearson de 0.9689, para (b) 0.47775, para (c)  0.9731
y (d) 0.9921. Cabe destacar que el caso (b) es el único caso -de 40 casos- bajo 0.9, y esto debido a que fue
el único caso donde se alcanzó la saturación del componente, siendo reiniciado por Kubernetes. 
En nuestro contexto, no se pretende llegar al escenario de saturación, al contrario, es necesario conocer 
el límite para no llegar a él, por lo tanto los resultados obtenidos son buenos.


{% figure caption:"Figura 4: Utilización de 4 actividades distintas y sus 4 tramos (16 actividades en total)" %}
![figura_3](/assets/validación_comparacion_util.png)
{% endfigure %}


[jekyll-organization]: https://github.com/jekyll
