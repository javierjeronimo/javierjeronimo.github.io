---
id: 169
title: Gestión de dependencias en proyectos alojados en repositorios git
date: 2013-06-07T18:47:13+00:00
author: javier
guid: http://javierjeronimo.es/?p=169
permalink: /2013/06/07/gestion-de-dependencias-en-proyectos-alojados-en-repositorios-git/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - Programación
tags:
  - dependencias
  - dvcs
  - git
  - módulos
  - programación
  - proyecto
---
EDITADO: No había puesto el comando para descargar una dependencia git.

Acabo de escribir un artículo sobre [composer](http://javierjeronimo.es/2013/06/07/gestion-de-dependencias-en-proyectos-php-con-composer/ "Gestión de dependencias en proyectos PHP con “composer”"), una herramienta para gestionar de forma sencilla dependencias en proyectos PHP. Y no me ha funcionado. Seguro que porque he hecho algo mal, y también porque el Mac lo tengo a rebentar de instalaciones de cosas que quiero probar.

La cuestión es que no he podido instalar las dependencias del proyecto, al menos una de ellas, usando composer.

Estoy tonteando con _git_ desde hace unas semanas, principalmente porque veo que tiene mucha acogida, pero también porque en _Genexies Mobile_ usamos [Mercurial](http://mercurial.selenic.com/), y las funciones que tienen estos sistemas distribuidos de control de versiones me gustan.

En el nuevo proyecto que me estoy embarcando uso [BitBucket](https://bitbucket.org/) con un repositorio _git_, y ahora que me planteo cómo gestionar las dependencias del proyecto, descubro que _git_ incorpora una función llamada [_sub-módulos_](http://git-scm.com/book/en/Git-Tools-Submodules).

En una primera toma de contacto me parece similar a los _externos_ de _Subversion_, es decir, un punto en la jerarquía de directorios del proyecto que corresponde con proyectos externos, alojados en otros repositorios.

Tengo que reconocer que ahora mismo no recuerdo los detalles de uso de los _externos_ de _Subversion_, pero para el uso que voy a hacer yo ahora mismo de los _sub-módulos_ de _git_, me sirven igual: mientras no haga cambios dentro de la carpeta de la dependencia, es decir, dentro del _sub-módulo_, _git_ lo ignorará, pero ahí lo tendré disponible:

<pre>git clone git://mi_repo mi_repo
cd mi_repo
<code>git submodule init
git submodule add git://dependencia vendor/dependencia
</code><code>git submodule update</code></pre>

Ahora bien, si todas las dependencias del proyecto no están alojadas en repositorios git&#8230;