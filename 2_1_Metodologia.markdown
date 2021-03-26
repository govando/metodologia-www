---
layout: page
title: Metodología
permalink: /metodologia/
resource: true
categories: [Indice]
---

## Metodología de 2 Pasos

La metodología desarrollada contempla 2 grandes pasos, los cuales se entregan
en la forma de módulos, y cada uno contiene sub módulos, como se muestra en Figura 1.
 
{% figure caption:"Figura 1: Metodología de 2 pasos" %}
![figura_1](/assets/modelos_metod_2_pasos.png)
{% endfigure %}

### Paso 1: Planeamiento de Capacidad de múltiples Aplicaciones


#### Módulo 1.1 AOSA y Teoría de Colas

El primer paso tiene como objetivo obtener el número óptimo de VMs para soportar una
carga de trabajo dada expresada como la tasa de peticiones entrantes por milisegundo a una 
aplicación. 

El Espacio de Soluciones esta compuesto por tuplas de tipos de máquinas virtuales, las cuales son
las variables independientes. Cada tipo de máquina virtual aloja un servicio específico, y pertenece
un tipo de servidor. El tamaño de una tupla solución estará dado por el 
número de sus *N* componentes, el *M* tipo de servidores y los *O* tipos de máquinas virtuales,
resultando así en una tupla de tamaño *N*x*M*x*O*.

El resultado de la tupla es la Utilización y Tiempo Medio de Respuesta, expresado como una 
nueva tupla de igual tamaño, donde cada elemento respectivo indica la utilización promedio
y el tiempo medio de respuesta sobre el sub conjunto de máquinas virtuales.  


En la Figura 2 se muestra como entran parámetros a la metodología y 
se obtiene el Espacio de Soluciones de la forma mencionada. Se muestra
en verde aquellas soluciones que no superan los límites de tiempo promedio de respuesta
y la utilización, en rojo si ocurre lo contrario, es decir, una violación de servicio. 


{% figure caption:"Figura 2: Entradas, espacio de soluciones y resultados" %}
![figura_2](/assets/modelos_tuplas_solucion_resultado.png)
{% endfigure %}


El tamaño del *espacio de soluciones* para una aplicación
está dado por el algoritmo de fuerza bruta.
Por lo tanto, está en función del tamaño de la tupla solución 
y los valores máximos, esto es, la tupla solución 0 (más info
en [Modelo de Rendimiento](#modelo-de-rendimiento-aosa-y-teoría-de-colas)).

Para el ejemplo de Figura 2, se tiene como *tupla solución 0* -> (9, 6, 4, 7, 5, 3)
por lo tanto el número de tuplas soluciones esta dado por:

[(9+1) * (6+1) * (4+1) * (7+1) * (5+1) * (3+1)] -1 = 67.199 combinaciones


Los números en gris de la Figura 4 representan la cantidad máxima de VM que se necesita
para  ejecutar las aplicaciones en un sistema computacional dada la menor utilización deseada. 
Al combinar tipos de VMs, la aplicación del método AOSA se hace más compleja debido a la
cantidad de variables que intervienen.

Las soluciones en rojo no se consideran seguras y se descartarán en el siguiente módulo (módulo. 1.2)

El algoritmo de lo descrito corresponde al módulo 1.1 y se muestra en Figura 3. Se calcula
la cantidad máxima de réplicas para cada tipo de VM en cada Servidor. En la Imagen de la Figura 2,
son los números marcados en gris. Ej., para la VM tipo L, en el Servidor 1 (en PM 1), se calcula 
que se requieren 3 réplicas como máximo, para una utilización de 40% (mínima utilización). 

Luego, se itera y procesa cada combinación con Fuerza Bruta, desde la tupla [ 0 0 0 0 0 1]
hasta la máxima [ 9 6 4 7 5 3 ]. En cada iteración, se calcula una carga de trabajo distribuida
para cada sub conjunto de tipos de VMs con el objetivo de que la distribución sea equitativa. 
Por cada tupla, se calcula la utilización y Tiempo Medio de Respuesta. 

La complejidad de utilizar AOSA está dada por:
 - Al existir servidores y VM heterogéneas, se complejiza la utilización directa de AOSA,
 ya que asume que la carga de trabajo se distribuye equitativamente en las VMs.
 - Al aplicar AOSA directamente en nuestro escenario heterogéneo, se sub-utilizarían recursos: 
 Ej. si existen 2 VMs, de 2 y 6 CPUs, AOSA entregará resultados tal que la carga de trabajo 
 se distribuye en partes iguales, es decir, la VM de 2 CPU tendrá sobre-utilización y la de
 6 CPU sub-utilización


{% figure caption:"Figura 3: algoritmo de módulo 1.1" %}
![figura_3](/assets/modelos_paso1.1.png)
{% endfigure %}

### Módulo 1.2 Políticas y Algoritmos de Tolerancia a Fallas

Dado el Espacio de Soluciones (posibles despliegues), se debe encontrar la solución óptima para
desplegar. Se considera como óptimo para nuestro contexto: Sistema desplegado que sea estable
incluso ante posibles fallas.

Los pasos a realizar se muestran en Figura 4 y son los siguientes:
- Remover Soluciones no Seguras por utilización: Se descartan las soluciones con utilización mayor al 80%
- Remover Solución si no cumple criterio Multi-Zona: parámetro de entrada: Multi-Zona, que es
 un entero e indica en cuántas Zonas se desea desplegar la App. 
 Por ejemplo, para Bases de datos se recomienda un 3 (o un número impar). Se descartan 
 las soluciones que no cumplan:
 $$SUMA (VMs) >= nivel\\_multi\\_zona$$ 
 por ejemplo, si se desea un nivel_multi_zona = 3, no sirve una solución que contemple solo 2 VMs.
 - Análisis de Tolerancia a fallas: parámetro de entrada: número mínimo de réplicas. Dada la 
 tupla de VMs de la solución, se remueven (replicas-1) VMs (se remueven las que tengan mayor
  número de CPU). Luego se calcula la utilización y tiempo medio de respuesta sobre la nueva tupla.
  Estos nuevos parámetros se almacenan en la solución como *parámetros ante degradación de servicio*. 

{% figure caption:"Figura 4: algoritmo de módulo 1.2" %}
![figura_4](/assets/modelos_paso1.2.png)
{% endfigure %}

Luego se genera un Ranking de Rendimiento por aplicación, el cual es una
heurística basada en Tiempo de Ejecución en cada tipo de sistema y Peticiones entrantes $\lambda$
(Figura 5).
Se busca obtener el menor valor posible. El significado de esto es encontrar los servidores
donde las aplicaciones se ejecuten más rápido.


{% figure caption:"Figura 5: Ranking de rendimiento" %}
![figura_5](/assets/modelos1.2__ranking.png)
{% endfigure %}

## Paso 2 despliegue de aplicaciones

Objetivo: Desplegar cada uno de los componentes de una aplicación de manera que el
despliegue sea tolerante a fallas y de alta disponibilidad

Tolerancia a fallas: replicación (entregada de soluciones del módulo anterior)
Alta disponibilidad: selección de Clusters resilientes

Objetivo:
Generar un ranking de prioridades de Clusters según sus datos de disponibilidades conocidas

Método usado: Heurística basada en una fórmula matemática para generar un Ranking (Figura 6)

El Paso Ranking (módulos 2.1 y 2.2) sirven para hacer una heurística en el paso del despliegue. 
La salida del módulo 1.1 sirve se utiliza como entrada para el Paso 2, en el Paso Despliegue, 
se partan seleccionando los Cluster que sean más resilientes, pensando en que en un contexto de 
Catástrofe, esto sería algo prioritario. Por ejemplo, un Cluster con UPS 
(u otro tipo de soportes eléctricos) debería tener una mayor Disponibilidad histórica que uno sin UPS. 


{% figure caption:"Figura 5: Ranking de rendimiento" %}
![figura_5](/assets/modelos_ranking_resiliencia.png)
{% endfigure %}






