---
title: "Introducción a Kubernetes"
date: 2021-08-23
menu:
  sidebar:
    name: "Introducción a Kubernetes"
    identifier: kubernetes
    parent: systems
    weight: 600
hero: images/kubernetes.png
---

Te recomiendo si no conoces Docker ir esta [entrada](https://www.celiagm.es/posts/sistemas/docker/) antes de continuar con este post.
______________
En este post vamos a dar una breve introducción de Kubernetes:


- [¿Qué es Kubernetes?](#qué-es-kubernetes)
- [Características mas notables](#características-mas-notables)
- [Arquitectura de Kubernetes](#arquitectura-de-kubernetes)
- [Recursos de kubernetes](#recursos-de-kubernetes)
  - [**Conceptos:**](#conceptos)
    - [¿Qué es un POD?](#qué-es-un-pod)
    - [¿Qué es ReplicaSet?](#qué-es-replicaset)
    - [¿Qué es Deployment?](#qué-es-deployment)
    - [¿Qué son los Servicios?](#qué-son-los-servicios)
    - [¿Qué es ingress?](#qué-es-ingress)
- [Crear un despliegue con Kubernetes](#crear-un-despliegue-con-kubernetes)
  - [Instalación de minikube](#instalación-de-minikube)
  - [Instalación de kubectl](#instalación-de-kubectl)
  - [Iniciar minkube y obtener la ip](#iniciar-minkube-y-obtener-la-ip)
  - [Obtener la ip de minikube.](#obtener-la-ip-de-minikube)
  - [Crear deployment](#crear-deployment)
______________

{{< alert type="info" >}}
Para más información consulta la [documentación oficial de kubernetes](https://kubernetes.io/docs/home/)
{{< /alert >}}

## ¿Qué es Kubernetes?

{{< alert type="dark" >}}
Kubernetes es un **orquestador de contenedores** que actualmente es uno de los más populares del mercado. La **orquestación de contenedores** surge de la necesidad de automatizar el despliegue, la gestión, el escalado, la interconexión y disponibilidad de las aplicaciones. 

Este software agrupa un conjunto de contenedores que mantienen una aplicación en unidades lógicas para hacer más fácil la gestión de los mismos.

Está escrito en Go y fue lanzado por Google en un principio, que se unió a Linux Foundation para crear la Cloud Native Computing Foundation que es quien lo gestiona.
{{< /alert >}}

## Características mas notables

{{< alert type="success" >}}
* Kubernetes trabaja sobre un **clúster de equipos**, que permite que si algún nodo deja de funcionar, el cluster sigue en pleno funcionamiento y las aplicaciones implementadas en él también sigan operativas. De forma que los contenedores que se estaban ejecutando en el nodo que ha fallado se distribuye a otro equipo para seguir operativos.
* Despliega aplicaciones de forma rápida y las escala al vuelo.
* Integra los cambios sin interrupciones.
* Permite limitar recursos.
* Es importante saber que es un proyecto centrado en la puesta en **PRODUCCIÓN** de contenedores. La gestión del mismo es el papel principal de un administrador de sistemas **DEVOPS**.
* El proyecto kubernetes está pensado para desplegarse en **nube** (pública, privada o híbrida).
* Es extensible y autoreparable.
{{< /alert >}}

## Arquitectura de Kubernetes 

![arq.png](/images/posts/kubernetes/arq.png)

-> Tenemos múltiples **controllers** o controladores:

* API Server: Es el servidor de la API de Kubernetes que valida y configura los objetos de la API incluyendo (pods,servicios,replicaset..) además proporciona la interfaz necesaria para interactuar.
* Scheduler: Con el mismo se puede programar un componente en el mismo software y forma parte del plano de control.
* Controller Manager: Es un demonio que integra los bucles de control centrales.
* etcd: Base de datos no relacional para alta disponibilidad.

-> Tenemos múltiples nodos que soportan la carga de trabajo (llamados **workers**):

* Infraestructura física
* Sistema operativo 
* Contenedores 
* kubelet (Recibe las ordenes del controller y se comunica con el containerruntime para ejecutarlas)
* kube-proxy (Conectividad de los contenedores a través de la red.)


## Recursos de kubernetes 

![recursos.png](/images/posts/kubernetes/recursos.png)

### **Conceptos:**

#### ¿Qué es un POD?
{{< alert type="dark" >}}
Con el término **POD** nos referimos a la **unidad más pequeña de kubernetes** con la que podemos correr los contenedores. Un pod representa un conjunto de contenedores que **comparten almacenamiento y una IP única**. Cuando se destruyen pierden toda la información, de forma que si queremos desarrollar aplicaciones persistentes tenemos que utlizar **volúmenes**.
{{< /alert >}}

#### ¿Qué es ReplicaSet?

{{< alert type="dark" >}}
Es el responsable de que los contenedores se estén ejecutando. Se asegura que se ejecuten un número de réplicas de un pod en concreto. Así se asegura de que siempre están funcionando y disponibles por lo que es **tolerante a fallos** y permite **escalabilidad dinámica**.
{{< /alert >}}

#### ¿Qué es Deployment?

{{< alert type="dark" >}}
Es la unidad de más alto nivel que podemos gestionar en Kubernetes. Se encarga de que todo esto funciones:

* Control de replicas
* Escalabilidad de pods
* Actualizaciones continuas
* Despliegues automáticos
* Rollback a versiones anteriores
{{< /alert >}}

#### ¿Qué son los Servicios?

{{< alert type="dark" >}}
Un servicio es una abstracción que define un conjunto de pods que implementan un micro-servicio. El servicio me permite acceder a la aplicación independientemente de el numero de pods que se estén ejecutando.
{{< /alert >}}

#### ¿Qué es ingress?

{{< alert type="dark" >}}
Es un objeto de la API que administra el acceso externo a los servicios en un clúster, generalmente HTTP. Básicamente actúa como proxy inverso dando una URL para acceder a la aplicación. Nos permite tambien el **balanceo de carga**.
{{< /alert >}}


{{< alert type="warning">}}
Pues así a *grandes rasgos* resumimos:

Kubernetes es un software que actúa sobre un clúster de nodos donde se ponen en funcionamiento varios contenedores, hay varios controladores o nodos que se encargan de mandar trabajo a otros nodos (los workers) que son los que ejecutan el trabajo. Este trabajo consiste en gestionar aplicaciones de forma más organizada, rápida y eficaz.

Teniendo en cuenta este resumen pasamos a la práctica.
{{< /alert >}}

## Crear un despliegue con Kubernetes 

Nuestro objetivo en este punto es desplegar una aplicación en Kubernetes. 

> Necesitaremos:
> * Una aplicación simple para usar. En mi caso vamos a utilizar una que tengo subida en mis repositorios de DockerHub: [cgmarquez95/pruebanginx](https://hub.docker.com/repository/docker/cgmarquez95/pruebanginx).
>* Descargar kubectl y minikube
>* Tener un sistema de virtualización (Docker,Virtualbox,KVM...)


### Instalación de minikube

Aquí tenemos la documentación oficial de minikube:

[Minikube start](https://minikube.sigs.k8s.io/docs/start/)

> Es una implementación más ligera de Kubernetes que crea un cluster de un sólo nodo y es ideal para pruebas.

En mi caso lo voy a instalar en Debian, usando la paquetería de Debian.

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```
{{< alert type="warning">}}
Minikube se puede iniciar en el sistema de virtualización que elijamos dentro de sus posibilidades, en este caso por defecto usará Docker, pero podríamos especificar Virtualbox o KVM  por ejemplo.
{{< /alert >}}

* La versión que tenemos actualmente de minikube es:

```shell
celiagm@debian:~/kubernetes$ minikube version
minikube version: v1.17.1
```

### Instalación de kubectl 

Sin más lo descargamos tal y como indica en la [documentación oficial de kubernetes](https://kubernetes.io/es/docs/tasks/tools/install-kubectl/) 

```shell 
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2 curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

La versión de kubectl es:

```shell
celiagm@debian:~/kubernetes$ kubectl version
Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.1", GitCommit:"632ed300f2c34f6d6d15ca4cef3d3c7073412212", GitTreeState:"clean", BuildDate:"2021-08-19T15:45:37Z", GoVersion:"go1.16.7", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.2", GitCommit:"faecb196815e248d3ecfb03c680a4507229c2a56", GitTreeState:"clean", BuildDate:"2021-01-13T13:20:00Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}
WARNING: version difference between client (1.22) and server (1.20) exceeds the supported minor version skew of +/-1
```

### Iniciar minkube y obtener la ip

Una vez instalados los dos paquetes vamos a proceder a iniciar minikube que levantará un nodo virtual 

```shell 
minikube start 
```

Con `kubectl` comprobamos los nodos que tenemos disponibles 

```shell 
celiagm@debian:~/kubernetes$ kubectl get nodes
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   51s   v1.20.2

```

```shell
celiagm@debian:~/kubernetes$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
KubeDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```
En mi caso como hemos dicho anteriormente se ha creado un contenedor en Docker virtualizando este servicio. 

```shell 
celiagm@debian:~/kubernetes$ docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED         STATUS         PORTS                                                                                                      NAMES
628ab184370c   gcr.io/k8s-minikube/kicbase:v0.0.17   "/usr/local/bin/entr…"   8 minutes ago   Up 8 minutes   127.0.0.1:49156->22/tcp, 127.0.0.1:49155->2376/tcp, 127.0.0.1:49154->5000/tcp, 127.0.0.1:49153->8443/tcp   minikube

```

Tambien podemos ver el estado de minikube 

```shell 
celiagm@debian:~/kubernetes$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
timeToStop: Nonexistent

```

### Obtener la ip de minikube. 

```shell 
celiagm@debian:~/kubernetes$ minikube ip 
192.168.39.34

```

### Crear deployment 

Primero creamos un despliegue con la aplicación que tenemos en docker, yo voy a usar la siguiente [cgmarquez95/pruebanginx:v2](https://hub.docker.com/repository/docker/cgmarquez95/pruebanginx)

```shell 
celiagm@debian:~/kubernetes$ kubectl create deployment web-nginx --image=cgmarquez95/pruebanginx:v2
deployment.apps/web-nginx created
```
Comprobamos que está disponible 

```shell 
celiagm@debian:~/kubernetes$ kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
web-nginx   1/1     1            1           16s
```

Ahora vamos a exponer el puerto 80 de nuestro nginx especificando un tipo NodePort para poder acceder desde el exterior y ver nuestra aplicación en el navegador. Kubernetes asigna un puerto aleatorio del 30000 al 40000 para cada servicio. 

```shell 
celiagm@debian:~/kubernetes$ kubectl expose deployment web-nginx --port=80 --type=NodePort
service/web-nginx exposed
celiagm@debian:~/github/onebit$ kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        10m
web-nginx    NodePort    10.106.84.137   <none>        80:30680/TCP   9m34s
```

De manera que podemos acceder a la ip de minkube con el puerto asignado y debería de verse la página inidial de nuestro Nginx, que en este caso la modificamos en el [post anterior](https://www.celiagm.es/posts/sistemas/docker/) con Docker.

![nginx-kubernetes.png](/images/posts/kubernetes/nginx-kubernetes.png)


