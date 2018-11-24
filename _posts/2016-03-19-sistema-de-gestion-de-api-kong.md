---
id: 412
title: 'Sistema de gestión de API &#8211; KONG'
date: 2016-03-19T11:08:11+00:00
author: javier
guid: http://javierjeronimo.es/?p=412
permalink: /2016/03/19/sistema-de-gestion-de-api-kong/
image: /wp-content/uploads/2016/03/kong.png
categories:
  - arquitectura
tags:
  - api
  - cassandra
  - docker
  - gateway
  - gestión
  - kong
  - management
---
Desde hace tiempo me corroe por dentro no tener un sistema de gestión del API de Genexies Mobile. ¿Qué uso se hace del API? ¿Funcionan igual de bien todos los recursos? ¿Quién la usa? ¿Estamos preparados para que siga creciendo su importancia y usos en GNX?

Sinceramente, es la segunda vez que me doy una vuelta por internet buscando soluciones de gestión de APIs (&#8220;API management&#8221;) y haberlas las hay. Algunos precios, a los que sólo he podido acceder previo registro y llamada del comercial de turno, me han parecido astronómicos. Como si las empresas como GNX no se merecieran o necesitaran estos servicios 🙁

Hubo uno que me llamó la atención, por ser una solución que podríamos instalar en nuestros servidores: Kong.

En esta segunda ronda de búsqueda me ha sucedido lo mismo, he acabado en ese mismo servicio.

<!--more-->

# Requisitos sistema gestión API

Tengo algunos requisitos que me han hecho descartar las soluciones que he ido encontrando:

  * No puedo usar cualquier dominio para el API (tengo flujos de &#8220;Single Sign On&#8221; donde debo ser un sub-dominio del dominio del cliente, para recibir su cookie). Esto me hace descartar soluciones que me obliguen a dominios como _https://tu-dominio-con-guiones-mitoken.gestorapi.com_ y todas las que no acepten un dominio personalizado apuntado por CNAME al del servicio.
  * En GNX recibimos tráfico de navegadores antiguos, de terminales antiguos, muy antiguos 🙁 Así que tenemos unas configuraciones especiales en los hosts SSL que algunos proveedores no soportan. Esto me hace descartar inicialmente todos los servicios en la nube, para evitar el tiempo de configuración inicial (es una prueba de concepto la que quiero hacer).
  * Quiero responder a las siguientes preguntas:
  * ¿Qué uso se hace del API a nivel de recurso? ¿Se usan todos los recursos?
  * ¿Qué rendimiento tiene cada recurso del API?
  * ¿Está habiendo errores? ¿En qué recursos?
  * ¿Para los recursos M2M, quién está haciendo uso de los recursos?
  * ¿Hay patrones de uso extraños de los recursos?
  * Además, quiero preparar el API para lo siguiente:
  * Bloquear usos indebidos o no permitidos.
  * Limitar el uso para poder garantizar la calidad

# Kong

Kong es un sistema de gestión de API que puedes instalar en tus servidores.<figure style="width: 1040px" class="wp-caption alignnone">

<img src="https://camo.githubusercontent.com/0e1bbef1559f33dd31b9f352c83d2caf2bc4e61b/687474703a2f2f636c2e6c792f696d6167652f3142334a33623368314831632f496d616765253230323031352d30372d30372532306174253230362e35372e3235253230504d2e706e67" alt="KONG (https://github.com/Mashape/kong)" width="1040" height="941" /><figcaption class="wp-caption-text">KONG (https://github.com/Mashape/kong)</figcaption></figure> 

Te permite hacer cosas como:

  * Dirigir el tráfico a distintos servidores en función de reglas, sirviendo de un punto común de exposición para varias APIs.
  * Añadir capacidades de autenticación a los clientes usando el API
  * Añadir seguridad a los recursos del API: ACL, restricción por IPs, SSL, CORS, etc.
  * Limitar el tráfico.
  * Enviar datos a sistemas de analítica externos.
  * Transformar las peticiones y las respuestas al vuelo.
  * Enviar trazas de cada acceso al API a sistemas externos.
  * Escribir tu propio plugin 😉
  * Mira tú mismo la lista completa: https://github.com/Mashape/kong#features

Además, el producto está evolucionando mucho y ya tiene lo que se espera de un buen producto tecnológico hoy en día:

  * Análisis del rendimiento: https://getkong.org/about/benchmark/
  * Instaladores para todos los gustos (¿quieres probarlo? pues ellos se encargan de que lo tengas sencillo): https://github.com/Mashape/kong#distributions
  * Buena documentación, por ejemplo, cómo actualizar desde versiones antiguas: https://github.com/Mashape/kong/blob/master/UPGRADE.md
  * Arquitectura escalable, alta disponibilidad: modo cluster https://getkong.org/docs/latest/clustering/
  * Comienza a tener integraciones con servicios de terceros: estadísticas, analítica API, documentación API, etc.

# Primeros pasos

En el siguiente artículo detallaré cómo he probado Kong con el API de GNX (con el API de GNX, pero en el dominio de pruebas internas), pero de momento voy a listar lo que tengo en mente (estoy con el primer paso):

  * Crear una imagen docker de Cassandra 3.3 con la personalización mínima (para entender cómo es un cluster de Cassandra, con el que hasta ahora no he trabajado nunca).
  * Lanzar un cluster de Cassandra de tres nodos en una pequeña infraestructura para contenedores docker que tenemos en nuestro CPD en GNX.
  * Lanzar un nodo de Kong en el entorno de pruebas del API de GNX.
  * Hacer pasar el tráfico de pruebas del API de GNX por el nodo Kong.

# ¿Qué pretendo con esto?

Primero, aprender a configurar un cluster de Cassandra. Segundo, aprender a configurar un cluster de Kong. Tercero, aprender a configurar un API en Kong y hacer que &#8220;no haga nada y sea un mero gateway hacia los servidores&#8221;. Cuarto, ver la estabilidad del sistema (Kong está basado en NGINX, pero la versión actual es 0.7.0 < 1.0.0 😉

Ya contaré cómo avanzo en mi aprendizaje&#8230;