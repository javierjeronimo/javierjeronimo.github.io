---
id: 365
title: Push to deploy desde BitBucket
date: 2015-03-22T12:28:41+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=365
permalink: /2015/03/22/push-to-deploy-desde-bitbucket/
categories:
  - Procesos software
tags:
  - atlassian
  - bitbucket
  - cloud
  - deploy
  - despliegues
  - git
  - google compute engine
  - grunt
  - maven
  - push
  - repositorio
---
En _Genexies Mobile_ estamos lanzando un nuevo servicio (lo estamos presentando a varios concursos, entre ellos al _TechCrunch Startup Battlefield_ de mayo de 2015&#8230; ya iremos contando&#8230;) y para agilizar las pruebas en pre-producción hemos configurado un sistema de &#8220;_push to deploy_&#8221; desde _BitBucket_ hacia los servidores de pruebas que tenemos en _Google Compute Engine_.

<!--more-->

La solución ha sido bastante sencilla de configurar y voy a explicar un poco cómo lo hemos hecho.

## Configuración del servidor de pre-producción

Como he dicho, tenemos un servidor de pre-producción en _Google Compute Engine_. De hecho, tenemos dos servidores: el servidor _backend_ (con el API _Spring_ + _Jersey);_ y el servidor _MongoDB._

El servidor en cuestión, el del backend, tiene una estructura montada que nos permite usarlo para PRE/PRO, es decir, nos permite usarlo en dos entornos simultáneamente:

  * _www.example.com_: Aquí tenemos publicado un banner, mientras terminamos la versión _beta_.
  * _www-pro.example.com_: Aquí tendremos la versión _beta_ desplegada. Al disponer de este entorno y del anterior, podemos alternar en el dominio principal entre el _banner_ y la _beta_ (por si hubiera problemas graves que nos obligaran a tener que parar el servicio).
  * _www-pre.example.com_: La versión pre-producción del producto.

Como de momento sólo tenemos montado un grupo de servidores, tenemos los tres entornos montados compartiendo el mismo espacio:

<pre># ##########
# Banner: sitio de Apache sirviendo HTML estático.
/etc/apache2/sites-enabled/www-banner.conf
/srv/example/www-banner


# ##########
# PRO: desactivado (alternamos su publicación en el mismo dominio que el banner anterior). Consta de dos partes (servicio) y una adicional ("push-to-deploy"):
# - Sitio sirviendo interfaz HTML5
/etc/apache2/sites-available/www-pro.conf
/srv/example/www-pro # DocumentRoot

# - Proxy inverso a la aplicación en Tomcat7
/etc/apache2/sites-available/api-pro.conf
/srv/example/api-pro

# - Sitio sirviendo script PHP para "push-to-deploy"
/etc/apache2/sites-enabled/deploy-pro.conf
/srv/example/deploy-pro


# ##########
# PRE: Consta de las mismas tres partes:

# - Sitio sirviendo interfaz HTML5
/etc/apache2/sites-enabled/www-pre.conf
/srv/example/www-pre

# - Proxy inverso a la aplicación en Tomcat7
/etc/apache2/sites-enabled/api-pre.conf
/srv/example/api-pre

# - Sitio sirviendo script PHP para "push-to-deploy"
/etc/apache2/sites-enabled/deploy-pre.conf
/srv/example/deploy-pre

</pre>

## Push to deploy

_BitBucket_, el servicio de repositorios que usamos para algunos proyectos y para este también, permite hacer despliegues mediante una integración sencilla entre los servidores que ejecutan las aplicaciones y los servidores que alojan el código.

En el caso de este servicio, permite configurar _anclas_ (_hooks_) que no son más que puntos en el flujo de ejecución del servicio (repositorio Git) a los que añadir acciones adicionales a realizar. En general, acciones que se ejecutarán en servicios remotos.

_Push to deploy_ consiste en:

  * Cada vez que se realice un _push_ al repositorio _git_ alojado en _BitBucket_&#8230;
  * Ejecuta este ancla&#8230;

¿Qué tipos de anclas permite _BitBucket?_ Bastante, entre otras: integración con servicios _Bamboo, Campfire, Jenkins,_ enviar un _email,_ enviar un _tweet&#8230;_ y la que nos interesa a nosotros: en cada push al repositorio, envía una petición HTTP POST a una URL concreta (<https://confluence.atlassian.com/display/BITBUCKET/POST+hook+management>).

La implementación es sencilla: los sitios _Apache2_ &#8220;_deploy-xxx.conf_&#8221; que listaba anteriormente contienen un _script PHP_ que es invocado remotamente por _BitBucket_ cada vez que hacemos un _PUSH_ al repositorio _git._

Estos _scripts PHP_ realizan lo siguiente:

  * Comprobaciones del &#8220;_git push_&#8221; que ha servido de disparador para este proceso. Las explico más abajo.
  * Actualizan la copia del repositorio en el servidor (_git fetch_).
  * Exportan el contenido al punto de instalación.
  * Tareas propias de la generación de los artefactos en el punto de instalación: _mvn install_&#8230; _grunt build_&#8230; etc.
  * _DocumentRoot_ de los sitios de Apache2 apuntan al lugar donde se generan los artefactos en el paso anterior.

### Algunas preguntas importantes sobre el proceso

¿Siempre se despliegan los cambios cuando ha habido un_ push_ al repositorio central? ¿E independientemente de la rama sobre la que se hayan hecho los cambios? No, las comprobaciones iniciales de los _scripts PHP_ sirven para esto mismo:

  * En _www-pre_ y _api-pre_ sólo se desplieguen cambios (_git push_) realizados en la rama _integration_.
  * En _www-pro_ y _api-pro_ sólo cambios en la rama _master_.

¿Y si cualquier tunante invoca el POST en vuestros servidores? Es posible, pero improbable. Aunque es una implementación inicial, sencilla, para agilizar las pruebas durante el desarrollo, tiene dos puntos importantes de seguridad:

  * _HTTPS_ en la URL del _POST_ (tampoco hay que hacer el tonto sólo con _HTTP)._
  * El nombre del _script PHP_ tiene una componente aleatoria (por ejemplo: deploy-script-abcabc.php).

¿Y si algún tunante interno hace un _push_ de código roto? Pues efectivamente&#8230; pero somos tres los ingenieros que estamos lanzando el nuevo servicio, así que en ese caso nos tiramos una pelota de goma o damos un pequeño _alarido dirigido_ y listo&#8230; 😉 Cuando esté publicada la beta, será una máquina (_Jenkins_, _CodeShip_, &#8230;) la que despliegue, sólo si las pruebas pasan 100% ok.

Como se puede ver, tiene puntos de mejora, pero nos sirve para esta fase del proyecto.

## Resumen del proceso push-to-deploy

El proceso completo quedaría de la siguiente forma:

  1. Ingeniero &#8211; realiza un _git push_ al repositorio central _BitBucket_. bien de código de pruebas (rama _integration)_ o bien algo ya probado y consensuado para subida (rama _master)._
  2. Servidor _BitBucket_ &#8211; ejecuta el ancla: _POST https://deploy-pre.example.com/deploy-script-abcabc.php_
  3. _Script PHP_ despliegues &#8211; realizar comprobaciones iniciales.
  4. _Script PHP_ despliegues &#8211; actualizar el código en el servidor: _git fetch_.
  5. _Script PHP_ despliegues &#8211; exportar el código a la ruta de instalación.
  6. _Script PHP_ despliegues &#8211; generar los artefactos en la ruta de instalación (_mvn clean package tomcat7:redeploy_ en un caso y _grunt build_ en otro).

## Conclusiones

Es una integración sencilla, pero nos permite tener dos entornos: (1) pre-producción para pruebas en _entorno-real-no-localhost_ y (2) _casi-producción_ para la _beta_. También nos proporciona una agilidad brutal ya que cualquier miembro del equipo puede desplegar una nueva versión de código en cuestión de segundos. Y como _git_ impide que hagas un _push_ si no has mezclado previamente tus cambios con los más recientes en la rama en el repositorio&#8230; pues perfecto&#8230;

Otro punto importante: esta implementación del mecanismo _push-to-deploy_ es independiente de la infraestructura sobre la que despleguemos finalmente el servicio. _Google App Engine_, _Heroku_ y otros servicios de plataforma tienen sus propios sistemas de _push-to-deploy_, pero en nuestro caso estamos usando _infraestructura como servicio_ (_IaaS_) así que la solución es más casera, pero también compatible con cualquier servicio de infraestructura, porque al fin y al cabo es un _script PHP_ invocado remotamente.

La evolución de este sistema será:

  * Para los despliegues en **producción**: tener un repositorio central específico para este fin. Sólo el sistema de integración continua, o el maestro de la mazmorra si es una persona, tendrá permisos para hacer _push_ a este repositorio, y lo hará después de que las pruebas pasen 100% ok. O si terminamos usando un servicio externo de integración continua como _CodeShip_, quitaremos el ancla de _BitBucket_ y dejaremos que sea _CodeShip_ quien lance las pruebas y realice el _POST_ para el despliegue sólo si las pruebas salen 100% ok.
  * Para los despliegues en **pre-producción**: probablemente esta solución es suficiente, ya que cualquier ingeniero con acceso de escritura al repositorio central puede lanzar un despliegue del contenido de la rama _integration_ en este entorno de pruebas en la nube.
  * Mejorar el sistema de seguridad, dejando de usar una componente aleatoria en el nombre del _script,_ y pasando a usar un parámetro (_query-string_) que nos permita ir cambiando fácilmente dicho secreto con cierta frecuencia para mayor seguridad.