[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/kdetony)

Docker Swarm           
========
 ![ballena_roja](https://github.com/kdetony/docker-swarm/blob/master/images/ballena_roja.jpg)

## Arquitectura planteada 

 ![swarm](https://github.com/kdetony/docker-swarm/blob/master/images/Swarm.png)             

     

## Iniciando el Cluster:

En el nodo master vamos a ejecutar: 

> docker swarm init --advertise-addr $IP 

Esto nos va a generar un codigo de la siguiente manera: 

> docker swarm join --token SWMTKN-1-[code] $IP:2377

## Agreando los workers al cluster:

En cada uno de los nodos vamos a ejecutar el comando que obtuvimos: 

> docker swarm join --token SWMTKN-1-[code] $IP:2377

**Si olvidamos el Token o deseamos agregar otro worker a futuro:**

> docker swarm join-token -q worker


## Validando la arquitectura: 

En el nodo master, vamos a validar toda nuestra infraestructura, para ello vamos a ejecutar el sgt comando:

> docker node ls 


**OBSERVACION**

Debemos tener en claro lo siguiente, antes de empezar a trabajar con Swarm: 

**Task / Tarea**, es la acción que deseamos realizar, un deploy, escalamiento, crear un contenedor, una red, un volumen, etc 

**Service / Servicio**, es lo que deseamos ejecutar por medio de la tarea… 


## Ejercicios para entender Docker Swarm 

### Ejemplo 1 

En este ejemplo vamos a desplegar una aplicación sencilla, vamos a usar Nginx como imagen base y vamos a iniciarlo con 3 replicas:

> docker service create **--name webnginx** --replicas 3 nginx 

Donde: 

- name: nombre para el contenedor
- replicas: cantidad de replicas que deseamos en base al contenedor
- nginx: nombre de la imagen

Para listar los servicios creados usamos:

> docker service ls 

Y si deseamos saber la cantidad de réplicas y donde están distribuidas:

> docker service ps ID

Vemos que ya contamos con nuestro despliegue de Nginx en nuestros nodos, y como validamos ? pues de la siguiente manera, nos vamos a ubicar en nodo master y ejecutamos: 

> docker inspect $ID | grep IPA 

> curl IP_CONTAINER  

Como podemos observar, tenemos **Nginx** en funcionamiento, pero no es muy accesible que digamos, porque solo "vive" en la red de Docker. 

### Ejemplo 2

Vamos ahora modificar esa limitante, para ello recurrimos al siguiente comando: 

> docker service **update** **--publish-add 9090:80**  webnginx

donde: 

- update: comando a usar para modificar un servicio
- publish-add: puertos Host/Contenedor

Ahora, para poder validar, solo ingresamos la *ip del nodo master* en el browser usando el puerto *9090*.

Lo que estamos realizando, es decir la acción de publicar una entrada de acceso "externa" hacia el contenedor, se conoce como **routing mesh**

Analizemos este ejemplo:

>**docker service create --name my-web --publish 8080:80 --replicas 2 nginx**

![routing_mesh](https://github.com/kdetony/docker-swarm/blob/master/images/ingress-routing-mesh.png)


### Ejemplo 3 

Vamos ahora a ver el update de imagenes, supongamos que tenemos la aplicación Grafana con la version 5.0 y queremos desplegarla en nuestro Cluster, bueno lo podemos hacer de la siguiente manera: 

> docker service create --replicas 3 --name monitor **--update-delay 10s** grafana/grafana:5.0.0

donde:
–replicas: es el número de tareas (task)
–name: es el nombre del servicio
–update-delay: es el tiempo que transcurre entre la actualización de cada una de las tareas.
-grafana/grafana:5.0.0: es la imagen que vamos a utilizar

Validamos con el comando (ejecutado en el nodo manager) : 

> docker service ls  && docker service ps $ID

**Observación**

si deseo ver información adicional del contenedor: 

> docker service inspect --pretty monitor

Vemos que ya tenemos ejecutandose Grafana, pero, si queremos actualizar la imagen?? ... pues es sencillo,solo debemos recurrir al siguiente comando: 

> docker service update --image grafana/grafana:5.0.1 monitor

Validamos con el comando (ejecutado en el nodo manager) : 

> docker service ls  && docker service ps $ID

Y si queremos asignarle un puerto especifico: 

> docker service update --publish-add 5000:3000 monitor

TIP
=====
- Una buena practica, es que el manager solo gestione los contenedores, para poder lograr esto, usamos lo siguiente:

> --constraint=node.role==manager/worker

EJM:

> docker service create --name mydb --replicas 1 **--constraint=node.role==manager** --network my_net --secret source=root_db_password,target=root_db_password --secret source=wp_db_password,target=wp_db_password -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/root_db_password -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/root_db_password -e MYSQL_PASSWORD_FILE=/run/secrets/wp_db_password -e MYSQL_USER=wp -e MYSQL_DATABASE=wp mariadb:10.1


- Podemos tener tambien una arquitectura "multi-master" para ello es recomendable usar un Balanceador, por ejm. Haproxy, Nginx, Traefik.

![loadbalancing](https://github.com/kdetony/docker-swarm/blob/master/images/ingress-lb.png)

- Para temas de monitoreo de contenedores, podemos usar Prometheus. 
