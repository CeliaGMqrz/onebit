---
title: "Introducción a Kubernetes"
date: 2021-03-20
menu:
  sidebar:
    name: "Introducción a Kubernetes"
    identifier: kubernetes
    parent: systems
    weight: 600
hero: images/kubernetes.png
---

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
- [Instalación de minikube](#instalación-de-minikube)
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


## Instalación de minikube

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb

sudo dpkg -i minikube_latest_amd64.deb

```

