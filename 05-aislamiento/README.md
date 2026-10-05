# Aislamiento y serializabilidad

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Observar y modificar datos concurrentes](#observar-y-modificar-datos-concurrentes)
- [Read committed](#read-committed)
  - [Qué hace sucia a una lectura o escritura](#qué-hace-sucia-a-una-lectura-o-escritura)
  - [La transferencia vista en dos momentos](#la-transferencia-vista-en-dos-momentos)
- [Aislamiento de instantánea y MVCC](#aislamiento-de-instantánea-y-mvcc)
  - [Versiones y reglas de visibilidad](#versiones-y-reglas-de-visibilidad)
  - [Una vista estable no protege cualquier decisión](#una-vista-estable-no-protege-cualquier-decisión)
- [Actualizaciones perdidas, write skew y fantasmas](#actualizaciones-perdidas-write-skew-y-fantasmas)
  - [Proteger una actualización perdida](#proteger-una-actualización-perdida)
  - [Dos médicos, dos filas y una misma condición](#dos-médicos-dos-filas-y-una-misma-condición)
  - [Bloquear una ausencia](#bloquear-una-ausencia)
- [Implementar serializabilidad](#implementar-serializabilidad)
  - [Ejecución serial y duración de las transacciones](#ejecución-serial-y-duración-de-las-transacciones)
  - [2PL: esperar y proteger predicados](#2pl-esperar-y-proteger-predicados)
  - [SSI: detectar decisiones basadas en premisas obsoletas](#ssi-detectar-decisiones-basadas-en-premisas-obsoletas)

## Observar y modificar datos concurrentes

Los problemas de concurrencia surgen cuando una transacción lee datos que otra modifica o cuando ambas modifican datos relacionados. Los niveles de aislamiento permiten razonar sobre esa interacción. Sus nombres no siempre identifican la misma garantía en todos los motores.

Kleppmann diferencia anomalías concretas porque una protección no implica las demás. Una base transaccional puede evitar escrituras parciales y aun admitir decisiones incorrectas basadas en lecturas concurrentes.

Referencias: cap. 7, “Weak Isolation Levels”, pp. 233-234; “Repeatable read and naming confusion”, p. 242.

## Read committed

Read committed evita leer valores no confirmados y evita sobrescribir escrituras no confirmadas. El libro describe bloqueos para escritores y conservación de valores previamente confirmados para lectores. Estas técnicas permiten ocultar resultados provisionales sin garantizar que toda una transacción lea un único estado.

El ejemplo de transferencia entre dos cuentas muestra una lectura desfasada: observar una cuenta antes de la transferencia y otra después puede dar un total que nunca fue el estado consistente de ambas. Cada lectura individual puede ser de datos confirmados. El problema es la combinación de momentos.

Referencias: cap. 7, “Read Committed”, pp. 234-237; “Snapshot Isolation and Repeatable Read”, pp. 237-238.

### Qué hace sucia a una lectura o escritura

Una lectura sucia observa un cambio de otra transacción que todavía no confirmó. Si esa transacción aborta, el lector habrá tomado una decisión con un dato que nunca quedó confirmado. Además, si hay varias escrituras, puede ver solo una parte. Read committed oculta esos valores provisionales.

Una escritura sucia sobrescribe un cambio todavía no confirmado. El ejemplo de la venta de un auto muestra por qué importa: dos compradores modifican el anuncio y la factura, y sus escrituras pueden mezclarse de modo que el comprador del anuncio difiera del de la factura. Evitar escrituras sucias obliga a esperar la resolución de la transacción que ya está escribiendo el objeto.

Eso no equivale a proteger toda secuencia de lectura y escritura. Si un segundo escritor calculó su valor a partir de una lectura antigua y escribe después del commit del primero, ya no sobrescribe un valor provisional. Puede perder una actualización sin realizar una escritura sucia.

Referencia: cap. 7, “No dirty reads”, “No dirty writes” e “Implementing read committed”, pp. 234-237. Síntesis del ejemplo de venta, sin reproducir sus figuras.

### La transferencia vista en dos momentos

Alice tiene dos cuentas con 500 cada una. Una transacción transfiere 100 desde la segunda a la primera. Kleppmann muestra una observación que mezcla el estado anterior con el posterior:

| Momento | Qué ocurre | Qué conoce la lectura de Alice |
| --- | --- | --- |
| Antes de confirmar la transferencia | Alice lee la primera cuenta. | Ve 500. |
| La transferencia confirma | Los saldos pasan a 600 y 400. | La primera lectura ya realizada no cambia. |
| Después de confirmar | Alice lee la segunda cuenta. | Ve 400 y calcula un total de 900. |

El total real sigue siendo 1.000. La transacción de transferencia puede ser atómica y ambas lecturas pueden ser de valores confirmados. Lo que falta es una vista común para las dos consultas de Alice. Repetir la primera lectura podría devolver 600, por eso el libro relaciona el caso con lectura no repetible y read skew.

Una nueva consulta puede corregir la pantalla, pero una copia de seguridad o una verificación de integridad que conserva ese resultado puede volver permanente una observación inconsistente.

Referencia: cap. 7, “Snapshot Isolation and Repeatable Read”, pp. 237-238. Tabla de elaboración propia que sintetiza el ejemplo de las cuentas.


## Aislamiento de instantánea y MVCC

El aislamiento de instantánea mantiene para una transacción una vista consistente de datos confirmados al tomar la instantánea. Una copia de seguridad o una consulta analítica prolongada evita así mezclar estados de distintos momentos.

MVCC conserva distintas versiones de los objetos para atender vistas diferentes. En el diseño descrito, los lectores no bloquean a los escritores ni los escritores a los lectores, aunque escritores sobre el mismo objeto pueden necesitar coordinación. El libro contrasta una instantánea por consulta en read committed con una por transacción en snapshot isolation.

Una instantánea consistente no garantiza serializabilidad. Protege la vista de lectura, pero las decisiones de varias transacciones pueden combinarse de manera incorrecta cuando escriben.

Referencia: cap. 7, “Snapshot Isolation and Repeatable Read”, pp. 237-242.

### Versiones y reglas de visibilidad

MVCC conserva versiones para que lectores diferentes puedan observar estados diferentes. En la implementación descrita mediante identificadores de transacción, una versión no se vuelve visible para un lector solamente porque exista físicamente: debe cumplir sus reglas de visibilidad.

La transacción lectora ignora escrituras de transacciones que seguían en curso al tomar su instantánea, incluso si luego confirman. También ignora cambios abortados y cambios de transacciones posteriores. Para los datos de otras transacciones, conserva la perspectiva de lo confirmado cuando empezó su vista.

Borrar requiere la misma distinción: una fila puede haber sido eliminada para lectores nuevos y seguir siendo visible para uno cuya instantánea precede al borrado. Las versiones antiguas se retiran cuando ya no son necesarias para transacciones activas. Los índices deben permitir encontrar versiones y filtrar las que no corresponden a la vista.

El libro también describe B-trees copy-on-write. En lugar de sobrescribir páginas, crean páginas nuevas y una nueva raíz. Una raíz anterior identifica una instantánea que los cambios posteriores no modifican. Ambas técnicas sostienen vistas anteriores mediante mecanismos físicos diferentes.

Referencia: cap. 7, “Implementing snapshot isolation”, “Visibility rules for observing a consistent snapshot” e “Indexes and snapshot isolation”, pp. 239-242. Las implementaciones citadas corresponden a 2017.

### Una vista estable no protege cualquier decisión

Una consulta de solo lectura puede analizar una instantánea coherente sin exigir que toda transacción concurrente se ejecute serialmente. Una transacción que lee y después escribe necesita algo más: otra transacción puede modificar las condiciones que justificaron su decisión.

Por eso, snapshot isolation y serializabilidad no son sinónimos. También conviene evitar deducir garantías del nombre repeatable read. Kleppmann muestra diferencias entre motores que usan esa denominación y advierte sobre ambigüedades del estándar y de sus implementaciones.

Referencia: cap. 7, “Repeatable read and naming confusion”, p. 242; “Write Skew and Phantoms”, pp. 246-251.


## Actualizaciones perdidas, write skew y fantasmas

Una actualización perdida ocurre cuando dos ciclos de lectura, modificación y escritura parten de un valor y una escritura ignora el cambio de la otra. El contador del libro ilustra cómo dos incrementos pueden producir solamente uno. Operaciones atómicas, bloqueos explícitos, detección de conflictos y compare-and-set ofrecen alternativas, con garantías que deben comprobarse según el motor.

El write skew puede involucrar objetos diferentes. En el ejemplo de los médicos de guardia, dos médicos comprueban que hay otro disponible y cada uno se retira. Ambas transacciones pueden confirmar bajo aislamiento de instantánea y dejar la guardia vacía. No sobrescriben el mismo registro; violan una condición compartida.

Un fantasma aparece cuando una escritura cambia el conjunto que satisface una consulta. En la reserva de salas del libro, comprobar que no hay reservas incompatibles y luego insertar no impide otra inserción concurrente. Si no existe una fila que bloquear, bloquear solamente las filas devueltas no resuelve la ausencia.

Referencias: cap. 7, “Preventing Lost Updates”, pp. 242-246; “Write Skew and Phantoms”, pp. 246-251.

### Proteger una actualización perdida

El problema del contador surge cuando dos clientes leen el mismo valor, calculan su incremento y escriben el resultado. El último valor escrito no incorpora el trabajo del otro cliente. Que cada escritura sea atómica no convierte en atómico el ciclo completo.

| Alternativa del libro | Cómo trata el conflicto | Límite que debe considerarse |
| --- | --- | --- |
| Operación atómica del motor | Expresa la modificación para que el motor coordine su aplicación. | Debe existir una operación que represente el cambio requerido. |
| Bloqueo explícito | Protege el objeto antes de calcular y conservar el cambio. | Otros accesos conflictivos esperan; la transacción debe respetar el protocolo. |
| Detección de actualización perdida | Permite concurrencia, detecta conflicto y aborta para repetir el trabajo. | No todas las implementaciones lo detectan igual. |
| Compare-and-set | Escribe solo si el valor sigue siendo el esperado. | Debe comprobarse el resultado y evitar basarse inadvertidamente en una versión antigua. |

El libro advierte que un ORM puede facilitar un ciclo inseguro de lectura, modificación y escritura. La comodidad de trabajar con objetos no determina la garantía del acceso a la base.

Referencia: cap. 7, “Atomic write operations”, “Explicit locking”, “Automatically detecting lost updates” y “Compare-and-set”, pp. 243-246.

### Dos médicos, dos filas y una misma condición

Alice y Bob son los médicos de guardia del ejemplo. La condición exige que al menos uno permanezca. Cada transacción comprueba la guardia antes de retirar a su médico:

| Etapa | Transacción de Alice | Transacción de Bob |
| --- | --- | --- |
| Leer la instantánea | Cuenta dos médicos de guardia. | Cuenta dos médicos de guardia. |
| Decidir | Considera que Bob puede quedarse. | Considera que Alice puede quedarse. |
| Escribir | Retira a Alice de la guardia. | Retira a Bob de la guardia. |
| Resultado conjunto | Ambas escrituras confirmadas dejan cero médicos. | Se viola la condición compartida. |

Las escrituras no se pisan porque afectan filas distintas. Detectar únicamente conflictos entre escritores de la misma fila no basta. En un orden serial, el segundo médico encontraría solo uno de guardia y no podría retirarse.

El libro plantea bloquear los registros leídos para coordinar las decisiones o utilizar aislamiento serializable. Una restricción sobre un campo individual tampoco expresa por sí sola la regla que abarca varios médicos.

Referencia: cap. 7, “Write Skew and Phantoms” y “Characterizing write skew”, pp. 246-248. Tabla de elaboración propia basada en el ejemplo del libro.

### Bloquear una ausencia

En la reserva de salas, dos transacciones pueden comprobar que no existe una reserva incompatible e insertar después dos reservas solapadas. No hay una fila existente que represente la ausencia que comprobaron. Bloquear las filas devueltas por una consulta vacía no impide esa inserción.

Kleppmann explica materializar conflictos: crear filas que representen salas y franjas de tiempo para que las transacciones compitan por bloqueos sobre objetos concretos. Esas filas sirven para coordinar, no para almacenar las reservas. La solución traslada el mecanismo de concurrencia al modelo y exige identificar correctamente qué conflictos deben cubrirse.

El autor la presenta como último recurso frente a alternativas de aislamiento serializable. La cuestión general es proteger las condiciones de la consulta, incluso cuando incluyen objetos que todavía no existen.

Referencia: cap. 7, “More examples of write skew”, “Phantoms causing write skew” y “Materializing conflicts”, pp. 249-251.


## Implementar serializabilidad

La serializabilidad garantiza un resultado equivalente a algún orden serial, aunque la ejecución pueda ser concurrente. El libro desarrolla tres enfoques:

| Enfoque | Mecanismo y costo |
| --- | --- |
| Ejecución serial real | Evita concurrencia entre transacciones; depende de mantenerlas breves y de organizar el trabajo sin esperas interactivas. |
| Bloqueo en dos fases, 2PL | Conserva bloqueos hasta terminar. Lectores y escritores pueden bloquearse; pueden surgir interbloqueos. Los predicados o rangos también necesitan protección. |
| Aislamiento de instantánea serializable, SSI | Usa instantáneas y detección de conflictos de serialización. Puede abortar transacciones para reintentarlas en lugar de bloquear por anticipado. |

Los bloqueos de rango aproximan predicados para proteger también contra registros que podrían insertarse. SSI debe considerar dependencias entre lecturas y escrituras; no consiste solamente en detectar que dos transacciones escribieron la misma fila. La contención y los reintentos influyen en su costo.

Referencia: cap. 7, “Serializability”, “Actual Serial Execution”, “Two-Phase Locking (2PL)” y “Serializable Snapshot Isolation (SSI)”, pp. 251-266. Las valoraciones de implementaciones son las de 2017. 2PL no es 2PC: el primero trata aislamiento; el segundo, compromiso atómico distribuido.

### Ejecución serial y duración de las transacciones

Ejecutar una transacción por vez elimina la intercalación dentro del ámbito serializado. El libro relaciona la viabilidad del enfoque con datos en memoria y transacciones breves. Esperar entradas del usuario o viajes de red dentro de ese trabajo puede desperdiciar la capacidad de ejecución.

Los procedimientos almacenados permiten enviar el trabajo completo para ejecutarlo cerca de los datos. La partición puede aumentar capacidad cuando cada transacción necesita una sola parte; las operaciones que cruzan particiones requieren coordinación y pierden parte de esa ventaja. El costo depende de los accesos, no solamente de elegir un único hilo.

Referencia: cap. 7, “Actual Serial Execution”, “Encapsulating transactions in stored procedures” y “Partitioning”, pp. 252-257.

### 2PL: esperar y proteger predicados

En la variante de 2PL desarrollada por el libro, leer requiere un bloqueo compartido y escribir uno exclusivo. Varios lectores pueden compartir el acceso, pero un escritor necesita excluir accesos incompatibles. Los bloqueos se conservan hasta confirmar o abortar.

Eso permite que lectores bloqueen escritores y escritores bloqueen lectores. Una transacción larga puede formar una cola de otras que esperan. Si dos transacciones esperan recursos que la otra conserva, surge un interbloqueo; el motor puede abortar una para permitir progreso y la aplicación debe repetirla.

Para serializabilidad no alcanza con bloquear filas existentes. Los bloqueos de predicado protegen conjuntos definidos por condiciones. Los de rango de índice aproximan esos conjuntos: pueden bloquear más de lo estrictamente necesario, pero deben cubrir todos los cambios capaces de alterar la consulta. El precio es menor concurrencia en ese rango.

Referencia: cap. 7, “Two-Phase Locking (2PL)”, “Performance of two-phase locking”, “Predicate locks” e “Index-range locks”, pp. 257-261.

### SSI: detectar decisiones basadas en premisas obsoletas

SSI permite continuar sobre una instantánea y sigue dependencias entre lecturas y escrituras. El libro distingue cambios que el lector ignoró por sus reglas de visibilidad y cambios posteriores que afectan lo ya leído. Esa información permite detectar conflictos que pueden impedir un resultado serializable.

Si una decisión de escritura se basó en una condición que dejó de sostenerse por otra transacción, puede ser necesario abortar. Detectar una dependencia no implica que cualquier lectura antigua deba abortarse: una transacción de solo lectura puede continuar sobre su instantánea, y algunas transacciones concurrentes pueden abortar sin confirmar sus cambios.

El enfoque cambia esperas anticipadas por seguimiento de dependencias y posibles reintentos. Con mucha contención, repetir trabajo puede reducir el rendimiento. La comparación con 2PL depende de duración, carga y frecuencia de conflictos, no de que optimista signifique siempre más rápido.

Referencia: cap. 7, “Serializable Snapshot Isolation (SSI)”, “Pessimistic versus optimistic concurrency control”, “Decisions based on an outdated premise” y “Performance of serializable snapshot isolation”, pp. 261-266.


## Continuación de la lectura

[Anterior: Transacciones y ACID](../04-transacciones-acid/README.md) · [Siguiente: Introducción a NoSQL](../06-introduccion-nosql/README.md)
