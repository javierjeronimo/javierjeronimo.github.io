---
id: 164
title: 'Entorno de desarrollo completo para Android (1): Eclipse'
date: 2013-06-30T12:00:52+00:00
author: javier
guid: http://javierjeronimo.es/?p=164
permalink: /2013/06/30/entorno-de-desarrollo-completo-para-android-1-eclipse/
categories:
  - programacion
tags:
  - android
  - eclipse
  - entorno desarrollo
  - productividad
  - programación
---
Hay una cosa que me encanta cuando estoy en un proyecto y es tener un entorno de desarrollo completo y útil. Sin desvariar, que aunque me guste tener todas las herramientas de trabajo en el mismo lugar, tiene que ser útil.

Esta semana pasada he tenido que entrar hasta las tripas de un proyecto de aplicación móvil para Android, y lo primero que he hecho ha sido montar el entorno lo mejor posible.

Mi opinión es que el tiempo que se invierte en montar un entorno de desarrollo completo, que yo más bien llamaría _entorno de desarrollo decente_, es un tiempo que se ahorra en el futuro, y mucho. Primero porque si otras personas tienen que entrar en el proyecto, no van a tener que dedicar mucho esfuerzo a montar el entorno. Y segundo, porque gestionar determinadas cosas de forma manual lleva a un caos antes o después: dependencias, pruebas, integraciones.

Una aplicación Android es una aplicación JAVA, así que el primer objetivo es montar un proyecto pensando en la interfaz más extendida: Eclipse.

Ya tenemos un punto de comienzo y será el que trate en este primer artículo.

# Proyecto Android en Eclipse

Como nota introductoria quiero dejar claro que este artículo supone que ya tienes un entorno de desarrollo con Eclipse que usas habitualmente. Así, en el artículo reflejo cómo actualizar el entorno para añadir soporte a los procesos propios del desarrollo Android.

Sin embargo, si lo que quieres es montar un entorno completo desde el principio, existe una distribución de Google para esto mismo, que ya incluye una versión de Eclipse con los plugins instalados: <http://developer.android.com/sdk/index.html>

No obstante, este artículo quiero que sirva de justificación sobre la necesidad de un entorno de desarrollo completo orientado a Android, y la verdad, tener una instalación de Eclipse con absolutamente todos los plugins del universo instalados, acaba siendo un problema a la larga.

## Android Development Tools en Eclipse

La instalación de los plugins de Android para Eclipse es sencilla y se pueden usar varias alternativas. Para mí, _Eclipse Marketplace_ siempre es la primera opción:<figure id="attachment_184" style="width: 509px" class="wp-caption alignnone">

[<img class="size-full wp-image-184" alt="Plugin ADT para Eclipse" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.03.29.png" width="509" height="131" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.03.29.png 509w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.03.29-300x77.png 300w" sizes="(max-width: 509px) 100vw, 509px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.03.29.png)<figcaption class="wp-caption-text">Plugin ADT para Eclipse</figcaption></figure> 

Aunque si no sale realizando una búsqueda, siempre se puede instalar desde la otra interfaz, añadiendo un nuevo &#8220;_update site_&#8220;: https://dl-ssl.google.com/android/eclipse/

Y la documentación de Google, que mejora por momentos:

  * <http://developer.android.com/tools/sdk/eclipse-adt.html>
  * [http://developer.android.com/sdk/installing/installing-adt.html](https://dl-ssl.google.com/android/eclipse/)

Algo que no conocía hasta este momento, cuando he redactado este artículo y he buscado las direcciones de referencia, es otro plugin para gestionar traducciones en las aplicaciones. Como siempre, añadiendo un nuevo &#8220;_update site_&#8221; a Eclipse: https://dl.google.com/alt/

## SDK de Android

El plugin de Android se apoya en el SDK que Google distribuye y que contiene las bibliotecas, las herramientas de gestión, etc, así que es fundamental tenerlo instalado.

<http://developer.android.com/sdk/installing/index.html>

Después de instalarlo y configurarlo en las opciones de Eclipse no queda más que empezar a usarlo:<figure id="attachment_190" style="width: 806px" class="wp-caption alignnone">

[<img class="size-full wp-image-190" alt="Configuración SDK Android en plugin Eclipse" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.41.20.png" width="806" height="204" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.41.20.png 806w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.41.20-300x75.png 300w" sizes="(max-width: 806px) 100vw, 806px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.41.20.png)<figcaption class="wp-caption-text">Configuración SDK Android en plugin Eclipse</figcaption></figure> 

## ¿Y esto para qué?

El objetivo que tengo es claro, disponer en mi entorno de desarrollo, y sin salir de él, es decir, disponer en la aplicación en la que pasaré la mayor parte de las horas durante el proyecto, de las herramientas de gestión de Android:

  * Plataformas y versiones del API instaladas.
  * Emuladores.
  * Herramientas de depuración.

Y del _azúcar_ que viene de regalo con el plugin:

  * Plantillas para proyectos y nuevos ficheros: actividades, fragmentos, recursos&#8230;
  * Funcionalidad de &#8220;completar código&#8221;, &#8220;ir a definición&#8221; para los recursos de Android, etc.
  * Perspectivas y vistas propias de Android que completan las ya existentes en Eclipse.

Por ejemplo, disponer de un botón en la propia interfaz de Eclipse para la gestión de los distintos SDKs instalados (Android SDK Manager):<figure id="attachment_185" style="width: 740px" class="wp-caption alignnone">

[<img class=" wp-image-185" alt="Android SDK Manager desde Eclipse" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.16.33.png" width="740" height="743" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.16.33.png 740w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.16.33-150x150.png 150w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.16.33-298x300.png 298w" sizes="(max-width: 740px) 100vw, 740px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.16.33.png)<figcaption class="wp-caption-text">Android SDK Manager desde Eclipse</figcaption></figure> 

Disponer de los asistentes de nuevos proyectos Android (en la siguiente captura también salen proyectos GAE porque tengo otros plugins de Google instalados):<figure id="attachment_186" style="width: 526px" class="wp-caption alignnone">

[<img class="size-full wp-image-186" alt="Asistentes Eclipse proporcionados por plugin ADT" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.18.12.png" width="526" height="501" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.18.12.png 526w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.18.12-300x285.png 300w" sizes="(max-width: 526px) 100vw, 526px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.18.12.png)<figcaption class="wp-caption-text">Asistentes Eclipse proporcionados por plugin ADT</figcaption></figure> 

Y para terminar la gestión de los emuladores que se ejecutan en el ordenador de desarrollo, por una parte teniendo la posibilidad de lanzarlos desde Eclipse, pero sobretodo por las posibilidades de depuración de las aplicaciones que se ejecutan en ellos:<figure id="attachment_187" style="width: 701px" class="wp-caption alignnone">

[<img class=" wp-image-187" alt="Android Virtual Device Manager" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.25.03.png" width="701" height="501" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.25.03.png 701w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.25.03-300x214.png 300w" sizes="(max-width: 701px) 100vw, 701px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.25.03.png)<figcaption class="wp-caption-text">Android Virtual Device Manager</figcaption></figure> <figure id="attachment_188" style="width: 1312px" class="wp-caption alignnone">[<img class="size-full wp-image-188" alt="Emulador Android lanzado desde Eclipse" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.26.57.png" width="1312" height="692" srcset="https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.26.57.png 1312w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.26.57-300x158.png 300w, https://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.26.57-1024x540.png 1024w" sizes="(max-width: 1312px) 100vw, 1312px" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-20.26.57.png)<figcaption class="wp-caption-text">Depuración del emulador Android lanzado desde Eclipse</figcaption></figure> 

## Terminando

Este primer artículo no es más que una introducción a las herramientas que Google ofrece para los desarrollos sobre Android, y cómo se pueden integrar, y de hecho se integran, en el entorno de desarrollo Eclipse.

Muchos nos sentimos cómodos con la línea de comandos, y sinceramente, las herramientas del SDK de Android son bastante buenas en ese sentido: ayuda, parámetros para configurar todo, etc.

Sin embargo, un buen entorno de desarrollo donde todo esté a mano, visible, bajo la misma interfaz, me parece más eficiente. Pero esto sólo es el primer paso en la creación del entorno de desarrollo, y es el paso donde más entran los gustos de cada programador. Yo he elegido Eclipse porque es la herramienta que uso, pero podría haber sido cualquier otra. Incluso un editor, menos IDE, más editor, y la línea de comandos con las herramientas del SDK en el PATH.

En las siguientes entregas de esta serie entraré en profundidad en partes del entorno de desarrollo más importantes: la definición del proyecto software, sus dependencias, y el ciclo de vida completo del mismo, desde la instalación del entorno de desarrollo (clone-and-play), las pruebas y la integración final en producción&#8230;