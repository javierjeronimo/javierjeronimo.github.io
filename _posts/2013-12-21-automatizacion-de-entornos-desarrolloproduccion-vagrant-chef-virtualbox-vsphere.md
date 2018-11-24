---
id: 272
title: 'Automatización de entornos desarrollo/producción: Vagrant + Chef + VirtualBox + vSphere'
date: 2013-12-21T17:24:19+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=272
permalink: /2013/12/21/automatizacion-de-entornos-desarrolloproduccion-vagrant-chef-virtualbox-vsphere/
categories:
  - IT
tags:
  - automatización
  - berkshelf
  - bundler
  - chef
  - control de configuración
  - dependencias
  - infraestructura
  - servidor
  - vagrant
  - virtual
  - virtualbox
  - vmware
  - vsphere
---
Hoy he subido a Slideshare una presentación que usaré en el trabajo para introducir a mis compañeros lo que he venido investigando desde las últimas semanas: automatización de entornos de desarrollo y producción usando Vagrant, Chef, VirtualBox y VMware vSphere.

Quiero profundizar un poco más en el entorno de producción vSphere, pero de momento cuelgo las diapositivas que muestran cómo he intentado llegar a tres simples objetivos:

  * Que el entorno de desarrollo sea exactamente igual que el de producción: versiones SO, paquetes, configuraciones, etc.
  * Tener en control de configuración toda la infraestructura y configuración de las máquinas y servicios.
  * Tener automatizada la gestión de los entornos, que al final es lo único que garantiza que sean iguales.

Aquí está la presentación: [Vagrant para automatizar entornos DEV/PRO](http://goo.gl/VtGYl5)