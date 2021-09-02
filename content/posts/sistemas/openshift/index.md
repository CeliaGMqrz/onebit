---
title: "Despliegue con OpenShift"
date: 2021-08-31
menu:
  sidebar:
    name: "Despliegue con OpenShift"
    identifier: openshift
    parent: systems
    weight: 900
hero: images/opens.png
---

En este post vamos a ver de forma muy simple cómo desplegar una aplicación en OpenShift y de qué se trata esta plataforma.

_______________________________
- [¿Qué es OpenShift?](#qué-es-openshift)
- [¿Por qué utilizar OpenShift?](#por-qué-utilizar-openshift)
- [Desplegar una aplicación en OpenShift](#desplegar-una-aplicación-en-openshift)
- [Aplicación funcionando](#aplicación-funcionando)
- [Conclusión](#conclusión)

______________

{{< alert type="info" >}}
Esta plataforma utiliza internamente Docker y Kubernetes por lo que recomiendo ver los siguientes posts:
* [Docker](https://www.celiagm.es/posts/sistemas/docker/)
* [Kubernetes](https://www.celiagm.es/posts/sistemas/kubernetes/)
{{< /alert >}}

## ¿Qué es OpenShift?

{{< alert type="dark" >}}
OpenShift es una plataforma de desarrollo, con características de Cloud Computing desarrollada por la empresa Red Hat. 

Utiliza internamente Docker y Kubernetes con operaciones automatizadas integrales lo que facilita a los desarrolladores centrarse sólo en la tarea del desarrollo. Permite desplegar aplicaciones tanto en desarrollo como en producción.

[Más información](https://www.redhat.com/es/technologies/cloud-computing/openshift)
{{< /alert >}}

## ¿Por qué utilizar OpenShift?

{{< alert type="success" >}}
* No tienes porqué saber como funciona internamente la plataforma, o cómo usar Docker o Kubernetes. Brinda todas las ventajas de estos de forma más rápida y sencilla, así el desarrollador se centra en su tarea.

* OpenShift es una ditribución certificada de Kubernetes que facilita la gestión de las imágenes, creándolas de forma automática y listas para utilizar en los contenedores.
  
* Ofrece la escalabilidad con la que cuenta Kubernetes.

* La aplicación se puede enlazar fácilmente a un repositorio donde tengamos nuestro proyecto y de forma rápida y fácil desplegarla.

* Ofrece un servicio Online con una interfaz bastante intuitiva, esta es OpenShift Online, solo hay que registrarse y elegir el plan que más se adecúe a nuestras características. 

* Tiene una versión de software libre con la que podemos montar nuestro propio clúster. OKD (Origin)

* Si necesitamos un clúster para una empresa o uso privado podemos optar por OpenShift Dedicated.
   
* Soporta muchas tecnologías como Java, Jenkins, MongoDB, Python, MySQL ... entre otras.
{{< /alert >}}

## Desplegar una aplicación en OpenShift


Pasos a seguir:

* Registrarse en el siguiente [enlace](https://manage.openshift.com/register/plan)
* Elegir el plan adecuado para tí. 
  
* Get Started in the Sandbox

![get_started.png](/images/posts/openshift/get_started.png)

* Start using your sandbox 

![start_using.png](/images/posts/openshift/start_using.png)

* Una vez dentro de la interfaz ya podemos enlazar nuestra aplicación alojada (en nuestro caso en GitHub)

![add.png](/images/posts/openshift/add.png)

* Solo tenemos que añadir la url del repositorio y elegir nuestro servicio web, en nuestro caso elegiremos apache. 

![import.png](/images/posts/openshift/import.png)

* Vemos que se nos ha creado el despliegue 

![topology.png](/images/posts/openshift/topology.png)

* Si vamos viendo el menú de despliegue a la izquierda podremos comprobar que se nos ha creado un pod, un despliegue, replicaset, un servicio... Todo lo que conlleva el despliegue de la aplicación. 

![elementos.png](/images/posts/openshift/elementos.png)

## Aplicación funcionando

Vamos a la url del sitio y comprobamos que está en funcionamiento 

![despliegue.png](/images/posts/openshift/despliegue.png)

## Conclusión 

Esta plataforma es una buena opción para que los desarrolladores se centren en su trabajo sin la necesidad de conocer profundamente cómo funciona internamente con Docker y Kubernetes. 
