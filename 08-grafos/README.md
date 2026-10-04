# Bases orientadas a grafos

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Entidades y conexiones](#entidades-y-conexiones)
- [Grafos de propiedades](#grafos-de-propiedades)
- [Cypher y recorridos variables](#cypher-y-recorridos-variables)
- [Triples, RDF y SPARQL](#triples-rdf-y-sparql)
- [Datalog y reglas reutilizables](#datalog-y-reglas-reutilizables)

## Entidades y conexiones

Cuando las relaciones muchos a muchos son frecuentes, un grafo permite tratar las conexiones como parte explícita del modelo. Los vértices representan entidades; las aristas, relaciones. Los tipos de entidad pueden ser heterogéneos dentro de un mismo grafo.

Kleppmann usa personas y lugares para conectar nacimiento, residencia y pertenencia geográfica. Lucy nació en Idaho y vive en Londres; Alain nació en Beaune y también vive en Londres. Los lugares pueden estar contenidos en otras regiones, con distinta cantidad de niveles. El ejemplo permite estudiar recorridos sin fijar una profundidad idéntica para todos los datos.

Referencia: cap. 2, “Graph-Like Data Models”, pp. 49-50. Síntesis del ejemplo de Lucy y Alain, sin copiar la figura.

## Grafos de propiedades

En un grafo de propiedades, los vértices tienen identificadores y propiedades. Las aristas tienen origen, destino, etiqueta y propiedades propias. El modelo permite recorrer conexiones entrantes o salientes y añadir nuevos tipos de relación.

El libro muestra que también puede representarse un grafo mediante tablas de vértices y aristas. La representación lógica del grafo y el lenguaje cómodo para consultarlo son cuestiones relacionadas, pero diferentes. Neo4j aparece como implementación del modelo de propiedades en el panorama de 2017.

Referencia: cap. 2, “Property Graphs”, pp. 50-52. No se reproduce el esquema SQL del ejemplo.

## Cypher y recorridos variables

Cypher expresa patrones que deben satisfacer los vértices y las relaciones. La consulta del libro busca personas nacidas en Estados Unidos que viven en Europa. Para resolverla, parte del lugar de nacimiento o residencia y sigue relaciones de pertenencia geográfica.

El número de niveles varía: un lugar puede ser ciudad, estado o país. Expresar un recorrido de longitud variable evita enumerar una cantidad fija de combinaciones. En SQL, el ejemplo utiliza consultas recursivas para expresar el mismo razonamiento; no es imposible representar el grafo relacionalmente, pero la consulta resulta más extensa.

Referencias: cap. 2, “The Cypher Query Language”, pp. 52-53; “Graph Queries in SQL”, pp. 53-55.

## Triples, RDF y SPARQL

Un triple contiene sujeto, predicado y objeto. El sujeto corresponde a una entidad; el objeto puede ser un valor simple o una segunda entidad. Así, los triples expresan tanto propiedades como conexiones.

RDF define un modelo que puede representarse con diferentes sintaxis. SPARQL consulta datos de ese modelo mediante patrones. Kleppmann utiliza otra vez el recorrido entre nacimiento, residencia y región para comparar la expresión de una misma consulta.

El libro distingue estos mecanismos de la promesa más amplia de la web semántica. No es necesario adoptar toda esa visión para comprender un almacén de triples o su lenguaje de consulta.

Referencia: cap. 2, “Triple-Stores and SPARQL”, pp. 55-60.

## Datalog y reglas reutilizables

Datalog expresa hechos mediante predicados y obtiene otros resultados mediante reglas. En el ejemplo geográfico, una regla reconoce un lugar por su nombre y otra extiende la pertenencia a través de ubicaciones intermedias. La aplicación repetida permite relacionar lugares con regiones más amplias.

El enfoque organiza conocimiento que varias consultas pueden reutilizar. El libro lo presenta como una alternativa potente para relaciones complejas, aunque menos directa para ciertas consultas aisladas.

Los grafos tampoco equivalen al modelo de red CODASYL: pueden identificar vértices directamente, consultar mediante índices y expresar recorridos declarativos sin exigir que la aplicación controle siempre un cursor por un camino predefinido.

Referencias: cap. 2, “The Foundation: Datalog”, pp. 60-63; recuadro “Graph Databases Compared to the Network Model”, p. 60. Este capítulo no añade un tutorial de instalación o API de Neo4j.

## Continuación de la lectura

[Anterior: Bases documentales](../07-bases-documentales/README.md) · [Siguiente: Arquitectura de sistemas de datos](../09-arquitectura/README.md)
