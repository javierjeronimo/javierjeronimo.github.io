---
id: 167
title: 'Gestión de dependencias en proyectos PHP con &#8220;composer&#8221;'
date: 2013-06-07T18:23:12+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=167
permalink: /2013/06/07/gestion-de-dependencias-en-proyectos-php-con-composer/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - Programación
tags:
  - automatizada
  - dependencias
  - gestión
  - php
  - programación
---
Para un nuevo proyecto en PHP estoy usando un gestor de dependencias: composer. Es la segunda vez que lo veo navegando por proyectos en Github, y como no tengo ningún otro motivo para buscar alternativas, es el que voy a usar para el proyecto.

En OS X siempre busco la opción de instalar los paquetes con homebrew, por la comodidad, pero en este caso he tenido que usar el método oficial de instalación &#8220;[root](http://getcomposer.org/doc/00-intro.md "Instalando composer")&#8220;:

    cd proyecto
    curl -sS https://getcomposer.org/installer | php
    sudo mv composer.phar /usr/local/bin/composer

La gestión de dependencias básica se reduce a crear un fichero composer.json en la raíz del proyecto e indicar ahí las dependencias:

    {
        "require": {
            "nombre_de_la_dependencia": ">= version"
        }
    }

Y para instalar las dependencias:

<pre>php composer.phar install</pre>

Y poco más puedo decir de este gestor de dependencias para PHP&#8230;

EDITADO: También podemos realizar otros comandos como actualizar las dependencias:

<pre>php composer.phar update</pre>

&#8230; actualizar el propio composer:

<pre>php composer.phar self-update</pre>

&#8230; y añadir nuevas dependencias sin modificar el fichero composer.json:

<pre>php composer.phar require foo/bar=1.0.0</pre>

&nbsp;