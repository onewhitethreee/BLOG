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