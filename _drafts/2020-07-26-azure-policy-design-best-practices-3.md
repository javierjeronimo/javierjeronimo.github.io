---
title: Mejores Prácticas de Diseño Azure Policy - Control de versiones y despliegue
date: 2020-07-26 22:00:00 -0000
author: javier
categories: SecDevOps Cloud Programación
tags:
  - azure
  - policy
  - shift-left
---

> Esta es una serie de artículos con mi experiencia después de un año en un equipo de diseño e implementación de Azure Policy en el área de Ciber Seguridad Global de Grupo Santander. En estos artículos intentaré resumir las lecciones que hemos aprendido al diseñar, implementar y mantener Azure Policy:
>
> * [Mejores Prácticas de Diseño Azure Policy - Estructura lógica simple](/2020/07/18/azure-policy-design-best-practices-1/)
> * [Mejores Prácticas de Diseño Azure Policy - Separación de Conceptos (SoC)](/2020/07/20/azure-policy-design-best-practices-2/)
> * [Mejores Prácticas de Diseño Azure Policy - Control de versiones y despliegue](/2020/07/26/azure-policy-design-best-practices-3/)

## Tabla de Contenido

* [Control de versiones](#control-de-versiones)
* 
* [Conclusión](#conclusión)

En el primer artículo presenté la que desde mi punto de vista es la [primera mejor práctica al diseñar definiciones de Azure Policy](/2020/07/18/azure-policy-design-best-practices-1/): definir una estructura simple y crear una convención de diseño para implementar todas las definiciones. En el segundo di continuidad a ese principio con la separación de conceptos, teniendo con resultado de ambos cada política separada en su propia definición, sin mezclar conceptos y con una estructura igual en todas ellas para facilitar el mantenimiento.

En este artículo vamos a gestionar la complejidad derivada de tener muchas políticas: cómo versionarlas y gestionar su despliegue en producción.

## Control de versiones

Me gusta esta definición de la Wikipedia, es bastante sencilla [[referencia](https://es.wikipedia.org/wiki/Control_de_versiones)]:
> Se llama control de versiones a la gestión de los diversos cambios que se realizan sobre los elementos de algún producto o una configuración del mismo. Una versión, revisión o edición de un producto, es el estado en el que se encuentra el mismo en un momento dado de su desarrollo o modificación.

El control de versiones es fundamental y además es muy fácil de introducir en un proyecto: basta con alojar los ficheros en un repositorio que soporte control de versiones. En los proyectos software son habituales los sistemas de control de versiones como git. ¿Qué se consigue con ello? Pues básicamente no perder ficheros o cambios y tener un histórico de los mismos, además de que usando todas las capacidades de los sistemas de control de versiones y las formas de trabajo que permiten, se consigue que varias personas colaboren en la misma línea base de código de forma eficiente.

> Primer paso: alojar los ficheros en un sistema de control de versiones. Ejemplo: git.

## Objetivo del control de versiones

Con los ficheros bajo control, que es lo básico, nos tenemos que plantear quién va a usar nuestras Azure Policy, porque esto define cómo debemos implementar el control de versiones.

Si enfocamos cada política de forma individual, como un componente software atómico, con una finalidad bien definida (recordemos el principio de separación de conceptos que estamos aplicando), la política en sí misma podría estar versionada. Pero cuando me refiero a estar versionada no me refiero a que esté alojada y gestionada por un sistema de control de versiones, que lo está, si no que tenga versiones explícitas. Por ejemplo, una Azure Policy podría tener una versión propia v1.3.0, y si tras una serie de cambios la versión de esa política cambiara a v1.3.1, tendríamos información muy útil sobre los cambios que hubiera sufrido.

En cambio, podemos ver el conjunto de todas nuestras políticas como un todo, porque por ejemplo implementen un marco de controles normativo, y necesitemos hacer referencia a qué versión de dicho marco normativo correspondan. En este caso, todas las Azure Policy estarían grupadas en un todo y este conjunto estaría versionada. Por ejemplo, podríamos tener un conjunto de Azure Policy y versionarlo como v1.1.

Estos dos tipos de versionados, el individual de cada Azure Policy y el grupal de un conjunto de ellas se pueden, y deben, combinar:

* El versionado individual estaría enfocado al desarrollador de Azure Policy, o a un técnico que quiere conocer los detalles de la lógica de la política y su efecto.

  Este versionado es fundamental si tratamos cada definición de Azure Policy como un componente software. Es muy fácil de implementar, puede parecer excesivo, pero a la larga mitiga el ruido y la confusión que se generan cuando hay incidencias o peticiones de soporte con respecto al efecto que están teniendo las Azure Policy que implementamos.
  
* El versionado en conjunto estaría enfocado al auditor, que quiere comprobar el grado de cumplimiento de sus recursos de Azure con respecto a una versión concreta del conjunto de políticas.

  Sin este versionado en conjunto, un auditor no puede usar las Azure Policy como métrica de cumplimiento, pues si hacemos cambios en las políticas y el auditor no es consciente de ello, le generará incertidumbre. Con este versionado, podemos hacer cambios en definiciones individuales de políticas (por ejemplo una mejora), pero mantener el conjunto versionado sin alterar, creando una nueva versión de conjunto y dejando al auditor la elección de usar una versión de conjunto u otra para usar como métrica.
  
Una ventaja de usar combinar tipos de versionado es que se pueden desarrollar las Azure Policy de forma ágil, con cambios frecuentes, y controlando qué cambio sufren cada una de ellas individualmente, pero al mismo tiempo quitar incertidumbre al usuario que usa el conjunto de políticas como métrica de cumplimiento. Este segundo usuario necesita certidumbre, y esto muchas veces se traduce en cambios menos frecuentes. Por ejemplo, si un conjunto de Azure Policy se usan como métrica de seguridad del uso de Azure, quizá no se admitan cambios más que con una frecuencia anual en la definición de esas políticas.

## Versionado del conjunto de Azure Policy

