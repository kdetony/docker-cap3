Docker Swarm 
========

## Arquitectura planteada 

- - - - --   |      |   |      |
1 Master --> |node01| + |node02|
- - - - -    |      |   |      |

## Iniciando el Cluster 

En el nodo master vamos a ejecutar: 

> docker swarm init --advertise-addr $IP 

Esto nos va a generar un codigo de la siguiente manera: 

> docker swarm join --token SWMTKN-1-1t45c9j2l2gvalvld0i18z7dvjgv2d1bzw7d3rnlrae19q3d3i-6k452lwtyfdb9hh3afjveykhh $IP:2377


