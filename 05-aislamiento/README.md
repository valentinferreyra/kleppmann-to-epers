# Aislamiento y serializabilidad

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Observar y modificar datos concurrentes](#observar-y-modificar-datos-concurrentes)
- [Read committed](#read-committed)
- [Aislamiento de instantánea y MVCC](#aislamiento-de-instantánea-y-mvcc)
- [Actualizaciones perdidas, write skew y fantasmas](#actualizaciones-perdidas-write-skew-y-fantasmas)
- [Implementar serializabilidad](#implementar-serializabilidad)

## Observar y modificar datos concurrentes

Los problemas de concurrencia surgen cuando una transacción lee datos que otra modifica o cuando ambas modifican datos relacionados. Los niveles de aislamiento permiten razonar sobre esa interacción. Sus nombres no siempre identifican la misma garantía en todos los motores.

Kleppmann diferencia anomalías concretas porque una protección no implica las demás. Una base transaccional puede evitar escrituras parciales y aun admitir decisiones incorrectas basadas en lecturas concurrentes.

Referencias: cap. 7, “Weak Isolation Levels”, pp. 233-234; “Repeatable read and naming confusion”, p. 242.

## Read committed

Read committed evita leer valores no confirmados y evita sobrescribir escrituras no confirmadas. El libro describe bloqueos para escritores y conservación de valores previamente confirmados para lectores. Estas técnicas permiten ocultar resultados provisionales sin garantizar que toda una transacción lea un único estado.

El ejemplo de transferencia entre dos cuentas muestra una lectura desfasada: observar una cuenta antes de la transferencia y otra después puede dar un total que nunca fue el estado consistente de ambas. Cada lectura individual puede ser de datos confirmados. El problema es la combinación de momentos.

Referencias: cap. 7, “Read Committed”, pp. 234-237; “Snapshot Isolation and Repeatable Read”, pp. 237-238.

## Aislamiento de instantánea y MVCC

El aislamiento de instantánea mantiene para una transacción una vista consistente de datos confirmados al tomar la instantánea. Una copia de seguridad o una consulta analítica prolongada evita así mezclar estados de distintos momentos.

MVCC conserva distintas versiones de los objetos para atender vistas diferentes. En el diseño descrito, los lectores no bloquean a los escritores ni los escritores a los lectores, aunque escritores sobre el mismo objeto pueden necesitar coordinación. El libro contrasta una instantánea por consulta en read committed con una por transacción en snapshot isolation.

Una instantánea consistente no garantiza serializabilidad. Protege la vista de lectura, pero las decisiones de varias transacciones pueden combinarse de manera incorrecta cuando escriben.

Referencia: cap. 7, “Snapshot Isolation and Repeatable Read”, pp. 237-242.

## Actualizaciones perdidas, write skew y fantasmas

Una actualización perdida ocurre cuando dos ciclos de lectura, modificación y escritura parten de un valor y una escritura ignora el cambio de la otra. El contador del libro ilustra cómo dos incrementos pueden producir solamente uno. Operaciones atómicas, bloqueos explícitos, detección de conflictos y compare-and-set ofrecen alternativas, con garantías que deben comprobarse según el motor.

El write skew puede involucrar objetos diferentes. En el ejemplo de los médicos de guardia, dos médicos comprueban que hay otro disponible y cada uno se retira. Ambas transacciones pueden confirmar bajo aislamiento de instantánea y dejar la guardia vacía. No sobrescriben el mismo registro; violan una condición compartida.

Un fantasma aparece cuando una escritura cambia el conjunto que satisface una consulta. En la reserva de salas del libro, comprobar que no hay reservas incompatibles y luego insertar no impide otra inserción concurrente. Si no existe una fila que bloquear, bloquear solamente las filas devueltas no resuelve la ausencia.

Referencias: cap. 7, “Preventing Lost Updates”, pp. 242-246; “Write Skew and Phantoms”, pp. 246-251.

## Implementar serializabilidad

La serializabilidad garantiza un resultado equivalente a algún orden serial, aunque la ejecución pueda ser concurrente. El libro desarrolla tres enfoques:

| Enfoque | Mecanismo y costo |
| --- | --- |
| Ejecución serial real | Evita concurrencia entre transacciones; depende de mantenerlas breves y de organizar el trabajo sin esperas interactivas. |
| Bloqueo en dos fases, 2PL | Conserva bloqueos hasta terminar. Lectores y escritores pueden bloquearse; pueden surgir interbloqueos. Los predicados o rangos también necesitan protección. |
| Aislamiento de instantánea serializable, SSI | Usa instantáneas y detección de conflictos de serialización. Puede abortar transacciones para reintentarlas en lugar de bloquear por anticipado. |

Los bloqueos de rango aproximan predicados para proteger también contra registros que podrían insertarse. SSI debe considerar dependencias entre lecturas y escrituras; no consiste solamente en detectar que dos transacciones escribieron la misma fila. La contención y los reintentos influyen en su costo.

Referencia: cap. 7, “Serializability”, “Actual Serial Execution”, “Two-Phase Locking (2PL)” y “Serializable Snapshot Isolation (SSI)”, pp. 251-266. Las valoraciones de implementaciones son las de 2017. 2PL no es 2PC: el primero trata aislamiento; el segundo, compromiso atómico distribuido.

## Continuación de la lectura

[Anterior: Transacciones y ACID](../04-transacciones-acid/README.md) · [Siguiente: Introducción a NoSQL](../06-introduccion-nosql/README.md)
