---
layout: page
title: Manual de uso
permalink: /manual/
resource: true
categories: [Indice]
---


Listado y descripción de los *inputs* de la metodología, los cuales están implementados
como archivos JSON.

A continuación se muestran todos los parámetros que intervienen en la metodología.

## geo_distribution.json

### Zonas geográficas

    "zonas": [
        {"nombre":"Norte", "zona_id":0, "clusters_ids": [0]},
        {"nombre":"Centro", "zona_id":1, "clusters_ids": [1]},
        {"nombre":"Sur", "zona_id":2, "clusters_ids": [2]}
    ],

Definición de las zonas de un territorio geográfico, en este caso de Chile.
Se debe ingresar el id de cada zona, y los *id* de los clusters de la zona   

## methodology_params.json

### Tolerancia a fallas

    "caidas": {
        "modo": "estocastico",
        "estocastico": {
            "hosts": 1
        },
    },

La metodología permite experimentar fallas del tipo estocástico. El número
debe ser int e indica el número de máquinas virtuales que se pretende simular que fallan.

En la metodología se usa un algoritmo goloso para seleccionar las máquinas virtuales que
fallan (los que cuenten con mayor número de cores).

- hosts: int : número de máquinas virtuales que se simulan que fallan

--------------
### Aplicaciones

    "aplicaciones": [
            {
                "nombre": "AYNI",
                "id": 0,
                "programs_id": [0],
                "augmented_inputs_rates": {
                    "0": [0.20570138888 ]
                },
                "read_operations": [0.0],
                "write_operations": "son (1-read_operations) porq es porcentual. se calcula en el programa",
                "nivel_multi_zona": 2
            }, 
            .....
        ],

Las aplicaciones son sistemas los cuales están conformados por una serie de componentes.
Aquí se definen los parámetros del sistema.

- Nombre del sistema: str
- id: int
- programs_id: [int] : ids de los componentes del sistema
- augmented_inputs_rates: [key:str - value:[float]] : cada llave es el id de uno de sus componentes y
corresponde al componente que le llegan las peticiones de los usuarios. Suele ser el 
backend o el bot, y el frontend. Se permite poner varios componentes como entradas.
La cantidad de value es igual al número de actividades, y deben ir en orden ascendente
de id de actividad.
- nivel_multi_zona: int : número de Zonas donde se desea desplegar el sistema. Para
sistemas shared nothing se recomienda 2 o más y para sistemas con base de datos 3 o
un número impar mayor.

----

### Ranking de resiliencia
    
    "reliability_params": {
        "weight_cluster": 0.0,
        "weight_host": 1
    },

*Pesos* para priorizar el nivel de resiliencia en clusters o servidores. La suma de ambos
valores debe ser 1.

- weight_cluster: float : valor entre 0 y 1.
- weight_host: float : valor entre 0 y 1.

---

### Utilización

    "generic_params": {
        "utilization": {
            "app": {
                "max": 0.5
            }
        }
    }
   
Utilización máxima genérica para los sistemas. Esto representa la mayor
utilización promedio que deberían alcanzar las máquinas virtuales del sistema,
pero podría existir una utilización menor para soportar la carga de trabajo dada.
  
 - max: float : valor entre 0 y 1

----

### Versionamiento de código

    "version_params": {
        "version_to_use": 0,
        "comment": "incluye dumimes, y db",
        "v0": {
            "nbr_replicas_for_ha": {
                "db": 0
            },
        },
        "v1": {
            "nbr_replicas_for_ha": {
                "db": 2,
                "cache": 1
            },
        }
    }

No se utiliza ya que está en etapa de validación. Permitirá indicar el número réplicas
mínimos de bases de datos secundarias y caches mínimos, ya que por defecto una base
de datos tiene solo una réplica secundaria para entregar tolerancia a fallas. El
*cache* es de pruebas experimentales.
            
## settings.json

### Servidores multicores

    "multicores": [
        {
            "name": "MULTICORE_CATOLICA_NORTE",
            "ip": [0],
            "type_id": 0,
            "nbr_cores": 8,
            "frecuency": 1400,
            "ram": {  "size_bytes": 33554415000000, "occupied_bytes": [ 2000000 ],
                "h": "32GB  y 2 usados"
            },
            "hhd": {  "size_bytes": 1000000000000,  "occupied_bytes": [ 20000000 ],
                "h": "1 tera y 20gb usados"
            }
        },
        ...
        ...
    ],

Se definen los *tipos de servidores* de manera genérica. Varios cluster pueden tener
los mismos *tipos de servidores*.

 - name: str : nombre del tipo de servidor
- ip: [int] : lista de ips con todos los servidores que se modelarán en el experimento.
el valor puede ir desde 0. Debe ser único.
- type_id: int : identificador único del tipo de servidor
- nbr_cores: número de cores del servidor. No se utiliza pero sirve si se desea agregar
otro modelo de rendimiento como un simulador.
- frecuency: frencuencia del tipo de servidor. No se utiliza pero sirve si se desea agregar
otro modelo de rendimiento como un simulador.
- ram->size_bytes: [int] : cantidad de ram de cada servidor. Cada servidor debe tener
su propio valor. El tamaño de esta lista debe ser igual al tamaño de lista de *ip* 
- ram->occupied_bytes: [int] : cantidad de ram usada por cada servidor. Cada servidor debe tener
su propio valor. El tamaño de esta lista debe ser igual al tamaño de lista de *ip* 
- hhd->size_bytes: [int] : cantidad de disco de cada servidor. Cada servidor debe tener
su propio valor. El tamaño de esta lista debe ser igual al tamaño de lista de *ip* 
- hhd->occupied_bytes: [int] :  cantidad de disco usado por cada servidor. Cada servidor debe tener
su propio valor. El tamaño de esta lista debe ser igual al tamaño de lista de *ip*

-----

### Clusters

     "clusters": [
        {
            "name": "CLUSTER_CATOLICA_NORTE",
            "cluster_id": 0, "multicores": [ 0 ],
            "resilience_data": {
                "cluster_availability": {"MTTF": 0.0,"MTTR": 0.0 }, "host_availability": { "MTTF": 0.0, "MTTR": 0.0 }
            },
        },
        ...
        ...
    ],
Definición de los clusters y sus posiciones geográficas.

- name: str : nombre del cluster
- cluster_id: int : identificador único del cluster
- multicores: [int] : ips de servidores que se alojan en un cluster
- resilience_data->cluster_availability->MTTF: Tiempo Medio en Fallar del cluster
- resilience_data->cluster_availability->MTTR: Tiempo Medio en Reparar del cluster
- resilience_data->host_availability->MTTF: Tiempo Medio en Fallar de los 
servidores del cluster
- resilience_data->host_availability->MTTR: Tiempo Medio en Reparar de los 
servidores del cluster

----

### Componentes

    "programs": [
        {
            "type": "dummy",
            "activities": [
                {
                    "id": 0,
                    "name": "MIS_TAREAS_AYNI",
                    "works": [
                        {
                            "data": {
                                "time_fun_distribution": [
                                    { "multicore": 0, "perf_per_vm": [ { "params": [ 79.02991997,0], "type": "norm", "vm_type": 0 } ] },
                                    { "multicore": 1, "perf_per_vm": [ { "params": [ 79.02991997,0], "type": "norm", "vm_type": 0 } ] },
                                    { "multicore": 2, "perf_per_vm": [ { "params": [ 79.02991997,0], "type": "norm", "vm_type": 0 } ] }
                                ]
                            },
                            "desc": "webhook",
                            "type": "WorkProcess"
                        }
                    ]
                }
            ],
            "config_conn": [
                "END_POINT"
            ],
            "id": 0,
            "name": "AYNI"
        },

Los componentes (programas o servicios) con los que cuenta un sistema. 

- type: str : tipo del componente. Por ahora solo usa *dummy*, esto es un componente
que solo recibe y deriva peticiones sin una lógica interna. 
Otros tipos como *DB* y *CACHE* se encuentran en validación. 
- name: str : nombre del componente
- id: int : identificador único del componente
- activities: [{*Activity*}] : lista de actividades. Corresponde a los casos de uso de un sistema
- activities -> id: int : identificador de la actividad
- activities -> name: str : nombre de la actividad 
- activities -> works: [{*Work*}] : Lista de trabajos. Cada trabajo (work) es un evento
de una actividad y contiene un tiempo de ejecución en CPU
- activities -> works -> data -> time_fun_distribution -> multicore: int : id del
tipo de servidor. Se debe tener uno de cada tipo de servidor.
- activities -> works -> data -> time_fun_distribution -> perf_per_vm: benchmarks
en un tipo de VM en un servidor. Por defecto, si solo se cuenta con un benchmark
y existen varios tipos de VM, se calculan los tiempos de servicio en otras VMs
de manera proporcional al número de CPU.
- activities -> works -> data -> time_fun_distribution -> perf_per_vm -> type: str:
distribución que siguen los tiempos de servicio del work. Se asume normal
- activities -> works -> data -> time_fun_distribution -> perf_per_vm -> params: [float,float]
parámetros de la distribución de los tiempos de servicio. Se asume normal.
El primer elemento es la media y el segundo es la desviación estándar
- activities -> works -> data -> time_fun_distribution -> perf_per_vm -> vm_type: int :
id del tipo de VM
- activities -> works -> desc: str : descripción del work. "webhook" indica que la actividad
no cuenta con lógica interna. 
- activities -> works -> type: tipo de work, similar a descripción. No se utilizan, pero
si se incluye el simulador, se pueden usar "WorkProcess", "WorkRequestToApi" y "WorkRequestToMongo""
- activities -> config_conn: [str] : indica el/los componentes con los que interactua

## vms.json

### Máquinas virtuales

      "vms":[
          {"type": 0, "name_type": "vm_type_0", "name": "small", "cores":2,"ram":8192, "hhd": 100}
        ]

Tipos de máquinas virtuales (VMs).

- type: int : identificador único del tipo de VM
- name_type: str : nombre del identificador para mejor seguimiento en el DEBUG y el código.
Corresponde a *type* de VM más el prefijo "vm_type" 
- name: str : nombre genérico del tipo de VM.
- cores: int : número de cores de la VM
- ram: int: cantidad de memoria ram que contiene la VM
- hhd: int: cantidad de disco que contiene la VM

[jekyll-organization]: https://github.com/jekyll
