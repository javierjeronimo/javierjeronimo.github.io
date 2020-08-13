---
title: Mejores Prácticas de Diseño Azure Policy - Estructura lógica simple
date: 2020-07-18 16:00:00 -0000
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
> * [Mejores Prácticas de Diseño Azure Policy - Control de versiones](/2020/07/27/azure-policy-design-best-practices-3/)
> * [Mejores Prácticas de Diseño Azure Policy - Identificadores y trazabilidad](/2020/08/02/azure-policy-design-best-practices-4/)
> * [Mejores Prácticas con Azure Policy - SSDLC - Automatizar la revisión](/2020/08/13/azure-policy-ssdlc-1/)

## Tabla de Contenido

* [Introducción a Azure Policy](#introducción-a-azure-policy)
* [Algebra de Boole](#álgebra-de-boole)
* [Azure Policy - Mejor Práctica de Diseño: simplifica la lógica de la definición](#azure-policy---mejor-práctica-de-diseño-simplifica-la-lógica-de-la-definición)
* [Estructura básica de una Azure Policy](#estructura-básica-de-una-azure-policy)
* [Conclusión](#conclusión)

## Introducción a Azure Policy

La definición más corta que se me ocurre de una Azure Policy es:

> Una tecnología basada en un lenguaje JSON que permite definir reglas lógicas sobre atributos de recursos de Azure. Estas reglas lógicas (*y*, *o*, *entonces*, *para todo*, etc) ofrecen flexibilidad para implementar controles **detectivos**, **correctivos**, lo más importante, **preventivos**, sobre recursos en Azure.

Usos que se pueden hacer de Azure Policy:

* **Controles detectivos**: lo que se suele llamar *auditoría continua*, es decir, detección casi en tiempo real del cambio en un atributo de un recurso de nube a un valor no deseado. Por ejemplo, la desactivación de cifrado en tránsito en el API de una base de datos gestionada.

* **Controles correctivos**: como los **detectivos** pero además de detectar el recurso no conforme con la regla, cambian el valor del atributo para que sea el deseado.

* **Controles preventivos**: dentro de lo que se suele llamar el *shift-left* de la seguridad, es decir, llevar los controles de seguridad *a la izquierda* en el ciclo de desarrollo (más cerca del diseño, mucho antes de la fase de operación y mantenimiento de los sistemas), las Azure Policy permiten implementar controles preventivos en el momento mismo de despliegue de los recursos de nube.

Haciendo uso de la propia definición de Microsoft del ciclo de desarrollo seguro del software, las Azure Policy como controles preventivos estarían en la fase *Release*, mientras que las Azure Policy como controles detectivos y correctivos estarían en la fase *Response*:

![The Microsoft Security Development Lifecycle - Simplified](/static/img/microsoft-sdl-simplified.png "The Microsoft Security Development Lifecycle - Simplified")

Es decir, con Azure Policy podemos crear por una parte reglas que detecten y corrijan recursos **ya desplegados** que tienen atributos con valores no deseados, y por otra, reglas que **impidan** desplegar siquiera estos recursos.

Antes de continuar quiero aclarar mi postura al respecto del *shift-left* de la seguyridad y es la siguiente: detectar y opcionalmente corregir un problema en la configuración de seguridad de un sistema cuando ya está en producción es demasiado tarde y hacerlo cuando se está en el proceso mismo de despliegue es igualmente tarde y además un generador de ruido infinito. Quien no haya sufrido las prisas de un despliegue-que-llega-tarde-para-negocio que levante la mano. Y ahora imaginad que falla porque se pasaron por alto requisitos durante la fase de diseño e implementación y la Azure Policy lo deniega. Resultado: caos absoluto.

## Álgebra de Boole

Como decía en la introducción, las definiciones de *Azure Policy* que implementan nuestros controles, lo hacen con un lenguaje basado en JSON cuya sintaxis permite definir reglas lógicas.

El *álgebra de Boole* define todas las reglas que estamos habituados a usar en programación en las expresiones condicionales, que en el álgebra de Boole reciben el nombre de operadores, axiomas, teoremas...:

* *complemento*: que solemos llamar *negación*, es decir, el `NOT`.
* *suma*: que solemos llamar *o*, es decir, el `OR`.
* *producto*: que solemos llamar *y*, el `AND`.

Y todas las propiedades que más o menos aplicamos de forma automática:

* `A and B` es lo mismo que `B and A` (ley conmutativa del producto).
* `A or B` es lo mismo que `B or A` (ley conmutativa de la suma).
* `not not A` es `A` (doble negación).
* `A and (B or C)` es lo mismo que `(A and B) or (A and C)` (distributiva respecto del producto).
* `A or (B and C)` es lo mismo que `(A or B) and (A or C)` (distributiva respecto de la suma).
* ...

Y las Leyes de Morgan, que junto con lo anterior, me llevan a mi primer mejor práctica de diseño de Azure Policy:

* `not (A and B)` es lo mismo que `(not A) or (not B)`
* `not (A or B)` es lo mismo que `(not A) and (not B)`

## Azure Policy - Mejor Práctica de Diseño: simplifica la lógica de la definición

Antes de implementar una Azure Policy, debes dominar el álgebra de Boole. Quizá en tus estudios tuviste esta asignatura (en informática hay varias asignaturas: álgebra, lógica de primer orden, programación...), y si no, en matemáticas básicas también se tratan estos axiomas (pensando en sumas y productos en vez de en operadores `OR` y `AND`).

En cualquier caso, un programador con cierta experiencia en casi cualquier lenguaje, habrá sudado depurando condiciones lógicas en los programas que crea o mantiene.

¿Por qué pongo este principio como el primero?

Por lo siguiente...

## Estructura básica de una Azure Policy

Dejando de lado los detalles técnicos de la sintaxis de Azure Policy, que siempre tienen zonas oscuras como todo lenguaje de programación, la estructura de una Azure Policy, desde un punto de vista de alto nivel, podría ser algo así:

1. Tipo de recurso sobre el que quiero comprobar atributos. Por ejemplo: *Storage Account* (*STA*).
1. Filtro adicional sobre los recursos de ese tipo para aplicar el control solo a algunos. Por ejemplo, sólo las *STA* que tengan una etiqueta concreta.
1. Condición sobre el atributo que quiero comprobar. Por ejemplo, si el cifrado en tránsito está desactivado (HTTPS).
1. Efecto: detectivo/preventivo/...

Además, en muchos casos, la lógica de una Azure Policy es del tipo `si... entonces...`, con lo que la estructura sería así:

1. Si...
   1. El recurso es del tipo que busco, **y**...
   1. El recurso cumple con el filtro adicional, **y**...
   1. El atributo tiene el valor que busco
1. Entonces: el efecto debe ser...

De pronto, ya estamos usando álgebra de Boole:

`si ( TIPO y FILTRO y VALOR ) entonces EFECTO`

Esta estructura es sencilla de entender: si se cumplen las tres condiciones `TIPO` y `FILTRO` y el `VALOR` es el esperado, entonces se produce el efecto.

Sencillo, ¿no?

Consejo:
> Como en cualquier lenguaje de programación, se debe definir una convención en el diseño de Azure Policy, por ejemplo, forzando a usar siempre una misma estructura, como la definida arriba.

Consejo:
> Y como en cualquier otro lenguaje de programación, se deben implementar comprobaciones automáticas estáticas sobre la calidad de un *programa*: comprobar automáticamente que el fichero JSON que define una Azure Policy es conforme al esquema JSON de estas.

## Conclusión

Es muy importante partir de una estructura sencilla y establecer una convención de diseño al crear Azure Policy, porque las reglas lógicas se complican cuando hay varias condiciones, estas se anidan y aplicamos las leyes y axiomas presentados anteriormente. Disponer de una estructura para repasar a alto nivel una Azure Policy es fundamental para el proceso de revisión y la mantenibilidad de las mismas. Como en todo componente software, se implementa una vez pero se mantiene para siempre y hay que simplificar para que sea entendible.

En el siguiente artículo de mejores prácticas daré continuidad a estos puntos abiertos y enlazaré con la importancia de (1) tener una estructura simple y convención de diseño de Azure Policy, y (2) conocer el álgebra de Boole al diseñar estas reglas.
