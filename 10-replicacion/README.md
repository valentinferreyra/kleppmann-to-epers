# Replicación

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Copiar datos y sostener garantías](#copiar-datos-y-sostener-garantías)
- [Un líder y seguidores](#un-líder-y-seguidores)
- [Registros y retraso de replicación](#registros-y-retraso-de-replicación)
- [Múltiples líderes y conflictos](#múltiples-líderes-y-conflictos)
- [Sin líder, quorums y versiones](#sin-líder-quorums-y-versiones)
- [Diagrama de apoyo](#diagrama-de-apoyo)

## Copiar datos y sostener garantías

Replicar mantiene copias del mismo dato en varias máquinas. El libro enumera objetivos como cercanía geográfica, continuidad ante fallos y distribución de lecturas. Si los datos cambian, el problema incluye cómo propagar cambios y qué pueden observar los lectores mientras las copias difieren.

Replicación y partición resuelven problemas distintos. La primera copia; la segunda divide el conjunto de datos. Pueden combinarse: cada partición puede tener varias réplicas.

Referencia: cap. 5, introducción, pp. 151-152; cap. 6, “Partitioning and Replication”, pp. 200-201.

## Un líder y seguidores

En el esquema de líder único, el líder acepta escrituras y transmite cambios a seguidores. Las lecturas pueden utilizar distintas réplicas según las garantías buscadas. Agregar un seguidor requiere una instantánea consistente y continuar desde una posición conocida del registro para no perder cambios.

La replicación síncrona espera una confirmación del seguidor requerido antes de informar éxito. Refuerza la existencia de otra copia, pero puede detener escrituras si ese seguidor no responde. La asíncrona permite continuar sin esperar; si se pierde el líder antes de transmitir ciertos cambios, una promoción puede perder escrituras previamente confirmadas.

El failover promueve un nuevo líder y reconfigura participantes. Debe afrontar retraso, posibles pérdidas y el riesgo de que dos nodos actúen como líder. Un timeout inicia una sospecha, no demuestra por sí solo que el líder anterior dejó de operar.

Referencia: cap. 5, “Leaders and Followers”, “Synchronous Versus Asynchronous Replication”, “Setting Up New Followers” y “Handling Node Outages”, pp. 152-158.

## Registros y retraso de replicación

El libro distingue replicación de sentencias, de cambios físicos mediante WAL y de cambios lógicos en registros. Reejecutar sentencias exige atender funciones no deterministas; copiar bytes vincula versiones y formatos físicos; un registro lógico puede separar mejor replicación y almacenamiento. Cada alternativa tiene compromisos.

El retraso hace que una réplica asíncrona pueda responder con información antigua. La convergencia eventual supone que los cambios terminan propagándose; no establece por sí sola un plazo máximo.

| Garantía | Qué evita |
| --- | --- |
| Leer las propias escrituras | Que un usuario deje de ver un cambio que acaba de realizar. |
| Lecturas monotónicas | Que lecturas sucesivas del mismo usuario retrocedan a estados anteriores. |
| Lecturas de prefijo consistente | Que se observen efectos antes de sus causas en una secuencia de escrituras. |

El libro ilustra la primera con una actualización del perfil, la segunda con un comentario que aparece y luego desaparece, y la tercera con una conversación cuya respuesta se observa antes de la pregunta.

Referencias: cap. 5, “Implementation of Replication Logs”, pp. 158-161; “Problems with Replication Lag”, pp. 161-168.

## Múltiples líderes y conflictos

Permitir escrituras en varios líderes puede facilitar trabajo en centros de datos separados, operación desconectada y edición colaborativa. La contrapartida es aceptar cambios concurrentes y resolver sus conflictos después.

Seleccionar un ganador descarta otros cambios. Combinar versiones requiere una regla que haga converger las réplicas. El carrito de compras del libro muestra que unir elementos puede conservar agregados pero reintroducir elementos eliminados si la resolución no representa correctamente los borrados.

Referencia: cap. 5, “Multi-Leader Replication”, “Handling Write Conflicts” y “Multi-Leader Replication Topologies”, pp. 168-177.

## Sin líder, quorums y versiones

En el esquema sin líder descrito, el cliente puede escribir y leer varias réplicas. Para una clave con n réplicas, w confirmaciones de escritura y r respuestas de lectura, w + r > n asegura intersección entre esos conjuntos bajo los supuestos del esquema. No demuestra por sí solo linealizabilidad: concurrencia, escrituras parciales y políticas de reparación afectan el resultado.

La reparación durante lecturas actualiza copias atrasadas que se consultan; la anti-entropía busca diferencias en segundo plano. Un sloppy quorum puede usar nodos fuera del conjunto habitual y devolver luego los datos mediante hinted handoff; la intersección habitual ya no está asegurada.

Detectar concurrencia requiere dependencias causales, no solamente marcas de hora. Los vectores de versión ayudan a distinguir versiones derivadas de cambios concurrentes; los tombstones representan borrados que deben sobrevivir a la combinación.

Referencia: cap. 5, “Leaderless Replication”, “Limitations of Quorum Consistency”, “Sloppy Quorums and Hinted Handoff” y “Detecting Concurrent Writes”, pp. 177-192. Las configuraciones de productos son las de la edición de 2017.

## Diagrama de apoyo

![Un líder acepta escrituras y propaga cambios](diagramas/lider-seguidores.svg)

[Fuente editable](diagramas/lider-seguidores.drawio). Se omiten lecturas y confirmaciones. Las flechas no establecen sincronía ni orden entre seguidores. Referencia: Cap. 5, “Leaders and Followers”, pp. 152-155.

## Continuación de la lectura

[Anterior: Arquitectura de sistemas de datos](../09-arquitectura/README.md) · [Siguiente: Partición](../11-particion/README.md)
