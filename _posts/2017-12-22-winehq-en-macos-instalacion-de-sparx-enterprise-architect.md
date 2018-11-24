---
id: 474
title: 'WineHQ en macOS: instalación de Sparx Enterprise Architect'
date: 2017-12-22T10:36:22+00:00
author: javier
guid: https://javierjeronimo.es/?p=474
permalink: /2017/12/22/winehq-en-macos-instalacion-de-sparx-enterprise-architect/
categories:
  - OS X
tags:
  - EA
  - os x
  - wineHQ
---
Hace mucho tiempo que no usaba WineHQ, tenía muchas expectativas en él para la necesidad que tenía y la verdad es que no me ha decepcionado nada en absoluto.

En concreto necesitaba instalar Sparx Enterprise Architect en macOS y encontré una pequeña guía en los foros de la herramienta: <https://www.sparxsystems.com/support/faq/enterprise-architect-WINE.html#point1>

Aunque las instrucciones en esa guía son para una distribución de Linux basada en Debian, sirven para macOS con algunos cambios. También tuve que hacer algunos pasos adicionales, y como en parte uso este blog como un super-post-it donde dejar anotadas cosas que quizá necesite en el futuro, pues ahí va:

<pre>brew install wine winetricks
winetricks msxml3 msxml4 mdac28

# En mi caso, por otras herramientas relacionadas con EA, tuve que instalar también:
winetricks dotnet452 corefonts

# Y para que las fuentes se vean "decentes":
winetricks fontsmooth-rgb

# Finalmente:
wine msiexec /i easetupfull.msi

# Para configurar el tamaño de la fuente en pantalla:
wine winecfg</pre>

Y fin.