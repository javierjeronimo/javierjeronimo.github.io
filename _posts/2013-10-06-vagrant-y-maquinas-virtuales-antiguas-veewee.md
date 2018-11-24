---
id: 261
title: 'Vagrant y máquinas virtuales antiguas: veewee'
date: 2013-10-06T21:43:22+00:00
author: javier
guid: http://javierjeronimo.es/?p=261
permalink: /2013/10/06/vagrant-y-maquinas-virtuales-antiguas-veewee/
categories:
  - programacion
tags:
  - hardy
  - ubuntu
  - vagrant
  - veewee
  - virtualbox
---
En otro artículo mostraba cómo montar un [entorno de desarrollo para recetas Chef usando Vagrant](http://javierjeronimo.es/2013/09/29/aws-opsworks-1-entorno-de-desarrollo-y-pruebas-para-chef/ "AWS OpsWorks (1) – Entorno de desarrollo y pruebas para Chef") y, en mi caso, _VirtualBox_ como sistema de virtualización local.

En este caso mostraré cómo _Vagrant_ y otra herramienta llamada _Veewee_ resuelven el siguiente problema: la necesidad de montar una máquina virtual con una versión tan antigua o rara de un sistema operativo que no existan imágenes virtuales _Vagrant_ para ella. Por ejemplo: _Ubuntu Hardy (8.04 LTS)_, ya sin soporte oficial&#8230;

[Veewee](https://github.com/jedi4ever/veewee) es una herramienta que permite, entre otras cosas, crear máquinas virtuales _Vagrant_ desde máquinas ya existentes _VirtualBox_. Y esto es lo que es útil para el problema que planteo arriba. No es un proceso muy complicado el que detallo a continuación, y simplemente requiere: _VirtualBox_, _Veewee_ y una imagen ISO de, en este caso, _Ubuntu Hardy_.

## Instalación de Veewee

Primero _Ruby_ con RVM:

<pre>$ \curl -L https://get.rvm.io | bash
$ rvm install 1.9.2
<code>$ git clone https://github.com/jedi4ever/veewee.gi</code>t
<code>$ cd veewee
$ rvm use 1.9.2@veewee --create
</code><code>$ gem install bundler
</code><code>$ bundle install</code></pre>

## Creación de la máquina virtual

_Veewee_ trae varias plantillas de máquinas, con lo que el proceso es muy sencillo, por ejemplo, para crear una máquina llamada _web_:

<pre>$ bundle exec veewee vbox templates | grep -i ubuntu
$ bundle exec veewee vbox define 'web' 'ubuntu-8.04.4-server-amd64'
$ bundle exec veewee vbox build 'web' --workdir=/Users/javier/Documents/workspace/veewee</pre>

## La dura realidad: no funciona para Ubuntu 8.04

Es la verdad, el proceso no funciona. Básicamente hace lo siguiente:

  * Cuando define la máquina usando la plantilla, crea una nueva plantilla copia de la original. Esta plantilla está formada por tres ficheros: 
      * _postinstall.sh_: Guión para preparar la máquina virtual y dejarla lista para su uso en _Vagrant_. Por ejemplo, en el caso de una máquina basada en _VirtualBox_ instala las herramientas de invitado (VBoxGuestAdditions), pero también realiza tareas propias del entorno _Vagrant_ como crear la pareja de claves RSA para la conexión SSH (_vagrant ssh_) y otras.
      * _d__efinition.rb_, propio de Veewee para crear la máquina, que en el caso de VirtualBox establece las características de la máquina virtual (memoria, CPU) e incluso la URL de descarga de la imagen ISO.
      * _preseed.cfg_ se usa en el instalador de Ubuntu (debian installer) y establece las opciones por defecto en una instalación automatizada.
  * Al construir la máquina se lanza _VirtualBox_ (se puede ver la máquina arrancando en modo gráfico) y la instalación continua de forma automatizada. Bajando un poco a los detalles, _Veewee_ inyecta pulsaciones de teclado de forma que se carga una imagen de Linux por red (servidor local lanzado por el propio _Veewee_) y se inicia una instalación automatizada definida en _preseed.cfg_, seguida de _postinstall.sh_

Y hasta aquí puedo leer, porque es en este punto donde la instalación falla: el asistente de instalación (debian-installer) que se ve gráficamente en la máquina de _VirtualBox_, no pasa del punto &#8220;instalar paquetes&#8221;. Y falla simplemente porque la ISO de Hardy trae como ruta de los repositorios la URL _archive.ubuntu.com_ cuando ahora es _old-releases.ubuntu.com_&#8230; #fail

## Workaround

_Veewee_ permite modificar la plantilla que se usará para la creación de una máquina, así que el problema se soluciona editando el fichero _preseed.cfg_ y estableciendo el repositorio de paquetes _old-releases.ubuntu.com_:

<pre># debconf-get-selections --install
#Use mirror
d-i apt-setup/use_mirror boolean true
d-i     mirror/country          string manual
choose-mirror-bin mirror/protocol       string http
choose-mirror-bin mirror/http/hostname  string old-releases.ubuntu.com
choose-mirror-bin mirror/http/directory string /ubuntu 
choose-mirror-bin mirror/suite  select hardy
d-i debian-installer/allow_unauthenticated      string true</pre>

Con este sencillo cambio la instalación de _Ubuntu 8.04_ termina y el proceso automatizado de veewee continua sin problemas. Lo he subido a un fork de github<del>, supongo que algún día lo llevarán al master del proyecto</del> y ya está mezclado en el proyecto oficial.

Para terminar: validar la imagen y exportarla:

    $ bundle exec veewee vbox validate 'web'
    $ bundle exec veewee vbox export 'web

## Terminando

_Veewee_ es una buena herramienta que permite crear máquinas virtuales _Vagrant_ (en mi caso _VirtualBox_) para requisitos que no se cumplen con las máquinas disponibles en Internet o el propio Vagrant.

En mi caso, una máquina _Ubuntu 8.04_ (x64 server), que además requiere cambios en el propio _Veewee_ por la antiguedad de la misma.

El resultado es un fichero _box_ de _Vagrant_ que contiene la máquina y que se puede usa como base para crear máquinas virtuales:

<pre>$ vagrant box add 'web' '/Users/javier/Documents/workspace/veewee/web.box'
$ vagrant init 'web'
$ vagrant up
$ vagrant ssh</pre>