---
id: 323
title: kubernetes = cluster servicios docker fácil
date: 2014-11-15T22:57:53+00:00
author: javier
guid: http://javierjeronimo.es/?p=323
permalink: /2014/11/15/kubernetes-cluster-servicios-docker-facil/
categories:
  - cloud
tags:
  - cloud
  - contenedor
  - docker
  - google cloud platform
  - google container engine
  - kubernetes
  - vagrant
---
Llevo varios meses dándole vueltas al tema. He hecho algún intento de meter la cabeza en el ecosistema de docker. Es el futuro. Portabilidad. deploy-anywhere.

Esta semana he podido sacar la cabeza de [los](http://www.graylog2.org "Graylog2") [proyectos](http://moda.genexies.net "Movistar España - Fashion BIP") en los que estaba apoyando en el trabajo. Me le limpiado un poco el barro de los brazos y he podido navegar y echar un vistazo a las newsletters que recibo. Otro producto de _Google Cloud Platform_: el [motor de contenedores (Google Container Engine)](https://cloud.google.com/container-engine "Google Cloud Platform - Container Engine").

La excusa que me faltaba para [terminar de probar docker](https://github.com/javierjeronimo/raichuserver "Servidor doméstico con Vagrant + Docker"): una interfaz para crear un cluster de nodos &#8220;docker&#8221;, gestionar los servicios desplegados, y con soporte para varios proveedores: Vagrant, vSphere, AWS, Google Compute Engine&#8230;).

Así que comienzo a probarlo (OS X):

  1. <span style="letter-spacing: 0.05em;">Vagrant >=1.6.2</span>
  2. VirtualBox
  3. Kubernetes (distribución binaria&#8230; tampoco vamos a perder el tiempo&#8230;): https://github.com/GoogleCloudPlatform/kubernetes/releases

Vamos a echar un vistazo al Vagrantfile de kubernetes.

Anecdótico: el editor [atom de GitHub](https://atom.io "ATOM: A hackable text editor for the 21st Century") está desarrollado sobre Chromium (_Comando+Tab_ me muestra esto):

[<img class="alignnone size-full wp-image-324" src="http://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.06.59.png" alt="Captura de pantalla 2014-11-15 a las 22.06.59" width="1440" height="900" srcset="https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.06.59.png 1440w, https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.06.59-300x187.png 300w, https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.06.59-1024x640.png 1024w" sizes="(max-width: 1440px) 100vw, 1440px" />](http://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.06.59.png)

Puntos reseñables, ya sí, sobre el Vagrantfile de kubernetes:

  * Código Ruby para hacer parametrizable el entorno que construirá el Vagrantfile. Elegante.
  * La distribución preferida para los nodos es Fedora 20 + Chef.
  * Código Ruby (bucle) para crear N máquinas en el cluster (minions). Elegante.

<span style="letter-spacing: 0.05em;">Este Vagrantfile es un ejemplo de por qué es una buena opción usar Ruby como base para un DSL (</span><a style="letter-spacing: 0.05em;" title="lenguaje específico del dominio" href="http://es.wikipedia.org/w/index.php?title=Lenguaje_espec%C3%ADfico_del_dominio&oldid=76011622"><em>domain specific language</em></a><span style="letter-spacing: 0.05em;">).</span>

Estilo vagrant:

<pre>curl ...
tar xvzf kubernetes...
cd kubernetes
vagrant up</pre>

Y al cabo de un rato&#8230;

<pre style="color: #333333;">$ export KUBERNETES_PROVIDER=vagrant
$ ./cluster/kubecfg.sh list /minions
Minion identifier
----------
10.245.2.3
10.245.2.2

$ cluster/kubecfg.sh list /pods
ID Image(s) Host Labels Status
---------- ---------- ---------- ---------- ----------

$ cluster/kubecfg.sh list /services
ID Labels Selector Port
---------- ---------- ---------- ----------

$ cluster/kubecfg.sh list /replicationControllers
ID Image(s) Selector Replicas
---------- ---------- ---------- ----------</pre>

<p style="color: #333333;">
  Y pruebo a lanzar un cluster de 3 servidores nginx como indica la documentación:
</p>

<pre>$ cluster/kubecfg.sh -p 8080:80 run dockerfile/nginx 3 myNginx
id: myNginx
creationTimestamp: 2014-11-15T21:43:15Z
selfLink: /api/v1beta1/replicationControllers/myNginx
resourceVersion: "3"
namespace: default
desiredState:
 replicas: 3
 replicaSelector:
 simpleService: myNginx
 podTemplate:
 desiredState:
 manifest:
 version: v1beta2
 id: ""
 volumes: []
 containers:
 - name: mynginx
 image: dockerfile/nginx
 ports:
 - hostPort: 8080
 containerPort: 80
 protocol: TCP
 imagePullPolicy: ""
 restartPolicy:
 always: {}
 labels:
 simpleService: myNginx
currentState:
 replicas: 0
 podTemplate:
 desiredState:
 manifest:
 version: ""
 id: ""
 volumes: []
 containers: []
 restartPolicy: {}</pre>

<p style="color: #333333;">
  Im-presionante:
</p>

<pre style="color: #333333;">$ cluster/kubecfg.sh list /pods
ID Image(s) Host Labels Status
---------- ---------- ---------- ---------- ----------
67a3b726-6d10-11e4-a8de-0800279696e1 dockerfile/nginx 10.245.2.2/10.245.2.2 replicationController=myNginx,simpleService=myNginx Waiting
67a4dbae-6d10-11e4-a8de-0800279696e1 dockerfile/nginx 10.245.2.3/10.245.2.3 replicationController=myNginx,simpleService=myNginx Waiting
67a4fc05-6d10-11e4-a8de-0800279696e1 dockerfile/nginx &lt;unassigned&gt; replicationController=myNginx,simpleService=myNginx Waiting

$ cluster/kubecfg.sh list /replicationControllers
ID Image(s) Selector Replicas
---------- ---------- ---------- ----------
myNginx dockerfile/nginx simpleService=myNginx 3</pre>

<p style="color: #333333;">
  Y al cabo de un rato, los dos &#8220;minions&#8221; (nodos del cluster) terminan de aprovisionar los contenedores docker de NGINX:
</p>

<pre style="color: #333333;">$ cluster/kubecfg.sh list /pods
ID Image(s) Host Labels Status
---------- ---------- ---------- ---------- ----------
67a3b726-6d10-11e4-a8de-0800279696e1 dockerfile/nginx 10.245.2.2/10.245.2.2 replicationController=myNginx,simpleService=myNginx Running
67a4dbae-6d10-11e4-a8de-0800279696e1 dockerfile/nginx 10.245.2.3/10.245.2.3 replicationController=myNginx,simpleService=myNginx Running
67a4fc05-6d10-11e4-a8de-0800279696e1 dockerfile/nginx &lt;unassigned&gt; replicationController=myNginx,simpleService=myNginx Waiting</pre>

<p style="color: #333333;">
  Y&#8230;
</p>

<p style="color: #333333;">
  <a href="http://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.52.08.png"><img class="alignnone size-full wp-image-326" src="http://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.52.08.png" alt="Captura de pantalla 2014-11-15 a las 22.52.08" width="1052" height="347" srcset="https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.52.08.png 1052w, https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.52.08-300x98.png 300w, https://javierjeronimo.es/wp-content/uploads/2014/11/Captura-de-pantalla-2014-11-15-a-las-22.52.08-1024x337.png 1024w" sizes="(max-width: 1052px) 100vw, 1052px" /></a>
</p>

<p style="color: #333333;">
  Tengo que seguir investigando&#8230;
</p>