---
id: 443
title: Calidad del código de proyectos de código abierto
date: 2017-05-12T15:33:34+00:00
author: javier
guid: http://javierjeronimo.es/?p=443
permalink: /2017/05/12/calidad-del-codigo-de-proyectos-de-codigo-abierto/
categories:
  - secdevops
tags:
  - automatización
  - calidad
  - integración continua
  - programación
  - seguridad
---
Estamos emocionados con los proyectos de código abierto. Tanto, que en ocasiones ni miramos la licencia, pero ese es otro tema de debate&#8230;

¿Has comprobado alguna vez la calidad del código de un proyecto de código abierto que usas? ¿De alguna de las decenas de dependencias que instala tu npm o maven de turno? ¿De al menos la más importante? (dejo a tu elección la definición de &#8220;importante&#8221;).

Yo no.

Pero&#8230;

<!--more-->

Desde hace unos meses trabajo en un equipo de ciberseguridad, y entre lo que estoy aprendiendo y los boletines de seguridad a los que me estoy suscribiendo, cada vez tengo más miedito en el cuerpo.

Miedito porque absolutamente todos los proyectos que conozco dependen de otros de código abierto, y porque <del>aporreamos el teclado</del> programamos a una velocidad que nos hace pasar por alto muchas cosas, entre ellas la seguridad. No pienso siquiera en &#8220;programación segura&#8221;, es decir, que seas un verdadero Ninja de la programación (no un Ninja-postureo) y que no te hagan un overflow&#8230; o seamos más actuales, que no te hagan una inyección de SQL (es muy triste que en 2017 todavía estemos hablando de esto)&#8230; o te ejecuten código JavaScript en el servidor Node.js de tu API REST molona (porque te envíen un JSON malicioso que no sanitizas)&#8230;

Y lo peor de todo es que si nos tomamos la seguridad como se merece, con un buen modelo de madurez encima de la mesa (lo hay, ya haré otro artículo al respecto), pasar de cero a &#8220;algo&#8221; es casi trivial.

Repito, pasar de &#8220;no hacemos ninguna comprobación de seguridad de nuestro código&#8221; a &#8220;pasamos comprobaciones básicas en nuestro sistema de _integración casi-continua_&#8221; es&#8230; TRI-VI-AL.

# Mal de muchos, consuelo de tontos

Las barbaridades que se oyen sobre cómo trabajan en startups y no-tan-startups&#8230; vamos a ser positivos, vayamos a cómo hacerlo.

# Programar es un arte y ahora viene una máquina a decirte tal o cual&#8230;

Pero atención, usar analizadores estáticos de código, que es de lo que voy a hablar en el artículo, que son herramientas que buscan patrones en el código, que buscan la adhesión a convenciones&#8230; puede hacer algo de daño al principio. Siempre existen falsos positivos, hay reglas con las que no todos los ingenieros estamos de acuerdo (por eso debemos tener criterio), y algunas, aun cuando están justificadas, incluso escuecen.

No puedes suponer que después de varios años escribiendo código de una forma, vayas a aceptar las recomendaciones con alegria y gozo. Algunas no te gustarán. Otras incluso harán que te aflore un sentimiento de vergüenza sobre el código que has podido escribir en el pasado. Creo que eso es positivo, significa que estás aprendiendo de los errores, pero sólo lo consigues con la mente abierta. No hay nada más objetivo que la crítica (constructiva) de un algoritmo basado en evidencias y el conocimiento de muchos ingenieros.

# Comprobaciones básicas _on-premise_

Voy a centrarme sólo en aplicaciones JAVA + MAVEN, pero con algo de Google se pueden encontrar alternativas para otros lenguajes para muchos de los puntos que listo a continuación (<https://docs.sonarqube.org/display/PLUG/Plugin+Library>).

## Calidad del código: seguir convenciones

A estas alturas, no vamos a discutir este tema recurrente.

### Opción 1 &#8211; &#8220;cero&#8221; esfuerzo (SonarQube)

**Cómo**: SonarQube / SonarJava, el plugin oficialmente soportado por SonarQube para JAVA hace este tipo de comprobaciones.

Si usas MAVEN, instala SonarQube y después: <https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Maven>

**Qué**: &#8220;Coding Convention&#8221; (<https://www.sonarsource.com/why-us/products/codeanalyzers/sonarjava.html>)

**Resultado**: Comprobaciones típicas de JAVA.

### Opción 2 &#8211; Instalar otro plugin adicionalmente (SonarQube)

**Cómo**: SonarQube Checkstyle plugin (<https://github.com/checkstyle/sonar-checkstyle>)

**Qué**: Checkstyle para JAVA (<http://checkstyle.sourceforge.net/>)

**Resultado**: Las comprobaciones básicas de SonarJava y las de Checkstyle. Ooook.

## Calidad del código: evitar malos olores

Las comunicaciones entre las personas que forman una organización imitan la estructura de la organización. Del mismo modo, el código huele según el estado de ánimo del programador en cada momento, o de sus hábitos.

**Cómo**: SonarQube / SonarJava

**Qué**: 218 reglas específicas de &#8220;Code Smells&#8221; ([https://www.sonarsource.com/why-us/products/codeanalyzers/sonarjava/rules.html#Code\_Smell\_Detection](https://www.sonarsource.com/why-us/products/codeanalyzers/sonarjava/rules.html#Code_Smell_Detection))

**Resultado**: No sólo convenciones, si no estructuras y uso del lenguaje que tienen mala pinta&#8230; Oooooook.

## Calidad del código: programación segura-qué?

Hay muchas fuentes de documentación sobre seguridad (errores típicos de programación, vulnerabilidades, etc). Un [analizador estático de código](https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis) es capaz de encontrar patrones de programación insegura (somos tan predecibles los programadores&#8230;), así que&#8230;

### Opción 1 &#8211; &#8220;cero&#8221; esfuerzo (SonarQube)

**Cómo**: SonarQube / SonarJava

**Qué**: 212 reglas específicas que cubren recomendaciones de seguridad de los siguientes &#8220;estándares&#8221;

  * **CWE** (Common Weakness Enumeration): <https://cwe.mitre.org/>
  * **CWE/SANS TOP 25** Most Dangerous Software Errors: <https://www.sans.org/top25-software-errors/>
  * **OWASP Top Ten** (10 Most Critical Web Application Security Risks): [https://www.owasp.org/index.php/Category:OWASP\_Top\_Ten_Project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
  * **MISRA**: <https://www.misra.org.uk/MISRAHome/WhatisMISRA/tabid/66/Default.aspx>
  * **SEI CERT** (Software Engineering Institute / Carnegie Mellon University): <https://www.securecoding.cert.org/confluence/display>/seccode/SEI+CERT+Coding+Standards

**Resultado**: Resulta que sólo con SonarJava ya tenemos unas cuantas comprobaciones de calidad&#8230; Ooooooooook

### Opción 2 &#8211; OWASP Dependency Check (JAVA / .NET)

¿Usas alguna dependencia con vulnerabilidades conocidas? Lo primero es conocer la respuesta a esta pregunta, y lo segundo actualizar tus dependencias vulnerables a versiones sin esa vulnerabilidad CONOCIDA. Resalto lo de CONOCIDA.

**Qué**: OWASP Dependency Check

**Cómo**: herramienta integrable con MAVEN, ANT, sbt&#8230; [https://www.owasp.org/index.php/OWASP\_Dependency\_Check](https://www.owasp.org/index.php/OWASP_Dependency_Check)

**Resultado**: Otro plugin más en el flujo de compilación de MAVEN, pero te obliga a mantenerte al día&#8230;

## Comprobaciones básicas en-la-nube

Para proyectos de código abierto existen muchos recursos gratuitos en internet. Todas las startups están como locas captando usuarios, y las que ofrecen productos y servicios entorno al desarrollo de aplicaciones no son menos.

Así que si tienes un proyecto de código abierto, te puedes evitar instalar un SonarQube, un TeamCity y toda la parafernalia que los rodea. Simplemente podrás darte de alta, indicar cuáles son tus repositorios y darle al &#8220;play&#8221;:

  * SonarQube: ellos mismos ofrecen un servicio online gratuito para proyectos de código abierto, así que todas las comprobaciones de la sección anterior están disponibles.
  * CodeClimate: &#8220;Obtén revisiones de código automatizadas para cobertura de pruebas, complejidad, duplicación, seguridad, estilo y más, y mezcla con confianza&#8221;. Los filtros para navegar por los problemas encontrados son un poco pobres, pero es un primer paso.
  * Codacy: me da la impresión de que internamente usan SonarQube, la interfaz está muy cuidada y me ha gustado bastante.

Y con TravisCI completamos el ciclo&#8230;

# Volviendo al tema&#8230;

Por tanto&#8230; todo software de código abierto que se precie, sin invertir un céntimo, tiene acceso a recursos que le permitirían revisar la calidad de su código, incluso desde el punto de vista de la seguridad.

Supongamos que alguien, por ejemplo yo, está evaluando soluciones de código abierto de un tipo concreto. Estas ofrecen versiones &#8220;enterprise&#8221; (EE) que aportan valor adicional a la versión &#8220;community&#8221; (OSS), que será las que contrates cuando tomes la decisión de cuál usar.

Al haber varias empresas ofreciendo soluciones de ese tipo, bajo ese mismo modelo de negocio OSS+EE, ¿no deberían todas ellas cuidar su código? ¿no deberían aprovechar todos los recursos que hubiera disponibles? ¿no deberían demostrar públicamente que su producto EE está sostenido por una solución de código abierto de calidad comprobable, de calidad de la buena&#8230;?

Pues no. Y es una pena.

# Solución

Quieres evaluar una solución de código abierto porque te estás planteando contratar los servicios de la versión &#8220;enterprise&#8221;. Pues si ellos no han hecho bien su trabajo, al menos podrás:

  * Crear un &#8220;fork&#8221; de su repositorio community.
  * Darte tú de alta en los servicios.
  * Configurarlos hacia tus &#8220;forks&#8221;.
  * Analizar el código.
  * FIN.

&nbsp;