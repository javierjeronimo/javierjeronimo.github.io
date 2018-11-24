---
id: 70
title: Feature switching y los despliegues sólo para valientes
date: 2012-10-31T21:37:05+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=70
permalink: /2012/10/31/feature-switching-y-los-despliegues-solo-para-valientes/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - Programación
tags:
  - cambios
  - despliegues
  - transición
---
_<img class="alignleft" title="Switch with cover, para no meter la pata" src="http://www.lesliewong.us/images/1201/cover.jpg" alt="Switch with cover, para no meter la pata" width="216" height="173" />Feature switching_ es una técnica que permite establecer por configuración las funciones que están activas en un software. Con este estilo de diseño se puede, por ejemplo, introducir funciones en pruebas de forma que si se desactivan no afecten al comportamiento original de la aplicación. Pero como todas las soluciones, _feature switching_ no se puede o debe aplicar siempre.

En Genexies Mobile (GNX) hemos tenido recientemente una demostración de esto, un escenario donde parece que se puede aplicar esta técnica pero también donde, tras un análisis, se comprueba que no. El caso en cuestión es el cambio en la numeración de telefonía móvil que sufrió Ecuador el pasado 30 de septiembre. Simplemente se añadió un dígito más al prefijo, que pasó de ser 593 a 5939.

En GNX ofrecemos un software (SaaS) consistente en portales de venta y descarga de contenidos multimedia para operadores de telefonía móvil. De _marca blanca_ lo llaman. Las implicaciones de un cambio tan fácil de explicar son realmente pocas pero una mala gestión de los cambios puede desembocar en un corte del servicio o un descenso en la facturación.

El cambio en cuestión que tomaré como ejemplo, se reduce a dos escenarios claros:

  1. Reconocer al usuario ya se conecte con la antigua o la nueva numeración.
  2. Enviar peticiones de facturación con el número correcto, dependiendo si ya se ha producido el cambio en el operador o no (¿fechaActual >= 2012/09/30 00:00:00?, en hora local Ecuador)

Una solución que se puede plantear consiste en añadir la funcionalidad y usando _feature switching_ activarla a la hora correspondiente, pero esto requiere programar ese cambio, o hacerlo manualmente. En cambio, si se hacen cambios en el sistema para hacerlo compatible al cambio de numeración en los dos escenarios descritos anteriormente, el proceso de cambio por parte del operador será menos traumático.

Supongamos por ejemplo que el cambio en el operador de telefonía no es instantáneo, es decir, que a partir de una hora determinada comienzan a llegar de forma progresiva peticiones con la nueva numeración. Supongamos que ese proceso de transición dura dos horas. En este caso, _feature switching_ no hubiera sido válido pues algunas peticiones se hubieran tratado de forma incorrecta. La funcionalidad debe contemplar detectar al usuario de las dos formas posibles, es decir, debemos hacer nuestro sistema más robusto ante fallos en los sistemas externos, lo que en este caso se traduce en que recibiremos simultáneamente visitas con la antigua y la nueva numeración.

El segundo caso del escenario planteado parece más claro, desde una hora determinada debemos enviar peticiones de facturación con la nueva numeración, dado que era el requisito. Supongamos ahora que el servicio externo de facturación no gestiona bien el cambio y hasta pasado un tiempo desde la hora límite no admite la nueva numeración. Nosotros habremos cumplido con el requisito, pero la facturación se verá afectada.

Es cierto que en este caso el error es de un sistema externo, pero nuestro sistema se ve directamente afectado. En este caso se podría optar por usar _feature switching_: la funcionalidad de realizar los intentos de cobro con la nueva numeración a partir de una fecha determinada deberá estar activada por defecto pero ser desactivable. Es decir:

  * _Sin el cambio_: sólo se pueden intentar cobros con la antigua numeración.
  * _Con el cambio_: antes de la hora X se hacen cobros con la antigua numeración, y después con la nueva numeración.

¿Por qué? Porque si llegada la hora límite, los cobros empiezan a fallar porque el sistema externo todavía no espera la nueva numeración, ese caso se puede detectar (monitorización mediante un componente específico del sistema, o tratando los ficheros de trazas), y el equipo de soporte puede desactivar la nueva funcionalidad y notificar al proveedor del servicio de cobros. Más tarde, cuando se confirme el correcto funcionamiento, se podrá activar de nuevo la funcionalidad y dejarla activa.

En ambos casos el objetivo es proteger nuestro sistema: información entrante que no cumple con los requisitos, e información saliente que a pesar de cumplir con los requisitos es rechazada por un sistema externo. En el primero no se puede usar _feature switching_, mientras que en el segundo sí. En este ejemplo concreto, pasado el periodo de cambio se podría quitar el comportamiento del _feature switching_ y dejar el definitivo (numeración cambiada con nuevo prefijo).

La idea clave detrás de este ejemplo es que algunos procesos de cambio no son atómicos, tardan un tiempo en completarse, y si puede haber fallos, los habrá. Simplemente hay que estar preparado. No es sencillo implementar un cambio siguiendo esta filosofía, pero permite ser más ágil ante incidencias en este tipo de procesos, y hace que los teléfonos de soporte no suenen los fines de semana, que ya es algo.