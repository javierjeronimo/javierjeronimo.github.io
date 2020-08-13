---
title: Mejores Prácticas con Azure Policy - SSDLC - Automatizar la revisión
date: 2020-08-09 10:00:00 -0000
author: javier
categories: SecDevOps Cloud Programación
tags:
  - azure
  - policy
  - shift-left
  - ci/cd
---

> Esta es una serie de artículos con mi experiencia después de un año en un equipo de diseño e implementación de Azure Policy en el área de Ciber Seguridad Global de Grupo Santander. En estos artículos intentaré resumir las lecciones que hemos aprendido al diseñar, implementar y mantener Azure Policy:
>
> * [Mejores Prácticas de Diseño Azure Policy - Estructura lógica simple](/2020/07/18/azure-policy-design-best-practices-1/)
> * [Mejores Prácticas de Diseño Azure Policy - Separación de Conceptos (SoC)](/2020/07/20/azure-policy-design-best-practices-2/)
> * [Mejores Prácticas de Diseño Azure Policy - Control de versiones](/2020/07/26/azure-policy-design-best-practices-3/)
> * [Mejores Prácticas de Diseño Azure Policy - Identificadores y trazabilidad](/2020/08/02/azure-policy-design-best-practices-4/)
> * [Mejores Prácticas con Azure Policy - SSDLC - Automatizar la revisión](/2020/08/09/azure-policy-ssdlc-1/)

## Tabla de Contenido

* [Despliegue](#despliegue)
* [Revisión de convenciones](#revisión-de-convenciones)
* [Azure Policy - Mejor Práctica: Automatizar la revisión](#azure-policy---automatizar-la-revisión)
* [Conclusión](#conclusión)

Los artículos anteriores de esta serie de mejores prácticas introducían un conjunto de convenciones en el diseño de las definiciones de Azure Policy para que fueran facilmente gestionables. Una de mis obsesiones en proyectos software siempre ha sido que la información sea visible y accesible, pero además que se actualice de forma automática. Por eso adoro usar herramientas de gestión como Jira, donde puedes mostrar el grado de progreso de tareas, grupos de tareas (épicas) o versiones, sin más esfuero que el inicial de montar bien el proyecto. Las PPTs no se actualizan solas, supongo que por eso no me gustan tanto los informes en diapositivas.

En este artículo abordo ahora otra parte importante de la gestión del proyecto de Azure Policy: el despliegue y las pruebas previas. Aquí mi obsesión es la automatización. Comencemos...

## Despliegue

Desde mi punto de vista el despliegue es una actividad que siendo esencial, debería ser casi transparente. Cierto es que esto requiere un nivel de madurez que permita implementarlo de forma que funcione sin problemas, pero creo que todo el tiempo que se invierta en automatizar al completo esta actividad, redunda en una mejor calidad del software que se despliega. Porque automatizar esta actividad al 100% afecta a la forma en la que se organiza el código, la configuración del propio software que se despliega, y lo hace mejorando ambos.

Me gusta mucho esta recomendación [[ref](https://devonblog.com/continuous-delivery/6-best-practices-for-application-deployments/)]:

> Deploying to production need not to be a ceremony. Production deployments need to be routine, boring events because same process is used all along for each environment. New features you deploy to production should give you excitement but not the deployment process J. You will add unnecessary complexity if you customize deployment process for each environment.
>
> Hint: Use same repeatable and reliable way of deployments to each environments.

Y para desplegar en producción debes usar... abrimos el melón...

Yo no me lío, no quiero *scripts* complicados (despliegue *imperativo*), me gusta la idea de definir el estado deseado y que una herramienta se encargue de hacer todo lo necesario para que se cumpla (despliegue *declarativo*). Porque seamos realistas, conseguir *idempotencia* con una herramienta imperativa de despliegue no es sencillo, y ya sabemos que si un despliegue falla, lo hará cuando menos tiempo tengamos para revisarlo [[ref](https://es.wikipedia.org/wiki/Ley_de_Murphy)]:

> Todo lo que puede suceder, sucede.

He encontrado algunas referencias en Internet sobre este asunto, y en concreto usando Terraform, que me encanta. En esta serie de artículos no voy a definir cómo implementar adecuadamente un sistema de despliegue con Terraform en varios entornos, porque hay artículos muy buenos que ya lo hacen, como este: [Azure Policy as Code with Terraform Part 1](https://jloudon.com/cloud/Azure-Policy-as-Code-with-Terraform-Part-1/).

Siguiendo la guía del mismo autor en [Using GitHub Actions and Terraform for IaC Automation](https://jloudon.com/cloud/Using-GitHub-Actions-and-Terraform-for-IaC-Automation/) he creado un repositorio con un ejemplo de Azure Policy que sigue las convenciones de mi serie de artículos: <https://github.com/javierjeronimo/azure-policy-deploy-example>

* Una definición de Política Azure siguiendo las convenciones en el nombrado, versionado y contenido.
* Un sencillo módulo Terraform para el despliegue de la misma. Ojo, que quizá existan mejores formas de desplegar cientos de definiciones y asociaciones usando `for_each` y otras construcciones de Terraform.
* Un pipeline de GitHub Actions donde implemento las recomendaciones de este artículo.
* Una organización (gratuita) y un espacio de trabajo en [terraform.io](https://app.terraform.io/).

## Revisión automática

En el proceso revisión automática, como parte de CI/CD por ejemplo, debemos automatizar la mayor cantidad posible de actividades de revisión. En nuestro caso, tenemos los siguientes puntos importantes que podríamos revisar:

1. Definiciones de reglas de Azure Policy en ficheros `.json`.

   Estos ficheros JSON deben cumplir con un esquema que define su estructura. Microsoft tiene publicado el esquema que define la forma de una definición de Azure Policy: <https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json>

   Una comprobación que se puede automatizar facilmente consiste en comprobar si los ficheros `.json` cumplen con su esquema. Para ello podemos usar la herramienta [Ajv: Another JSON Schema Validator](https://github.com/ajv-validator/ajv).

1. Módulos Terraform en ficheros `.tf`.

   Estos ficheros se pueden comprobar con los propios comandos del CLI de Terraform: `validate`, `fmt`, `plan`.

1. Convención de nombres para las definiciones de Azure Policy. Fundamental para poder automatizar la extracción de informes de trazabilidad, progreso, de nuestras definiciones.

   En nuestro caso, el nombre de cada fichero JSON de Azure Policy debe seguir una convención y contener determinados campos en él. Podemos usar la herramienta [file-name-linter](https://gitlab.com/winniehell/file-name-linter) que mediante expresiones regulares permite añadir reglas de validación sobre ficheros.

Existen otras validaciones que se pueden hacer, que se salen del alcance de este artículo:

* Comprobaciones sobre ficheros *importantes* de un repositorio: licencia, fichero *code owners*, plantillas de *issue* de GitHub, etc. [Repo Linter](https://github.com/todogroup/repolinter). Básicamente, que falle si alguien los borra.
* Validación de documentación *Markdown*. [markdownlint-cli](https://github.com/igorshubovych/markdownlint-cli)

## Azure Policy - Mejor Práctica: Automatizar la revisión

Como hemos visto, hay determinadas tareas de revisión que se debe automatizar, desde las más básicas relacionadas con la sintaxis o las convenciones de formato, hasta otras como convenciones en nombres de ficheros, que pueden ser útiles como he sugerido.

Por tanto, automatizar estas comprobaciones en el pipeline de CI/CD es fundamental, para que una revisión manual se pueda dedicar a aspectos más importantes, donde una persona aporta más valor que una máquina.

Esta buena práctica consiste por tanto en automatizar la revisión de:

* Ficheros JSON con definiciones de Azure Policy con respecto al esquema que los define.
* Otros ficheros, por ejemplo, Terraform, con los correspondientes comandos del CLI.
* Convenciones de nombres de ficheros, importantes para trazabilidad de Azure Policy y mejor usabilidad al revisar grado de cumplimiento.

## Conclusión

Ya programemos en un lenguaje al uso como *C*, *JAVA* o cualquier otra *modernez*, o lo hagamos representando nuestra lógica de negocio en formatos como *YAML*, *JSON*... nuestro código fuente siempre debe cumpir con las normas de sintaxis del lenguaje en cuestión.

Para lenguajes de programación de uso general, podemos revisar automáticamente la sintaxis y otros aspectos como la complejidad, la seguridad, etc., con herramientas como [SonarQube](https://github.com/SonarSource/sonarqube).

Para herramientas que usan ficheros *XML* o *JSON* no disponemos de comprobaciones tan avanzadas, pero siempre tenemos la opción de revisar si los ficheros son conforme al esquema que los define. Esto es lo básico.

¿Por qué? Porque no tiene sentido que una persona dedique su esfuerzo a revisar algo que puede comprobar una máquina más rápido. Tenemos que centrar nuestros esfuerzos allá donde aportamos valor, y esto está del lado de la *semántica* de los programas (qué hacen) y no tanto del lado la *sintaxis* (cómo están escritos). Aunque esto segundo también requiere cuidado, por aspectos como la complejidad o seguridad. Como digo, hay que dedicar los esfuerzos (y costes) de la revisión efectuada por personas allá donde aportan valor.

Las definiciones de Azure Policy se implementan en ficheros en formato *JSON* con un esquema bien definido. Esto no asegura la semántica de nuestras políticas, pero al menos sí que estas tienen la forma correcta. Y esto elimina problemas y quebraderos de cabeza futuros (post-despliegue).

Además, dado que el nombre de una Azure Policy es su identificador, tanto en informes como en el portal web de Azure, como vimos en [otro artículo](/2020/08/02/azure-policy-design-best-practices-4/), es fundamental definir una convención. Esto lleva a requerir automatización en la revisión de estos nombres en el proceso de CI/CD.

Con estas revisiones automáticas, sabremos que no se subirán definiciones de Azure Policy incorrectas sintacticamente (lo que rompería el despliegue o la ejecución de la política), o que no tengan el nombre conforme a nuestra convención (lo que rompería nuestros informes automáticos de trazabilidad y/o progreso).

En el siguiente artículo abordaré las pruebas funcionales de regresión automáticas, con las que podremos saber cómo una política se comporta con un recurso creado ad-hoc para hacerla pasar o fallar.
