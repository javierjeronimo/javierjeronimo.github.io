---
title: Mejores Prácticas de Diseño Azure Policy - Identificadores y trazabilidad
date: 2020-08-02 10:00:00 -0000
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
> * [Mejores Prácticas de Diseño Azure Policy - Control de versiones](/2020/07/26/azure-policy-design-best-practices-3/)
> * [Mejores Prácticas de Diseño Azure Policy - Identificadores y trazabilidad](/2020/08/02/azure-policy-design-best-practices-4/)
> * [Mejores Prácticas con Azure Policy - SSDLC - Automatizar la revisión](/2020/08/13/azure-policy-ssdlc-1/)

## Tabla de Contenido

* [El objetivo de una Azure Policy](#el-objetivo-de-una-azure-policy)
* [Trazabilidad](#trazabilidad)
* [Identificadores](#identificadores)
* [Azure Policy - Mejor Práctica de Diseño: Versionado de definiciones e iniciativas de Azure Policy](#azure-policy---mejor-práctica-de-diseño-identificadores-y-trazabilidad)
* [Conclusión](#conclusión)

En artículos anteriores vimos una serie de recomendaciones para diseñar Azure policy de forma que sean fácilmente mantenibles: diseñar todas las definiciones siguiendo una convención en su estructura lógica, que además sea simple; separando distintos conceptos en distintas definiciones y versionando las mismas individualmente y en conjunto.

Siguiendo estas recomendaciones se tendrá una base de definiciones que debería crecer de forma mantenible, siendo cada una de ellas sencilla de mantener, acotada y controlada. Pero no podemos evitar la complejidad derivada del conjunto de definiciones, que crecerá irremediablemente hasta convertirse en inmanejable.

## El objetivo de una Azure Policy

Antes de diseñar una Azure Policy debemos tener claro el objetivo que queremos con ello. Quizá queremos detectar un recurso con atributos de seguridad inválidos (ej. no cifrado en tránsito en Storage Account) o desplegado en una región que no deseamos. Esto aplica siempre que vayamos a diseñar una nueva Azure Policy.

Pero, ¿qué objetivo tenemos antes de empezar siquiera a diseñar definiciones de Azure Policy? ¿Un marco de normas de seguridad que queremos cumplir? ¿Limitar el gasto?

Sea lo que sea, algún objetivo tendremos, y ese objetivo define un alcance en dos dimensiones:

* La dimensión de los productos de Azure: sobre qué productos queremos hacer las comprobaciones.
* La dimensión de los atributos a revisar: qué queremos comprobar en cada producto.

Estas dos dimensiones definen la cantidad de trabajo que tenemos que hacer. Si las representamos en una tabla podemos ir rellenando las combinaciones para las que hemos creado una definición de Azure Policy:

| Productos       | Norma 1 | Norma 2 | Norma 3 | ... | Norma N |
|-----------------|:-------:|:-------:|:-------:|:---:|:-------:|
| Azure Key Vault |         |    X    |         |     |         |
| Storage Account |    X    |    X    |    X    |     |    X    |
| ...             |         |         |         |     |         |
| Data Factory    |         |         |         |     |    X    |

Nuestro objetivo puede ser completar la tabla, o implementar Azure Policy para una columna que representa una norma crítica y prioritaria. También podría darse el caso de que el número de filas (productos Azure) fuera pequeño o creciera cada mes porque vamos usando más y más productos.

En cualquier caso, esta matriz de dos dimensiones, las normas que queremos implementar y los productos de Azure, define y permite hacer un seguimiento del grado de progreso en la implementación de Azure Policy.

## Trazabilidad

Retomamos la buena práctica de la separación de conceptos y la aplicamos a la matriz de objetivo. Cada celda de la matriz representa una norma aplicada a un producto. Esto es un alcance muy bien definido y encaja a la perfección con la práctica de la separación de conceptos.

Vamos a suponer un alcance muy reducido a modo de ejemplo:

| Productos       | Norma 1 | Norma 2 |
|-----------------|:-------:|:-------:|
| Azure Key Vault |         |         |
| Storage Account |         |         |

En este ejemplo tenemos dos normas que queremos revisar y un conjunto de dos productos sobre los que hacer las comprobaciones. Por tanto, el número de definiciones de Azure Policy sería a priori de 4, cada una acotada a un producto y una norma concretos. Sencillo.

Como decía en la introducción, conforme esta tabla crece en columnas o filas, el número de definiciones evidentemente también lo hace, hasta que llegue un punto donde sea complicado revisar qué tenemos y qué no. Necesitamos una forma sencilla de trazar las definiciones de Azure Policy contra nuestro objetivo (celdas de la tabla).

## Identificadores

Las Azure Policy se identifican por su nombre, como ya vimos en artículos anteriores. Esto hace que por ejemplo debamos incluir el número de versión como sufijo del propio nombre de la definición. Por ejemplo: `Storage_Account_Norma_1-v1.0`

Y acabo de introducir en el ejemplo lo que quería describir en este apartado...

Si cada celda de la tabla corresponde con una norma y un producto Azure, si usáramos identificadores, el ejemplo anterior quedaría así:

| Productos | N1     | N2     |
|-----------|:------:|:------:|
| AKV       | N1-AKV | N2-AKV |
| STA       | N1-STA | N2-STA |

Vamos a tomar esos identificadores como la forma de referirnos a las celdas de la tabla, que como dije, representan cada una de ellas definiciones de Azure Policy. Tendremos por tanto, cuatro potenciales definiciones de Azure Policy en este ejemplo:

1. `N1-AKV`
1. `N1-STA`
1. `N2-AKV`
1. `N2-STA`

## Azure Policy - Mejor Práctica de Diseño: Identificadores y trazabilidad

Y con todo esto llegamos a la buena práctica de diseño de definiciones de Azure Policy: asignar identificadores a cada una, usando una convención basada en la tabla de objetivo (dimensiones de normas y productos), y usarlas en el nombre de cada definición.

Es decir, siguiendo con el ejemplo anterior, y teniendo en cuenta las mejores prácticas de esta serie de artículos, tendríamos las siguientes definiciones de políticas:

1. `N1-AKV-v1.0.0`
1. `N1-STA-v1.0.0`
1. `N2-AKV-v1.0.0`
1. `N2-STA-v1.0.0`

Pueden parecer identificadores crípticos, pero hay que tener en cuenta que `N1` y `N2` son identificadores de nuestras propias normas. Algo más cercado a la realidad podrían ser identificadores como `N1` (requisito de red 1), `D5` (requisito de datos 5), `I2` (requisito de IAM 2).

El resultado es que tendremos una lista de ficheros con las definiciones, por ejemplo:

```bash
policy-definitions/
    N1-AKV-v1.0.0-rule.json
    N1-STA-v1.0.0-rule.json
    N2-AKV-v1.0.0-rule.json
    N2-STA-v1.0.0-rule.json
```

Con este listado de ficheros es trivial construir una hoja de cálculo donde se autorrellen las celdas de la tabla de objetivo, ya que todos los ficheros siguen la convención `<id de norma>-<id de producto>-<version>-rule.json`

Ya tenemos nuestra trazabilidad y una sencilla gestión del alcance de nuestra implementación con respecto a nuestro objetivo.

> Podemos rizar el rizo y añadir una tercera dimensión: el modo de la Azure Policy.
>
> Los ficheros del ejemplo podrían quedar de la siguiente forma, donde una misma combinación norma-producto podría estar implementada en modo detectivo (*audit*) y preventivo (*deny*) al mismo tiempo, por ejemplo en ámbitos de asignación distintos:
>
> ```bash
> policy-definitions/
>     N1-AKV-AUDIT-v1.0.0-rule.json
>     N1-AKV-DENY-v1.0.0-rule.json
>     N1-STA-AUDIT-v1.0.0-rule.json
>     N2-AKV-AUDIT-v1.0.0-rule.json
>     N2-STA-AUDIT-v1.0.0-rule.json
> ```

## Conclusión

Separando conceptos de forma que cada definición de Azure Policy corresponda con un alcance reducido y muy determinado de nuestros objetivos conseguimos simplificar la gestión de un gran número de Azure Policy. Si añadimos a esto una convención en los nombres, permitimos automatizar la gestión de la trazabilidad.

Además, esta forma de identificar y nombrar definiciones de Azure Policy nos va a obligar a seguir el principio de separación de conceptos, pues si no dicha trazabilidad automática dejará de funcionar. Estamos pues en un círculo virtuoso.

El resultado es que puede crecer el número de definiciones de Azure Policy tanto como se desee, pero tendremos el control de qué tenemos y qué nos falta con respecto a nuestro objetivo. Es importante plantearse un objetivo de conjunto además del objetivo concreto de cada Azure Policy.

Esto simplifica mucho la gestión del alcance. Imaginemos este entorno:

* 10 controles de seguridad que queremos comprobar
* 40 productos de Azure, que no es nada descabellado, pongamos un ejemplo:

Generic
, ASG
, Subnet
, Routing Table
, Virtual Network
, Azure Firewall
, Load Balancer
, Network Interface
, Security Center
, DevOps
, Bastion
, Key Vault
, Private DNS
, Private Endpoint
, Recovery Vault
, Shared Image gallery
, Storage Account
, WAF
, Cognitive
, CosmosDB
, Data Bricks
, Data Factory
, Data Lake Sto g1
, HD Insights
, MSQL
, MySQL
, PostgreSQL
, Redis Cache
, Search Service
, SQL DataWarehouse
, AKS
, API Manager
, App Service
, App Serv. Env.
, Container Reg.
, EventHub
, Front Door
, Functions
, Logic App
, OpenShift cluster
, ServiceBus
, Notification HUB

En este ejemplo nos salen 400 potenciales definiciones de Azure Policy. Si bien es cierto que no todas tendrán sentido, por ejemplo, porque el requisito de cifrado en tránsito no se lo apliquemos a la red virtual, o el de IAM venga implementado de forma implítica por el plan de control de Azure, este es nuestro universo de posibles Azure Policy que debemos implementar y mantener.

Quiero usar ese número para demostrar que con pocos controles o normas que queramos comprobar, dado que el número de productos es grande, enseguida tendremos un número poco manejable de definciones de Azure Policy. Si no seguimos todas las buenas prácticas de diseño de esta serie de artículos, la gestión será un infierno.

En siguientes artículos trataré el asunto del proceso de desarrollo: integración continua, pruebas automatizadas, etc. Vamos a apoyarnos en las prácticas de diseño y convenciones de esta serie de artículos para automatizar gran parte del trabajo de revisión que se debe hacer sobre nuevas definiciones de Azure Policy.
