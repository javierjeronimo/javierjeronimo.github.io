---
id: 336
title: 'Balanceador + WordPress &#8211; test de disponibilidad del servidor'
date: 2014-12-04T15:20:53+00:00
author: javier
guid: http://javierjeronimo.es/?p=336
permalink: /2014/12/04/balanceador-wordpress-test-de-disponibilidad-del-servidor/
categories:
  - Programación
tags:
  - keepalive
  - php
  - varnish
  - wordpress
---
Interesante que no haya encontrado nada al respecto en internet, aunque tampoco he buscado mucho porque la solución es trivial.

El problema:

  * Cache frontal / balanceador de carga: Varnish en nuestro caso ([Genexies Mobile](http://www.genexies.com "Genexies Mobile")).
  * Conjunto de servidores WordPress (balanceo de carga, alta disponibilidad&#8230;)
  * Varnish usa un &#8220;_probe_&#8221; para comprobar la disponibilidad de los &#8220;_backends_&#8221; que forman un &#8220;_director_&#8221; (un conjunto de servidores que ofrecen el mismo servicio).
  * Queremos que el &#8220;_probe_&#8221; de Varnish no dependa de recursos de WordPress (un artículo, una página&#8230;) pero que compruebe conectividad con base de datos.

Opciones:

  * probe = _GET /_ 
      * Es un recurso dinámico de WordPress, por ejemplo, la página establecida como principal.
      * Requiere conectividad con BDD.
      * PHP
  * probe = _GET /license.txt_ 
      * No es un recurso dinámico de WordPress, sirve como prueba estable.
      * No requiere conectividad con BDD.
      * text/plain
  * probe = _GET /alive.php_ 
      * No es un recurso dinámico de WordPress.
      * Requiere conectividad con BDD.
      * PHP
      * No existe en WordPress.

¿La solución?

alive.php

<pre style="color: #a9b7c6;"><span style="color: #e8bf6a;">&lt;?php
</span><span style="color: #e8bf6a;">
</span><span style="color: #e8bf6a;">require './wp-config.php';
</span><span style="color: #e8bf6a;">
</span><span style="color: #e8bf6a;">// Create connection
</span><span style="color: #e8bf6a;">$conn = new mysqli(</span><span style="font-style: italic; color: #9876aa;">DB_HOST</span><span style="color: #e8bf6a;">, </span><span style="font-style: italic; color: #9876aa;">DB_USER</span><span style="color: #e8bf6a;">, </span><span style="font-style: italic; color: #9876aa;">DB_PASSWORD</span><span style="color: #e8bf6a;">);
</span><span style="color: #e8bf6a;">
</span><span style="color: #e8bf6a;">// Check connection
</span><span style="color: #e8bf6a;">if ($conn-&gt;</span><span style="color: #9876aa;">connect_error</span>) {
    die("FAILED: " . $conn-&gt;<span style="color: #9876aa;">connect_error</span>);
}
echo "ALIVE";
?&gt;</pre>

Hecho.

¿Qué toca ahora?

  * hg pull -u; hg branch &#8230;  hg ci -m&#8221;&#8230;&#8221; && hg push &#8211;new-branch
  * Integrar&#8230;
  * Probar en dev / pre: vagrant provision&#8230;
  * Desplegar en pro: vagrant provision&#8230;