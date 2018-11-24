---
id: 162
title: 'Atlassian Jira &#8211; Creación de plugin (1): Integración continua'
date: 2013-06-05T20:48:32+00:00
author: javier
guid: http://javierjeronimo.es/?p=162
permalink: /2013/06/05/atlassian-jira-creacion-de-plugin-1-integracion-continua/
categories:
  - programacion
tags:
  - atlassian
  - continua
  - integración
  - jenkins
  - jira
  - plugin
  - proceso
---
Jira es una herramienta de Atlassian que sirve para muchas cosas -vaya forma de empezar el artículo pensarás- pero que está destinada principalmente a la gestión de proyectos software. Con esto me refiero a que es su principal uso, donde mejor encaja si se ven las extensiones que tiene disponibles y cómo se integra con otros productos de la compañía relacionados con el desarrollo software.

Una de las cosas que siempre intento es automatizar todos los procesos mecánicos, repetitivos, propensos a errores (_error prone_ que se dice en inglés). El proceso de integración de cambios software es bastante repetitivo y gasta tiempo.

En Genexies Mobile usamos Jira como herramienta de gestión de nuestro trabajo diario en el departamento de tecnología, tanto de las incidencias como de los nuevos proyectos. Todo gira entorno a Jira:

  * Peticiones del departamento comercial para preparación de demostraciones del producto.
  * Peticiones de negocio de nuevas funciones.
  * Gestión de incidencias.
  * Votaciones para talleres internos 😉

Todos los procesos relacionados con el desarrollo de software pasan por Jira, reducidos a su mínima expresión para que sean útiles. Quiero decir que tampoco estamos obsesionados con crear flujos ni tipos de tareas propios para todos nuestros procesos internos, pero sí nos apoyamos los suficiente en la herramienta como para que sea un pilar fundamental del departamento, sin llegar a lastrar el trabajo diario con excesiva _burocracia_.

Dentro de todos los procesos en el desarrollo de software, la integración de cambios para su puesta en producción es el proceso en el que me centro en esta serie de artículos. Un proceso que puede ser algo tan sencillo como:

  1. Notificar la finalización de un trabajo de desarrollo. Bajando a tierra como dice Marisa, nuestra facilitadora (aka scrum master, gestora de proceso, persona que ayuda a los desarrolladores en el día a día, en los bloqueos, en levantar la cabeza del teclado y entender a negocio&#8230;): _subir los cambios a una rama en el repositorio_.
  2. Integrar los cambios en una rama de integración.
  3. Pasar una pruebas de regresión. Bajando a tierra: _no has roto nada para lo que tenemos pruebas_.
  4. Llevar la rama de integración a la línea base del proyecto.
  5. Etiquetar la revisión de la rama de integración.
  6. Desplegar en producción.
  7. Comentar, resolver, etiquetar la tarea en Jira que representa el desarrollo integrado.

Y todos los pasos anteriores con sus correspondientes _on error goto_:

  * Devolver la tarea de Jira al desarrollador con comentarios sobre los problemas encontrados.

Y me planteo una cuestión: dado que tenemos un entorno de integración continua, por qué no hacer que la tarea que representa el proceso esté ligada a los pasos automáticos del proceso.

No voy a entrar a valorar que teniendo un entorno de integración continua de verdad, donde tras cada cambio se lanzan el proceso de pruebas y, por tanto, el desarrollador recibe la notificación &#8220;ha roto algo&#8221; mientras está trabajando, y no al final cuando &#8220;transita la tarea de Jira&#8221;.

El objetivo es que esa tarea de Jira que usamos formalmente para conocer el punto del proceso donde se encuentra un desarrollo, sirva, mediante acciones automáticas, para realizar las tareas repetitivas, automáticas, sencillas, y que no deba estar una persona realizándolas.

De esta forma, en un caso ideal, además de existir el entorno de integración continua, tendríamos una automatización que fusionaría las pruebas lanzadas desde Jenkins (por nombrar el que tenemos nosotros) con el estado de la tarea que representa el proceso. El encargado del proceso de integración podría dedicar entonces su tiempo a otras cosas más importantes que mezclar ramas, etiquetar y lanzar trabajos de Jenkins.

Para terminar esta larga introducción, llego al objetivo de esta serie de artículos: crear un plugin de Jira que permita, al efectuar una transición en un tipo de tarea concreta (en nuestro caso Release request), lanzar un trabajo de Jenkins concreto (dependiente del componente software que se está modificando), sobre una rama concreta.

Toda la información necesaria estará en la tarea Jira Release request:

  * Qué componente software se está modificando y se debe probar. Esto determinará el trabajo Jenkins a lanzar.
  * Qué rama del repositorio contiene los cambios. Esto determinará los parámetros que habrá que pasar al trabajo de Jenkins.

Así, mediante un flujo personalizado, que ya tenemos, una acción de transición implementada por nosotros, y una configuración determinada del trabajo de Jenkins podremos lanzar pruebas de forma automática sobre una rama de desarrollo y que los resultados de estas pruebas se incorporen a la propia tarea de Jira.

En la próxima entrega: preparación del entorno de desarrollo para crear un plugin JAVA para Jira.
