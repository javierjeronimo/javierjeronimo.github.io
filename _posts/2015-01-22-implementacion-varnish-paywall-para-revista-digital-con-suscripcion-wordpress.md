---
id: 314
title: Implementación Varnish paywall para revista digital con suscripción (WordPress)
date: 2015-01-22T18:30:18+00:00
author: javier
guid: http://javierjeronimo.es/?p=314
permalink: /2015/01/22/implementacion-varnish-paywall-para-revista-digital-con-suscripcion-wordpress/
categories:
  - Arquitectura
tags:
  - api
  - arquitectura
  - cache
  - genexies mobile
  - movistar
  - varnish
  - wordpress
---
EDITADO: corregida errata. Gracias a [@JesLopCru](https://twitter.com/JesLopCru "@JesLopCru"){.anti-aede-checked}

[Genexies Mobile](http://www.genexies.com){.anti-aede-checked} ofrece desde hace pocas semanas un nuevo servicio de suscripción bajo el paraguas de Movistar España. [Fashion BIP](http://moda.genexies.net "Fashion BIP - Portal de noticias de moda de Movistar / Genexies Mobile"){.anti-aede-checked} es una revista digital en forma de portal web, diseñada específicamente para móviles y tabletas, de suscripción semanal, y con contenidos relacionados con la moda, la belleza y las noticias de actualidad del sector.

En este artículo voy a detallar como hemos creado el servicio usando una implementación propia de [Varnish](https://www.varnish-cache.org "Varnish Cache"){.anti-aede-checked} [Paywall](https://www.varnish-software.com/product/varnish-paywall "Varnish PayWall"){.anti-aede-checked}, [WordPress](https://es.wordpress.org "Wordpress"){.anti-aede-checked} (y algunos plugins propios), y los servicios propios de Genexies que nos permiten integrarnos con los operadores de telefonía móvil, en este caso Movistar España.

<!--more-->

## Objetivos de la arquitectura del servicio

El desarrollo de este servicio tenía unos objetivos principales desde el punto de vista técnico:

  1. <span style="letter-spacing: 0.05em;">Integrarse con los servicios de <em>Genexies</em> a través de su API privada, con el fin de evitar los detalles de la integración con los sistemas de identificación y cobro de <em>Movistar España</em>.</span>
  2. <span style="letter-spacing: 0.05em;">Proporcionar al visitante y suscriptor una excelente experiencia de uso en tiempo de acceso al contenido.</span>
  3. <span style="letter-spacing: 0.05em;">Evitar en la medida de lo posible el desarrollo de soluciones a medida sobre la plataforma <em>WordPress</em>, usando una distribución estándar con los mínimos <em>plugins</em> necesarios.</span>

El primer objetivo es claro: no reinventar la rueda. _Genexies Mobile_ ofrece servicios de descarga de contenido multimedia bajo la marca de operadores de telefonía móvil, por lo que está integrado con sus sistemas de identificación y cobro. Además, dispone de un API privada donde expone estos servicios, y evidentemente en este nuevo proyecto debíamos usar este API para la integración con _Movistar España_.

El segundo objetivo no tendría ni que mencionarlo. Un _sistema de cache_, _acelerador web_, o como se quiera llamar, es obligado en todo proyecto web que espera un crecimiento y un gran número de visitas. El punto clave aquí es que la solución está construida alrededor de la cache (_cache frontal_ la hemos llamado).

Todo gira entorno a la _cache frontal_, y me refiero a que los requisitos del resto de componentes dependen directamente de lo siguiente: la cache frontal existe en este servicio, va a existir siempre en este servicio, y son el resto de componentes los que se deben adaptar a esta situación. Podríamos llamarlo &#8220;_cache driven development_&#8220;.

El tercer objetivo es fruto de (malas) experiencias pasadas haciendo desarrollos a medida, sin control (todo hay que decirlo), sobre otros _CMS_ (_Drupal_, etc, etc). En concreto, queríamos evitar a toda costa la implementación de cualquier lógica de cobro o identificación dentro de _WordPress_.

## Arquitectura del servicio

Son cuatro los componentes involucrados en este servicio:

  * <span style="letter-spacing: 0.05em;"><strong>Cache frontal</strong>: solución basada en <em>Varnish Cache</em> que implementa:</span> 
      * <span style="letter-spacing: 0.05em;"><em>Cache web</em>: típica cache <em>Varnish</em> de recursos estáticos (imágenes, scripts, hojas de estilo, etc) y de los propios recursos del contenido (páginas y artículos de WordPress)</span>
      * <span style="letter-spacing: 0.05em;"><em>Barrera de cobros</em> (<em>pay-wall</em>): dado que el contenido de suscripción se puede encontrar en la cache, esta incorpora una lógica de barrera de cobros, permitiendo el acceso solo a los suscriptores.</span>
  * <span style="letter-spacing: 0.05em;"><strong>CMS</strong>: contiene el portal de contenidos y la interfaz de autoría de los mismos (<em>WordPress</em>).</span>
  * <span style="letter-spacing: 0.05em;"><strong>API Genexies</strong>: intermediaria en la identificación y cobro a los visitantes clientes de Movistar.</span>
  * <span style="letter-spacing: 0.05em;"><strong>Servicios de Movistar</strong> de identificación y cobro.</span>

## Caso de uso típico del servicio

Para explicar como se integran los componentes de la arquitectura, y sin entrar en detalles, muestro aquí el típico caso de uso: un cliente de Movistar accede al portal usando una conexión wifi, se identifica en _Movistar-ID_, se suscribe a la revista digital y finalmente accede al contenido.

[<img class="alignnone size-full wp-image-346" src="http://javierjeronimo.es/wp-content/uploads/2014/11/Arquitectura-de-acceso-a-un-recurso-del-portal-Fashion-BIP-moda.genexies.net_1.png" alt="Arquitectura de acceso a un recurso del portal Fashion BIP (moda.genexies.net)" width="1472" height="1404" srcset="https://javierjeronimo.es/wp-content/uploads/2014/11/Arquitectura-de-acceso-a-un-recurso-del-portal-Fashion-BIP-moda.genexies.net_1.png 1472w, https://javierjeronimo.es/wp-content/uploads/2014/11/Arquitectura-de-acceso-a-un-recurso-del-portal-Fashion-BIP-moda.genexies.net_1-300x286.png 300w, https://javierjeronimo.es/wp-content/uploads/2014/11/Arquitectura-de-acceso-a-un-recurso-del-portal-Fashion-BIP-moda.genexies.net_1-1024x976.png 1024w" sizes="(max-width: 1472px) 100vw, 1472px" />](http://javierjeronimo.es/wp-content/uploads/2014/11/Arquitectura-de-acceso-a-un-recurso-del-portal-Fashion-BIP-moda.genexies.net_1.png){.anti-aede-checked}

NOTA: este diagrama lo he creado con la herramienta online [http://plantuml.sourceforge.net/](http://plantuml.sourceforge.net/ "PlantUML"){.anti-aede-checked} que me ha parecido muy útil (me ha dejado de funcionar VisualParadigm for UML en el MacBook desde que actualicé a Mavericks&#8230;). Si sientes curiosidad, el código que genera el diagrama es:

<pre>title Arquitectura de acceso a un recurso del portal Fashion BIP (moda.genexies.net)
hide footbox
autonumber

actor Visitante as V &lt;&lt;Navegador móvil&gt;&gt;
participant CacheFrontal as C &lt;&lt;Servidor www&gt;&gt;
participant WordPress as W &lt;&lt;Servidor www&gt;&gt;
participant ApiGenexies as A &lt;&lt;Servidor www&gt;&gt;
participant MovistarID as M &lt;&lt;Servidor www&gt;&gt;
participant "Servicio web cobros Movistar" as SPG &lt;&lt;WS&gt;&gt;

activate V
V-&gt;C: Accede a recurso en http://moda.genexies.net/...
activate C
C-&gt;C: Obtención de cache e identificación del visitante
activate C
C-&gt;W: [no en cache] Obtener recurso desde servidor
activate W
deactivate W
C--&gt;V: HTTP 302 redirección a WEB-SSO
note right
El visitante es anónimo, ej:
 - sin cookie de sesión
 - sin navegación por red datos de Movistar
 - etc
end note
deactivate C
deactivate C

== Inicio del proceso de identificación WEB-SSO ==

V-&gt;A: Inicia proceso WEB-SSO
activate A
deactivate A

V-&gt;M: Identificación en sistemas de Movistar
activate M
note right
Dependiendo del entorno:
 - formulario
 - recordatorio contraseña por SMS
 - etc.
end note
deactivate M

V-&gt;A: Termina proceso WEB-SSO
activate A
deactivate A
== Fin del proceso de identificación WEB-SSO ==

V-&gt;C: Accede a recurso en http://moda.genexies.net/...
activate C
C-&gt;C: Obtención de cache e identificación del visitante
activate C
C-&gt;W: [no en cache] Obtener recurso desde servidor
activate W
deactivate W
deactivate C
C-&gt;C: ¿Es el visitante suscriptor?
activate C
C-&gt;V: HTTP 302 Redirección a carta de pago
note right
Visitante es cliente de Movistar identificado pero no es suscriptor
end note
deactivate C
deactivate C

== Carta de pago y cobro online ==

V-&gt;C: Accede a carta de pago
activate C
deactivate C

V-&gt;A: Confirma pago usando enlace de API Genexies cobros
activate A
A-&gt;SPG: Cobro síncrono
activate SPG
deactivate SPG
A--&gt;V: Redirección a recurso original
deactivate A

== Acceso definitivo a recurso protegido ==

V-&gt;C: Accede a recurso en http://moda.genexies.net/...
activate C
C-&gt;C: Obtención de cache e identificación del visitante
activate C
C-&gt;W: [no en cache] Obtener recurso desde servidor
activate W
deactivate W
deactivate C
C-&gt;C: Comprobación suscripción
activate C
deactivate C
C--&gt;V: HTTP 200 OK + recurso original bajo suscripción
deactivate C</pre>

## Cuestiones que se plantean en la arquitectura

Estas son unas cuestiones interesantes que se plantean al diseñar el servicio con la arquitectura y restricciones explicadas anteriormente.

### _Barrera de cobros_ VS _acelerador web_

Si la cache frontal contiene la lógica correspondiente a la &#8220;barrera de cobros&#8221;, y por tanto debe acceder a cierta información de cada visitante (¿es el visitante un cliente de Movistar?, ¿tiene una suscripción activa en el servicio?)&#8230;¿cómo es esto compatible con una cache web? ¿y cómo se implementa con _Varnish_?

El punto clave aquí es el siguiente: de cada visitante, que está identificado por una cookie de sesión (y otros mecanismos que no entro a detallar), la cache frontal sólo conoce el ID de sesión, pero mediante llamadas a un API puede obtener toda la información que necesita.

De esta forma, la cache frontal tiene toda la información necesaria para permitir o no a cada visitante el acceso al contenido, sin producir este acceso a dicha información demasiado tiempo extra en la entrega del contenido al visitante, ya que las llamadas a esta API REST también se cachean en la propia cache frontal.

### _Lógica de negocio en Varnish_ VS _lógica de negocio en WordPress_

Si al final tienes que implementar la lógica de negocio, es decir, las restricciones de acceso al contenido de suscripción, la lógica de identificación, la de cobro, etc, ¿qué más da implementarla en _WordPress_ que en _Varnish_?, además, WordPress es PHP y Varnish [pseudo-C](https://www.varnish-cache.org/trac/wiki/VCL "Varnish Configuration Language"){.anti-aede-checked}&#8230; ¿no será mejor hacer todo en WordPress directamente?

Vamos a repasar punto por punto:

  * Tenemos un API que sabe cómo identificar y cobrar a clientes de Movistar España. Simplemente redirigimos al visitante a ese API web (escenarios (_web-sso_ y _web-billing_ los hemos llamado), y cuando vuelve ya es un cliente de Movistar conocido y/o suscrito al servicio. Delegamos en el API de Genexies para esa lógica de identificación y cobro. Ni a favor, ni en contra: +1 Varnish, +1 WordPress
  * Tenemos un API REST para llamadas servidor-servidor, cacheable, y que por tanto no supone prácticamente ninguna sobrecarga en el tiempo de procesamiento de cada visita. Varnish o WordPress obtendrían de ese API la información de estado de suscripción de cada visitante cliente de Movistar. Ni a favor, ni en contra: +1 Varnish, +1 WordPress.
  * _Cache-driven-development_. Lo voy a justificar al revés. Si WordPress y sólo WordPress tiene la lógica de la barrera de cobros, todo el tráfico debe llegar a WordPress. Si todo el tráfico debe llegar a WordPress, no se puede tener una cache externa a WordPress para el contenido de suscripción. Ahora habría que entrar a valorar los sistemas de cache integrados en WordPress y los externos (Varnish, CDN, etc).
  * En Genexies ya tenemos una instalación base de Varnish, sirviendo cada vez más portales de los distintos servicios que ofrece la empresa, con bastante código propio de las integraciones que tenemos con cada uno de los operadores, con un porcentaje altísimo de cobertura del código VCL de Varnish en las pruebas, con el ciclo de vida del proyecto automatizado al completo (infraestructura, pruebas, despliegue), con un foco importantísimo puesto en esta herramienta para crear la cache frontal de los servicios de Genexies&#8230; Esto son razones muy personales de nuestra empresa, pero justifican claramente un +1 para Varnish.

Las decisiones de diseño, y la arquitectura es diseño, dependen mucho de cada circunstancia. Quizá en una empresa donde WordPress sea el centro de los desarrollos, se podría haber implementado la misma arquitectura pero centrada en WordPress, usando sus mecanismos de cache para los contenidos de suscripción (artículos), y dejando a caches externas (CDNs) los demás recursos. Hubiera sido una solución igualmente válida.

## Conclusiones

El servicio está en funcionamiento desde hace varias semanas, y salvo el &#8220;ruido&#8221; inicial de la puesta en producción, los sistemas parecen estar funcionando correctamente, la suscripción está funcionando, y ya estamos pensando en las siguientes mejoras del servicio (mejoras para aumentar la conversión, las altas.. que es de lo que al final va esto, de vender).

Es importante tener en consideración que tanto el API de Genexies y sus integraciones con los servicios de Movistar, como la propia cache frontal ya estaban en producción usándose en otros servicios de Genexies. No obstante, en esos casos hemos tenido que realizar alguna adaptación y pruebas de integración con los nuevos componentes.

En cuanto al desarrollo de la solución propia de _pay-wall_ en Varnish, y aunque se ha hecho desde cero para este servicio, se puede usar como base para otros que tengan este modelo de suscripción _all-you-can-eat_ (AUCE).

De hecho, en los próximos meses el servicio de suscripción evolucionará, y la solución _pay-wall_ que hemos implementado en Varnish se refinará para adaptarse y permitir otros modelos de negocio distintos al actual AUCE. En cualquier caso, la arquitectura básica de este servicio _pay-wall_ seguirá siendo la misma.