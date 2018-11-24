---
id: 477
title: Instalación de minishift y Containers Development Kit (RedHat) en macOS
date: 2017-12-27T10:40:26+00:00
author: javier
guid: https://javierjeronimo.es/?p=477
permalink: /2017/12/27/instalacion-de-minishift-y-containers-development-kit-redhat-en-macos/
categories:
  - macos
tags:
  - cdk
  - docker
  - homebrew
  - kubernetes
  - oc
  - openshift
  - redhat
---
OpenShift es una plataforma de desarrollo basada en contenedores de RedHat, creada sobre Kubernetes y siguiendo la filosofía de muchas plataformas y frameworks actuales. Es decir, tiene un CLI para operar con ella.

Minishift es un CLI para lanzar en tu propia estación de trabajo un mini-cluster de OpenShift.

CDK (Containers Development Kit) son un conjunto de herramientas (JDK 1.8, entorno basado en Eclipse&#8230;) para el desarrollo rápido de aplicaciones JAVA basadas en contenedores. Básicamente, poder desarrollar aplicaciones sin tener que dedicarle mucho tiempo a montar el entorno y con un montón de configuraciones (en el IDE por ejemplo), para que desplegar en local y depurar sean acciones a las que no haya que dedicarle mucho tiempo.

Son herramientas o soluciones de RedHat, pero no dejan de estar basadas en otras de código abierto, con lo que no es &#8220;peligroso&#8221; apoyarse en ellas para el desarrollo, siempre que se tenga clara la línea que separa ambas, y conociendo lo que subyace.

Para instalar el entorno en macOS hay principalmente dos opciones:

  * Seguir las instrucciones oficiales y descargar paquetes desde el navegador.
  * Usar homebrew, o una mezcla de ambas, pero instalando desde esta herramienta lo más posible.

He optado por la segunda, y aquí está la chuleta:

<pre># Para usar virtualización "nativa" xhyve en macOS:
brew info --installed docker-machine-driver-xhyve

# Ahora sí:
brew install docker-machine-driver-xhyve
docker-machine-driver-xhyve need root owner and uid
sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve

brew cask install minishift

# Ojito con los servicios proxy corporativos, porque la instalación captura
# la configuración y la inyecta en la máquina virtual...
export HTTPS_PROXY="http://odio.los.proxies:3128"
export HTTP_PROXY="http://odio.los.proxies:3128"

# Y al arrancar el cluster por primera vez, lo crea...
minishift start

# CLI de OpenShift
brew install openshift-cli

# Probando, probando, 1, 2, 3... ssssí, ssssí
eval $(minishift oc-env)
oc whoami
oc project

# Acceso via web (developer/developer por defecto en minishift):
open "https://192.168.64.4:8443"

# Hecho.</pre>

<div class="paragraph">
</div>

Espero que sirva de ayuda.