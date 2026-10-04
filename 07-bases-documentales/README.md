# Bases documentales

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [El documento como agrupamiento](#el-documento-como-agrupamiento)
- [Anidar o referenciar](#anidar-o-referenciar)
- [Esquema y evolución](#esquema-y-evolución)
- [Localidad y tamaño](#localidad-y-tamaño)
- [MongoDB dentro del alcance del libro](#mongodb-dentro-del-alcance-del-libro)

## El documento como agrupamiento

Un documento permite representar datos anidados, con estructuras y colecciones dentro de un registro. En el perfil profesional del libro, empleos y estudios se agrupan con los datos del usuario. Esa representación hace explícito un árbol de relaciones uno a muchos.

El agrupamiento puede simplificar la reconstrucción del objeto de la aplicación. Pero el documento debe evaluarse junto con sus operaciones: obtenerlo completo, consultar una parte y modificarlo tienen costos distintos. No equivale automáticamente a la unidad de consistencia de todas las relaciones del dominio.

Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32.

## Anidar o referenciar

El perfil incluye regiones e industrias compartidas. Guardar sus nombres en cada documento repite información; usar identificadores mantiene una referencia a una entidad común. La incorporación de organizaciones y recomendaciones entre usuarios vuelve más conectado el modelo.

Anidar datos que se consultan juntos puede evitar combinaciones. Referenciar entidades compartidas permite actualizarlas en un lugar, pero exige resolver esas referencias al leer. El libro destaca que tanto documentos como tablas pueden utilizar identificadores: la diferencia no consiste en que uno admita relaciones y el otro no.

Si el motor no combina los registros, la aplicación puede realizar varias consultas. Esa solución traslada complejidad y puede aumentar viajes de red. Desnormalizar reduce algunas consultas, pero obliga a mantener las copias coherentes.

Referencias: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 33-35; “Comparison to document databases”, p. 38; “Which data model leads to simpler application code?”, pp. 38-39.

## Esquema y evolución

La ausencia de un esquema exigido por la base no implica ausencia de expectativas. El lector necesita interpretar campos y tipos. Kleppmann denomina esquema en lectura a esa estructura implícita y lo contrasta con un esquema explícito exigido al escribir.

El ejemplo del nombre completo y su separación en campos ilustra la coexistencia de representaciones antiguas y nuevas. Si no se transforma todo de una vez, el lector debe reconocer ambas. La flexibilidad cambia dónde se afronta la compatibilidad; no elimina el trabajo.

La heterogeneidad puede justificar esa flexibilidad, especialmente cuando fuentes externas controlan el formato. Si todos los registros deben respetar una estructura común, un esquema explícito puede documentarla y protegerla.

Referencia: cap. 2, “Schema flexibility in the document model”, pp. 39-41.

## Localidad y tamaño

Almacenar juntos datos necesarios para una misma lectura puede reducir búsquedas. En el perfil, ese beneficio aparece cuando se recupera buena parte de su información. Si se necesita solamente un campo de un documento muy grande, cargarlo completo puede resultar costoso.

El libro también describe actualizaciones que requieren reescribir el documento y advierte sobre aumentar su tamaño. Estas observaciones pertenecen a los mecanismos de la edición de 2017. No deben convertirse en una regla idéntica para cualquier motor o versión.

La localidad puede conseguirse también mediante otros modelos y estructuras físicas. Es necesario separar el formato lógico de los datos de las decisiones del motor que los almacena.

Referencia: cap. 2, “Data locality for queries”, p. 41.

## MongoDB dentro del alcance del libro

MongoDB aparece como ejemplo de base orientada a documentos y BSON como una codificación utilizada para almacenarlos. La sección de convergencia comenta funciones y comportamiento de productos de la época. No se extrapolan esas afirmaciones a versiones actuales.

Este capítulo cubre el modelo documental, no una guía de comandos, índices específicos ni transacciones de MongoDB. Las garantías generales de concurrencia se estudian en [Aislamiento](../05-aislamiento/README.md), y la distribución en [Replicación](../10-replicacion/README.md) y [Partición](../11-particion/README.md).

Referencias: cap. 2, “The Object-Relational Mismatch”, p. 31; “Data locality for queries” y “Convergence of document and relational databases”, pp. 41-42.

## Continuación de la lectura

[Anterior: Introducción a NoSQL](../06-introduccion-nosql/README.md) · [Siguiente: Bases orientadas a grafos](../08-grafos/README.md)
