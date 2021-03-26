---
layout: page
title: Ejemplo de experimentación
permalink: /experimentacion/
resource: true
categories: [Indice]
---

## Descripción del escenario de experimentación

A continuación se describe el escenario y el contexto sobre el cual se realiza 
la experimentación de la metodología. Estos datos corresponden a datos de entrada
en la metodología. 

### Lugar geográfico y población

La experimentación se realizó en base a datos conocidos y supuestos en torno al terremoto
en Chile el año 2015. Se identificó al total de la población de la quinta región. 

    Emergencia: Terremoto
    Lugar: Quinta región
    Población total: 1.979.373 personas [1]
    Número de personas que evacuaron: 660.000 [2]
    Número aproximado de personas afectadas: 1.000.000

### Aplicaciones a desplegar

Asumimos el despliegue de 3 aplicaciones para este evento: Jayma, Ayni y Rimay. 
Jayma tiene como misión reportar el estado actual de un usuario durante la 
situación de crisis, entregando su localización, por este motivo asumimos
el despliegue de una versión general para el uso de un porcentaje los 
afectados (esto sin desmedro de la cantidad de réplicas de la aplicación 
que se tengan que desplegar para cubrir la demanda). Respecto a las otras 2 aplicaciones,
se busca realizar la coordinación de equipos de trabajo mediante Rimay
y la coordinación de los voluntarios gracias a Ayni. Este esquema quedará distribuido con
un Rimay por Institución u ONG, y un Ayni por cada Líder de equipo determinado por las 
instituciones. Además, existirá un Rimay para coordinar el trabajo entre todas las instituciones. 


### Numero de Participantes por ONG/Instituciones y de población

La cantidad de ONG/Instituciones y el número de sus usuarios se detallan en Tabla 1, 
donde “población” representa el número real de participantes por Institución, 
el “% de part” representa el porcentaje de participantes por institución, obtenido 
de noticias reales publicadas en la web [3, 4, 5, 6], con este porcentaje se obtuvieron
el número de participantes, el número de Líderes por Institución se definió 
arbitrariamente pensando en un número de voluntarios manejable por cada Líder. 
Se destaca que la Institución “Movidos X Chile*” tiene como rol principal 
coordinar las diversas ONG/Instituciones de voluntariado.

{% figure caption:"Tabla 1" %}
| Institución | Población | % de part. | Voluntarios | Líderes |
|-------|--------|---------|---------|---------|
| Psicólogos Voluntarios | 2000 | 80 | 1.600| 16 |
| Techo|3157|80|2.526|22|
|Cruz Roja Chilena|2250|80|1.800|18|
|Universidad de Valparaíso|14000|10|1.400|14|
|Universidad de Viña del Mar|9000|20|1.800|18|
|Pontificia Universidad Católica|29000|70|20.300|100|
|Movidos X Chile *|0 |||1|
|Total-voluntarios|||29.426|195|
{% endfigure %}


Adicionalmente, se estima de un 6% de la población afectada hace uso de la 
aplicación Jayma (aproximadamente 1.000.000 de personas), para identificar
los puntos de seguridad en la zona afectada e informar a sus cercanos su 
estado actual, con lo cual el número de usuarios de Jayma ascendería a los 60.000.

Se analizará el intervalo de una hora donde se asume un periodo crítico, en
donde los usuarios usarán las aplicaciones. Se calcula la tasa de peticiones
para cada tipo de aplicación usando la Fórmula 1.


{% figure caption:"Fórmula 1: fórmula para el calculo de la tasa de arribo de peticiones" %}
![figura_1](/assets/experimentacion_formula_tasa_peticiones.png)
{% endfigure %}


Debido a que la metodología utiliza como unidad de tiempo los milisegundos, las tasas de 
peticiones por usuario se estiman como *peticiones/milisegundo*, obteniendo así los resultados 
de Tabla 2.

{% figure caption:"Tabla 2" %}
|Aplicaciones|Usuarios|Peticiones|Tiempo Peak (hora)|Tasa de arribo (peticiones/milisegundos)|
|-------|-------|-------|-------|-------|
|Ayni|29621 (voluntarios)|25|1|0.20570138888|
|Rimay|195 (Líderes)|100|1|0.00541666666|
|Jayma|60000 (Usuarios)|5|1|0.42280555555|
{% endfigure %}


### Infraestructura simulada

La infraestructura simulada corresponde a datos reales obtenidos a través de encuestas, los
cuales fueron publicados en Libro Blanco de este mismo proyecto (FONDEF IDeA ID15I20560).
Para esta experimentación, se simularon 3 universidades: U. Católica del Norte, U. de Chile/CSN
y U. de la Frontera. El número de servidores de cada uno son 1, 12 y 6 servidores respectivamente
y se indica que las universidades se encuentran en 3 zonas distintas, Norte, Centro y Sur
respectivamente.
De esta forma, la infraestructura disponible es distribuida geográficamente a lo largo de Chile,
como se muestra en Figura 1. En la metodología 


{% figure caption:"Figura 1: distribución de infraestructura" %}
![figura_1](/assets/experimentacion_despliegue_Experimental.png)
{% endfigure %}

Respecto a los tipos de máquinas virtuales se tiene solo uno. 

Otros datos de entrada a la metodología son:
 - Tolerancia a Fallas mínima: 1 (con 1 servidor caído, se debe seguir funcionando)
 - Nivel Multi-Zona: 2 (se debe desplegar en 2 zonas)
 - Utilización de recursos promedio: [40%-50%]

## Ejecución de la metodología

Los resultados de la ejecución de la metodología son los siguientes:

    - Ayni:
     * combinación de VMs:  [0, 10, 24] 
     * utilización promedio sistema: 47.81%
     * tiempo medio de respuesta del sistema: 151.43725964622155 ms
 
    - Rimay
     * combinación de VMs:  [0, 1, 2]
     * utilización promedio sistema: 23.00%
     * tiempo medio de respuesta del sistema: 159.47641914350476 ms
 
    - Jayma:
     * combinación de VMs:  [10, 35, 16]
     * utilización promedio sistema: 48.59%
     * tiempo medio de respuesta del sistema: 105.50141258161082 ms

La *combinación de VMs* mostrada en cada aplicación, representa de izquierda 
a derecha las máquinas virtuales que se deberían desplegar en servidores de
U. Católica del Norte, U. de Chile/CSN y U. de la Frontera. Así por ejemplo,
para desplegar Rimay se recomienda desplegar 1 máquina virtual en servidores de U de 
Chile/CSN y 2 servidores en U de la Frontera. 
 
Además, se muestra la utilización promedio de los recursos CPU.

Para la simplificación del experimento, se asumió que cada instancia es 
monolítica, encapsulando los diversos componentes que la conforman (Frontend, 
Backend, DB). Sin embargo como se mencionó en la sección 
[Implementación y Modelado, Modelo de Rendimiento](/modelos), 
es posible realizar la separación de los componentes para ser desplegados
independientemente.

El despliegue propuesto no contempla el despliegue del componente Master 
de Kubernetes, el cual debería ser replicado por zona.


## Especificaciones técnicas

La metodología se entrega como un software implementado en lenguaje Python 3.8.
El núcleo del programa permite modelas infraestuctura como servidores, máquinas virtuales
y aplicaciones, cada una con sus especificaciones por medio de archivos JSON.

El programa fue desarrollado y probado en un computador con las
siguientes características:
 - Sistema Operativo: Ubuntu 18.04
 - RAM: 32 GB
 - Disco Duro: 1 TB
 - Disco SSD: 1 TB




