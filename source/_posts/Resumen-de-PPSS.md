---
uuid: 34e51ef0-5a80-11f0-9c8a-71c3160baf4b
title: Resumen de PPSS
date: 2025-07-06 17:45:13
tags:
---


# Caja negra

## MÉTODOS DE DISEÑO DE CAJA NEGRA
๏ Permiten seleccionar de forma SISTEMÁTICA un subconjunto de comportamientos a probar a partir de la ESPECIFICACIÓN. Se aplican en cualquier nivel de pruebas (unitarias, integración, sistema, aceptación).

๏ El conjunto de casos de prueba obtenidos será eficiente y efectivo (exactamente igual que si aplicamos métodos de diseño de caja blanca, de hecho, la estructura de la tabla obtenida será idéntica).

๏ Pueden aplicarse sin necesidad de implementar el código (podemos obtener la tabla de casos de prueba mucho antes de implementar, aunque no podremos detectar defectos sin ejecutar el código de la unidad a probar).

๏ No pueden detectar comportamientos implementados pero no especificados. 
## MÉTODO DE PARTICIONES EQUIVALENTES
๏ Necesitamos identificar previamente todas las entradas y salidas de la unidad a probar para poder aplicar correctamente el método. Es imprescindible tener claras qué entradas y salidas tendrá la unidad a probar (con este método y con cualquier otro, sea de caja blanca o caja negra), ya que de ello dependerá la estructura de la tabla de casos de prueba obtenida.

๏ Los valores posibles para cada entrada y salida de la unidad a probar se particiona en clases de equivalencia (todos los valores de una partición de entrada tendrán su imagen en la misma partición de salida. Además todas las particiones son disjuntas, y la unión de todas las particiones de una entrada tiene que contener todos los valores posibles para dicha entrada). Las particiones pueden realizarse sobre cada  ntrada por separado, o sobre agrupaciones de las entradas, (si la validez de una partición de entrada, depende de otra/s entradas, entonces hay qua agruparlas y particionar todas ellas a la vez). Cada partición (tanto de entrada como de salida, agrupadas o no) se etiqueta como válida o inválida.

๏ El objetivo es proporcionar el número mínimo de casos de prueba que garanticen que estamos probando todas y cada una de las particiones, al menos una vez, y que todas las particiones inválidas se prueban de una en una en cada caso de prueba (sólo puede haber una partición de entrada inválida en cada caso de prueba). Para ello es importante seguir un orden a la hora de combinar las particiones: primero las válidas, y después las inválidas de una en una.

๏ Cada caso de prueba será un comportamiento especificado. Puede ocurrir que la especificación sea incompleta, de forma que al hacer las particiones, no podamos determinar el resultado esperado. En ese caso definimos la partición de salida correspondiente con el valor “interrogante”. El tester NO debe completar/cambiar la especificación.

# Dependencias externas - Stubs

## DEPENDENCIAS EXTERNAS
๏ Cuando realizamos pruebas unitarias la cuestión fundamental es AISLAR la unidad a probar para garantizar que si encontramos evidencias de DEFECTOS, éstos se van a encontrar "dentro" de dicha unidad..

๏ Para aislar la unidad a probar, tenemos que controlar sus entradas indirectas, proporcionadas por sus colaboradores o dependencias externas (DOCs). Dicho control se realiza a través de los dobles de dichos colaboradores

๏ Durante la pruebas realizaremos un reemplazo controlado de las dependencias externas por sus dobles de forma que el código a probar (SUT) será IDÉNTICO al código de producción.

๏ Nuestro DOBLE siempre pertenecerá a una clase heredada de la clase que contiene nuestro DOC, o implementará su misma interfaz. y será  nyectado en la unidad a probar durante las pruebas. Hay varios tipos de dobles, concretamente usaremos STUBS 

๏ La implementación de un STUB tiene como objetivo controlar las entradas indirectas de nuestra SUT.

๏ El código del doble debe ser lo más simple posible, y también lo más genérico posible. No usaremos dobles para getters/setters ni para dependencias externas que sean de la librería estándar de java (a menos que queramos controlar el resultado que nos proporciona dicha dependencia)

๏ Para poder inyectar el doble durante las pruebas, es posible que tengamos que REFACTORIZAR nuestra SUT, para proporcionar un "seam enabling point" IMPLEMENTACIÓN DE LOS TESTS

๏ El driver debe, durante la preparación de los datos (Arrange), crear los dobles (uno por cada dependencia externa), y debe inyectar dichos dobles en la unidad a probar a través de uno de los "enabling seam points" de nuestro SUT.

๏ A continuación el driver ejecutará nuestra SUT. (Act) Las entradas directas de la SUT las proporciona el driver, mientras que las entradas indirectas llegan a nuestra unidad a través de los dobles (STUBS).

๏ Después de ejecutar la SUT, el driver comparará el resultado real obtenido con el esperado y finalmente generará un informe (Assert).

๏ Dado que el informe de pruebas dependerá exclusivamente del ESTADO resultante de la ejecución de nuestra SUT, nuestro driver estará realizando una VERIFICACIÓN BASADA EN EL ESTADO.

# Dependencias externas - Mocks

## VERIFICACIÓN BASADA EN EL ESTADO
๏ El driver de una prueba unitaria puede realizar una verificación basada en el estado resultante de la ejecución de la unidad a probar .

๏ Si la unidad a probar tiene dependencias externas que necesitemos controlar, éstas serán sustituidas por STUBS durante las pruebas. Los stubs  controlarán las entradas indirectas de nuestra SUT. Un stub no puede hacer que nuestro test falle (el resultado del test no depende de la  interacción de nuestra SUT con sus dependencias externas)

๏ Para poder usar los dobles (stubs) en nuestras pruebas, éstos tienen que poder inyectarse en nuestra SUT a través de los "enabling seam points".

๏ Podemos implementar los dobles de forma "manual" o usando el framework EasyMock. 

๏ Si implementamos los stubs "manualmente", éstos deben NECESARIAMENTE implementarse en una clase separada de la clase que contiene nuestros tests. La clase que contiene la implementación de nuestros dobles tendrá el sufijo "Stub", o "Testable" (este último caso sólo si necesitamos inyectar el stub a través de dicha clase).

๏ Si usamos el framework EasyMock, la implementación del doble necesariamente estará en cada driver. Nuestros dobles estarán implementados en objetos de tipo NiceMock. Tendremos que programar las expectativas de forma que nuestro test no falle si se invoca más veces de lo esperado al doble, o con diferentes parámetros.Finalmente necesitamos indicar al framework que hemos finalizado la programación de las expectativas (método replay()).

## VERIFICACIÓN BASADA EN EL COMPORTAMIENTO

๏ El driver de una prueba unitaria puede realizar una verificación basada en el comportamiento, de forma que no sólo se tenga en cuenta el resultado real, sino también la interacción de nuestra SUT con sus dependencias externas (cuántas veces se invocan, con qué parámetros, y en un orden determinado).

๏ Los dobles usados si realizamos una verificación basada en el comportamiento se denominan mocks. Un mock constituye un punto de observación de las salidas indirectas de nuestra SUT, y además registra la interacción del doble con el SUT. Un mock sí puede provocar que el test falle.

๏ Para poder usar los dobles (mocks), éstos tienen que poder inyectarse en nuestra SUT a través de los "enabling seam points".

๏ Para implementar los dobles usaremos la librería EasyMock. Para ello tendremos que crear el doble (de tipo Mock, o StrictMock, programar sus expectativas, indicar que el doble ya está listo para ser usado y finalmente verificar la interacción con el SUT

๏ Si nuestros dobles pertenecen a clases diferentes, necesariamente tendremos que incluirlos en un StrictControl. para que se tenga en cuenta el orden de invocaciones entre las diferentes clases.

๏ NO usaremos estructuras de control para programar las expectativas (bucles, condiciones...).

# Proyecto multimodulo 

## PROYECTOS INTELLIJ

๏ Un proyecto IntelliJ puede contener diferentes tipos de proyectos: proyectos maven, java, android, servicios rest, servicios web..., cada uno de ellos se denomina MÓDULO.

๏ Un proyecto IntelliJ no se construye ya que no contiene código, el proceso de construcción se realiza para cada
uno de sus módulos. 

## PROYECTOS MAVEN MULTIMÓDULO

๏ Un proyecto MAVEN puede ser "single" (single-maven proyect) o puede contener, a su vez, varios proyectos maven. (multimodule maven proyect), cada uno de los cuales se denomina MODULO. 

๏ El proyecto multimódulo tiene un empaquetado pom, en el que se pueden AGREGAR tantos módulos (proyectos maven) como se necesite. El mecanismo de agregación permite ejecutar un comando maven (una vez), y se ejecutará automáticamente en todos los módulos agregados, en el orden determinado por las dependencias entre ellos, e identificado por el mecanismo REACTOR de maven.

๏ Se pueden establecer adicionalmente relaciones de HERENCIA entre los módulos de un proyecto multimódulo, de esta forma pueden compartir  ropiedades, plugins, y dependencias.

๏ En un proyecto multimódulo se pueden usar ambas relaciones (herencia y agregación) o sólo una de ellas.

๏ Los módulos de un proyecto multimódulo también pueden construirse de forma individual: un módulo es un proyecto maven con un empaquetado diferente de pom: puede ser jar, war, ear..


# Integracion 

## PRUEBAS DE INTEGRACIÓN

๏ Nuestra SUT estará formado por un conjunto de unidades. El objetivo principal es detectar defectos en las INTERFACES de dicho conjunto de unidades. Recuerda que dichas unidades ya habrán sido probadas individualmente 

๏ Son pruebas dinámicas (requieren la ejecución de código), y también son pruebas de verificación (buscamos encontrar defectos en el código). Se realizan de forma INCREMENTAL siguiendo una ESTRATEGIA de integración, la cual determinará EL ORDEN en el que se deben seleccionar las unidades a integrar 

๏ Las pruebas de integración se realizan de forma incremental, añadiendo cada vez un determinado número de unidades, hasta que al final tengamos integradas TODAS ellas. En cada una de las "fases" tenemos que REPETIR TODAS las pruebas anteriores. A este proceso se le denomina PRUEBAS DE REGRESIÓN. Las pruebas de regresión provocan que las últimas unidades que se añaden al conjunto sean las MENOS probadas. Este hecho debemos tenerlo en cuenta a la hora de tomar una decisión para una estrategia de integración u otra, además del tipo de proyecto que estemos desarrollando (interfaz compleja o no, lógica de negocio compleja o no, tamaño del proyecto, riesgos,...) 

๏ Recuerda que no podemos integrar dos unidades si no hay ninguna relación entre ellas, ya que el objetivo es encontrar errores en las interfaces de dichas unidades (en la interconexión entre ellas). Por eso es indispensable tener en cuenta el diseño de nuestra aplicación (qué componentes tenemos y cómo están relacionados) a la hora de determinar la estrategia de integración (qué componentes van a integrarse cada vez y en qué orden.)

## DISEÑO DE PRUEBAS DE INTEGRACIÓN

๏ Se usan técnicas de caja negra. Básicamente se seleccionan los comportamientos a probar (en cada una de las fases de integración) siguiendo unas guías generales en función del tipo de interfaces que usemos en nuestra aplicación. Si usamos el método de particiones equivalentes debemos añadir filas adicionales proporcionando comportamientos que ejerciten los extremos de las particiones.

๏ Recuerda que si nuestro test de integración da como resultado "failure" buscaremos la causa del error en las interfaces, aunque es posible que en este nivel de pruebas, salgan a la luz defectos no detectados durante las pruebas unitarias (ya que NO podemos hacer pruebas exhaustivas, por lo tanto, nunca podremos demostrar que el código probado no tiene más defectos de los que hemos sido capaces de encontrar).

## AUTOMATIZACIÓN DE LAS PRUEBAS DE INTEGRACIÓN CON UNA BD

๏ Usaremos la librería dbunit para automatizar las pruebas de integración con una BD. Esta librería es necesaria para controlar el estado de la BD antes de la ejecución de la SUT, y comprobar el estado resultante después de dicha ejecución..

๏ Los tests de integración deben ejecutarse después de realizar todas la pruebas unitarias. Necesitamos disponer de todos los .class de nuestras unidades (antes de decidir un ORDEN de integración), por lo que los tests requerirán del empaquetado previo en un .jar de todos los .class de nuestro código. 

๏ Los tests de integración son ejecutados por el plugin failsafe. Debemos añadirlo al pom, asociando la goal integration-test a la fase con el mismo nombre. Esta goal no detiene el proceso de construcción si falla algún test. Por eso necesitamos incluir la goal verify (que asociaremos a la fase verify). De esta forma podemos detener la construcción del proyecto realicando las acciones que consideremos oportunas en la fase post-integration-test (como por ejemplo detener un servidor de aplicaciones, detener el servidor de base de datos)

๏ El plugin sql permite ejecutar scripts sql. Es interesante para incluir acciones sobre la BD durante el proceso de construcción del proyecto. Por ejemplo, "recrear" las tablas de nuestra BD antes de ejecutar los tests de integración, e inicializar dichas tablas con ciertos datos. La goal "execute" es la que se encarga de ejecutar el script, que situaremos físicamente en la carpeta "resources" de nuestro proyecto maven. Necesitaremos configurar el driver con los datos de acceso a la BD, y también asociar la goal a alguna fase previa a la fase en la que se ejecutan los tests de integración (por ejemplo pre-integration-test).

# Webdriver

## AUTOMATIZACIÓN DE PRUEBAS DE SISTEMA/ACEPTACIÓN

๏ Consideraremos el caso de que nuestra SUT sea una aplicación web, por lo que usaremos webdriver para implementar y ejecutar los casos de prueba. La aplicación estará desplegada en un servidor web o un servidor de aplicaciones, y nuestros tests accederán a nuestro SUT a través de webdriver, que será nuestro intermediario con el navegador (en este caso usaremos Chrome, junto con el driver correspondiente)

๏ Si usamos webdriver directamente en nuestros tests, éstos dependerán del código html de las páginas web de la aplicación a probar, y por lo tanto serán muy "sensibles" a cualquier cambio en el código html. Una forma de independizarlos de la interfaz web es usar el patrón PAGE OBJECT,, de forma que nuestros tests NO contendrán código webdriver (que dependa del código html). El código webdriver estará en las page objects que son las clases que dependen directamente del código html, a su vez nuestros tests dependerán de las page objects.

๏ Junto con el patrón Page Object, usaremos la clase PageFactory para crear e inicializar los atributos de una Page Object. Los valores de atributos se inyectan en el test mediante la anotación @Findby, y a través del locator correspondiente. Esta inyección se realiza de forma "lazy", es decir, los valores se inyectan justo antes de ser usados. Para que las anotaciones @Findby sean operativas, necesariamente tendremos que crear las "page objects" mediante el método estático PageFactory..initElements()

๏ Con webdriver podemos manejar las alertas generadas por la aplicación a probar, introducir esperas (implícitas y explícitas), realizar scroll en la pantalla del navegador, agrupar acciones sobre los elementos, movernos entre ventanas, y manejar cookies, entre otras cosas.

๏ El manejo de las cookies del navegador nos será útil para acortar la duración de los tests, ya que podremos evitar loguearnos en la aplicación para probar determinados escenarios.

๏ Además, podemos ejecutar nuestros tests en modo "headless" sin necesidad de abrir el navegador, por lo que
nuestros tests se ejecutarán mucho mucho más rápido.

# Jmeter

## DISEÑO DE PRUEBAS DE ACEPTACIÓN DE PROPIEDADES EMERGENTES NO FUNCIONALES

๏ Los comportamientos a probar se seleccionan teniendo en cuenta la especificación (caja negra)..

๏ En general, las propiedades emergentes no funcionales contribuyen a determinar el rendimiento (performance) de nuestra aplicación, en cuyo caso tendremos que tener en cuenta el "perfil operacional" de la misma, que refleja la frecuencia con la que un usuario usa normalmente los servicios del sistema.

๏ Es muy importante que las propiedades emergentes puedan cuantificarse, por lo que deberemos usar las métricas adecuadas que nos permitan medir dichas propiedades.

## AUTOMATIZACIÓN DE PRUEBAS DE ACEPTACIÓN

๏ Usaremos JMeter, para implementar nuestros drivers sin usar código java..

๏ Las "sentencias" de nuestros drivers NO son líneas de código escritas de forma secuencial, sino que usaremos una estructura jerárquica (árbol) en donde los nodos representan elementos de diferentes tipos (grupos de hilos, listeners, samplers, controllers...), que podremos configurar.. El orden de ejecución de dichos elementos dependerá de dónde estén situados en la jerarquía. Nuestro driver tendrá como nodo raíz un "plan de pruebas",

๏ Los "resultados" de la ejecución de nuestros test JMeter, consisten en una serie de datos calculados a partir de ciertas métricas (número de muestras tiempos de ejecución,...), que necesariamente estarán registradas en los listeners que hayamos usado para la implementación de cada driver.

๏ Los resultado obtenidos por la herramienta JMeter no son suficientes para determinar la validez o no de nuestras pruebas. Será necesario un análisis posterior, que dependerá de la propiedad emergente que queramos validar para poder cuantificarla.

# Cobertura 

## NIVELES DE COBERTURA

๏ La cobertura es una métrica que mide la extensión de nuestras pruebas. Existen diferentes variantes de esta métrica, que se pueden clasificar or niveles, de menos a más cobertura. Es importante entender cada uno de los niveles.

๏ El cálculo de esta métrica forma parte del análisis de pruebas, que se realiza después de su ejecución..

## HERRAMIENTA JaCoCo

๏ JaCoCo es una herramienta que permite analizar la cobertura de nuestras pruebas, calculando los valores de varios "contadores" (JaCoCo counters), como son: líneas de código, instrucciones, complejidad ciclomática, módulos, clases,... También se pueden calcular los valores a nivel de proyecto,, paquete, clases y métodos.

๏ JaCoCo puede usarse integrado con maven a través del plugin correspondiente. Es posible realizar una instrumentación de las clases on-the-fly, o de forma off-line. En nuestro caso usaremos la primera de las opciones.

๏ JaCoCo genera informes de cobertura tanto para los tests unitarios como para los tests de integración. Y en cualquier caso, se pueden establecer diferentes "reglas" para establecer diferentes niveles de cobertura dependiendo de los valores de los contadores, de forma que si no se cumplen las restricciones especificadas, el proceso de construcción no terminará con éxito.

๏ De igual forma, para proyectos multimódulo, se pueden generar informes "agregados", de forma que el informe agregado de cada módulo "incluye" los informes de cobertura de los módulos de los que depende.