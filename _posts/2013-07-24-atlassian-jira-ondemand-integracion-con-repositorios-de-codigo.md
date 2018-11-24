---
id: 231
title: Atlassian Jira OnDemand – Integración con repositorios de código
date: 2013-07-24T21:52:52+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=231
permalink: /2013/07/24/atlassian-jira-ondemand-integracion-con-repositorios-de-codigo/
categories:
  - Cloud
tags:
  - atlassian
  - bitbucket
  - cloud
  - github
  - jira
  - nube
  - repositorio
---
Después de la primera impresión al dar mis primeros pasos con Atlassian Jira OnDemand (tengo experiencia como usuario y administrador en una versión instalada), me encuentro con los primeros problemas, algunos graves y otros no tanto.

Reconozco que en el momento en que escribo esto no sé la madurez que tienen los productos OnDemand de Atlassian (justo ahora creo una pestaña para buscarlo)&#8230;

## Promoción de integración con otras herramientas

Como parte de las pruebas que estoy haciendo con la herramienta, he enlazado el proyecto de pruebas de Jira con dos repositorios de código: uno en BitBucket y otro en GitHub, ambos en sus versiones gratuitas (repositorios privados en BitBucket y públicos en GitHub). Simplemente por probar, no soy partidario de ninguno de los dos, todavía.

Una función muy interesante de Jira, como de casi todas las herramientas de este tipo, es que permite mostrar como parte de las tareas un histórico de cambios en el repositorio relacionados con ellas, lo que proporciona _trazabilidad_ entre los cambios en el código y la motivación de los mismos (un error detectado por una incidencia informada, o un desarrollo debido a una mejora o un proyecto).

Al ser una versión &#8220;en la nube&#8221;, fácilmente ampliable con más servicios de Atlassian, intentan promocionar la contratación del resto de herramientas. Lo que en una instalación local del software se traduciría normalmente en &#8220;no aparece una pestaña&#8221;, aquí se convierte casi en publicidad para ampliar tu versión en la nube con más funcionalidad.

[<img class="alignnone size-full wp-image-232" alt="Captura de pantalla 2013-07-24 a la(s) 22.18.46" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.18.46.png" width="836" height="578" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.18.46.png 836w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.18.46-300x207.png 300w" sizes="(max-width: 836px) 100vw, 836px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.18.46.png)

Para mí no es un problema. Reconozco que los primeros días tras la instalación de Jira en Genexies Mobile me los pasé buscando extensiones gratuitas (o no) que nos sirvieran para el trabajo diario. En Atlassian OnDemand, simplemente te ponen la miel en los labios.

## Enlaces 404: mala promoción

El problema del punto anterior es cuando uno de estos enlaces no lleva a ningún sitio.

[<img class="alignnone size-full wp-image-233" alt="Captura de pantalla 2013-07-24 a la(s) 22.11.47" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.11.47.png" width="586" height="459" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.11.47.png 586w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.11.47-300x234.png 300w" sizes="(max-width: 586px) 100vw, 586px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.11.47.png)

Pero tengo que decir, que a pesar de que el enlace de promoción es incorrecto, desde el panel de administración se puede configurar perfectamente la integración de Jira con BitBucket.

[<img class="alignnone size-full wp-image-237" alt="Captura de pantalla 2013-07-24 a la(s) 22.43.49" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.43.49.png" width="646" height="315" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.43.49.png 646w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.43.49-300x146.png 300w" sizes="(max-width: 646px) 100vw, 646px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.43.49.png)

## Integración con GitHub

Siguiendo en la línea de la integración de herramientas, y para el caso de repositorios GitHub, el problema que me encuentro es que el proceso no parece terminar nunca correctamente.

[<img class="alignnone size-full wp-image-235" alt="Captura de pantalla 2013-07-24 a la(s) 22.30.58" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.30.58.png" width="566" height="363" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.30.58.png 566w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.30.58-300x192.png 300w" sizes="(max-width: 566px) 100vw, 566px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.30.58.png)

A pesar de haber creado credenciales OAuth para Jira desde mi cuenta de GitHub, y aunque después de introducir los datos correctos en este formulario, veo la página de confirmación de GitHub (a continuación), vuelvo al &#8220;home&#8221; de Jira OnDemand&#8230; y ahí termina la historia.

**ACTUALIZADO**: Después de leer la [documentación](https://confluence.atlassian.com/x/kBLSEQ)&#8230;!!!!!!!&#8230; Sólo ha funcionado cuando he añadido una aplicación an GitHub con nombre &#8220;`JIRA DVCS`&#8221; y https en las URLs&#8230; aunque no sé si esto último ha ayudado en algo&#8230;

La ventaja de la integración repositorio <=> Jira es que se puede mediante [comentarios en los commits](https://confluence.atlassian.com/x/pBDSEQ): transitar tareas, comentarlas, añadir horas de trabajo, etc.

[<img class="alignnone size-full wp-image-236" alt="Captura de pantalla 2013-07-24 a la(s) 22.37.48" src="http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.37.48.png" width="834" height="678" srcset="https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.37.48.png 834w, https://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.37.48-300x243.png 300w" sizes="(max-width: 834px) 100vw, 834px" />](http://javierjeronimo.es/wp-content/uploads/2013/07/Captura-de-pantalla-2013-07-24-a-las-22.37.48.png)

## Conclusiones

Como decía al principio del artículo, mi intención era probar la integración de Jira OnDemand con los repositorios que tengo en GitHub y BitBucket. El resultado es una integración perfecta sin necesidad de addons del estilo de FishEye, que por otra parte creo que está en desuso y acabará muriendo&#8230;

Sobre el tema de las promociones de otros productos, sinceramente este modelo de &#8220;nube&#8221; bajo demanda (la contratación) permite que en función de las necesidades y posibilidades económicas se vaya ampliando el servicio contratado, con lo que no está de más disponer de esas &#8220;anclas&#8221; recordatorias de las posibilidades de ampliación del servicio. Siempre y cuando esto no degenere en simples banners de publicidad no hay problema.

De momento el producto cumple con las expectativas que tenía de él después de haber probado Jira 5 en una instalación local.