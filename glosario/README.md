# Glosario

[Índice general](../README.md) · [Introducción a NoSQL](../06-introduccion-nosql/README.md)

Las definiciones sintetizan los capítulos seleccionados de la primera edición de 2017. Cada entrada indica su sección de origen y las páginas impresas. Los nombres de garantías deben interpretarse junto con sus supuestos.

| Término | Equivalente en inglés | Definición y sección de referencia del cap. 2 |
| --- | --- | --- |
| NoSQL | NoSQL / Not Only SQL | Denominación que agrupa sistemas diferentes, sin identificar una tecnología única. “The Birth of NoSQL”, p. 29. |
| Persistencia políglota | Polyglot persistence | Coexistencia de tecnologías de almacenamiento elegidas para distintos requisitos. “The Birth of NoSQL”, p. 29. |
| Desajuste objeto-relacional | Object-relational mismatch | Diferencia entre la representación mediante objetos y la representación mediante tablas, filas y columnas. “The Object-Relational Mismatch”, pp. 29-30. |
| Mapeo objeto-relacional | Object-relational mapping / ORM | Traducción entre objetos y representación relacional. Los frameworks reducen código repetitivo sin ocultar todas las diferencias. “The Object-Relational Mismatch”, p. 30. |
| Modelo documental | Document model | Representación que permite agrupar registros anidados dentro de un documento. “The Object-Relational Mismatch”, pp. 30-32. |
| Normalización | Normalization | Organización que evita repetir información susceptible de cambio y representa referencias a los datos compartidos. “Many-to-One and Many-to-Many Relationships”, p. 33. |
| Desnormalización | Denormalization | Duplicación de información que puede reducir combinaciones, pero exige mantener coherentes las copias. “Which data model leads to simpler application code?”, p. 39. |
| Esquema en lectura | Schema-on-read | Estructura interpretada por el consumidor al leer los datos. “Schema flexibility in the document model”, pp. 39-40. |
| Esquema en escritura | Schema-on-write | Estructura explícita que la base exige a los datos escritos. “Schema flexibility in the document model”, pp. 39-40. |
| Localidad de datos | Data locality | Almacenamiento conjunto de datos relacionados que puede beneficiar su acceso conjunto. “Data locality for queries”, p. 41. |
| Camino de acceso | Access path | Recorrido para acceder a registros. En CODASYL lo seguía la aplicación; en el modelo relacional el optimizador decide el acceso para la consulta. “Are Document Databases Repeating History?”, pp. 37-38. |
| Grafo | Graph | Modelo con vértices que representan entidades y aristas que representan conexiones. “Graph-Like Data Models”, p. 49. |

## Términos de almacenamiento, transacciones y distribución

| Término | Equivalente en inglés | Definición | Referencia |
| --- | --- | --- | --- |
| Fiabilidad | Reliability | Continuidad del comportamiento esperado ante los fallos contemplados. | Cap. 1, “Reliability”, pp. 6-10. |
| Escalabilidad | Scalability | Capacidad de afrontar un crecimiento especificado de carga y recursos. | Cap. 1, “Scalability”, pp. 10-18. |
| Mantenibilidad | Maintainability | Facilidad para operar, comprender y modificar un sistema. | Cap. 1, “Maintainability”, pp. 18-22. |
| Índice secundario | Secondary index | Estructura para localizar registros mediante campos distintos de la clave primaria. | Cap. 3, “Other Indexing Structures”, pp. 85-87. |
| Compactación | Compaction | Proceso que descarta versiones obsoletas y puede combinar segmentos de almacenamiento. | Cap. 3, “Hash Indexes”, pp. 73-76. |
| SSTable | Sorted String Table | Segmento de pares clave-valor ordenados por clave. | Cap. 3, “SSTables and LSM-Trees”, pp. 76-79. |
| Memtable | Memtable | Estructura ordenada en memoria que acumula escrituras antes de volcarse a un segmento. | Cap. 3, “Constructing and maintaining SSTables”, pp. 78. |
| LSM-tree | Log-Structured Merge-Tree | Familia de estructuras que acumulan cambios y combinan segmentos ordenados. | Cap. 3, “SSTables and LSM-Trees”, pp. 76-79. |
| B-tree | B-tree | Árbol de búsqueda por claves ordenadas organizado en páginas. | Cap. 3, “B-Trees”, pp. 79-83. |
| WAL | Write-ahead log | Registro persistente previo a cambios en páginas, usado para recuperación. | Cap. 3, “Making B-trees reliable”, pp. 82. |
| Amplificación de escritura | Write amplification | Varias escrituras físicas provocadas por una escritura lógica. | Cap. 3, “Advantages of LSM-trees”, pp. 84. |
| Atomicidad | Atomicity | Garantía de que una transacción abortada no conserva solamente una parte de sus escrituras. | Cap. 7, “Atomicity”, pp. 223-224. |
| Consistencia de ACID | Consistency | Preservación de las invariantes definidas por la aplicación y las restricciones aplicables. | Cap. 7, “Consistency”, pp. 224-225. |
| Durabilidad | Durability | Conservación de escrituras confirmadas ante los fallos cubiertos por la garantía. | Cap. 7, “Durability”, pp. 226-228. |
| Aislamiento | Isolation | Garantías sobre la interacción y las observaciones de transacciones concurrentes. | Cap. 7, “Isolation”, pp. 225-226. |
| Actualización perdida | Lost update | Cambio sobrescrito por otra operación que partió de un estado anterior y no lo incorporó. | Cap. 7, “Preventing Lost Updates”, pp. 242-246. |
| Aislamiento de instantánea | Snapshot isolation | Lecturas de una transacción sobre una vista consistente de datos confirmados. | Cap. 7, “Snapshot Isolation and Repeatable Read”, pp. 237-242. |
| MVCC | Multi-version concurrency control | Conservación de versiones de datos para atender lecturas de diferentes instantáneas. | Cap. 7, “Implementing snapshot isolation”, pp. 239-242. |
| Write skew | Write skew | Anomalía donde decisiones concurrentes sobre datos compartidos producen escrituras que violan una condición, incluso sobre objetos diferentes. | Cap. 7, “Write Skew and Phantoms”, pp. 246-251. |
| Fantasma | Phantom | Cambio del conjunto que satisface una consulta debido a una escritura de otra transacción. | Cap. 7, “Phantoms causing write skew”, pp. 250-251. |
| Serializabilidad | Serializability | Efecto equivalente a algún orden de ejecución serial de las transacciones. | Cap. 7, “Serializability”, pp. 251-266. |
| 2PL | Two-phase locking | Mecanismo de aislamiento serializable mediante adquisición y conservación de bloqueos, con protección de predicados o rangos. | Cap. 7, “Two-Phase Locking (2PL)”, pp. 257-261. |
| SSI | Serializable snapshot isolation | Mecanismo que combina instantáneas y detección de conflictos para permitir solamente confirmaciones serializables. | Cap. 7, “Serializable Snapshot Isolation (SSI)”, pp. 261-266. |
| Replicación | Replication | Conservación de copias del mismo dato en varias máquinas. | Cap. 5, “Replication”, pp. 151-152. |
| Failover | Failover | Reemplazo de un líder y reconfiguración para continuar el servicio. | Cap. 5, “Leader failure: Failover”, pp. 157-158. |
| Consistencia eventual | Eventual consistency | Convergencia de réplicas una vez propagados los cambios, sin plazo máximo implícito. | Cap. 5, “Problems with Replication Lag”, pp. 161-162. |
| Quorum de lectura y escritura | Read/write quorum | Conjuntos de respuestas requeridas, cuya intersección depende de los parámetros y supuestos de replicación. | Cap. 5, “Quorums for reading and writing”, pp. 179-181. |
| Tombstone | Tombstone | Marca de borrado que evita recuperar un valor eliminado al combinar versiones. | Cap. 5, “Merging concurrently written values”, pp. 190-191. |
| Vector de versión | Version vector | Información de versiones por réplica para rastrear dependencias entre cambios. | Cap. 5, “Version vectors”, pp. 191. |
| Partición de datos | Partitioning / sharding | División deliberada de un conjunto de datos en partes. | Cap. 6, “Partitioning”, pp. 199-200. |
| Punto caliente | Hot spot | Concentración de solicitudes que sobrecarga una parte del sistema. | Cap. 6, “Skewed Workloads and Relieving Hot Spots”, pp. 205-206. |
| Scatter/gather | Scatter/gather | Distribución de una consulta entre particiones y reunión de sus resultados. | Cap. 6, “Partitioning Secondary Indexes by Document”, pp. 206-208. |
| Partición de red | Network partition | Interrupción de comunicación entre partes de un sistema distribuido. | Cap. 9, “The CAP theorem”, pp. 337-338. |
| Linealizabilidad | Linearizability | Comportamiento donde cada operación parece actuar en un punto entre invocación y respuesta, respetando el orden real de operaciones no superpuestas. | Cap. 9, “Linearizability”, pp. 324-330. |
| Causalidad | Causality | Dependencia por la que una operación conoce, depende de o deriva de otra. | Cap. 9, “Ordering and Causality”, pp. 339-343. |
| 2PC | Two-phase commit | Protocolo de compromiso atómico distribuido con preparación y decisión del coordinador. | Cap. 9, “Atomic Commit and Two-Phase Commit (2PC)”, pp. 354-359. |
| Consenso | Consensus | Acuerdo sobre una propuesta con propiedades de seguridad y progreso bajo supuestos definidos. | Cap. 9, “Fault-Tolerant Consensus”, pp. 364-370. |
| OLTP | Online transaction processing | Patrón de operaciones habituales con acceso selectivo y cambios acotados. | Cap. 3, “Transaction Processing or Analytics?”, pp. 90-91. |
| OLAP | Online analytic processing | Patrón de consultas analíticas que recorren muchos datos y calculan agregados. | Cap. 3, “Transaction Processing or Analytics?”, pp. 90-91. |
| ETL | Extract-transform-load | Extracción, transformación y carga de datos hacia un almacén analítico. | Cap. 3, “Data Warehousing”, pp. 91-93. |
| Esquema estrella | Star schema | Modelo analítico con hechos relacionados con dimensiones. | Cap. 3, “Stars and Snowflakes: Schemas for Analytics”, pp. 93-95. |
| Vista materializada | Materialized view | Resultado de una consulta almacenado y mantenido para reutilizarlo. | Cap. 3, “Aggregation: Data Cubes and Materialized Views”, pp. 101-103. |
