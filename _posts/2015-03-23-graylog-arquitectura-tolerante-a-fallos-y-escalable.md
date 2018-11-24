---
id: 305
title: 'Graylog &#8211; Arquitectura tolerante a fallos y escalable'
date: 2015-03-23T20:11:32+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=305
permalink: /2015/03/23/graylog-arquitectura-tolerante-a-fallos-y-escalable/
image: /wp-content/uploads/2014/10/about_logo.png
categories:
  - Arquitectura
tags:
  - arquitectura
  - elastic
  - elasticsearch
  - graylog
  - graylog2
  - kpi
  - logs
  - mongodb
  - rabbitmq
  - trazas
---
_Graylog_ es una solución para el almacenamiento centralizado de logs que además permite realizar consultas sobre los datos, crear tablones con los resultados de las mismas, alarmas sobre la presencia o ausencia de datos, etc.

Es una solución util para tener todos los logs de una arquitectura compleja en un mismo sitio y no repartidos por decenas de servidores y rutas dentro de los mismos. Además, dado que todas las trazas acaban almacenadas en el mismo lugar y es sencillo navegar por ellas, es muy fácil aumentar la calidad de estas revisándolas (&#8230; buscar una traza para un error en producción y descubrir que, oh sorpresa&#8230; no existe esa traza&#8230; al ingeniero no le pareció importante este caso de error&#8230; #fail)

<!--more-->

En [Genexies Mobile](http://www.genexies.com "Genexies Mobile"){.anti-aede-checked} estamos implantando esta solución para acabar con el ritmo actual de generación de varios gigabytes semanales de trazas dispersas por los servidores. Los objetivos son varios:

  * Almacenar las trazas en lugar central.
  * Aumentar la calidad de las trazas: niveles de trazas, eventos trazados, métricas de negocio, etc.
  * Reducir el volumen de trazas en disco que generamos a diario en los servidores.
  * Facilitar las labores de soporte a incidencias mediante una interfaz sencilla web de consulta de las trazas: filtros, gráficas, segmentación de los resultados por atributos, exportación de los datos (ahhhh bendito [formato CSV](http://tools.ietf.org/html/rfc4180 "Common Format and MIME Type for Comma-Separated Values (CSV) Files"){.anti-aede-checked}&#8230;)
  * Disponer de tablones de mando con métricas extraídas directamente de las trazas.

La forma en la que lo estamos consiguiendo la explico en este artículo.

# Diferentes arquitecturas para la integración de graylog

_[Graylog](http://www.graylog2.org "Graylog2 - Open Source Log Management"){.anti-aede-checked}_ permite usar distintos modos de integración en una arquitectura concreta y después de varias pruebas hemos escogido la más compleja de ellas, que además de ser la más tolerante a fallos (bien), es la única que nos ha funcionado en nuestro caso (facepalm). No obstante, voy a dar un repaso por cada una de ellas.

A modo de introducción, estos son los componentes de la arquitectura de _Graylog_ y otros conceptos importantes:

  * _graylog-server_: el núcleo de G_raylog2_. Procesa los mensajes y los guarda. 
      * _graylog-radio_: tipo de servidor más ligero, con funciones reducidas. En el último tipo de arquitectura describiré este componente.
      * _Entrada_ (&#8220;input&#8221;): punto de ingesta de datos, es decir de trazas, al sistema. Por ejemplo: protocolo GELF (propio _Graylog2_) por UDP, TCP, HTTP; syslog, etc.
  * _graylog-web_: interfaz de consulta y configuración del cluster de _Graylog_.
  * _Elastic_: sistema de almacenamiento de trazas.
  * _MongoDB_: base de datos de configuración.

## Arquitectura 1: servidor central graylog2-server

Esta arquitectura es la más básica: un único servidor central ejecutando el servicio _graylog-server_ recibe las trazas a través de sus entradas (&#8220;inputs&#8221; en la terminología _Graylog)_ y después de procesarlas las guarda en un servidor _Elastic_.

También dispone de la interfaz web para la configuración del sistema y la gestión de las trazas.<figure style="width: 798px" class="wp-caption aligncenter">

[<img src="http://docs.graylog.org/en/1.0/_images/simple_setup.png" alt="graylog minimum setup: 1 graylog2-server" width="798" height="537" />](http://docs.graylog.org/en/1.0/pages/architecture.html#minimum-setup){.anti-aede-checked}<figcaption class="wp-caption-text">graylog minimum setup</figcaption></figure> 

En esta arquitectura todos los componentes envían las trazas directamente al servidor _graylog-server_ usando alguno de los protocolos soportados por los &#8220;_inputs_&#8221; disponibles en el servidor.

Ventajas de esta arquitectura:

  * Configuración mínima.
  * Ideal para pruebas de concepto.
  * Requiere tan sólo: 
      * 1 servidor para _Elastic_
      * 1 servidor para _graylog-server_
      * 1 servidor para _graylog-web_ (se puede instalar _MongoDB_ aquí, ya que este último prácticamente no recibe peticiones).

Incluso _graylog-server_ y _graylog-web_ se pueden instalar en el mismo servidor para hacer pruebas con lo que sólo requeriría dos servidores:

  * _graylog-server_ + _graylog-web_ + _MongoDB_
  * _Elastic_

Problemas de esta arquitectura:

  * No hay tolerancia a fallos ni alta disponibilidad: 
      * Un nodo en el cluster de _ElasticSearch_
      * Un nodo en el cluster de _Graylog2_
      * Un servidor para la interfaz web
      * Un servidor _MongoDB_
  * Creo que con el punto anterior es suficiente&#8230;

## Arquitectura 2: cluster con tolerancia a fallos

La segunda arquitectura es una evolución de la primera donde simplemente se añade tolerancia a fallos mediante la creación de un cluster en cada parte de la arquitectura anterior.<figure style="width: 1252px" class="wp-caption alignnone">

[<img src="http://docs.graylog.org/en/1.0/_images/extended_setup.png" alt="graylog high available setup: cluster graylog-server + cluster MongoDB + cluster Elastic" width="1252" height="1452" />](http://docs.graylog.org/en/1.0/pages/architecture.html#bigger-production-setup){.anti-aede-checked}<figcaption class="wp-caption-text">Graylog production setup</figcaption></figure> 

En esta arquitectura existe un sistema que balancea las peticiones hacia un cluster de servidores _graylog-server_ que procesan e insertan los datos en un cluster de _Elastic_. Además, el almacenamiento de configuración se hace en un cluster de _MongoDB_.

En el ejemplo de la figura, un error en hasta dos de los tres servidores _graylog-server_ no sería fatal, siempre suponiendo que un sólo servidor es capaz de ofrecer servicio al volumen de trazas recibidas por todo el sistema.

De igual forma, el cluster de _Elastic_ y el de _MongoDB_ proporcionan tolerancia a fallos cuando se despliegan los componentes de la forma correcta, y en esta arquitectura estoy suponiendo que es así y entraré en sus detalles. Por tanto, podría haber cierto grado de fallo en los servidores de _Elastic_ y/o _MongoDB_ y el sistema seguiría funcionando.

Es interesante ver cómo _Graylog_ está diseñado con la tolerancia a fallos bien presente en sus requisitos. De ahí, que la &#8220;potente&#8221; API REST que expone, tenga recursos destinados a conocer el estado de salud de los distintos componentes. Por ejemplo: el API REST que el balanceador de carga debe usar para saber si un servidor _graylog-server_ sigue disponible y así seguir enviándole peticiones:

<pre>curl -v http://graylog2-server.example.com:12900/system/lbstatus
...
&lt; HTTP/1.1 200 OK
&lt; Content-Type: text/plain
&lt; X-Runtime-Microseconds: 141
&lt; Transfer-Encoding: chunked
&lt;
* Connection #0 to host graylog2-server.example.com left intact
<span style="color: #00ff00;"><strong>ALIVE</strong></span></pre>

Ventajas de esta arquitectura:

  * Cada parte de la arquitectura es tolerante a fallos (_procesamiento_, _configuración_ y _almacenamiento_). Cada una de ellas, es tolerante hasta el grado que su configuración específica permita. En el ejemplo: hasta 2 servidores _graylog-server_, y en los casos de _MongoDB_ y _Elastic_ dependerá del número de &#8220;shards&#8221; y réplicas configuradas en cada caso.

Inconvenientes de esta arquitectura:

  * La interfaz web de consulta no se ha considerado para la tolerancia a fallos. Si muchos usuarios hacen uso de ella (ingenieros de soporte, equipo de marketing, de negocio, de operativa&#8230;), sería necesario disponer también de varios servidores y un sistema de balanceo de carga.
  * La entrada de trazas al sistema sigue basándose en una comunicación directa desde los componentes externos que generan las trazas hacia los servidores _graylog-server_. En la tercera variante explico por qué esto es un inconveniente.

## Arquitectura 3: cluster tolerante a fallos con opción de parada técnica

Desde que empecé a escribir este artículo, graylog ha evolucionado mucho (ha dejado de llamarse _Graylog2_ para llamarse _Graylog_&#8230; han recibido inversiones, se han internacionalizado&#8230;). Incluso _ElasticSearch_ ahora se llama _Elastic_ a secas. Cosas del sector.

En las primeras versiones de _Graylog_ existía una posible arquitectura que explico en este apartado. Esta posibilidad se descartó en versiones intermedias de Graylog (versiones justo anteriores a 1.0), pero la han vuelto a recuperar, aunque ahora no soy capaz de encontrar documentación en su web oficial.

Esta tercera arquitectura consiste en situar un sistema de colas _AMQP_ entre los productores de trazas (&#8220;appenders&#8221; _JAVA_ tipo _log4j+gelf_, &#8220;appenders&#8221; _PHP_, &#8220;appenders&#8221; _Python_, etc), y los servidores graylog-server.

Así, la composición de esta arquitectura sería:

  * Sistemas productores de trazas.
  * Cluster de colas _AMQP_ (en nuestro caso, un cluster _RabbitMQ_)
  * Cluster de _graylog-server_: servidores que consumen de las colas, procesan los mensajes (streams, alarmas, etc) y persisten los datos en el cluster _Elastic_.
  * Cluster _MongoDB_ para almacenar los metadatos de _Graylog_ (configuraciones, etc).
  * Cluster _Elastic_ para almacenar las trazas.

Ventajas con respecto a la segunda arquitectura:

  * Se puede realizar una parada técnica del cluster de servidores graylog-server ya que los mensajes se irían almacenando en el cluster de colas.
  * Por el mismo motivo, se puede realizar una parada técnica del cluster de Elastic.
  * El cluster de colas AMQP además permite amortiguar picos de altos niveles de trazas, ya que cuando el sistema se recupera (o se introducen más servidores en los niveles de graylog-server o Elastic), se pueden ir consumiendo de las colas el exceso de mensajes que llegaron en avalancha.
  * Tres capas (sin contar la auxiliar de metadatos de MongoDB) que pueden escalar horizontalmente de forma independiente conforme a las necesidades.
  * Los &#8220;appenders&#8221; que usan conexiones TCP con los servidores de colas AMQP, reutilizan dichas conexiones para enviar su flujo de trazas. Esto hace que al ser conexiones persistentes, la sobrecarga debida al envío continuo de trazas sea menor. Por lo menos ha sido nuestra experiencia comparando las arquitecturas 2 y 3. En el caso de la arquirectura 2, el volumen de paquetes UDP era tan grande (un paquete por traza), que tuvimos problemas con la estabilidad del sistema, además de que aumentaron los tiempos de respuesta en los productores de trazas (servidores web). Con esta solución 3, al enviar un servidor las trazas a través del mismo canal TCP ya creado, no hemos notado un descenso apreciable en el rendimiento de los servicios que se integran con las colas AMQP.

Desventajas:

  * Tres clusters de servidores a montar: _RabbitMQ_, _graylog-server_, _Elastic._

# Conclusiones

El sistema actual que estamos probando en producción, que sigue la arquitectura 3 planteada, tiene las siguientes dimensiones:

  * De momento, unos 25 servidores enviando trazas (en cada servidor hay una aplicación al menos: PHP, JAVA&#8230;)
  * **Cluster RabbitMQ**: 
      * 2 servidores: 4 vCPUs, 8 GB RAM
  * **Cluster graylog-server**: 
      * 3 servidores: 4vCPUs, 16 GB RAM
      * Uso: ~60% CPU, ~12 GB RAM <== procesado de streams/alarmas
  * **Cluster MongoDB**: hemos reutilizado un cluster de 3 servidores que usamos para otros servicios.
  * **Cluster Elastic**: 
      * 5 servidores: 4 vCPUs, 72 GB RAM, 300 GB datos _Elastic_.
      * Uso: ~50% CPU, ~70 GB RAM <== almacenamiento datos
      * Esto hace un total de 1,5 TB de almacenamiento para trazas, pero por la configuración de réplicas se reducirá a unos 1,2 TB netos para trazas.

Por el momento nos sirve, ya hemos visto en _Elastic_ la cifra de mil millones de trazas (ahí es nada), y actualmente lo usamos para monitorizar algunas variables de negocio.

Además, ya nos está permitiendo definir unos estándares de calidad para las trazas del nuevo código que hacemos: qué es INFO, qué es DEBUG, qué es importante para negocio&#8230; y estamos planteando una convención para trazar KPIs usando este mecanismo, porque las posibilidades que supone tener toda esta información en un cluster de _Elastic_ son tremendas&#8230; por la sencillez con la que se puede navegar por estos datos medio estructurados&#8230;