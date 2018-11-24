---
id: 120
title: 'Nota mental: &#8216;tar&#8217; de ficheros listados con &#8216;grep&#8217;'
date: 2013-01-05T12:19:32+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=120
permalink: /2013/01/05/nota-mental-tar-de-ficheros-listados-con-grep/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - OS X
  - UNIX
tags:
  - comandos
  - comprimir
  - consola
  - grep
  - os x
  - tail
---
Para no volver a buscarlo:

<pre>grep -Ilr 'algo' sitio &gt; fichero
grep -Ilr 'otro' sitio &gt;&gt; fichero
tar cjf comprimido.tar.bz2 -T fichero</pre>

La clave está en el modificador &#8216;-T&#8217; de tar:

> -T filename
  
> In x or t mode, tar will read the list of names to be extracted from filename. In c mode, tar will read names to be archived from filename. The special name &#8220;-C&#8221; on a line by itself will cause the current directory to be changed to the directory specified on the following line. Names are terminated by newlines unless &#8211;null is specified. Note that &#8211;null also disables the special handling of lines containing &#8220;-C&#8221;.

Ojo, que esto lo he ejecutado en OS X&#8230;