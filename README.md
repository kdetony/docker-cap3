Docker Swarm 
========

## Arquitectura planteada 

             
1 Master --> |node01| + |node02|
     

## Iniciando el Cluster:

En el nodo master vamos a ejecutar: 

> docker swarm init --advertise-addr $IP 

Esto nos va a generar un codigo de la siguiente manera: 

> docker swarm join --token SWMTKN-1-1t45c9j2l2gvalvld0i18z7dvjgv2d1bzw7d3rnlrae19q3d3i-6k452lwtyfdb9hh3afjveykhh $IP:2377

## Agreando los workers al cluster:

En cada uno de los nodos vamos a ejecutar el comando que obtuvimos: 

> docker swarm join --token SWMTKN-1-[code] $IP:2377

## Validando la arquitectura: 

En el nodo master, vamos a validar toda nuestra infraestructura, para ello vamos a ejecutar el sgt comando:

> docker node ls 


**OBSERVACION**
Debemos tener en claro lo siguiente, antes de empezar a trabajar con Swarm: 

**Task / Tarea**, es la acción que deseamos realizar, un deploy, escalamiento, crear un contenedor, una red, un volumen, etc 

**Service / Servicio**, es lo que deseamos ejecutar por medio de la tarea… 


## Ejercicios para entender Docker Swarm 

### Ejemplo 1 

En este ejemplo vamos a desplegar una aplicación sencilla
