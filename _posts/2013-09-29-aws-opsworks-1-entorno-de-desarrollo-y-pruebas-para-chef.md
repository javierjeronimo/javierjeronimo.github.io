---
id: 243
title: AWS OpsWorks (1) – Entorno de desarrollo y pruebas para Chef
date: 2013-09-29T18:19:01+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=243
permalink: /2013/09/29/aws-opsworks-1-entorno-de-desarrollo-y-pruebas-para-chef/
categories:
  - Programación
tags:
  - aws
  - chef
  - devops
  - entorno de desarrollo
  - ide
  - opsworks
  - programación
  - pruebas
---
Este artículo va en la línea de [otros](http://javierjeronimo.es/?p=164 "Entorno de desarrollo Android") que tengo en el blog sobre la creación de entornos de desarrollo, y en este caso para _Chef_.

_Chef_ es una plataforma de automatización de infraestructuras de TI: permite mediante _recetas_, definir la configuración de los nodos de una infraestructura (servidores, cortafuegos, routers&#8230;), es decir, definir: paquetes instalados, configuraciones, usuarios, etc. Lo que en muchas ocasiones se hace de forma manual, _Chef_ permite hacerlo programáticamente.

## AWS OpsWorks: recetas Chef

Al ser _Amazon Web Services_ como es, todo virtual para el administrador de sistemas, lleva de forma natural a usar un sistema de este tipo para que la gestión de la infraestructura sea igual de escalable que el software que se despliega en ella. Amazon no puede pensar en la escalabilidad sólo para el software que sus clientes despliegan en su nube, y consciente del coste de gestión de las infraestructuras de TI, proporciona la posibilidad de usar recetas de _Chef_ para crear dicha infraestructura. En Amazon han llamado al servicio _OpsWorks_.

En el artículo anterior explicaba el ciclo de vida de _OpsWorks_, donde cada paso supone la ejecución de recetas propias de AWS para crear una nueva instancia de un servidor virtual y dejarla en ejecución como último objetivo, pero donde el administrador de sistemas puede añadir recetas propias para completar la creación y configuración del nodo que está siendo creado en ese momento.

Volviendo al tema principal del artículo, el administrador en este caso necesita un entorno de desarrollo de las recetas _Chef_ que usará en _OpsWorks_.

## Entorno de desarrollo Chef

En primer lugar: ¿qué necesita un administrador de sistemas para poder desarrollar y probar recetas _Chef_ [para _OpsWorks_]?

Después de buscar por Internet y de asistir a algún que otro hangout de _OpsCode_, la empresa que ha creado _Chef_, tengo clara la respuesta:

  * Una máquina virtual que corresponda con la máquina objetivo que se usará en AWS.
  * [_Vagrant_](http://www.vagrantup.com/) como herramienta (interfaz de usuario) para para ejecutar la máquina virtual en el equipo del desarrollador.
  * [_Chef Solo_](http://docs.opscode.com/chef_solo.html) _o Chef (cliente-servidor)._

### Vagrant y máquina virtual

En el caso que pongo como ejemplo, que es al que me enfrenté yo con AWS, necesitamos preparar una máquina virtual _Ubuntu 12.04 x64_:

<pre>vagrant init precise64 http://files.vagrantup.com/precise64.box
vagrant up</pre>

Y después de la magia de _Vagrant_, y dependiendo del ancho de banda disponible para la descarga de la imagen de la máquina virtual, tenemos una máquina arrancada y totalmente funcional con Ubuntu 12.04 x64:

<pre>$ vagrant ssh
Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
Welcome to your Vagrant-built virtual machine.
Last login: Sun Sep  8 10:39:31 2013 from 10.0.2.2
vagrant@precise64:~$</pre>

### _Chef Solo_ y configuración de la máquina

Además de otras funciones de _Vagrant_ que no vienen al caso en este artículo (muy interesantes el _port forwarding_ o las _carpetas compartidas_ entre las máquinas _host_ y _guest_), la que interesa para este artículo es la posibilidad de indicarle a _Vagrant_ que configure la máquina usando recetas _Chef_.

Este proceso de configuración de la máquina virtual, llamado &#8220;provisioning&#8221; (_abastecimiento_ en español), permite definir de forma exacta la configuración de la máquina, la instalación de paquetes, servicios, etc. De esta forma, en un sólo fichero de definición de la máquina virtual tenemos todo lo necesario para un fin determinado.

Usando _Chef Solo_, que no requiere un servidor central que gestione un grupo de nodos con _Chef_, podemos definir la forma de la máquina, por ejemplo, para instalar _Apache2_ con el módulo _PHP_ activado y alguna configuración adicional _de fábrica_:

      config.vm.provision "chef_solo" do |chef|
        chef.add_recipe "apache2"
      end
    
      chef.json = {
        "apache" => {
          "listen_address" => "0.0.0.0",
          "contact" => "localhost@example.com",
          "default_modules" => [ "php5" ]
        }
      }

### Entorno de pruebas con recetas propias: Vagrant + Chef Solo

Con todo lo anterior, disponemos de un entorno virtualizado donde en poco tiempo podemos tener una máquina &#8220;_limpia_&#8221; en la cual probar nuestras propias recetas de _Chef Solo_ para después usarlas en _OpsWorks_. Siempre teniendo en cuenta lo siguiente:

  * La versión de _Chef Solo_ que se usa en _OpsWorks___: 0.9 y **11**, siendo esta última la versión por defecto.
  * La versión de _Chef Solo_ que se usa en Vagrant

<pre>vagrant@precise64:~$ chef-solo --version
Chef: <strong>10.14.2</strong></pre>

  * Las particularidades del entorno final de ejecución de las recetas (_OpsWorks_ en una máquina virtual en AWS) como por ejemplo, el ciclo de vida de _OpsWorks_ y las recetas propias de _OpsWorks_ que se ejecutan siempre.

Y la forma es sencilla, un repositorio local en forma de carpeta anexa al fichero de configuración Vagrant de la máquina, donde disponer las recetas propias que se referencian desde él:

    Vagrantfile
    
    cookbooks
     - cookbook1
     - cookbook2
     - cookbook3

Esto permite:

  * Crear recetas Chef y probarlas en la máquina (forzar el aprovisionamiento, y es aquí donde se aprovecha la característica de [idempotencia de Chef](http://es.wikipedia.org/wiki/Idempotencia_%28inform%C3%A1tica%29)):

<pre>vagrant reload --provision</pre>

  * Limpiar la máquina y volver a probar las recetas sin la interferencia que supone la suciedad de las pruebas que se hayan podido hacer anteriormente:

<pre>vagrant destroy && vagrant up</pre>

## Terminando

El objetivo de este artículo no era ni mucho menos servir de guía de programación de recetas _Chef_ para _OpsWorks_, si no demostrar cómo siempre existe un entorno de desarrollo que permite maximizar el rendimiento del tiempo que empleamos en implementar una cierta tarea de programación. En este caso, el objetivo era mostrar como disponer de un entorno de desarrollo similar al entorno final de ejecución del código (recetas _Chef_ en este caso), para poder realizar pruebas hasta obtener la implementación deseada.

Como nota sobre el entorno de desarrollo, existe un proyecto en versión beta actualmente que proporciona un plugin para Eclipse para la edición de recetas _Chef_: https://github.com/limepepper/chefclipse/