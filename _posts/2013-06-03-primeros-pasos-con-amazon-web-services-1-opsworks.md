---
id: 159
title: 'Primeros pasos con Amazon Web Services (1): OpsWorks'
date: 2013-06-03T22:01:36+00:00
author: javier
guid: http://javierjeronimo.es/?p=159
permalink: /2013/06/03/primeros-pasos-con-amazon-web-services-1-opsworks/
image: /wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-03-a-las-22.38.31.png
categories:
  - cloud
tags:
  - aws
  - cloud
  - devops
  - nube
---
Hoy he dado mis primeros pasos con Amazon Web Services y ha sido más facil de lo que esperaba. También es cierto que ha sido gracias a que ahora que el servicio [OpsWorks de AWS](http://aws.amazon.com/es/opsworks/) está disponible en todo el mundo.

OpsWorks es una solución &#8220;devops&#8221; para AWS, es decir, una forma de definir y gestionar de forma automatizada una infraestructura completa basada en AWS, además de todas las capas de software y servicios necesarios para disponer de una aplicación funcionando online.

En mi caso he definido una aplicación PHP básica, para no exceder los límites de la cuenta gratuita de AWS, alojada en un repositorio git de BitBucket, y el resultado ha sido que en cuestión de media hora, la instancia estaba arrancada, con el software inicial desplegado y en ejecución.<figure id="attachment_160" style="width: 957px" class="wp-caption alignnone">

[<img class="size-full wp-image-160" alt="Aplicación PHP en AWS usando OpsWorks" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-03-a-las-22.38.31.png" width="957" height="509" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-03-a-las-22.38.31.png 957w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-03-a-las-22.38.31-300x159.png 300w" sizes="(max-width: 957px) 100vw, 957px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-03-a-las-22.38.31.png)<figcaption class="wp-caption-text">Aplicación PHP en AWS usando OpsWorks</figcaption></figure> 

En el siguiente artículo de esta serie trataré con más profundidad OpsWorks y las opciones de personalización mediante recetas [Chef](http://www.opscode.com/chef/) de los pasos de instalación de las pilas, las capas y demás componentes.