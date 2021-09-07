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
- [Volumenes en Docker](#volumenes-en-docker)
  - [Volúmenes Docker](#volúmenes-docker)
    - [Gestión de Volúmenes](#gestión-de-volúmenes)
    - [Compartiendo volumen entre un contenedor y otro](#compartiendo-volumen-entre-un-contenedor-y-otro)
      - [Crear volumen](#crear-volumen)
      - [Crear contenedor y asociarle el volumen creado](#crear-contenedor-y-asociarle-el-volumen-creado)
      - [La información es persistente](#la-información-es-persistente)
      - [Crear nuevo contenedor y asociarle el volumen](#crear-nuevo-contenedor-y-asociarle-el-volumen)
      - [Eliminar volumenes](#eliminar-volumenes)
  - [Volumenes con Bind mounts](#volumenes-con-bind-mounts)
    - [Crear el directorio compartido](#crear-el-directorio-compartido)
    - [Crear el contenedor y montar el volumen](#crear-el-contenedor-y-montar-el-volumen)
    - [Eliminar contenedores y volumenes: Bind Mount](#eliminar-contenedores-y-volumenes-bind-mount)
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


## Volumenes en Docker 

Cuando ejecutamos un contenedor normalmente como hemos estado haciendo hasta ahora, cuando se cierra o se termina la información se pierde. Por lo tanto si queremos mantener esa información tenemos varias opciones:

* Volúmenes Docker 
* tmpfs mounts
* Bind mounts 

### Volúmenes Docker 

Docker aporta la capacidad de crear volúmenes. Estos volúmenes no son más que un directorio que se crea en el sistema de ficheros. Dependiendo del sistema operativo se guardará en una ruta u otra. En Debian por ejemplo se almacena en `/var/lib/docker/volumes`.

Con estos volúmenes se permite compartir información entre contenedores, hacer copias de seguridad entre otras cosas.

#### Gestión de Volúmenes 

Los comandos más usados son:

```shell
docker volume create 
docker volume rm 
docker volume ls 
# Elimina los volumenes que no estan siendo utilizados
docker volume prune 
docker volume inspect 
```

#### Compartiendo volumen entre un contenedor y otro 

##### Crear volumen 

Vamos a crear un volumen para guardar el contenido html de nginx, de la imagen con la que hemos estado trabajando hasta ahora. 

Ademas comprobamos dónde crea Docker por defecto sus volúmenes, en este caso es en la ruta `/var/lib/docker/volumes/`

```shell
# Creamos el volumen 
celiagm@debian:~$ docker volume create datos_nginx
datos_nginx

# Vemos que se ha creado.
celiagm@debian:~$ docker volume ls
DRIVER    VOLUME NAME
local     datos_nginx
local     minikube

# Con inspect podemos ver el directorio que ha creado Docker donde va a guardar toda la información.

celiagm@debian:~$ docker inspect datos_nginx
[
    {
        "CreatedAt": "2021-09-07T13:34:12+02:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/datos_nginx/_data",
        "Name": "datos_nginx",
        "Options": {},
        "Scope": "local"
    }
]

```
##### Crear contenedor y asociarle el volumen creado

Vamos a crear un contenedor asociando el volumen que hemos creado, indicando donde se va a montar con la opcion -v. En este caso vamos a montar el volumen en `/usr/share/nginx/html`, ya que es el documenroot por defecto.

> Nota: Si el volumen no está creado se creará automáticamente en la ruta por defecto de Docker.

```shell
# Creamos el contenedor asociando el volumen. 
celiagm@debian:~$ docker run -d --name my-nginx -v datos_nginx:/usr/share/nginx/html -p 5000:80 cgmarquez95/pruebanginx:v3
7c7bc77c0728e089e62f47625f6e7dc2907b4ad3dbc224aa49804561569ddf31
# Comprobamos que está funcionando 
celiagm@debian:~$ docker ps 
CONTAINER ID   IMAGE                        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
7c7bc77c0728   cgmarquez95/pruebanginx:v3   "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   0.0.0.0:5000->80/tcp, :::5000->80/tcp   my-nginx
```

Si vamos al directorio que ha creado docker en nuestro host físico podemos comprobar que se han sincronizado los directorios y tenemos todo el contenido de nuestra web.

> Este directorio está gestionado directamente por Docker por lo que normalmente si no nos hemos otorgado permisos de superusuario no podremos ver el contenido. Así que entramos como **root** para comprobarlo.

```shell
root@debian:/var/lib/docker/volumes/datos_nginx/_data# ls
50x.html		       css    images	  info_gatos.html   README.md
convivencia_perros_gatos.html  fonts  index.html  info_perros.html  w3layouts-License.txt
```
Si entramos en el contenedor igualmente podemos comprobar que tenemos el *volumen montado* en la ruta indicada.

```shell 
celiagm@debian:~$ docker exec -ti my-nginx /bin/bash
root@7c7bc77c0728:/# lsblk -f
NAME               FSTYPE LABEL UUID FSAVAIL FSUSE% MOUNTPOINT
nvme0n1                                             
|-nvme0n1p1                                         
|-nvme0n1p2                                         
|-nvme0n1p3                                         
|-nvme0n1p4                                         
`-nvme0n1p5                                         
nvme1n1                                             
|-nvme1n1p1                                         
`-nvme1n1p2                                         
  |-grupoLVM1-raiz                      291G    31% /usr/share/nginx/html
  `-grupoLVM1-swap              
```

##### La información es persistente

Si por ejemplo cambiamos algo dentro del contenedor en ese directorio html lo podemos hacer directamente y la información queda grabada. Además se sincroniza con el directorio creado por Docker en nuestro host físico.

**Además si borramos el contenedor el volumen no se borra.**

```shell
# Modificamos por ejemplo el index.html y le añadimos algun contenido.
root@7c7bc77c0728:/usr/share/nginx/html# nano index.html 
# Incluso añadimos un fichero de texto para la prueba 
root@7c7bc77c0728:/usr/share/nginx/html# touch prueba.txt
root@7c7bc77c0728:/usr/share/nginx/html# ls
50x.html		       css     index.html	 prueba.txt
README.md		       fonts   info_gatos.html	 w3layouts-License.txt
convivencia_perros_gatos.html  images  info_perros.html

# En el host físico comprobamos que la información se sincroniza por lo tanto es persistente.
root@debian:/var/lib/docker/volumes/datos_nginx/_data# ls -l
total 112
-rw-r--r-- 1 root root   494 jul  6 16:59 50x.html
-rw-r--r-- 1 root root  9432 ago 27 18:32 convivencia_perros_gatos.html
drwxr-xr-x 2 root root  4096 sep  7 13:34 css
drwxr-xr-x 2 root root  4096 sep  7 13:34 fonts
drwxr-xr-x 2 root root  4096 sep  7 13:34 images
-rw-r--r-- 1 root root 10755 sep  7 13:51 index.html
-rw-r--r-- 1 root root 32666 ago 27 18:32 info_gatos.html
-rw-r--r-- 1 root root 32315 ago 27 18:32 info_perros.html
-rw-r--r-- 1 root root     0 sep  7 14:00 prueba.txt
-rw-r--r-- 1 root root   590 ago 27 18:32 README.md
-rw-r--r-- 1 root root  2009 ago 27 18:32 w3layouts-License.txt

```

##### Crear nuevo contenedor y asociarle el volumen

Supongamos que queremos pasar la aplicación de nuestro nginx a un apache por lo que creamos el nuevo contenedor y le asociamos el volumen en la ruta adecuada.

```shell
# Creamos el nuevo contenedor 
docker run -d --name my-apache -v datos_nginx:/usr/local/apache2/htdocs -p 5001 httpd:2.4

#Comprobamos que están funcionando los dos 
celiagm@debian:~$ docker ps 
CONTAINER ID   IMAGE                        COMMAND                  CREATED          STATUS          PORTS                                   NAMES
98654335505b   httpd:2.4                    "httpd-foreground"       2 minutes ago    Up 2 minutes    0.0.0.0:5001->80/tcp, :::5001->80/tcp   my-apache
7c7bc77c0728   cgmarquez95/pruebanginx:v3   "/docker-entrypoint.…"   39 minutes ago   Up 39 minutes   0.0.0.0:5000->80/tcp, :::5000->80/tcp   my-nginx

```
Ahora si todo ha ido bien, en el navegador podemos ver que efectivamente se han salvado los datos. 

Y se sirve el contenido tanto en Apache como en Nginx.

![datos-nginx.png](/images/posts/docker/datos-nginx.png)

##### Eliminar volumenes 

Como podemos comprobar si eliminamos los contenedores los volúmenes no se eliminan, para ello usamos lo siguiente:

```shell 
# Paramos y eliminamos los contenedores
celiagm@debian:~$ docker stop my-apache my-nginx && docker rm my-apache my-nginx
my-apache
my-nginx
my-apache
my-nginx
# Listamos volúmenes
celiagm@debian:~$ docker volume ls
DRIVER    VOLUME NAME
local     datos_nginx
local     minikube
# Elminamos volumen 

# Podemos hacerlo con prune que elimina los volúmenes que ya no se están utilizando. O con "docker volume rm datos_nignx"

celiagm@debian:~$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
datos_nginx

Total reclaimed space: 12.2MB
```

### Volumenes con Bind mounts

Con el método bind mount hacemos la información persistente y estará gestionada por nosotros mismos, a diferencia de los volúmenes Docker que requieren permisos. Este sistema es muy parecido ya que se trata de montar una parte del sistema de ficheros en el contenedor. Sería así como un **directorio compartido**.

#### Crear el directorio compartido 

Vamos a crear un directorio en el que vamos a alojar los datos de nuestra aplicación. En este caso será un html simple.

```shell 
mkdir datos
nano index.html 
```
Contenido:

```shell 
celiagm@debian:~/trabajo/datos$ cat index.html 
<h1>Hello World</h1>
```

#### Crear el contenedor y montar el volumen 

```shell
# Creamos el contenedor indicando la ruta de nuestro directorio con el html y la ruta en la que se va a montar el volumen.
celiagm@debian:~/trabajo/datos$ docker run -d --name web1 -v /home/celiagm/trabajo/datos:/usr/local/apache2/htdocs -p 5002:80 httpd:2.4
a13d97cfdf0fece81511747c4ffdb39ae4ce22c66e3e39cc6a572bcee212fb64

# Comprobamos que efectivamente se ha montado el volumen y obtenemos nuestro html simple.
celiagm@debian:~/trabajo/datos$ curl localhost:5002
<h1>Hello World</h1>
```

Si paramos el contenedor y asociamos el volumen a otro contenedor la información sigue siendo persistente. 

Además podemos modificar los datos directamente desde nuestro host físico 

```shell
celiagm@debian:~/trabajo/datos$ curl localhost:5002
<h1>Modificado</h1>

```

Si inspeccionamos el contenedor y vemos los Mounts comprobaremos que están asociados los directorios.

```shell
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/celiagm/trabajo/datos",
                "Destination": "/usr/local/apache2/htdocs",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],

```
#### Eliminar contenedores y volumenes: Bind Mount

Si eliminamos los contenedores, el volumen sigue siendo persistente. En este caso como es bind mount para eliminarlos tendríamos que eliminar el directorio que hemos creado nosotros. 

```shell 
celiagm@debian:~/trabajo/datos$ docker stop web1 && docker rm web1
web1
web1
celiagm@debian:~/trabajo/datos$ ls
index.html

``` 
{{< alert type="info" >}}
Otra opción es crear volúmenes de contenedores de datos pero eso no lo vamos a ver en este post.
{{< /alert >}}

## Docker Compose 

Hasta ahora hemos utilizado Docker para la creación de contenedores, pero ahora vamos a hablar de Docker-Compose.
{{< alert type="dark" >}}
Docker-Compose es una herramienta que sirve para definir y ejecutar aplicaciones con múltiples contenedores a partir de un fichero de extensión YAML. Estos contenedores por lo general están configurados de forma que interaccionan entre ellos. 
{{< /alert >}}
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

