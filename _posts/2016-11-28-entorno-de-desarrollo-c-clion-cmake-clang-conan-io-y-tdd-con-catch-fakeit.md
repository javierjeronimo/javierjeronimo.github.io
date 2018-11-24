---
id: 429
title: 'Entorno de desarrollo C++: CLion, CMake, Clang, Conan.io y TDD con Catch, FakeIt'
date: 2016-11-28T11:55:46+00:00
author: javier
layout: post
guid: http://javierjeronimo.es/?p=429
permalink: /2016/11/28/entorno-de-desarrollo-c-clion-cmake-clang-conan-io-y-tdd-con-catch-fakeit/
categories:
  - Programación
tags:
  - c++
  - catch
  - clion
  - cmake
  - conan.io
  - crow
  - dependencias
  - fakeit
  - programación
  - tdd
---
Esta semana he retomado el lenguaje C++. Tenía morriña.

Me he propuesto hacer un pequeño ejercicio para desoxidarme con el lenguaje y su entorno de bibliotecas, herramientas, etc. Cuando lo dejé, hace ya unos años, todavía se veía lejos el estandar C++-0x (finalmente se llamaría C++-11) y ya ni me acuerdo de la versión de Boost, pero bastante menor que 1.60 !!!

Hay un buen artículo sobre [las cuatro C&#8217;s del desarrollo en C++](http://blog.conan.io/2016/05/10/Programming-C++-with-the-4-Cs-Clang,-CMake,-CLion-and-Conan.html), a las que yo añado dos frameworks más que voy a presentar en este artículo:

  * **CLang**: un frontend para la familia de lenguajes C para el compilador LLVM
  * **CLion**: entorno de JetBrains. Una maravilla. Me tienen enamorado con sus productos.
  * **CMake**: recuerdo el infierno de las compilaciones multi-plataforma (VS2015 / Linux / MacOS), se acabó.
  * **Conan.io**: gestor de dependencias para C++ ==> Conan.io + CMake = MAVEN
  * **Catch**: un framework para implementar pruebas que descubrí en la fabulosa charla de Raúl Huertas en la jornada [using std::cpp de 2016](https://usingstdcpp.org/using-stdcpp-2016/programa-2016/tdd-en-cpp/) (introducción de TDD a la audiencia de profesionales C++).
  * **FakeIt**: un framework para crear de forma sencilla mocks de interfaces (estructuras con todos sus métodos virtuales puros), que es compatible con Catch, aunque en la versión disponible en Conan me funcionan juntos con calzador&#8230;

El proyecto implementa un API REST sencilla de una hipotética tienda con carrito de la compra 😉

Está colgado en mi cuenta de GitHub, acepto críticas despiadadas siempre que sean constructivas: https://github.com/javierjeronimo/ashop-cplusplus-crow

<!--more-->

# Entorno de desarrollo: Conan.io + CMake !!!

Es una maravilla que exista por fin algo como Conan.io. Simplemente. maven, npm, bower, composer, grunt, gulp&#8230; la lista de gestores de dependencias y/o del ciclo de vida del software que existen en otros lenguajes o entornos (más tirando al entorno web) sigue creciendo cada año que pasa.

Para mí, MAVEN es el mejor ejemplo, ya que es un gestor de todo el ciclo de vida del software:

  * Asiste en la creación de un proyecto: desde plantillas de proyectos (arquetipos), o desde cero. Su filosofía: convención sobre configuración. La pega, está basado en XML para los ficheros de configuración, lo que personalmente veo un infierno.
  * Permite gestionar la configuración: de qué bibliotecas depende mi software y en qué versiones concretas.
  * Existen servidores centralizados con tooooodas las dependencias que se puedan imaginar. También puedes montar tu propio servidor en la intranet con soluciones como la de JFrog-Artifactory, que por cierto, ha comprado Conan.io&#8230; sólo espero que no metan el soporte de Conan.io bajo la versión comercial como han hecho con el soporte para _docker_, _npm_ o _apt_ entre otros&#8230;
  * Permite gestionar el ciclo de vida: 
      * Preparar y compilar los ficheros fuentes
      * Crear artefactos: ejecutables, paquetes comprimidos&#8230; lo que sea
      * Desplegar artefactos: por FTP, en servidores de aplicaciones local o remotamente (Tomcat y cía).
      * Compilar las pruebas, ejecutarlas&#8230;
      * Ejecutar validaciones de calidad localmente o usando servidores remotos (cobertura, análisis estático de código&#8230;)
      * Crear documentación&#8230; incluso un sitio web con toda la información de los artefactos&#8230;
      * Todo lo anterior, gracias al flujo del ciclo de vida que implementa y a la posibilidad de anclar plugins en cada paso del mismo. Hay plugins para todo&#8230;

De los gestores que presenté más arriba, algunos sólo implementan algunos puntos de la lista anterior (por ejemplo, sólo _gestión de configuración_, más conocida como _gestión de dependencias_), y esto es lo que cubre precisamente Conan.io para el caso de C++. Pero como CMake cubre muchos de los demás puntos (la compilación, enlazado, generación de artefactos, ejecución de pruebas, etc.), pues la combinación de ambos es perfecta.

# El proyecto: API REST C++ con Crow

Algo que me esperaba al volver a C++ era la falta de frameworks para el entorno WEB. Estoy acostumbrado a trabajar en este tipo de aplicaciones con JAVA, PHP o Node.js, donde abundan frameworks gigantescos que prácticamente tienen todo lo que necesitas.

Aún así, me llevé la grata sorpresa de encontrar para C++ más frameworks de los que ya descubrí en su momento. Escogí [Crow](https://github.com/ipkn/crow) fundamentalmente por la sintaxis de configuración del controlador frontal (http://www.martinfowler.com/eaaCatalog/frontController.html):

<pre style="background-color: #2b2b2b; color: #a9b7c6; font-family: 'Menlo'; font-size: 9.0pt;"><span style="color: #908b25;">CROW_ROUTE</span>(<span style="color: #cc7832; font-weight: bold;">this</span>-&gt;<span style="color: #9373a5;">app</span><span style="color: #cc7832;">, </span><span style="color: #6a8759;">"/basket/&lt;string&gt;"</span>)
        ([<span style="color: #cc7832; font-weight: bold;">this</span>](<span style="color: #cc7832; font-weight: bold;">const </span><span style="background-color: #344134;">crow</span>::<span style="color: #b5b6e3;">request </span>&request<span style="color: #cc7832;">,
</span>                crow::<span style="color: #b5b6e3;">response </span>&response<span style="color: #cc7832;">,
</span><span style="color: #b5b6e3;">                std</span>::<span style="color: #b9bcd1;">string </span>basket_id) {

            <span style="color: #cc7832; font-weight: bold;">this</span>-&gt;controller_basket_get(request<span style="color: #cc7832;">,
                                        </span>response<span style="color: #cc7832;">,
                                        </span>basket_id)<span style="color: #cc7832;">;
</span>})<span style="color: #cc7832;">;</span></pre>

El framework es muy simple, y para el pequeño ejercicio me es suficiente, aunque hecho en falta lo siguiente. Sintaxis para definir el origen de los parámetros del controlador de la ruta, no sólo del &#8220;path&#8221; (además Crow sólo soporta parámetros posicionales): de cabeceras de la petición http, de la URL (&#8220;path&#8221;, &#8220;query&#8221;, &#8220;fragment&#8221;&#8230;). Ejemplo Jersey/JAVA [https://jersey.java.net/documentation/latest/jaxrs-resources.html#d0e2225]

Me gusta que en la definición del controlador de la ruta se vea el contrato, es decir, todos los parámetros y sólo los parámetros que usa para procesar la petición. Con frameworks en JAVA o PHP es más sencillo gracias a las anotaciones. Otros frameworks como **pistache.io** tampoco lo tienen, así que sin anotaciones me imagino que será dificil de implementar en C++ [http://pistache.io/guide/#callbacks].

Una característica que me ha gustado de Crow es el concepto Middleware que tiene, que te permite introducir intermediarios en el proceso de procesamiento de las peticiones. Esto permite introducir anclas para procesar funciones comunes a todas las rutas de un API como por ejemplo seguridad (CORS, autenticación), logging, etc.

# TDD usando Catch y FakeIt

Tengo que reconocer que he hecho TDD pocas veces en mi vida. La excusa es la de siempre: código legado dificil o muy dificil de refactorizar para hacerlo &#8220;testeable&#8221;. Pero no es más que una excusa. Las pocas veces que he seguido esta metodología me ha encantado, me he sentido seguro de los cambios que había hecho (recuerdo refactorizar un componente que hacía de cliente del sistema de cobros de Terra Networks, es decir, de varios países de LATAM&#8230;).

Catch y FakeIt son frameworks &#8220;header only&#8221;, es decir, que están implementados completamente con MACROS y plantillas. Hardcore del bueno.

La pega, los tiempos de compilación (o yo, que no he sabido montar bien el entorno de desarrollo). Además, he tenido que usar un par de directivas _#pragma_ de gcc/clang para evitar incómodas advertencias por deficiencias en su implementación:

<pre style="background-color: #2b2b2b; color: #a9b7c6; font-family: 'Menlo'; font-size: 9.0pt;"><span style="color: #bbb529;">#define </span><span style="color: #908b25;">CATCH_CONFIG_MAIN
</span><span style="color: #808080;">// WTF!!!
</span><span style="color: #bbb529;">#pragma clang diagnostic push
</span><span style="color: #bbb529;">#pragma clang diagnostic ignored "-Winconsistent-missing-override"
</span><span style="color: #bbb529;">#include</span><span style="color: #6a8759;"> &lt;catch.hpp&gt;
</span><span style="color: #bbb529;">#include</span><span style="color: #6a8759;"> &lt;fakeit.hpp&gt;
</span><span style="color: #bbb529;">#pragma clang diagnostic pop</span></pre>

Haciendo referencia de nuevo a Raúl Huertas, la metodología TDD requiere que el ciclo &#8220;guardar-compilar-ejecutar&#8221; las pruebas sea instantáneo. Si no, no le podemos pedir feedback a las pruebas tan rápido como deseamos.

<pre>$ time make BuyerUseCasesTest
[ 71%] Built target ashop_cplusplus
Scanning dependencies of target BuyerUseCasesTest
[ 85%] Building CXX object test/CMakeFiles/BuyerUseCasesTest.dir/ashop/business_logic/BuyerUseCasesTest.cpp.o
[100%] Linking CXX executable ../bin/BuyerUseCasesTest
...
...
[100%] Built target BuyerUseCasesTest

real    0m12.283s # &lt;&lt;&lt;&lt;&lt;&lt; !!!!!!!!!!!! 12 segundos :-O
user    0m11.590s
sys     0m0.611s</pre>

En el caso de este proyecto, no lo he conseguido, el tiempo de compilación de las pruebas+programa es demasiado alto. Además de otros problemas.

## Integración Catch-CLion&#8230; no existente&#8230;

El IDE no trae soporte nativo para el framework de pruebas Catch. Sí lo trae para Googletest \[https://www.jetbrains.com/clion/features/unit-testing.html\] (lo voy a probar, haré otro artículo), pero con Catch te pierdes cosas como &#8220;Ir al test&#8221;, &#8220;Implementar Test&#8221;&#8230; ejecutar los tests desde la interfaz&#8230; etc.

Así que la única forma de ejecutar las pruebas consiste en:

  * Configurar las pruebas en el proyecto CMake para que CLion sepa generar los ejecutables de las pruebas, enlazarlos con el artefacto principal del desarrollo, etc.
  * Crear configuraciones de ejecución en CLion, una por cada conjunto de pruebas individual que quieras ejecutar por separado.
  * Ejecutar la configuración activa con el atajo de teclado, pero ojo, la &#8220;configuración activa de ejecución&#8221; no es lo mismo que &#8220;el fichero de pruebas actual abierto en el entorno&#8221;, así que no es tan sencillo como navegar a la prueba y pulsar el atajo, tienes que usar la interfaz gráfica para cambiar entre configuraciones de ejecución.

Como nota importante, con la integración CLion-Googletest, puedes posicionarte en una prueba concreta y lanzarla automáticamente con un atajo de teclado de CLion.

## Problemas de integración FakeIt-Catch (instalados con conan.io)

Ambas bibliotecas están implementadas como ficheros de cabeceras. Esto es útil al montar un entorno de desarrollo si no se dispone de conan.io, porque basta con arrastrar las cabeceras a tu proyecto. Pero al estar implementadas con macros y plantillas, afectan considerablemente al tiempo de compilación.

Por otra parte, en el GitHub de FakeIt afirman que es compatible con varios frameworks de pruebas. De hecho, generan distintos artefactos para cada uno de ellos, con lo que habrán modificado la forma de hacer aserciones sobre los mocks para que sea sencillo hacerlo en el contexto de cada framework de pruebas.

Desgraciadamente, la versión de Catch que hay en conan.io no parece tener todas estas variantes del artefacto. De hecho, las aserciones sobre los mocks encajan con calzador con los &#8220;REQUIRE&#8221; de TDD de Catch, teniendo que hacer una comparación explícita con un &#8220;bool&#8221; porque implícitamente el compilador era incapaz de instanciar correctamente las plantillas.

<pre style="background-color: #2b2b2b; color: #a9b7c6; font-family: 'Menlo'; font-size: 9.0pt;"><span style="color: #908b25;">SCENARIO</span>(<span style="color: #6a8759;">"buyer use cases"</span>) {

<span style="color: #908b25;">GIVEN</span>(<span style="color: #6a8759;">"an implementation and a basket repository mock"</span>) {
    <span style="color: #b5b6e3;">Mock</span>&lt;<span style="color: #b5b6e3;">ashopi</span>::<span style="color: #b5b6e3;">BasketRepositoryBase</span>&gt; basket_repository_mock<span style="color: #cc7832;">;
</span><span style="color: #b5b6e3;">    ashopb</span>::<span style="color: #b5b6e3;">BuyerUseCases 
        </span>test_object(basket_repository_mock.get())<span style="color: #cc7832;">;
</span><span style="color: #908b25;">WHEN</span>(<span style="color: #6a8759;">"we ask with an empty basket ID"</span>)   {
    <span style="color: #cc7832; font-weight: bold;">auto </span>uuid = <span style="color: #b5b6e3;">boost</span>::<span style="color: #b5b6e3;">uuids</span>::<span style="color: #b5b6e3;">uuid</span>{}<span style="color: #cc7832;">;
</span><span style="color: #908b25;">    When</span>(<span style="color: #908b25;">Method</span>(basket_repository_mock<span style="color: #cc7832;">, 
                </span>get_by_id).<span style="color: #9876aa; font-style: italic;">Using</span>(uuid)).Return(<span style="color: #cc7832; font-weight: bold;">nullptr</span>)<span style="color: #cc7832;">;
</span><span style="color: #cc7832; font-weight: bold;">    auto </span>basket = test_object.get_basket(uuid)<span style="color: #cc7832;">;
</span><span style="color: #908b25;">THEN</span>(<span style="color: #6a8759;">"we don't get back any basket"</span>) {
    <span style="color: #908b25;">REQUIRE</span>(<span style="color: #5f8c8a;">!</span>basket)<span style="color: #cc7832;">;
</span>}

<span style="color: #908b25;">THEN</span>(<span style="color: #6a8759;">"the repository is asked for the basket at least once"</span>) {
    <span style="color: #908b25;">REQUIRE</span>(Verify<span style="color: #5f8c8a;">(</span><span style="color: #908b25;">Method</span>(basket_repository_mock<span style="color: #cc7832;">,
                          </span>get_by_id).<span style="color: #9876aa; font-style: italic;">Using</span>(uuid)<span style="color: #5f8c8a;">) == </span><span style="color: #cc7832; font-weight: bold;">true</span>)<span style="color: #cc7832;">;
</span>}
}
}
}</pre>

NOTA: último REQUIRE debería ser suficiente con escribir:

<pre style="background-color: #2b2b2b; color: #a9b7c6; font-family: 'Menlo'; font-size: 9.0pt;"><span style="color: #908b25;">REQUIRE</span>(Verify<span style="color: #5f8c8a;">(</span><span style="color: #908b25;">Method</span>(basket_repository_mock<span style="color: #cc7832;">,
                          </span>get_by_id).<span style="color: #9876aa; font-style: italic;">Using</span>(uuid)<span style="color: #5f8c8a;">)</span>);</pre>

Pero el operador &#8220;_operator bool()_&#8221; que implementa el tipo devuelto por _VerifyFunctor_ no parece encajar con la implementación de la macro _REQUIRE_:

<pre>...
[ 85%] Building CXX object ...
...
/Users/javier/.conan/data/catch/1.5.0/TyRoXx/stable/package/5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9/include/catch.hpp:1846:22: error: <span style="color: #ff0000;"><strong>no viable conversion from 'const fakeit::SequenceVerificationProgress' to 'bool'</strong></span>
        bool value = m_lhs ? true : false;
...
note: <span style="color: #ff0000;"><strong>in instantiation of member function</strong></span> 'Catch::ExpressionLhs&lt;const fakeit::SequenceVerificationProgress &&gt;::endExpression' <strong><span style="color: #ff0000;">requested here</span></strong>
<strong><span style="color: #ff0000;">                REQUIRE(Verify(Method(basket_repository_mock, get_by_id).Using(uuid)));</span></strong></pre>

Es un contratiempo menor, y seguro que creando un nuevo paquete para conan.io de la biblioteca FakeIt lo soluciono. Seguiré investigando.

# Conclusiones

Montar el entorno de desarrollo debería tomar tiempo de calidad. En mi etapa en Genexies Mobile llegamos a dedicarle tres días completos dos personas para montar un entorno de desarrollo JAVA + Spring Boot + IntelliJ + Docker para los nuevos servicios que íbamos a crear para estrangular el monolito PHP que teníamos. El resultado fue un repositorio plantilla con lo básico para poder empezar a crear un nuevo servicio.

En mi caso han sucedido varias cosas, que me esperaba y de hecho encontrarme con estos problemas era en parte el objetivo:

  * No tengo ni idea de CMake. Pero con los conocimientos que tengo de C++ y de otros entornos de desarrollo, y con la cadena &#8220;Sé lo que quiero hacer => Google => Stackoverflow&#8221;, no he tenido mayores problemas.
  * CLion tiene mucho recorrido por delante. Lo que hace, lo hace bien, pero hacen falta plugins (IntelliJ tiene decenas de plugins ya maduros del entorno JAVA y WEB&#8230;).
  * Catch y FakeIt, a pesar de los problemas, me han encantado. Mocks en C++ sin necesidad de reflexión en el lenguaje !!!!!!!!!!! :-O
  * Programar o usar bibliotecas implementadas usando MACROS o plantillas sigue siendo hardcore.
  * Adoro C++

Mi siguiente objetivo: implementar el mismo proyecto con pistache.io (API) y Googletest (que trae sus propios mocks)&#8230; y probar también Fruit (inyección de dependencias de la mano de Google).