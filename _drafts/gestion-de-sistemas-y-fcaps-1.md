---
id: 24
title: Gestión de sistemas y FCAPS (1)
date: 2013-05-31T14:37:02+00:00
author: javier
guid: http://javierjeronimo.es/?p=24
permalink: /2013/05/31/gestion-de-sistemas-y-fcaps-1/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - IT
tags:
  - gestión
  - sistemas
---
La [gestión de sistemas](http://en.wikipedia.org/wiki/Systems_management "Wikipedia: Systems management") es esencial cuando tu negocio se basa en internet, como es caso de Genexies Mobile S.L. (GNX) y su sistema de distribución de contenidos multimedia para dispositivos móviles.

En GNX tenemos una cantidad _decente_ de sistemas, no voy a decir _gran_ cantidad porque seguro que existen otras empresas que tienen muchos más sistemas entre servidores y demás aparatos. Pero nuestros sistemas son los suficientes como para poder causarnos dolores de cabeza en su gestión.

La gestión de sistemas implica tener bajo control el inventario de máquinas físicas y virtuales, las versiones de software instaladas, tanto de los sistemas operativos como de los servicios (web, correo, cortafuegos&#8230;), la monitorización, etc.

[FCAPS](http://es.wikipedia.org/wiki/FCAPS "FCAPS en Wikipedia") son unas siglas en inglés que se refieren a los conceptos _fallos, configuración, contabilidad, rendimiento_ y _seguridad._ Son los pilares de la gestión de redes:

  * ¿qué tenemos?
  * ¿qué falla?
  * ¿qué cantidades de datos/peticiones gestionamos?
  * ¿qué uso se hace de nuestra infraestructura?
  * ¿cómo de segura es?

Ahora bien, estos mismos puntos pueden aplicarse no sólo a la infraestructura de red, si no a toda la infraestructura de sistemas, incluyendo los servidores, e incluso a los servicios alojados en esos sistemas. La cuestión es ¿cómo documentamos las respuestas a las preguntas anteriores, o mejor, cómo recopilamos esta información?

La primera respuesta es sencilla si se dispone de sistemas de pequeña escala o no somos el departamento de sistemas: _preguntar al departamento de sistemas_. ¿Qué hacer en cualquier otro caso? ¿Cómo documenta esta información el propio departamento de sistemas?

Mi opnión es que la documentación se queda obsoleta en el momento en que se pulsa el botón _Guardar_. No queda más que recurrir a sistemas automatizados que recopilen la información sobre los sistemas que disponemos en cada organización. Y estos sistemas son el tema de una serie de artículos que comienzo con este y donde iré punto por punto analizando por qué es necesario responder a cada pregunta y qué se consigue no haciéndolo.