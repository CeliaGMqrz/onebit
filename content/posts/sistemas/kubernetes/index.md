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

* ¿Qué es Kubernetes? 
* [Características más notables](#características-mas-notables)
* Ejecutar Kubernetes sin SUDO 
* Gestión de contenedores
* Gestión de Imágenes 
* Ejemplos con NGINX
______________

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

-> Tenemos múltiples controller:

* API Server
* Scheduler
* Controller Manager
* etcd (base de datos no relacional para alta disponibilidad)

-> Tenemos múltiples nodos que soportan la carga de trabajo (worker):

* Infraestructura física
* Sistema operativo 
* Contenedores 
* kubelet (Recibe las ordenes del controller y se comunica con el containerruntime para ejecutarlas)
* kube-proxy (Conectividad de los contenedores a través de la red.)

* Instalación de minikube

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb

sudo dpkg -i minikube_latest_amd64.deb

```

El proyecto kubernetes

Gestiona el despliegue de aplicaciones sobre contenedores, automatizando el despliegue, con énfasis en la escabilidad y controlando todo el ciclo de vida.

* Despliega aplicaciones rápidamente
* Escala aplicaciones al vuelo
* Integra cambios sin interrupciones
* Permite limitar los recursos a utilizar
* Es un proyecto centrado en la puesta en PRODUCCIÓN de contenedores y por tanto indicada su gestión para administradores de sistemas y personal de equipos de operaciones.
* Afecta a los desarrolladores, ya que las aplicaciones deben adaptarse para poder desplegarse en kubernetes.
* Puede desplegarse en nube
* Extensible: Módulos, plugins, adaptable
* Autoreparable
* 