---
id: 284
title: 'Baches con Google Cloud Endpoints: redirecciones y dominios personalizados'
date: 2014-05-20T23:20:16+00:00
author: javier
guid: http://javierjeronimo.es/?p=284
permalink: /2014/05/20/baches-con-google-cloud-endpoints-redirecciones-y-dominios-personalizados/
categories:
  - Cloud
  - Programación
tags:
  - app engine
  - cloud
  - cloud endpoints
  - google
  - programación
---
[Google Cloud Endpoints](https://developers.google.com/appengine/docs/java/endpoints "Google Cloud Endpoints") es una solución que ofrece App Engine de Google Cloud para desarrollar APIs REST y las bibliotecas que las consumen (iOS, Android, JavaScript). Comparado con otras tecnologías en JAVA, es similar a [Jersey](https://jersey.java.net "Jersey: RESTful Web Services in Java"), ofreciendo al igual que este un conjunto de herramientas para crear el API: definición de las URLs y métodos asociados con los recursos, tratamiento de las peticiones (parámetros), generación de respuestas, excepciones, etc. Pero también ofrece funcionalidad como la generación automática de bibliotecas para el API o un explorador HTML interactivo del API.<figure style="width: 900px" class="wp-caption aligncenter">

<img src="https://developers.google.com/appengine/docs/images/endpoints.png" alt="Google Clound Endpoints" width="900" height="422" /><figcaption class="wp-caption-text">Google Clound Endpoints</figcaption></figure> 

Obviamente, las APIs REST creadas usando Cloud Endpoints sólo se pueden desplegar en la plataforma App Engine (PaaS de Google Cloud), algo que puede ser suficiente en algunos proyectos, aunque personalmente espero que este servicio se soporte algún día en soluciones como la ofrecida por [AppScale](http://www.appscale.com "AppScale: Open source development platform modeled on Google App Engine") (implementación de App Engine usando tecnologías open-source, lo que permite desplegar en tu propio CPD una solución basada en la plataforma de Google).

Esto anterior no tiene por qué ser una limitación para una solución concreta, pero al desarrollar [Compartir Tren Mesa AVE](https://compartirtrenmesaave.com?utm_source=blog&utm_medium=article&utm_campaign=javier "Compartir Tren Mesa AVE") (una idea, un prototipo&#8230; de [Genexies Mobile](http://www.genexies.com "Genexies Mobile") implementado usando Cloud Endpoints+AppEngine+DataStore+Angular.js) me he encontrado con algunos baches en el camino.

## Baches en Google Cloud Endpoints

### Sin redirecciones 30x en Cloud Endpoints

Google Cloud Endpoints no soporta respuestas de tipo &#8220;redirección&#8221;, y punto:

> <span style="color: #222222;">HTTP 3xx codes are not supported. Use of any HTTP 3xx codes will result in an HTTP 404 response.</span>

Pues vaya. Resulta que como parte de la implementación necesitaba tener un escenario web-SSO, que en este caso concreto involucraba a Facebook. El escenario sería algo como:

  1. Acceso a URL de recurso protegido: http://mi.dominio.es/recurso/protegido 
      * Respuesta: redirección a API &#8220;login-init&#8221;
  2. Acceso a URL de &#8220;API iniciar login&#8221;: https://mi.api.dominio.es/websso/v1/facebook/login-init?volver_a=http%3A%2F%2Fmi.dominio.es%2Frecurso%2Fprotegido 
      * <span style="letter-spacing: 0.05em;">Respuesta: redirección a Facebook SSO</span>
  3. Acceso a URL[s] SSO Facebook&#8230; 
      * <span style="letter-spacing: 0.05em;">Finalmente, redirección a &#8220;URL destino después de SSO&#8221;</span>
  4. Acceso a URL de &#8220;API terminar login&#8221;: https://mi.api.dominio.es/websso/v1/facebook/login-result 
      * Respuesta: redirección.
  5. Acceso a URL de recurso protegido: http://mi.dominio.es/recurso/protegido 
      * Respuesta: 200 OK + contenido de recurso

Con Google Cloud Endpoints, el paso 2 no es factible. Ahora no recuerdo bien los motivos de tener que disponer de un &#8220;login-init&#8221; y un &#8220;login-result&#8221;, pero supongamos que hay una motivación para ello.

**Solución**: implementar el API REST usando Cloud Endpoints pero implementar el API WEB-SSO usando Jersey&#8230; ¡pues vaya!

Así lo hice finalmente, todo el API es GCE salvo la parte de WEBSSO que usa Jersey 2.7. Afortunadamente la aplicación está dividida en capas (servicios distribuidos, aplicación, infraestructura) y esto sólo suponía tener dos implementaciones en la capa de servicios distribuidos, pero reutilizar toda la lógica por debajo.

### Dominio personalizado + HTTPS + Cloud Endpoints = #fail

Todas las aplicaciones de App Engine de Google se despliegan, por defecto y gratis, bajo dominios de appspot.com. Por ejemplo: miaplicacion.appspot.com

Además, ese dominio que Google ofrece también permite tener una conexión SSL bajo https://miaplicacion.appspot.com

No está mal, pero en el caso de nuestra aplicación teníamos como requisito situarla bajo el dominio principal, en este caso app.compartirtrenmesaave.com

Pues bien, en App Engine puedes configurar un dominio personalizado. Aquí empieza el juego, estos son los pasos:

  1. [OTRO] Registra el dominio: dominio.es = x € / año
  2. [GOOGLE] Da de alta el dominio en Google Apps (ya no es gratis :P): mínimo un usuario = **40$ / año**
  3. [GOOGLE] Asocia la cuenta de Google Apps de dominio.es con la aplicación de App Engine = **0 €**
  4. [OTRO] Crea sub-dominio para asociarlo a la aplicación de App Engine: app.dominio.es (CNAME hacia Google Apps)
  5. [GOOGLE] Asocia sub-dominio con la aplicación de App Engine = **0 €**
  6. [OTRO] Compra certificado para sub-dominio de app: app.dominio.es = x € / año
  7. [GOOGLE] Activa SSL en dominio personalizado en Google Apps = **9 $ / mes** (permite tener hasta 5 dominios con SNI)
  8. [GOOGLE] Configura el certificado SSL para el dominio personalizado de la aplicación App Engine (en Google Apps).

Y ya lo tienes, podrás ver tu aplicación App Engine a través de:

  * http[s]://miaplicacion.appspot.com
  * http[s]://app.dominio.es

¿Perfecto?&#8230; No, Google Cloud Endpoints [no soporta dominios personalizados](https://code.google.com/p/googleappengine/issues/detail?id=9384 "Google Cloud Endpoints VS custom domains").

**Solución**: si, como en nuestro caso, la aplicación es web (HTML+JS + API), puedes servir la aplicación desde https://app.dominio.es pero tendrás que hacer las llamadas al API hacia https://miaplicacion.appspot.com

Y no puedes hacer más.