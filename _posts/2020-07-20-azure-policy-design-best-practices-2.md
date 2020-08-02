---
title: Mejores Prácticas de Diseño Azure Policy - Separación de Conceptos (SoC)
date: 2020-07-20 22:00:00 -0000
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

## Tabla de Contenido

* [Convención: estructura lógica simple](#convención-estructura-lógica-simple)
* [Comprobando varios atributos de un recurso](#comprobando-varios-atributos-de-un-recurso)
* [Antes de seguir: operadores prefijos / operadores infijos y sintaxis en Azure Policy](#antes-de-seguir-operadores-prefijos--operadores-infijos-y-sintaxis-en-azure-policy)
* [Dónde quiero llegar](#dónde-quiero-llegar)
* [Azure Policy - Mejor Práctica de Diseño: Separación de Conceptos (SoC)](#azure-policy---mejor-práctica-de-diseño-separación-de-conceptos-soc)
* [Conclusión](#conclusión)
  * [Mejora para sintaxis de Azure Policy](#mejora-para-sintaxis-de-azure-policy)

En el primer artículo presenté la que desde mi punto de vista es la [primera mejor práctica al diseñar definiciones de Azure Policy](/2020/07/18/azure-policy-design-best-practices-1/): definir una estructura simple y crear una convención de diseño para implementar todas las definiciones. Vamos a retomar en el punto donde lo dejamos antes de llegar a la siguiente mejor práctica que recomiendo.

## Convención: estructura lógica simple

El lenguaje usado para definir Azure Policy es un lenguaje que usa únicamente expresiones lógicas, lo que hace que la complejidad de una definición aumente descontroladamente si no se establecen unas normas básicas de diseño.

Siguiendo con el razonamiento del artículo anterior, supongamos que tenemos definida la siguiente estructura como convención en el diseño de definiciones de Azure Policy:

`si( RECURSO y FILTRO y ATRIBUTO ) entonces EFECTO`

El `EFECTO` en las Azure Policy está relacionado con un recurso que no es conforme a nuestra norma, es decir, cuyo atributo **no** tiene el valor esperado. Vamos a actualizar la estructura para negar el atributo porque es más cercano a la realidad de las definiciones que crearemos casi siempre:

`si( RECURSO y FILTRO y (no ATRIBUTO) ) entonces EFECTO`

Por ejemplo, vamos a definir una política para comprobar que:

* Todo *Storage Account*
* Con la etiqueta `importante` con valor `si`
* Deba tener protocolo HTTPS en su API activado.

La definición, siguiendo la estructura, sería:

`si( "tipo=Storage Account" y "etiqueta importante=si" y (no "https") ) entonces EFECTO`

La estructura sigue siendo la misma, pero buscamos recursos que tengan un valor **no esperado** en el atributo. Estos recursos representan un problema y sobre ellos haremos actuar a Azure:

* Detectando: efecto será *audit*.
* Corrigiendo: efecto será alterar el valor para que sea el deseado.
* Previniendo: efecto será *denegar* el despliegue de todo recurso que no cumpla con la norma.

## Comprobando varios atributos de un recurso

Con nuestra estructura ya adaptada al proceso de *buscar recursos no conformes con nuestra norma* vamos a analizar qué sucede cuando necesitamos hacer más complejas las expresiones lógicas en ella.

En algunos casos la expresión lógica `RECURSO` o `FILTRO` no son simples comprobaciones de un atributo. Por ejemplo, para encontrar recursos de tipo *WAF*, debemos buscar los *Application Gateways* de *sku=WAF*. En este ejemplo, la condición `RECURSO` de nuestra estructura se convierte en una expresión lógica en sí misma:

`RECURSO := tipo="Application Gateway" y sku="WAF"`

Lo que hace que nuestra definición pase a ser:

`si( (tipo=AGW y sku=WAF) y FILTRO y (no ATRIBUTO) ) entonces EFECTO`

Vamos a complicar haciendo que el filtro nos permita encontrar los WAF que tengan la etiqueta `sistema` con valor `1` ó `2` (porque tenemos varios sistemas pero queremos hacer comprobaciones sólo sobre estos dos, es un ejemplo...):

`FILTRO := etiqueta sistema=1 o etiqueta sistema=2`

Completando de nuevo la definición:

`si( (tipo=AGW y sku=WAF) y (etiqueta sistema=1 o etiqueta sistema=2) y (no ATRIBUTO) ) entonces EFECTO`

Aunque en la expresión de `RECURSO` los parénteris no afecten al resultado (axiomas y teoremas del álgebra de Boole), los he mantenido para que se siga apreciando la convención en la estructura de la definición: `si( RECURSO y FILTRO y (no ATRIBUTO) ) entonces EFECTO`. Sí es importante el paréntesis en el caso de la expresión de `FILTRO` porque fijaos que dentro cambia el operador, que pasa a ser `o` porque esperamos encontrar recursos de uno u otro tipo indistintamente, y para ambos queremos hacer la comprobación.

La expresión lógica con dos condiciones `y` para encontrar el tipo de recurso, y dos condiciones `o` para quedarnos sólo con el subconjunto de recursos que nos interesan, ya se empieza a complicar.

## Antes de seguir: operadores prefijos / operadores infijos y sintaxis en Azure Policy

Las expresiones en los lenguajes de programación se pueden escribir mediante operadores prefijos, infijos y sufijos. Por ejemplo, para la suma de `A` y `B`:

* Prefijo: `+ A B`
* Infijo: `A + B`
* Sufijo: `A B +`

En los ejemplos que he puesto hasta ahora he usado la notación *infija* de los operadores, porque es la que usamos de forma natural:

`si( RECURSO y FILTRO y (no ATRIBUTO) ) entonces EFECTO`

Vamos a escribir esta misma estructura con operadores prefijos:

`si( y RECURSO FILTRO (no ATRIBUTO) ) entonces EFECTO`

O si estuviéramos en un lenguaje funcional: `si( y( RECURSO, FILTRO, no( ATRIBUTO) ) ) ...`

Y si lo representamos en forma de árbol:

* si
  * y
    * RECURSO
    * FILTRO
    * no ATRIBUTO
* entonces
  * EFECTO

Y ahora el último ejemplo para tener una representación visual de una definición más compleja: `si( (tipo=AGW y sku=WAF) y (etiqueta sistema=1 o etiqueta sistema=2) y (no ATRIBUTO) ) entonces EFECTO`

* si
  * y
    * y
      * tipo=AGW
      * sku=WAF
    * o
      * etiqueta sistema=1
      * etiqueta sistema=2
    * no
      * ATRIBUTO
* entonces
  * EFECTO

En la sintaxis de Azure Policy, los operadores son prefijos:

* Producto (`y`): se expresa con el operador prefijo `allOf` (todos deben cumplirse)
* Suma (`o`): se expresa con el operador prefijo `anyOf` (alguno debe cumplirse)
* `si`: se expresa con el operador prefijo `if`
* `entonces`: se expresa con el operador prefijo `then`
* `no`: se expresa con el operador prefijo `not`

Nuestro ejemplo queda como sigue:

* if
  * allOf
    * allOf
      * tipo=AGW
      * sku=WAF
    * anyOf
      * etiqueta sistema=1
      * etiqueta sistema=2
    * not
      * ATRIBUTO
* then
  * EFECTO

## Dónde quiero llegar

Esta historia que estoy contando tiene un objetivo: demostrar que la sintaxis de las Azure Policy, al estar basada en expresiones lógicas donde combinaremos los operadores `y`, `o`, `no`, condiciones sobre atributos y las propiedades distributivas y otras leyes del álgebra de Boole... llevan a que una definición de Azure Policy no *crezca* o *envejezca* de forma que sea facilmente mantenible.

La complejidad lógica de una Azure Policy, si no se tiene como objetivo principal la simplicidad, las hace inmanejables con el paso del tiempo.

## Azure Policy - Mejor Práctica de Diseño: Separación de Conceptos (SoC)

Es fundamental que una definición de Azure Policy implemente un sólo concepto, una sola norma que queremos validar.

Porque no es lo mismo tener una Azure Policy que tenga la estructura:

`si( RECURSO y FILTRO y (no ATRIBUTO) ) entonces EFECTO`

Que tener:

`si( RECURSO y FILTRO y (no ATRIBUTO1 o no ATRIBUTO2 o no ATRIBUTO3) ) entonces EFECTO`

Porque como hemos visto antes, ya estamos mezclando operadores anidados y en este pseudo-código se puede llegar a ver la estructura de forma sencilla, pero cuando se codifica con la sintaxis JSON de una Azure Policy, el código es más largo y menos legible.

Apliquemos entonces el principio de la separación de conceptos, y del mismo modo que separamos en ficheros/métodos/clases/módulos/componentes un sistema, hagamos lo mismo con las reglas: una regla por definición.

El último ejemplo quedaría:

* Definición primera: `si( RECURSO y FILTRO y (no ATRIBUTO1) ) entonces EFECTO`
* Definición segunda: `si( RECURSO y FILTRO y (no ATRIBUTO2) ) entonces EFECTO`
* Definición tercera: `si( RECURSO y FILTRO y (no ATRIBUTO3) ) entonces EFECTO`

Donde cada una de ellas es más legible, está en su propia definición, en su propio fichero JSON. Esto tiene grandes ventajas cuando varios programadores trabajan sobre la misma línea base, porque las posibilidades de conflicto en un fichero son menores, al tener más ficheros cada uno con una responsabilidad más específica.

## Conclusión

La segunda mejor práctica es sencilla pero algunas veces los programadores tendemos a pasarla por alto: separa conceptos. Una Azure Policy = un fichero JSON = una norma = una estructura sencilla `si( RECURSO y FILTRO y (no ATRIBUTO) ) entonces EFECTO`

No pasa nada por crear más definiciones, cada una tendrá una responsabilidad bien definida, cada una será más legible.

### Mejora para sintaxis de Azure Policy

En ingeniería del software hay una norma que consiste en no duplicar, en encapsular y reutilizar.

En el ejemplo trivial donde, para un mismo tipo de recurso queríamos comprobar tres atributos distintos (tres normas distintas para el mismo tipo de atributo), teníamos:

* Definición primera: `si( RECURSO y FILTRO y (no ATRIBUTO1) ) entonces EFECTO`
* Definición segunda: `si( RECURSO y FILTRO y (no ATRIBUTO2) ) entonces EFECTO`
* Definición tercera: `si( RECURSO y FILTRO y (no ATRIBUTO3) ) entonces EFECTO`

¿No sería bueno poder definir `RECURSO y FILTRO` en una *expresión*, importarla desde nuestras definiciones y así no duplicar código?

Algo como:

* Definición primera: `si( MI_PRODUCTO1 y (no ATRIBUTO1) ) entonces EFECTO`
* Definición segunda: `si( MI_PRODUCTO1 y (no ATRIBUTO2) ) entonces EFECTO`
* Definición tercera: `si( MI_PRODUCTO1 y (no ATRIBUTO3) ) entonces EFECTO`
* Expresión: `MI_PRODUCTO1 := RECURSO y FILTRO`

Algo como lo siguiente, donde dos definiciones incluyen una referencia a una tercera donde se define la parte común `RECURSO y FILTRO`:

* Definición primera:

  ```json
  ...
  {
    "allOf": [
      { "$ref": "#/products/sta_importante" },
      {
        "field": "location",
        "notEquals": "global"
      }
    ]
  }
  ...
  ```

* Definición segunda:

  ```json
  ...
  {
    "allOf": [
      { "$ref": "#/products/sta_importante" },
      {
        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
        "equals": "false"
      }
    ]
  }
  ...
  ```

* Siendo la parte común:

  ```json
  {
    "products": {
      "sta_importante": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "field": "tags.importante",
            "matchInsensitively": "si"
          }
        ]
      }
    }
  }
  ```

Esto permitiría modularizar las expresiones lógicas y por ende, hacer más legible y mantenible el código de las Azure Policy.

En el siguiente artículo continuaremos tirando del hilo sobre el hecho de que las definiciones de Azure Policy son componentes software en un sistema lo que lleva a mejores prácticas relacionadas: control de versiones, etc.
