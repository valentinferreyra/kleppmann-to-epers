# Arquitectura de sistemas de datos

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Componer un servicio](#componer-un-servicio)
- [Fiabilidad, escalabilidad y mantenibilidad](#fiabilidad-escalabilidad-y-mantenibilidad)
- [Fallos parciales y resultados desconocidos](#fallos-parciales-y-resultados-desconocidos)
- [Detectar un fallo no es conocerlo con certeza](#detectar-un-fallo-no-es-conocerlo-con-certeza)
- [Tiempo, pausas y coordinación](#tiempo-pausas-y-coordinación)

## Componer un servicio

Una aplicación puede combinar base principal, caché e índice de búsqueda. La interfaz del servicio oculta esa composición al cliente, pero el sistema debe mantener garantías sobre el conjunto. Una escritura no termina conceptualmente con modificar un componente si otros conservan representaciones relacionadas.

El ejemplo del primer capítulo muestra que la aplicación puede asumir la sincronización de cachés e índices separados. Kleppmann presenta ese trabajo como diseño de un sistema de datos. Elegir componentes exige examinar también sus interacciones y el comportamiento ante fallos.

Referencia: cap. 1, “Thinking About Data Systems”, pp. 4-6.

## Fiabilidad, escalabilidad y mantenibilidad

La fiabilidad exige conservar el comportamiento esperado ante fallos contemplados. La escalabilidad requiere responder a cambios concretos de carga. La mantenibilidad permite operar, comprender y modificar el sistema. Una decisión puede favorecer una propiedad y encarecer otra.

El ejemplo de la cronología de Twitter muestra un cambio de trabajo entre lectura y escritura. Preparar resultados al publicar acelera lecturas, pero la distribución de seguidores puede multiplicar las escrituras. El libro describe un enfoque híbrido para tratar ese desequilibrio.

Referencias: cap. 1, “Reliability”, “Scalability” y “Maintainability”, pp. 6-22. El ejemplo de Twitter es histórico y no documenta el servicio actual.

## Fallos parciales y resultados desconocidos

En un sistema distribuido, algunos componentes pueden funcionar mientras otros dejan de responder. No hay un único estado local que permita conocer inmediatamente el estado de todos. Los nodos dependen de mensajes para enterarse de lo ocurrido.

Una solicitud puede perderse, demorarse o procesarse sin que vuelva su respuesta. Por eso, una ausencia de respuesta no demuestra que la operación no ocurrió. Reintentar requiere considerar duplicaciones y efectos ya aplicados, como se explica en [Transacciones y ACID](../04-transacciones-acid/README.md).

Referencias: cap. 8, “Faults and Partial Failures”, pp. 274-277; “Unreliable Networks”, pp. 277-280.

## Detectar un fallo no es conocerlo con certeza

Los tiempos de espera permiten sospechar que un nodo no está disponible. Una demora también puede deberse a congestión, colas o pausas del proceso. Un tiempo breve detecta rápido, pero puede retirar del servicio nodos que todavía funcionan; uno largo demora la recuperación.

El libro describe cómo una detección equivocada puede trasladar trabajo a otros nodos y contribuir a una falla en cascada. El diseño necesita tanto un mecanismo de recuperación como supuestos claros sobre demoras y capacidad.

Referencia: cap. 8, “Detecting Faults” y “Timeouts and Unbounded Delays”, pp. 280-284.

## Tiempo, pausas y coordinación

Los relojes de distintas máquinas pueden diferir y ajustarse. Un reloj de hora del día sirve para fechas; uno monotónico permite medir duración local sin retroceder por ajustes. Ninguno convierte automáticamente las observaciones de todos los nodos en un orden causal fiable.

Un proceso también puede detenerse temporalmente mientras la red y los otros procesos continúan. Al reanudarse, sus creencias sobre un liderazgo o un permiso pueden haber quedado obsoletas. Los protocolos deben limitar lo que un participante antiguo puede hacer, en lugar de confiar solamente en que reconoce su situación.

Referencias: cap. 8, “Unreliable Clocks”, pp. 287-295; “Process Pauses”, pp. 295-300; “The Truth Is Defined by the Majority”, pp. 300-304.

Esta selección proporciona fundamentos de arquitectura de datos y distribución. No prescribe una arquitectura de Spring ni reemplaza los contenidos del taller de despliegue. Las implementaciones y episodios de productos conservan su contexto de 2017.

## Continuación de la lectura

[Anterior: Bases orientadas a grafos](../08-grafos/README.md) · [Siguiente: Replicación](../10-replicacion/README.md)
