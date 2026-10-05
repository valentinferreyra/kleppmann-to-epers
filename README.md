# Kleppmann para EPERS

Guía de estudio en español técnico basada exclusivamente en el libro *Designing Data-Intensive Applications*, de Martin Kleppmann. La página de créditos identifica la primera edición, publicada en marzo de 2017. Las afirmaciones sobre productos, prestaciones y adopción se presentarán en ese contexto histórico.

## Índice general

| Orden | Tema | Alcance en el libro |
| --- | --- | --- |
| 1 | [Fundamentos de persistencia](01-fundamentos/README.md) | Cap. 1, “Thinking About Data Systems”, “Reliability”, “Scalability” y “Maintainability”. Cap. 3, fundamentos de almacenamiento. |
| 2 | [Modelo relacional y mapeo de objetos](02-modelo-relacional/README.md) | Cap. 2, “Relational Model Versus Document Model”, “The Object-Relational Mismatch”, relaciones y antecedentes de los modelos. |
| 3 | [Almacenamiento e índices](03-almacenamiento-indices/README.md) | Cap. 3, “Data Structures That Power Your Database”: índices hash, SSTables, LSM-trees, B-trees y otras estructuras de indexación. |
| 4 | [Transacciones y ACID](04-transacciones-acid/README.md) | Cap. 7, “The Slippery Concept of a Transaction”, “The Meaning of ACID” y “Single-Object and Multi-Object Operations”. |
| 5 | [Aislamiento y serializabilidad](05-aislamiento/README.md) | Cap. 7, “Weak Isolation Levels” y “Serializability”. Anomalías, aislamiento de instantánea, 2PL y SSI. |
| 6 | [Introducción a NoSQL](06-introduccion-nosql/README.md) | Cap. 2, “The Birth of NoSQL” y comparación entre modelos relacional y documental. Panorama de grafos con enlace al tema específico. |
| 7 | [Bases documentales](07-bases-documentales/README.md) | Cap. 2, representación documental, relaciones, flexibilidad del esquema, localidad y convergencia de modelos. MongoDB solamente en los ejemplos y observaciones de la edición. |
| 8 | [Bases orientadas a grafos](08-grafos/README.md) | Cap. 2, “Graph-Like Data Models”, grafos de propiedades, Cypher, consultas en SQL, triple-stores, SPARQL y Datalog. Neo4j en el alcance que presenta el libro. |
| 9 | [Arquitectura de sistemas de datos](09-arquitectura/README.md) | Cap. 1, sistemas de datos y atributos de calidad. Selección del cap. 8, fallos parciales, redes y límites de detección de fallos como prerrequisito de distribución. |
| 10 | [Replicación](10-replicacion/README.md) | Cap. 5, líderes y seguidores, replicación síncrona y asíncrona, retraso, múltiples líderes y ausencia de líder. |
| 11 | [Partición](11-particion/README.md) | Cap. 6, partición por rango y hash, distribución desigual, índices secundarios, rebalanceo y enrutamiento. |
| 12 | [Transacciones distribuidas y consistencia](12-transacciones-distribuidas/README.md) | Cap. 9, “Consistency Guarantees”, “Linearizability” y “Distributed Transactions and Consensus”. Orden y causalidad como apoyo para distinguir consenso, compromiso atómico y garantías de lectura. |
| 13 | [Persistencia para análisis de datos](13-analitica/README.md) | Cap. 3, “Transaction Processing or Analytics?”, almacenes de datos, esquemas analíticos y almacenamiento por columnas. Alcance acordado para Data Science: únicamente persistencia analítica, sin procesamiento por lotes. |
| Anexo | [Glosario](glosario/README.md) | Términos de los capítulos seleccionados, con equivalentes en inglés, definición sintética y referencias. |

