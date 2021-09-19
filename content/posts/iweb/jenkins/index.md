---
title: "Introducción a Jenkins"
date: 2021-09-19
menu:
  sidebar:
    name: "Introducción a Jenkins"
    identifier: jenkins
    parent: iweb
    weight: 250
hero: images/jenkins.png
---

___
- [Integración Continua (CI) como concepto.](#integración-continua-ci-como-concepto)
- [¿Qué es Jenkins?](#qué-es-jenkins)
- [Instalación de Jenkins](#instalación-de-jenkins)
    - [Unlock Jenkins](#unlock-jenkins)
    - [Configuración Jenkins](#configuración-jenkins)
      - [Seguridad](#seguridad)
      - [Jenkins CLI](#jenkins-cli)
      - [Alias para facilitar el trabajo con Jenkins CLI](#alias-para-facilitar-el-trabajo-con-jenkins-cli)
      - [Comandos útiles](#comandos-útiles)
___

# Integración Continua (CI) como concepto.

{{< alert type="dark" >}}
La integración continua (CI) es la práctica de desarrollo software donde los miembros de un equipo integran su trabajo de forma periódica, verificando el código tras la compilacion y pruebas del mismo, con el objetivo de detectar errores así poder evitarlos. Además del control de versiones y generación de informes.
{{< /alert >}}


# ¿Qué es Jenkins?

{{< alert type="success" >}}
Jenkins es un servidor de automatización usado más para la integración continua y es de código abierto. Está basado en el proyecto [Hudson](https://es.wikipedia.org/wiki/Hudson_(software)). Está escrito en Java. 

Jenkins es capaz de realizar múltiples tareas necesarias para asegurar una calidad en el software:

* Facilita el control de versiones 
* Hace pruebas y testea el código
* Monitorización continua de las métricas de calidad de proyecto.
* Puede enviar los cambios una vez testeados al repositorio principal 
* Automatiza la compilación y puede también automatizar el despliegue, una vez integrado los cambios testeados.
* Notifica de cualquier cambio o error a los integrantes del equipo.
* Permite generar y visualizar la documentación del proyecto.
* Cuenta con muchísimos plugins para realizar diferentes tareas.

{{< /alert >}}


# Instalación de Jenkins

La instalación de Jenkins depende del SO en el que nos encontremos por lo que puedes seguir la guía de la [documentación oficial](https://www.jenkins.io/doc/book/installing/) 

En mi caso voy a lanzar un contenedor con Docker desde la imagen de jenkins en concreto "**jenkins/jenkins:lts**".

Para instalar Docker puedes ayudarte de mi post [Introducción a Docker](https://www.celiagm.es/posts/sistemas/docker/#instalaci%C3%B3n-de-docker-en-debian-buster), o consultar la documentación oficial de Docker.

Una vez tengamos Docker instalado vamos a lanzar el contenedor de forma que:

`docker run` - Arranca el contenedor
`-d` - En segundo plano 
`-v` - Indicamos el volumen como punto de montaje 
`-p` - Exponemos los puertos, que utilizara el **50000** y el **8080** pero indicaremos el **9080** para jenkins porque el 8080 será para la aplicación que vamos a desplegar. 
`-e` - Variable de entorno JENKINS_OPTS para indicar el puerto que va usar jenkins 
`--name` - Nombre del contenedor 
`jenkins/jenkins:lts` - Imagen

La instrucción queda de la siguiente manera:

```shell 
docker run -d -v $(pwd)/jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 -p 9080:9080 -e JENKINS_OPTS="--httpPort=9080" --name jenkins jenkins/jenkins:lts
```

Comprobamos que está funcionando y podemos acceder al navegador en el puerto indicado 

```shell 
celiagm@debian:~/github/onebit$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED        STATUS       PORTS                                                                                                                                 NAMES
f58dc8b0e18f   jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   32 hours ago   Up 7 hours   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:9080->9080/tcp, :::9080->9080/tcp, 0.0.0.0:50000->50000/tcp, :::50000->50000/tcp   jenkins
```


![unlock-jenkins.png](/images/posts/docker/unlock-jenkins.png)

### Unlock Jenkins 

Como podemos apreciar en el navegador nos indica que tenemos que introducir una clave que se ubica en la ruta `/var/jenkins_home/secrets/initialAdminPassword`. 

En este caso como hemos montado un volumen, accedemos a ese volumen y visualizamos la clave. También podríamos entrar en el contenedor con `exec -ti` y obtenerla.

```shell 
celiagm@debian:~/docker/jenkins_home/secrets$ cat initialAdminPassword 
```

Copiamos la clave y la introducimos en el navegador.

### Configuración Jenkins

Una vez hecho eso nos saldrá la pantalla de personalización, para este ejemplo instalaremos los plugins sugeridos.

![customize.png](/images/posts/docker/customize.png)

Ahora empezará a instalar 

![start.png](/images/posts/docker/start.png)

Crearemos el usuario administrador 

![admin.png](/images/posts/docker/admin.png)

Indicaremos la url, en este caso dejaremos localhost por defecto.

![url.png](/images/posts/docker/url.png)

Ya tendríamos el sitio listo para usar 

![inicio.png](/images/posts/docker/inicio.png)

#### Seguridad 

Iremos en el menú de la izquierda a >> Manage Jenkins >> En Security vamos a >> Configure Global Security. Para configurar la seguridad de Jenkins. 

En este caso está todo correcto, para este entorno de prueba pero aquí puedes modificarlo según tus necesidades 

![Security.png](/images/posts/docker/Security.png)

En `Credentials` podemos añadir formas de autenticación para conectarnos remotamente ya bien sea por usuario y contraseña o por clave ssh. 

![ssh.png](/images/posts/docker/ssh.png)

#### Jenkins CLI 

Con Jenkins CLI nos referimos al cliente de Jenkins desde la línea de comandos. Es más rápido de gestionar y más eficiente en cuanto a memoria utilizada. 

Para descargar Jenkins CLI accedemos a la url: http://docker.jenkins:9080/jnlpJars/jenkins-cli.jar 

Si entramos en la url: http://localhost:9080/cli/

Nos aparecerá la forma de ejecutar el jenkins cli y los muchos comandos para su gestión.

> Necesitaremos tener instalado java, en mi caso es:
> ```shell
> sudo apt-get install default-jdk 
> ``` 

Ejecutamos el siguiente comando, indicando la ruta donde tenemos el cli, y le indicamos la autentificación que en mi caso sera usuario y contraseña.

```shell 
java -jar jenkins-cli.jar -s http://localhost:9080/ -auth admin:admin -webSocket help
```

Una vez nos hayamos logueado podemos ir probando comandos por ejemplo:

```shell 
celiagm@debian:~/docker/jenkins_home$ java -jar /home/celiagm/jk/jenkins-cli.jar -s http://localhost:9080/ -auth admin:admin who-am-i
Authenticated as: admin
Authorities:
  authenticated
```

#### Alias para facilitar el trabajo con Jenkins CLI

El comando a utilizar es muy tedioso de escribir y podemos crear un alias para ahorrarnos tiempo. 

```shell 
alias jenkinscli="java -jar /home/celiagm/jk/jenkins-cli.jar -s http://localhost:9080/ -auth admin:admin"
```

Si queremos saber la versión

```shell 
celiagm@debian:~/docker/jenkins_home$ jenkinscli version
2.303.1
```

#### Comandos útiles 

```shell 
# Visualizar la id de la sesión
celiagm@debian:~/docker/jenkins_home$ jenkinscli session-id
50964909
# Listar las tareas (hemos creado una de prueba)
celiagm@debian:~/docker/jenkins_home$ jenkinscli list-jobs
prueba
# Reiniciar jenkins (esto no mata el contenedor)
celiagm@debian:~/docker/jenkins_home$ jenkinscli restart

celiagm@debian:~/docker/jenkins_home$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED        STATUS       PORTS                                                                                                                                 NAMES
f58dc8b0e18f   jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   34 hours ago   Up 8 hours   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:9080->9080/tcp, :::9080->9080/tcp, 0.0.0.0:50000->50000/tcp, :::50000->50000/tcp   jenkins

# Lanzamos la tarea de prueba 
celiagm@debian:~/docker/jenkins_home$ jenkinscli build prueba
celiagm@debian:~/docker/jenkins_home$ jenkinscli build prueba
```

Comprobamos en el navegador 

![build.png](/images/posts/docker/build.png)

Borramos la tarea 

```shell 
celiagm@debian:~/docker/jenkins_home$ jenkinscli delete-job prueba
celiagm@debian:~/docker/jenkins_home$ jenkinscli list-jobs
celiagm@debian:~/docker/jenkins_home$ 

```

[Este post está en proceso ... ;)]

Fuentes:

- [OpenWebinars](https://openwebinars.net/academia/portada/jenkins-principiantes/): Curso para principiantes por David Sebastián.
- [Integracion Continua OW](https://openwebinars.net/blog/integracion-continua-con-jenkins/)

- [Atlassian](https://www.atlassian.com/es/continuous-delivery/continuous-integration)

