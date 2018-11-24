---
id: 418
title: Mi primera subida a producción de 6 horas de duración
date: 2016-05-13T18:35:29+00:00
author: javier
guid: http://javierjeronimo.es/?p=418
permalink: /2016/05/13/mi-primera-subida-a-produccion-de-6-horas-de-duracion/
categories:
  - secdevops
tags:
  - automatización
  - despliegues
  - programación
---
Hace unas semanas, cerrábamos en Genexies Mobile la primera fase de un mega-proyecto. Un proyecto de esos de los que hacemos pocos. De meses de desarrollo. Ayer cerrábamos la segunda fase.

Y la puesta en producción, planificada al detalle por el equipo de GNX junto con el cliente, está a la altura del proyecto: seis horas, con parada planificada del servicio de varias horas en la primera fase, y algo menos en la segunda.

El trabajo bien hecho, en forma de planes de pruebas, de plan detallado de puesta en producción, de horas y horas del equipo en pruebas de componentes, de integración, de implementación de simuladores de los servicios remotos&#8230; tuvo su recompensa. Todo salió según lo planeado, prácticamente al 100%. Bien por el equipo.

Mentiría si dijera que todo funcionó. Hubo pequeños detalles, pero quedaron resueltos al día siguiente. Y casi todos estaban del &#8220;otro lado&#8221; (aunque la facturación nos afecta a todos 😉

Yo estuve más como un apoyo moral al equipo, hice acto de presencia por si el proceso se desmadraba y alguien debía tomar la decisión (o más bien ayudar a tomarla, porque el equipo es bastante autónomo y responsable en ese sentido). La suerte es que en la primera migración había partido, así que en los pasos del proceso donde debíamos esperar a que otro hiciera su trabajo en remoto, pues nos acompañaron las pizzas y los goles del Atleti en el proyector. Llegué a casa a las 00:40, pero para ser la primera vez en mi vida que tengo que formar parte de una puesta en producción de este tipo, no ha estado mal como experiencia&#8230; Tampoco hay que repetirla todos los meses, que no hace falta&#8230;

¿Qué conclusiones saco del proceso? Entre otras estas siguientes:

  * Si has hecho el trabajo, si llevas los deberes hechos, no habrá muchas sorpresas.
  * Siempre hay sorpresas.
  * Pocas veces te encontrarás con procesos sin marcha atrás, sin plan B. Estate alerta si te obligan a que sea así.
  * Automatiza todo lo que puedas, evita los pasos manuales absurdos: subir un fichero, comprobar la integridad de un dato, etc. No hacen más que aumentar el tiempo de migración.
  * Haz una ejecución &#8220;en seco&#8221; de la migración. Siempre se te puede haber olvidado algo del proceso. Automatízalo.
  * Comunicación constante con el cliente durante el proceso. La información tiene que fluir. Los silencios son peligrosos.
  * Ten bien definidos y acordados los criterios de aceptación: hemos terminado la migración y ha salido exitosa.