---
title: "Introducción a Docker"
date: 2021-08-24
menu:
  sidebar:
    name: "Introducción a Docker"
    identifier: docker
    parent: systems
    weight: 500
hero: images/docker.png
---
______________
En este post vamos a dar una breve introducción de Docker:

- [¿Qué son los contenedores?](#qué-son-los-contenedores)
        - [Las ventajas más importantes en comparación a una mv son:](#las-ventajas-más-importantes-en-comparación-a-una-mv-son)
- [¿Qué es Docker?](#qué-es-docker)
    - [Componentes de Docker](#componentes-de-docker)
- [Instalación de Docker en Debian Buster](#instalación-de-docker-en-debian-buster)
- [Ejecutar Docker sin SUDO](#ejecutar-docker-sin-sudo)
- [Gestión de contenedores](#gestión-de-contenedores)
- [Comprobar que docker está instalado correctamente](#comprobar-que-docker-está-instalado-correctamente)
- [Ejemplo básico: Ejecutar un contenedor con Nginx](#ejemplo-básico-ejecutar-un-contenedor-con-nginx)
- [Gestión de imágenes](#gestión-de-imágenes)
  - [Crear imágenes. Fichero Dockerfile](#crear-imágenes-fichero-dockerfile)
  - [Ejemplo 1: Crear imagen con Nginx](#ejemplo-1-crear-imagen-con-nginx)
  - [Ejemplo 2: Crear una nueva version de la app](#ejemplo-2-crear-una-nueva-version-de-la-app)
  - [Funcionamiento](#funcionamiento)
  - [Un ejemplo estático más visual](#un-ejemplo-estático-más-visual)
- [Un poco de Networking en Docker](#un-poco-de-networking-en-docker)
  - [Red bridge por defecto](#red-bridge-por-defecto)
    - [Mapeo de puertos. Reglas de iptables.](#mapeo-de-puertos-reglas-de-iptables)
    - [Crear una red en Docker](#crear-una-red-en-docker)
- [Docker Compose](#docker-compose)
  - [Instalación de Docker-Compose](#instalación-de-docker-compose)
______________

## ¿Qué son los contenedores?

{{< alert type="dark" >}}
 Son diferentes compartimentos aislados dentro de un solo sistema operativo y actúan como máquinas virtuales aunque no lo son, con esto nos referimos a que comparten muchas de las mismas características de una maquina virtual (seguridad, almacenamiento, aislamiento de redes, entre otras...), pero comparten el **mismo kernel**.
{{< /alert >}}

###### Las ventajas más importantes en comparación a una mv son:

* **Requieren menos recursos de hardware.**
* **Son más rápidos de iniciar y finalizar.**
* **Aislan librerías.**
* **Facilitan el control de versiones y gestión de las mismas.**
* **Minimiza el tiempo de ejecución en CPU y Almacenamiento gestionado por una aplicación, como por ejemplo al actualizar la aplicación.**

## ¿Qué es Docker?


> Docker significa 'estibador', que si lo llevamos a un campo general es 'el que mueve los contenedores en el puerto', le viene al pelo el nombre ¿no crees?.


Así a grandes rasgos:

{{< alert type="dark" >}}
Docker es un software que nos permite gestionar contenedores de aplicación en una máquina.
{{< /alert >}}

En más profundidad:

{{< alert type="success" >}}
* **Docker** fue una empresa que desarrolló este mecanismo. Cambia toda la forma del **despliegue de aplicaciones**. 
* Utiliza **Virtualización Ligera**, a **nivel de sistema operativo**, aprovechando mejor el hardware.
* Aisla los recursos a nivel de kernel.
* Aporta flexibilidad y portabilidad.
* Gestiona **contenedores a un alto nivel** proporcionando todas las capas y funcionalidad adicional. 
* Cambia la forma de distribuir la aplicación. Se crea el contenedor ya funcionando, **más rápido y eficaz**.
*  La instalación y **gestión de los contenedores es muy simple**. Se necesita solo de una imagen para crearlos.
*  El proyecto nos ofrece un repositorio de imagenes (Registry Docker Hub)
*  Está **escrito en GO** y es **software libre**.
*  Está enfocado a sistemas altamente distribuidos.
{{< /alert >}}

#### Componentes de Docker 

{{< alert type="success" >}}
**Docker engine**, es propiamente el software. En el que se distinguen tres capas: 
* El **demonio** como tal de docker, que es el que se encarga de ejecutar el software
* **Docker [REST]API** que es la capa de aplicacion que permite la comunicación con el demonio.
* **Docker CLI** que es la interfaz por línea de comandos por donde nosotros podemos acceder a la API y gestionar el software.
* **Docker Registry** que almacena las imágenes generadas por el Docker Engine. Se puede instalar un registro privado o uno público utilizando [Docker Hub](https://hub.docker.com/).
{{< /alert >}}


## Instalación de Docker en Debian Buster 

> Si tu sistema operativo es otra distribución consulta la página oficial para instalarlo [Instalar Docker según SO](https://docs.docker.com/engine/install/)

**Usando repositorio**

1. Actualizamos la paquetería
  
```shell
sudo apt-get update
```

2. Nos aseguramos que tenemos los siguientes paquetes

```shell
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
3. Añadimos la clave GPG correspondiente

```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

4. Ejecutamos el siguiente comando para configurar el repositorio estable de docker 

```shell
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
``` 

**Instalar Docker**

```shell
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io
```

## Ejecutar Docker sin SUDO 

El demonio de Docker solo se ejecuta como ROOT, normalmente usariamos `sudo` delante de los comandos para su uso. Para más comodidad podemos añadir nuestro usuario al grupo de `docker` y así nuestro usuario actual puede utilizarlo sin necesidad de ser supeusuario.

```shell
sudo usermod -aG docker $USER
```
{{< alert type="warning" >}}
 Recuerda que tienes que cerrar la sesion de usuario para actualizar los grupos. O bien puedes usar este comando para una solución más rápida:
    `exec su -l $USER`
{{< /alert >}}
## Gestión de contenedores 

Estos son los comandos más usados para gestionar los contenedores:

```shell
# Conectar al contenedor 
docker attach
# Ejecutar un comando en el contendor
docker exec
# Mostrar las características del contenedor
docker inspect
# Matar un contendor
docker kill
# Ver los logs del contendor
docker logs
# Pausar o iniciar un contendor si estaba pausado
docker pause/unpause
# Ver el puerto o establecerlo
docker port 
# Ver contenedores activos / Ver todos los contenedores aunque esten inactivos
docker ps / docker ps -a
# Renombrar el contendor
docker rename 
# Inicializar/parar/reiniciar
docker start/stop/restart
# Ejecutar un contenedor a partir de una imagen
docker run 
# Ver el estado de un contendor
docker stat 
# Monitorización del contenedor 
docker top 
# Actualizar contenedor a partir de una imagen
docker update 

```

## Comprobar que docker está instalado correctamente 

* Actualmente tenemos la siguiente **version** 

```shell
celiagm@debian:~$ docker --version
Docker version 20.10.8, build 3967b7d
```

> Si ejecutamos un contendor a partir de una imagen que no está descargada la descarga directamente del docker hub. Si ya esta descargada simplemente ejecuta el contenedor.

* De manera que ejecutamos el siguiente comando para ver que está instalado correctamente y funciona.

```sh
celiagm@debian:~$ docker run centos /bin/echo "Hello world!!"
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
7a0437f04f83: Pull complete 
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for centos:latest
Hello world!!
```
Comprobamos que la imagen de `centos` se ha descargado correctamente 

```sh
celiagm@debian:~$ docker images
REPOSITORY                         TAG       IMAGE ID       CREATED        SIZE
centos                             latest    300e315adb2f   8 months ago   209MB
```

## Ejemplo básico: Ejecutar un contenedor con Nginx 

Para ello ejecutaremos el siguiente comando 

```shell
docker run --name nginx-prueba -d -p 83:80 nginx
```

En la ejecución anterior tenemos:

* `docker run` = Ejecuta el contenedor 
* `--name` = nombre del contenedor 
* `-d` = Segundo plano
* `-p `= puerto al que se va exponer
* `nginx` = nombreimagen:version

Comprobamos que está descargada la imagen 

```sh
celiagm@debian:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    dd34e67e3371   6 days ago     133MB
centos       latest    300e315adb2f   8 months ago   209MB
```

Y que tenemos un contendor funcionando con nginx 

```sh
celiagm@debian:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                               NAMES
dec125a8d8ab   nginx     "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:83->80/tcp, :::83->80/tcp   nginx-prueba

```

Si accedemos al navegador podremos ver la página inicial de nuestro nginx funcionando correctamente

![docker-nginx.png](/images/posts/docker/docker-nginx.png)



Tambien podemos ejecutar un contenedor de forma interactiva de la siguiente forma:

```shell
celiagm@debian:~$ docker run --name prueba2 -t -i nginx /bin/bash
root@3cf0feb0f6ca:/# ls
bin   dev		   docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc	
```

Estaríamos dentro del contenedor. 


## Gestión de imágenes

{{< alert type="info" >}}
 Las imágenes se pueden entender como plantillas de solo lectura. Son como un sistema de ficheros y parámetros listos para su ejecución (así como una receta) y están basados en un sistema operativo Linux.
{{< /alert >}}



Los comandos más usados para su gestión son:

```shell
# Mostrar las imágenes instaladas en nuestro host físico
docker images 
# Mostrar los cambios de una imagen 
docker history <id_imagen>
# Mostrar las caracterísitcas de la imagen 
docker inspect <id_imagen>
# Borrar una imagen 
docker rmi <id_imagen>
# Salvar o cargar una imagen 
docker save/load 
```

### Crear imágenes. Fichero Dockerfile

**Crear una imagen propia a partir de otra**

> Podemos crear una imagen propia a partir de otra desde la línea de comandos, pero normalmente se usa un fichero Dockerfile. Así que vamos a explicar la segunda forma.

Podemos crear una imagen a partir de un fichero **Dockerfile**.

{{< alert type="info" >}}
Este fichero es un archivo de texto plano que contiene una serie de instrucciones necesarias para crear una imagen con el propósito de hacer funcionar una aplicación en un contendor.
{{< /alert >}}

### Ejemplo 1: Crear imagen con Nginx

Puedes consultar la [documentacion oficial](https://hub.docker.com/_/nginx) para más información.

Queremos crear una imagen propia a partir de la imagen oficial de nginx. 

Para ello hemos creado un directorio de trabajo previamente y en el vamos a crear un fichero Dockerfile, el cual tiene la siguiente información:

```shell
# De la siguiente imagen crearemos otra
FROM nginx
# La va mantener la persona indicada 
MAINTAINER Celia <cgarmai95@gmail.com>
# Va a ejecutar los siguientes pasos
RUN sed -i '$ d' /etc/apt/sources.list && apt-get update && apt-get install -y nano
```

Una vez creado el fichero vamos a 'quemarlo' y crear una nueva imagen propia que la vamos a llamar como queramos, es recomendable que tenga un nombre significativo, pues esta imagen si queremos la podemos subir a Docker Hub en el que previamente hemos creado el repositorio llamado 'pruebanginx'

```shell 
docker build -t cgmarquez95/pruebanginx .
```

Comprobamos que se ha creado la imagen 

```sh
celiagm@debian:~/nginx$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
cgmarquez95/pruebanginx   latest    9cccab4f589a   2 minutes ago   152MB
nginx                     latest    dd34e67e3371   7 days ago      133MB
debian                    latest    fe3c5de03486   7 days ago      124MB
centos                    latest    300e315adb2f   8 months ago    209MB

```

Ahora vamos a crear el contenedor de forma interactiva para comprobar que se ha instalado el paquete de nano como habíamos indicado anteriormente.

```sh
celiagm@debian:~/nginx$ docker run --name nginx-interactivo -ti cgmarquez95/pruebanginx /bin/bash
```

Arranca efectivamente con una consola y comprobamos que está instalado el paquete 'nano'

```sh
celiagm@debian:~/nginx$ docker run --name nginx-interactivo -ti cgmarquez95/pruebanginx /bin/bash
root@908dd2ff5e6a:/# dpkg -l | grep 'nano'
ii  nano                      3.2-3                       amd64        small, friendly text editor inspired by Pico
root@908dd2ff5e6a:/# apt-cache policy nano
nano:
  Installed: 3.2-3
  Candidate: 3.2-3
  Version table:
 *** 3.2-3 500
        500 http://deb.debian.org/debian buster/main amd64 Packages
        100 /var/lib/dpkg/status

```

Salimos del contenedor con `exit`.

> Si usaramos 'exit' normalmente se cierra el contenedor si queremos dejarlo en segundo plano podemos usar un atajo del teclado CTRL+q+p.

Como el contenedor está parado y le hemos hecho un cambio, vamos a hacer un `commit` para guardar los cambios en el repositorio. 

```sh
docker commit -m "paquete nano instalado" -a "celia" nginx-interactivo cgmarquez95/pruebanginx:v1
```

Ahora vemos que tenemos otra imagen con la v1 

```sh
celiagm@debian:~/nginx$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
cgmarquez95/pruebanginx   v1        fa4ac22a0c73   46 seconds ago   152MB
cgmarquez95/pruebanginx   latest    9cccab4f589a   17 minutes ago   152MB
nginx                     latest    dd34e67e3371   7 days ago       133MB
debian                    latest    fe3c5de03486   7 days ago       124MB
centos                    latest    300e315adb2f   8 months ago     209MB

```

Si queremos subir esta version a Docker hub:

1. Loguearse (en mi caso no me pide las credenciales porque ya estoy logueada)

```sh
celiagm@debian:~/nginx$ docker login
Authenticating with existing credentials...
WARNING! Your password will be stored unencrypted in /home/celiagm/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

```

2. Asegurarse de que está creado el repositorio 

3. Subir la imagen 

```sh
celiagm@debian:~/nginx$ docker push cgmarquez95/pruebanginx:v1
The push refers to repository [docker.io/cgmarquez95/pruebanginx]
0ab0b0b81458: Pushed 
98ccf51573dc: Pushed 
fb04ab8effa8: Mounted from library/nginx 
8f736d52032f: Mounted from library/nginx 
009f1d338b57: Mounted from library/nginx 
678bbd796838: Mounted from library/nginx 
d1279c519351: Mounted from library/nginx 
f68ef921efae: Mounted from library/nginx 
v1: digest: sha256:0decbe447f178f2f6d68f6b1a24b81d9c68395d5e34944f6e107a14b89ed1727 size: 1989

```
Si vamos a nuestro DockerHub estará subida.

![v1.png](/images/posts/docker/v1.png)


{{< alert type="danger" >}}

**IMPORTANTE**:
Para modificar una aplicación, sea cual sea, NO se puede entrar en el contenedor y modificarla si no que BORRAMOS el contenedor actual, MODIFICAMOS LA APP y CREAMOS UN nuevo contenedor con NUEVA IMAGEN y NUEVA VERSION. 
{{< /alert >}}


### Ejemplo 2: Crear una nueva version de la app

1. Vamos a utilizar la misma imagen que estabamos usando hasta ahora, para modificar la aplicación, en este caso, vamos a crear un fichero `index.html` simple (en el directorio actual)

2. Editamos el fichero Dockerfile indicando que copie el contenido estático (index.html) a la ruta adecuada.

```sh 
FROM nginx
MAINTAINER Celia <cgarmai95@gmail.com>
COPY index.html /usr/share/nginx/html/index.html
RUN sed -i '$ d' /etc/apt/sources.list && apt-get update && apt-get install -y nano
```

3. Creamos la imagen nueva 

```sh
docker build -t cgmarquez95/pruebanginx:v2 .
```

Ya tendríamos la v2 

```sh
celiagm@debian:~/nginx$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED             SIZE
cgmarquez95/pruebanginx   v2        5d1b348cc779   3 seconds ago       152MB
cgmarquez95/pruebanginx   v1        fa4ac22a0c73   44 minutes ago      152MB
cgmarquez95/pruebanginx   latest    9cccab4f589a   About an hour ago   152MB
nginx                     latest    dd34e67e3371   7 days ago          133MB
debian                    latest    fe3c5de03486   7 days ago          124MB
centos                    latest    300e315adb2f   8 months ago        209MB

```

4. Paramos y eliminamos el contendor actual 

```sh 
celiagm@debian:~/nginx$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS          PORTS     NAMES
908dd2ff5e6a   cgmarquez95/pruebanginx   "/docker-entrypoint.…"   55 minutes ago   Up 26 minutes   80/tcp    nginx-interactivo

celiagm@debian:~/nginx$ docker stop 908dd2ff5e6a && docker rm 908dd2ff5e6a
908dd2ff5e6a
908dd2ff5e6a

```

5. Ejecutamos el NUEVO CONTENEDOR CON LA VERSION 2 de la nueva imagen 

```sh
celiagm@debian:~/nginx$ docker run --name webv2 -d -p 8080:80 cgmarquez95/pruebanginx:v2
d53e8b411740cb827d391be948d757de3d07d56d4d864c6a41c16a0a9273cb16

celiagm@debian:~/nginx$ docker ps
CONTAINER ID   IMAGE                        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
d53e8b411740   cgmarquez95/pruebanginx:v2   "/docker-entrypoint.…"   9 minutes ago   Up 9 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   webv2

```

### Funcionamiento 

Comprobamos que efectivamente si accedemos al navegador al puerto 8080 indicado se ha modificado la aplicación.

![v2.png](/images/posts/docker/v2.png)


Si queremos distribuir esta nueva version solo tenemos que subirla a docker hub 

```sh 
docker push cgmarquez95/pruebanginx:v2
```


### Un ejemplo estático más visual 

Para ello vamos a tirar de un repo que tengo en mi git para la web. 

El fichero Dockerfile es:

```shell
FROM nginx
MAINTAINER Celia <cgarmai95@gmail.com>
RUN sed -i '$ d' /etc/apt/sources.list && apt-get update && apt-get install -y \
        git \
        nano && mkdir /home/proyecto_htmlcss && git clone https://github.com/CeliaGMqrz/proyecto_htmlcss.git /home/proyecto_htmlcss \
        && cp -r /home/proyecto_htmlcss/* /usr/share/nginx/html/.
```

Se vería de la siguiente manera, haciendo el mismo procedimiento que hemos hecho hasta ahora.

![v3.png](/images/posts/docker/v3.png)

## Un poco de Networking en Docker 

Hemos visto ejemplos de creación de contenedores a partir de imagenes propias o públicas. Ahora vamos a hablar un poco sobre las redes.

{{< alert type="dark" >}}
Estos son los comandos más básicos para gestionar las redes en Docker

```shell
docker network connect/disconnect
docker network create 
docker network inspect 
docker network ls 
dokcer network rm 
```
{{< /alert >}}

Docker crea tres tipos de redes al instalarlo. 

> En este post sólo vamos a hablar de red bridge por defecto.

```shell 
celiagm@debian:~$ docker network ls
NETWORK ID     NAME       DRIVER    SCOPE
2b48025c4cf6   bridge     bridge    local
4d0c4f476c87   host       host      local
0aa9a9de8df1   none       null      local
```
### Red bridge por defecto 

**Bridge**, es la red por defecto. Que corresponde a la interfaz de red `Docker0` que se crea en el host físico. Esta actúa como la **puerta de enlace** de los contenedores. Cada contenedor que se crea adopta una dirección ip en ese rango de red.

Si ejecutamos `ip a` en el host físico, podemos ver como se ha creado la siguiente interfaz:

```shell 
9: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:65:69:c5:6f brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:65ff:fe69:c56f/64 scope link 
       valid_lft forever preferred_lft forever
```

Si ejecutamos un contenedor:

```shell
celiagm@debian:~$ docker run --name web -d -p 8080:80 cgmarquez95/pruebanginx:v3
d0e07f340c0442f54963ff5ab8007ade6bc0c33542ad47b52734610f57f8d492
```

Y entramos dentro de ese contenedor:

```shell
celiagm@debian:~$ docker exec -ti  web /bin/bash
```
Comprobamos que la interfaz de red ha tomado una dirección ip que está en el rango que docker ha creado.

```shell 
root@d0e07f340c04:/# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
16: eth0@if17: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

```
De igual forma, en nuestro host físico tambien podemos comprobar que se ha creado esa red de ese contenedor en concreto

```shell
17: vethce4f46a@if16: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 72:ad:94:0a:fa:3e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::70ad:94ff:fe0a:fa3e/64 scope link 
       valid_lft forever preferred_lft forever

```

Si ejecutamos `docker inspect web` de nuestro contenedor y vamos al apartado de **networks**, vemos que la puerta de enlace coincide con la dirección ip de la interfaz de `Docker0` del host físico.

```shell 
celiagm@debian:~$ docker inspect web | grep 'Gateway'
            "Gateway": "172.17.0.1",
            "IPv6Gateway": "",
                    "Gateway": "172.17.0.1",
                    "IPv6Gateway": "",

```

Y también podemos comprobar la dirección ip que ha tomado en ese rango:

```shell 
celiagm@debian:~$ docker inspect web | grep -i 'ipaddress'
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```


#### Mapeo de puertos. Reglas de iptables.

Como hemos expuesto el puerto 80 del contenedor al puerto 8080 de nuestro host físico, Docker lo que hace es crear una **regla de iptables DNAT** para pasar el tráfico por ahí.

```sh
Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         
DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain DOCKER (2 references)
target     prot opt source               destination         
RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:80

```
#### Crear una red en Docker 

Podemos crear una red, a esta red la vamos a llamar `local1`. Le indicamos que va ser de tipo bridge.

```shell 
docker network create -d bridge local1
```
Vemos que se ha creado:

```shell 
celiagm@debian:~$ docker network ls
NETWORK ID     NAME       DRIVER    SCOPE
2b48025c4cf6   bridge     bridge    local
4d0c4f476c87   host       host      local
8871b1c21717   local1     bridge    local
0aa9a9de8df1   none       null      local
```
En el host físico también podemos ver que se ha creado la red de tipo bridge:

```shell 
18: br-8871b1c21717: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:c9:a6:56:c1 brd ff:ff:ff:ff:ff:ff
    inet 172.19.0.1/16 brd 172.19.255.255 scope global br-8871b1c21717
       valid_lft forever preferred_lft forever

```

En mi caso tengo el contenedor corriendo, lo vamos a parar y volvemos a ejecutarlo pero con la nueva red local que hemos creado ahora, pasándole el nuevo parámetro.

> Más adelante podemos ver como conectar a la red nueva sin necesidad de parar el contenedor.

```shell
docker run --name web --network=local1 -d -p 8080:80 cgmarquez95/pruebanginx:v3
```
Comprobamos que ahora tiene la puerta de enlace y la direccion ip en el rango de la nueva red `local1`.

```shell 
docker inspect web
```
Salida:
```sh
                   "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.2",

```

Si quisieramos conectar otro contenedor ,que estuviera funcionando, a esta nueva red para interconectarlos entre sí solo tenemos que ejecutar el siguiente comando.

```shell 
docker connect local1 otro_container
```

De esta forma ya estarían en la misma red y pueden verse entre sí.


## Docker Compose 

Hasta ahora hemos utilizado Docker para la creación de contenedores, pero ahora vamos a hablar de Docker-Compose.

Docker-Compose es una herramienta que sirve para definir y ejecutar aplicaciones con múltiples contenedores a partir de un fichero de extensión YAML. Estos contenedores por lo general están configurados de forma que interaccionan entre ellos. 

> Por ejemplo: Queremos desplegar una aplicación web que necesita una base de datos y un servidor web. Pues a partir del fichero *yml* levantamos varios contenedores interconectados entre sí, cada uno haciendo su función.

### Instalación de Docker-Compose 

Podemos descargarlo desde la paquetería de repositorios Debian.
(**Actualmente no recomendado**) 

```shell
# Añadimos la clave GPG
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
# Buscamos el paquete 
sudo apt-cache policy docker-compose
# Lo instalamos
sudo apt-get install docker-compose
```
Si comprobamos la versión podemos ver que está atrasada (actualmente) en referencia al repositorio oficial.

```shell 
celiagm@debian:~/docker/compose/hello-world$ docker-compose --version
docker-compose version 1.21.0, build unknown
```

Por lo que vamos a instalarlo desde el repositorio de github de la siguiente forma (así pudiendo elegir la [versión más reciente](https://github.com/docker/compose/releases)):

```sh
sudo curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

#Le damos los permisos necesarios 

sudo chmod +x /usr/local/bin/docker-compose

```
Comprobamos la version de docker-compose

Salida:
```shell
celiagm@debian:~$ docker-compose --version
docker-compose version 1.29.2, build 5becea4c

```

