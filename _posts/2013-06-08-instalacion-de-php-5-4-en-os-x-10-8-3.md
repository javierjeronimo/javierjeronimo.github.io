---
id: 171
title: Instalación de PHP 5.4 en OS X 10.8.3
date: 2013-06-08T17:43:25+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=171
permalink: /2013/06/08/instalacion-de-php-5-4-en-os-x-10-8-3/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - Programación
tags:
  - homebrew
  - instalar
  - os x
  - php
---
La instalación de PHP 5.4 (en OS X viene la versión 5.3.15) es sencilla con [homebrew](https://github.com/josegonzalez/homebrew-php):

    brew tap homebrew/dupes
    brew tap josegonzalez/homebrew-php
    <code>brew install php54</code>

Y a <del>volar</del> compilar&#8230;

Después, como no está en el PATH:

<pre>export PATH=/usr/local/php5/bin:$PATH</pre>

Se pueden seguir las instrucciones de [configuración aquí](http://php-osx.liip.ch/), siempre se sacan puntos importantes leyendo varias fuentes de información.

Y los típicos problemillas, que no todo es _instalar y listo_:

<pre>==&gt; Caveats
To enable PHP in Apache add the following to httpd.conf and restart Apache:
    LoadModule php5_module    /usr/local/opt/php54/libexec/apache2/libphp5.so

The php.ini file can be found in:
    /usr/local/etc/php/5.4/php.ini

✩✩✩✩ PEAR ✩✩✩✩

If PEAR complains about permissions, 'fix' the default PEAR permissions and config:
    chmod -R ug+w /usr/local/Cellar/php54/5.4.15/lib/php
    pear config-set php_ini /usr/local/etc/php/5.4/php.ini

✩✩✩✩ Extensions ✩✩✩✩

If you are having issues with custom extension compiling, ensure that this php is
in your PATH:
    PATH="$(brew --prefix josegonzalez/php/php54)/bin:$PATH"

PHP54 Extensions will always be compiled against this PHP. Please install them
using --without-homebrew-php to enable compiling against system PHP.
Warning: Could not link php54. Unlinking...
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
You can try again using `brew link php54'</pre>

EDITADO: Y tuve problemas así que corté por lo sano:

<pre>brew link --overwrite --dry-run php54
ARREGLAR PROBLEMAS
brew link php54</pre>

Fin.