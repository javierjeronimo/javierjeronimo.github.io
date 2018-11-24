---
id: 214
title: 'Atlassian Jira OnDemand &#8211; Primer vistazo a Jira 6 &#8220;en la nube&#8221;'
date: 2013-07-15T21:11:53+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=214
permalink: /2013/07/15/atlassian-jira-ondemand-primer-vistazo-a-jira-6-en-la-nube/
categories:
  - Cloud
tags:
  - atlassian
  - cloud
  - jira
  - nube
  - ondemand
---
Ayer me decidí a activar una &#8220;starter edition&#8221; de [Atlassian OnDemand](http://www.atlassian.com/software/ondemand/overview "Atlassian OnDemand"), integrada con la cuenta de Google Apps de mi dominio. De momento la experiencia es positiva.

He activado Jira+GreenHopper+Bamboo para hacer pruebas con la cuenta gratuita de BitBucket que tengo (espero no haberme equivocado en esto último y que finalmente resulte que necesito Stash para poder usar Bamboo&#8230;). La cuota no me parece ninguna locura, un par de salidas al mes salen más caras que estos productos de Atlassian en la nube, y además me permite _jugar_ con ellos.

Evidentemente, la primera ventaja de la solución OnDemand es la gestión de las herramientas, que se reduce a la administración de las propias configuraciones y opciones de cara a su uso, y no a su instalación, recursos hardware, etc.<figure id="attachment_215" style="width: 1294px" class="wp-caption alignnone">

[<img class="size-full wp-image-215" alt="Jira OnDemand" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.26.png" width="1294" height="408" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.26.png 1294w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.26-300x94.png 300w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.26-1024x322.png 1024w" sizes="(max-width: 1294px) 100vw, 1294px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.26.png)<figcaption class="wp-caption-text">Jira OnDemand</figcaption></figure> 

## Jira OnDemand

La versión de Jira OnDemand es, como no podía ser de otra forma, la más reciente: Jira 6. Los cambios más importantes que veo en esta versión, y que supongo que no son exclusivos de la distribución OnDemand (en Genexies tenemos la versión 5 en nuestro CPD) son, sobretodo, de ubicación de los menúes de administración, dispuestos ahora de forma mucho más cómoda para el administrador.<figure id="attachment_216" style="width: 554px" class="wp-caption alignnone">

[<img class="size-full wp-image-216" alt="Administrando Jira 6" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.49.png" width="554" height="586" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.49.png 554w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.49-283x300.png 283w" sizes="(max-width: 554px) 100vw, 554px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-15-a-las-21.50.49.png)<figcaption class="wp-caption-text">Administrando Jira 6</figcaption></figure> 

Hay una cosa que me llama la atención, y es el hecho de que no todos los plugins disponibles en el market de Atlassian están habilitados para su instalación en la versión OnDemand. Por ejemplo, no veo el plugin de diagramas Gantt que he usado otras veces.

El motivo es que, efectivamente, los plugins que se pueden instalar en los productos de Atlassian &#8220;for download&#8221; no se pueden instalar en las versiones &#8220;on demand&#8221;&#8230; vaya 🙁

Así que extraigo una conclusión: si quieres un entorno donde poder probar tus propios desarrollos de plugins, OnDemand no parece la solución. Quizá una mezcla de productos OnDemand + Download

## Atlassian SDK

Como inciso a lo anterior no puedo dejar de decir que el [SDK de Atlassian](https://developer.atlassian.com/display/DOCS/Getting+Started "Atlassian SDK") es bastante completo en ese sentido: te permite lanzar instancias de los productos sobre los que depurar tus desarrollos. Todo ello recubierto por la maravillosa azucar de Maven, es decir, que con unos segundos de tecleo en línea de comandos puedes:

  * Crear el proyecto de plugin para el producto que quieras
  * Compilar y lanzar una instancia &#8220;demo&#8221; con el plugin desplegado
  * &#8230;

Así que en este sentido no es necesario disponer de un entorno &#8220;producción&#8221; OnDemand para poder desarrollar tus propios plugins para los productos de Atlassian (nuevamente insisto, yo sólo he probado a crear un plugin para Jira 5, pero creo que se podrá extrapolar esta afirmación a casi todos los demás productos de la compañía).

## Conclusiones

A pesar de lo anterior, sólo la nula administración de sistemas que requiere una solución en la nube como esta (pay-and-play?) y en mi caso, la integración con las cuentas de usuario de Google Apps (lo que evita tener incluso que administrar los usuarios), 10$/mes por producto (para muchos de ellos) con un límite de 10 usuarios, que no está nada mal para un grupo de desarrolladores que _empiezan algo_, no me parece una mala inversión&#8230; ni muy cara&#8230;

50$/mes proporcionan a 10 usuarios lo siguiente, sin dolores de cabeza de administración &#8220;a bajo nivel&#8221;:

  * Gestión de proyectos ágil: Jira+GreenHopper
  * Repositorios Git: BitBucket
  * Integración continua: Bamboo
  * Wiki (gestión documental): Confluence

Ahora me toca sacarle punta a la instalación de Jira OnDemand y a su integración con BitBucket y Bamboo, donde me interesa especialmente la trazabilidad de cambios. Otra cosa es la migración mental que tengo que hacer desde Jenkins a Bamboo.

Continuará&#8230;