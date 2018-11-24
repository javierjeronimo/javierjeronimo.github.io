---
id: 105
title: 'Crear &#8220;App Bundle&#8221; OS X para una aplicación XUL'
date: 2013-06-27T18:52:00+00:00
author: javier
guid: http://javierjeronimo.es/?p=105
permalink: /2013/06/27/crear-app-bundle-os-x-para-una-aplicacion-xul/
categories:
  - OS X
  - Programación
tags:
  - continua
  - integración
  - jenkins
  - os x
  - programación
  - xul
---
No soy un fanboy de Apple, pero tuve la oportunidad de escoger un MacBook Air como mi ordenador de trabajo y estoy la mar de contento con esa opción: pesa poco, es un Mac ;), y tengo una consola para todo lo que necesito (bendito homebrew). Además, me permite hacer todo lo que necesito hacer en el día a día. Salvo una cosa, hasta hace poco tiempo: usar de forma cómoda la aplicación XUL que nos permite administrar los portales web.

Tenemos una aplicación de administración construida con XUL de Mozilla. En este artículo no voy a entrar en detalles sobre la aplicación ni sobre XUL. Simplemente voy a dejar por escrito lo que he tenido que hacer para poder ejecutar esta aplicación en OS X como una aplicación más.

# Instalar xulrunner

No recuerdo cómo he instalado esto&#8230; pero sí que ahora mismo tengo dos versiones instaladas:

<pre>raichu:~ javier$ ls -l /Library/Frameworks/XUL.framework/Versions/
total 8
drwxr-xr-x  41 root    admin 1394 11 ago 00:52 1.9.2
drwxr-xr-x@ 41 javier  admin 1394 11 ago 10:46 14.0.1
lrwxr-xr-x   1 javier  admin    6 14 jul 00:11 Current -&gt; 14.0.1</pre>

Están instaladas en la ruta /Library así que supongo que encontré las imágenes DMG por algún sitio.

# Crear &#8220;App Bundle&#8221; OS X

Aunque he buscado información en mil sitios, incluyendo la propia [página de desarrollo de XUL](https://developer.mozilla.org/en-US/docs/XULRunner/Deploying_XULRunner_1.8#Mac_OS_X), al final sólo un _popurrí_ de indicaciones me han funcionado.

## Estructura de directorios de un &#8220;App Bundle&#8221;

Los &#8220;app bundle&#8221;, es decir, las aplicaciones en OSX no son más que carpetas con una estructura concreta parecida a lo siguiente:

  * Contents/ 
      * Frameworks/
      * MacOS/
      * Resources/

El diseño que han hecho en Apple hace que tanto el contenido de la aplicación (ficheros binarios, recursos, etc) como las dependencias de la misma puedan ir contenidas en esa estructura de carpetas, lo que hace que las aplicaciones de OS X sean autocontenidas.

En un principio no me gustó la idea, claramente opuesta al concepto de biblioteca compartida como el que se puede encontrar en Linux. Pero pensándolo un poco, y recordando el &#8220;infierno de las DLLs&#8221; de Windows y la cantidad de enlaces simbólicos para versionar bibliotecas en Linux, no me parece tan mal idea. Además, gracias a esto, instalar es &#8220;mover a /Applications&#8221; y des-instalar es &#8220;mover a la papelera&#8221;.

El punto en contra de este diseño: se desperdicia espacio en disco pues sucederá que muchas aplicaciones auto-contenidas tengan las mismas dependencias, lo que llevará a que estén repetidas en disco.

### Info.plist

Este fichero define la aplicación: tipo de aplicación, información del desarrollador, versión, información necesaria para su ejecución, etc.

Comparándolo con otras plataformas, es el típico &#8220;manifest&#8221;.

En mi caso, lo que hice fue generar un info.plist básico con la información de la aplicación que conocía, y con una referencia al lanzador: xulrunner.

<pre>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
&lt;plist version="1.0"&gt;
&lt;dict&gt;
    &lt;key&gt;CFBundleDevelopmentRegion&lt;/key&gt;
    &lt;string&gt;English&lt;/string&gt;
    <strong>&lt;key&gt;CFBundleExecutable&lt;/key&gt;</strong>
<strong>    &lt;string&gt;xulrunner&lt;/string&gt;</strong>
    &lt;key&gt;CFBundleGetInfoString&lt;/key&gt;
    &lt;string&gt;3.0&lt;/string&gt;
    &lt;key&gt;CFBundleIdentifier&lt;/key&gt;
    &lt;string&gt;com.example&lt;/string&gt;
    &lt;key&gt;CFBundleInfoDictionaryVersion&lt;/key&gt;
    &lt;string&gt;${version}&lt;/string&gt;
    &lt;key&gt;CFBundleName&lt;/key&gt;
    &lt;string&gt;CMS-XUL&lt;/string&gt;
    <strong>&lt;key&gt;CFBundlePackageType&lt;/key&gt;</strong>
<strong>    &lt;string&gt;APPL&lt;/string&gt;</strong>
    &lt;key&gt;CFBundleShortVersionString&lt;/key&gt;
    &lt;string&gt;${version}&lt;/string&gt;
    &lt;key&gt;CFBundleSignature&lt;/key&gt;
    &lt;string&gt;My app&lt;/string&gt;
    &lt;key&gt;CFBundleURLTypes&lt;/key&gt;
    &lt;array&gt;
        &lt;dict&gt;
            &lt;key&gt;CFBundleURLName&lt;/key&gt;
            &lt;string&gt;CMS-XUL&lt;/string&gt;
            &lt;key&gt;CFBundleURLSchemes&lt;/key&gt;
            &lt;array&gt;
                &lt;string&gt;chrome&lt;/string&gt;
            &lt;/array&gt;
    &lt;/dict&gt;
    &lt;/array&gt;
    &lt;key&gt;CFBundleVersion&lt;/key&gt;
    &lt;string&gt;${version}&lt;/string&gt;
&lt;/dict&gt;
&lt;/plist&gt;</pre>

En el fichero anterior se pueden leer variables del tipo _${version}_. El motivo no es otro que la forma en que genero la aplicación: un trabajo del sistema de integración continua Jenkins se encarga de generar la aplicación desde un repositorio, y en el proceso sustituye la versión en el Info.plist con la que se está construyendo.

### Dependencias y recursos de la aplicación XUL

[<img class="size-full wp-image-181 alignleft" alt="Contenido de la aplicación basada en XUL" src="http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-19.40.00.png" width="203" height="267" />](http://javierjeronimo.es/wp-content/uploads/2013/06/Captura-de-pantalla-2013-06-27-a-las-19.40.00.png)La única dependencia que tiene esta aplicación es el propio XUL: por una parte el _framework_ de OS X, y por otra el ejecutable _xulrunner_, quien realmente ejecuta la aplicación.

Y para terminar, la propia aplicación basada en XUL, que se convierte en un conjunto de &#8220;recursos&#8221; de la aplicación OS X. La carpeta _Resources_ contiene el directorio raíz de mi aplicación.

### Resumiendo

El objetivo de este artículo es dejar constancia de cómo he creado la aplicación OS X (el bundle app) para esta aplicación basada en XUL, y resumiendo:

  1. Estructura de directorios estándar de una _bundle app_.
  2. Info.plist haciendo referencia al ejecutable _xulrunner_ en la carpeta MacOS. El ejecutable se encuentra dentro del framework con el nombre _xulrunner_: yo lo he copiado porque son escasos 59 KB.
  3. Dependencia _XUL.framework_ incluída como parte de la aplicación
  4. El código fuente de la aplicación basada en XUL dentro de la carpeta _Resources_.