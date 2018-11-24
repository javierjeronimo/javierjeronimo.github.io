---
id: 47
title: Impresora HP CP1215 en OS X
date: 2012-10-10T20:18:59+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=47
permalink: /2012/10/10/impresora-hp-cp1215-en-os-x/
shareaholic_disable_share_buttons:
  - "0"
shareaholic_disable_open_graph_tags:
  - "0"
categories:
  - OS X
tags:
  - cp1215
  - drivers
  - hp
  - impresora
  - os x
---
[<img class="alignleft size-full wp-image-50" style="margin: 10px 20px;" title="Impresora HP CP1215" src="http://javierjeronimo.es/wp-content/uploads/2012/09/Captura-de-pantalla-2012-09-10-a-las-19.15.22.png" alt="Impresora HP CP1215" width="185" height="149" />](http://h10025.www1.hp.com/ewfrf/wc/product?cc=es&lc=es&dlc=es&product=3422476 "Impresora HP CP1215")No sé cuántas veces he buscado esta información en Internet. Esta ha sido la última.

Esta entrada del blog quiero que sirva para dejar constancia de cómo configurar la maravillosa impresora láser a color HP CP1215 en OS X. Realmente las instrucciones son las mismas que se pueden encontrar aquí, pero resumidas: [http://foo2hp.rkkda.com/](http://foo2hp.rkkda.com/ "foo2hp: a linux printer driver for ZjStream protocol")

**EDITADO**: El primer paso no estaba en el artículo y como he tenido que instalar la impresora en el Mac de [@Araceli_Olmedo](https://twitter.com/Araceli_Olmedo "Twitter Araceli Olmedo") y he necesitado todos los pasos desde el principio, lo he añadido.

**EDITADO**: Añadido otro paso adicional para cambiar las opciones por defecto a &#8220;impresión a color&#8221;.

**EDITADO**: añadida otra URL porque el fichero original de Foomatic-RIP ya no está disponible.

## 1. Instalar brew

Para algunos pasos se debe instalar [Homebrew](http://mxcl.github.com/homebrew/ "Homebrew para OS X") que no es más que un gestor de paquetes al estilo apt de Debian o portage de Gentoo para OS X. Es bastante útil cuando se necesita alguna herramienta del mundo UNIX y no se quiere andar buscando en internet el DMG correspondiente.

Homebrew requiere [XCode](http://goo.gl/mTWVz "XCode"), así que es otro requisito que debe estar instalado. Además, se deben instalar las herramientas de consola de XCode desde la ventana de preferencias:

<img class=" wp-image-107 alignnone" title="XCode command line tools installation" src="http://javierjeronimo.es/wp-content/uploads/2012/10/XCode-commandline-tools-installation-300x211.png" alt="XCode command line tools installation" width="300" height="211" srcset="https://javierjeronimo.es/wp-content/uploads/2012/10/XCode-commandline-tools-installation-300x211.png 300w, https://javierjeronimo.es/wp-content/uploads/2012/10/XCode-commandline-tools-installation.png 750w" sizes="(max-width: 300px) 100vw, 300px" />

En las últimas versiones se debe descargar desde la página web: <https://developer.apple.com/downloads/index.action?name=for%20Xcode%20->

## 2. Instalar Foomatic-RIP

Se puede descargar desde: [http://www.linuxfoundation.org/collaborate/workgroups/openprinting/macosxfoomatic](http://www.linuxfoundation.org/collaborate/workgroups/openprinting/macosxfoomatic "The Linux Foundation: macosx/foomatic")

O desde este mismo blog: [foomatic-rip-4.0.6.230.dmg](http://javierjeronimo.es/wp-content/uploads/2012/10/foomatic-rip-4.0.6.230.dmg_.zip)

## 3. Instalar Ghostscript

Se puede descargar también desde el enlace anterior, o bien usando _brew_:

<pre>$ brew install gs</pre>

## 4. Instalar foo2zjs

<pre>$ brew install wget
$ wget -O foo2zjs.tar.gz http://foo2zjs.rkkda.com/foo2zjs.tar.gz
$ tar xvf foo2zjs.tar.gz
$ cd foo2zjs
$ brew install gnu-sed
$ make
$ ./getweb 1215
$ sudo make install</pre>

Y ya está, desde el panel de control de la impresora de OS X ya se puede agregar usando el controlador recién compilado e instalado:

## 5. Cambiar de monocromo a color

El último paso que he tenido que hacer siempre ha sido cambiar la configuración por defecto de la impresora de monocromo a color.

Para ello primero se debe activar la interfaz web de administración de CUPS en OS X:

<pre>$ cupsctl WebInterface=yes</pre>

A continuación se debe acceder a la impresora previamente creada desde la ventana de preferencias del sistema de OS X, y configurar el color por defecto:

[<img class="alignnone size-large wp-image-113" title="CUPS web interface - printers" src="http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-printers1-1024x553.png" alt="CUPS web interface - printers" width="680" height="367" srcset="https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-printers1-1024x553.png 1024w, https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-printers1-300x162.png 300w, https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-printers1.png 1280w" sizes="(max-width: 680px) 100vw, 680px" />](http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-printers1.png)

[<img class="alignnone size-full wp-image-111" title="CUPS web interface - Printer - set default options" src="http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-set-default-options1.png" alt="CUPS web interface - Printer - set default options" width="438" height="338" srcset="https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-set-default-options1.png 438w, https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-set-default-options1-300x231.png 300w" sizes="(max-width: 438px) 100vw, 438px" />](http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-set-default-options1.png)

[<img class="alignnone size-full wp-image-112" title="CUPS web interface - Printer - Default options - colour" src="http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-Default-options-color.png" alt="CUPS web interface - Printer - Default options - colour" width="931" height="587" srcset="https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-Default-options-color.png 931w, https://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-Default-options-color-300x189.png 300w" sizes="(max-width: 931px) 100vw, 931px" />](http://javierjeronimo.es/wp-content/uploads/2012/10/CUPS-web-interface-Printer-Default-options-color.png)

Ya puedo imprimir tranquilamente&#8230;