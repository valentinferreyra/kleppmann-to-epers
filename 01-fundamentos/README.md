# Fundamentos de persistencia

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Persistir dentro de un sistema de datos](#persistir-dentro-de-un-sistema-de-datos)
- [Fiabilidad y límites de los fallos](#fiabilidad-y-límites-de-los-fallos)
- [Carga y escalabilidad](#carga-y-escalabilidad)
- [Medir rendimiento](#medir-rendimiento)
- [Almacenamiento y mantenimiento](#almacenamiento-y-mantenimiento)

## Persistir dentro de un sistema de datos

La persistencia permite conservar datos y recuperarlos después. Kleppmann estudia esa función junto con cachés, índices de búsqueda y otros componentes porque una aplicación suele combinar herramientas con patrones de acceso diferentes. El desafío comprende volumen, complejidad y velocidad de cambio de los datos, además del costo de cómputo.

Una base ofrece una abstracción, pero elegirla exige examinar qué operaciones necesita la aplicación y qué garantías debe sostener. Si la aplicación mantiene una caché o un índice separado, también debe resolver cómo conservar la correspondencia con los datos principales. La persistencia deja de ser una llamada aislada al motor: forma parte del comportamiento del servicio.

Referencia: cap. 1, “Thinking About Data Systems”, pp. 4-6.

## Fiabilidad y límites de los fallos

El autor distingue un fallo de un componente de una falla del servicio completo. La tolerancia a fallos busca que el primero no produzca la segunda. Sus garantías tienen un alcance: ningún sistema tolera cualquier evento posible.

Los fallos de hardware motivan redundancia y recuperación. Los errores de software pueden afectar simultáneamente a varias máquinas porque comparten código y supuestos. Los errores humanos requieren interfaces comprensibles, entornos de prueba, cambios reversibles y visibilidad del estado del sistema. Agregar máquinas no elimina las causas comunes.

El libro usa Netflix Chaos Monkey como ejemplo de introducir fallos deliberados para ejercitar los mecanismos de recuperación. La idea es comprobar el comportamiento ante fallos que el sistema pretende tolerar, en lugar de asumir que la redundancia basta.

Referencia: cap. 1, “Reliability”, “Hardware Faults”, “Software Errors” y “Human Errors”, pp. 6-10. Los productos y episodios citados corresponden al contexto de 2017.

## Carga y escalabilidad

La escalabilidad describe cómo responder a un crecimiento concreto. Para discutirla, primero se caracteriza la carga: proporción de lecturas y escrituras, volumen de datos, cantidad de usuarios y distribución de las solicitudes. Una media puede ocultar casos que dominan el costo.

El ejemplo de Twitter compara reconstruir la cronología al leerla con preparar cronologías al publicar. Prepararlas abarata las lecturas, pero multiplica las escrituras según la cantidad de seguidores. El enfoque híbrido descrito en el libro exceptúa de esa distribución a usuarios con muchos seguidores y combina sus publicaciones al leer. No hay una solución única independiente del patrón de carga.

Referencia: cap. 1, “Scalability” y “Describing Load”, pp. 10-13. El ejemplo usa información de 2012 y describe el panorama del libro, no la arquitectura actual del servicio.

## Medir rendimiento

El rendimiento puede expresarse mediante operaciones procesadas por unidad de tiempo o mediante tiempos de respuesta. En este capítulo, el tiempo de respuesta incluye procesamiento, espera y demoras de red; la latencia se refiere a la espera antes de atender la solicitud.

Kleppmann trata los tiempos como una distribución. La mediana describe el centro, mientras los percentiles altos muestran los casos lentos. Si una respuesta depende de varias llamadas, una sola llamada lenta puede retrasar el resultado completo. Evaluar solamente el promedio puede ocultar ese efecto.

Escalar verticalmente agrega recursos a una máquina; escalar horizontalmente distribuye trabajo entre máquinas. Distribuir servicios sin estado suele ser más sencillo que distribuir datos con estado, porque estos últimos requieren coordinar cambios y recuperarse de fallos parciales.

Referencia: cap. 1, “Describing Performance” y “Approaches for Coping with Load”, pp. 13-18.

## Almacenamiento y mantenimiento

El libro distingue datos en memoria y mecanismos que permiten recuperarlos después de reiniciar. Un sistema en memoria puede buscar durabilidad mediante un registro en disco, instantáneas o replicación; una caché puede aceptar perder su contenido. El nombre de la herramienta no sustituye la garantía concreta. La durabilidad se desarrolla en [Transacciones y ACID](../04-transacciones-acid/README.md).

La mantenibilidad incluye operación, comprensión y adaptación. Abstracciones claras reducen complejidad accidental; monitoreo y documentación permiten operar; una organización flexible permite cambiar requisitos. Estas propiedades afectan el costo de sostener la estrategia de persistencia a lo largo del tiempo.

Referencias: cap. 3, “Keeping everything in memory”, pp. 88-90; cap. 1, “Maintainability”, pp. 18-22.

## Continuación de la lectura

[Siguiente: Modelo relacional y mapeo de objetos](../02-modelo-relacional/README.md)
