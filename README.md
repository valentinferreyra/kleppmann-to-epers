# Kleppmann para EPERS

Guía de estudio en español técnico basada exclusivamente en el libro *Designing Data-Intensive Applications*, de Martin Kleppmann. La página de créditos identifica la primera edición, publicada en marzo de 2017. Las afirmaciones sobre productos, prestaciones y adopción se presentarán en ese contexto histórico.

## Criterios de escritura

- Redactar síntesis originales y fieles. No reproducir ni traducir extensamente capítulos.
- Usar únicamente el PDF como fuente de contenido técnico. La referencia de learning-notes inspira la jerarquía, sin aportar notas ni explicaciones.
- Sintetizar los ejemplos del libro, sin agregar ejemplos propios ni tutoriales de herramientas.
- Agregar diagramas propios de modelos, arquitectura, secuencia o flujo cuando ayuden a explicar el contenido. Cada diagrama indicará las secciones del libro que sintetiza y las simplificaciones realizadas. No se copiarán las figuras originales ni se agregarán componentes o comportamientos ajenos a la fuente.
- Referenciar capítulo y sección con su título original. Las páginas citadas serán las impresas en el libro, no el contador del PDF.
- Explicar las diferencias y los compromisos que presenta el autor. No actualizar silenciosamente las afirmaciones de 2017.
- Mantener un README por tema, con índice local, referencias y enlaces al índice general y a los temas anterior y siguiente.

## Índice general

| Orden | Tema | Alcance en el libro |
| --- | --- | --- |
| 1 | [Fundamentos de persistencia](01-fundamentos/README.md) | Cap. 1, “Thinking About Data Systems”, “Reliability”, “Scalability” y “Maintainability”. Cap. 3, fundamentos de almacenamiento. |
| 2 | [Modelo relacional y mapeo de objetos](02-modelo-relacional/README.md) | Cap. 2, “Relational Model Versus Document Model”, “The Object-Relational Mismatch”, relaciones y antecedentes de los modelos. |
| 3 | [Almacenamiento e índices](03-almacenamiento-indices/README.md) | Cap. 3, “Data Structures That Power Your Database”: índices hash, SSTables, LSM-trees, B-trees y otras estructuras de indexación. |
| 4 | [Transacciones y ACID](04-transacciones-acid/README.md) | Cap. 7, “The Slippery Concept of a Transaction”, “The Meaning of ACID” y “Single-Object and Multi-Object Operations”. |
| 5 | [Aislamiento y serializabilidad](05-aislamiento/README.md) | Cap. 7, “Weak Isolation Levels” y “Serializability”. Anomalías, aislamiento de instantánea, 2PL y SSI. |
| 6 | [Introducción a NoSQL](06-introduccion-nosql/README.md) | Cap. 2, “The Birth of NoSQL” y comparación entre modelos relacional y documental. Panorama de grafos con enlace al tema específico. **Capítulo piloto.** |
| 7 | [Bases documentales](07-bases-documentales/README.md) | Cap. 2, representación documental, relaciones, flexibilidad del esquema, localidad y convergencia de modelos. MongoDB solamente en los ejemplos y observaciones de la edición. |
| 8 | [Bases orientadas a grafos](08-grafos/README.md) | Cap. 2, “Graph-Like Data Models”, grafos de propiedades, Cypher, consultas en SQL, triple-stores, SPARQL y Datalog. Neo4j en el alcance que presenta el libro. |
| 9 | [Arquitectura de sistemas de datos](09-arquitectura/README.md) | Cap. 1, sistemas de datos y atributos de calidad. Selección del cap. 8, fallos parciales, redes y límites de detección de fallos como prerrequisito de distribución. |
| 10 | [Replicación](10-replicacion/README.md) | Cap. 5, líderes y seguidores, replicación síncrona y asíncrona, retraso, múltiples líderes y ausencia de líder. |
| 11 | [Partición](11-particion/README.md) | Cap. 6, partición por rango y hash, distribución desigual, índices secundarios, rebalanceo y enrutamiento. |
| 12 | [Transacciones distribuidas y consistencia](12-transacciones-distribuidas/README.md) | Cap. 9, “Consistency Guarantees”, “Linearizability” y “Distributed Transactions and Consensus”. Orden y causalidad como apoyo para distinguir consenso, compromiso atómico y garantías de lectura. |
| 13 | [Persistencia para análisis de datos](13-analitica/README.md) | Cap. 3, “Transaction Processing or Analytics?”, almacenes de datos, esquemas analíticos y almacenamiento por columnas. Alcance acordado para Data Science: únicamente persistencia analítica, sin procesamiento por lotes. |
| Anexo | [Glosario](glosario/README.md) | Términos de los capítulos seleccionados, con equivalentes en inglés, definición sintética y referencias. |

El orden organiza la lectura como libro de estudio. Cada tema incluye índice local, referencias y navegación. Los diagramas se conservan en formato editable draw.io y se muestran como SVG.

## Alcance acordado y punto pendiente

La edición de 2017 y el formato del piloto están acordados. Data Science queda limitado a persistencia analítica del cap. 3, sin procesamiento por lotes del cap. 10.

El cruce de “problema del mapeo en memoria” con el desajuste objeto-relacional sigue siendo provisional hasta confirmar el sentido que utiliza la materia.

Los capítulos 4, 10, 11 y 12 del libro no se incorporarán íntegramente por defecto. Se agregarán únicamente secciones cuya relación con el programa quede establecida.
