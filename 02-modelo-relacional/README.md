# Modelo relacional y mapeo de objetos

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Representaciones en distintas capas](#representaciones-en-distintas-capas)
- [El desajuste objeto-relacional](#el-desajuste-objeto-relacional)
- [Relaciones, identificadores y normalización](#relaciones-identificadores-y-normalización)
- [Consultas y caminos de acceso](#consultas-y-caminos-de-acceso)
- [Esquema y capacidades del motor](#esquema-y-capacidades-del-motor)

## Representaciones en distintas capas

Una aplicación representa entidades con estructuras de su lenguaje; una base representa datos mediante su modelo; el motor transforma ese modelo en bytes. Kleppmann plantea estas capas como abstracciones: cada una oculta parte de la implementación inferior, pero cada traducción impone decisiones.

El modelo relacional organiza datos en relaciones, representadas habitualmente como tablas de filas. Permite expresar qué datos se necesitan sin exigir que la aplicación recorra una estructura física específica. Esa separación es central al compararlo con modelos basados en caminos de acceso.

Referencia: cap. 2, introducción y “Relational Model Versus Document Model”, pp. 27-29.

## El desajuste objeto-relacional

Un objeto con colecciones anidadas no se convierte automáticamente en una única fila. En el perfil profesional del libro, nombre y apellido son atributos simples, pero los empleos y estudios admiten cantidades variables. Una representación normalizada utiliza tablas relacionadas; otra puede agrupar la estructura como documento.

Un ORM como Hibernate reduce el código necesario para traducir entre objetos y tablas. No elimina las diferencias: los límites de los objetos, las relaciones y las consultas siguen dependiendo del modelo persistente. Esta sección da contexto conceptual a EPERS; no describe configuraciones, estados de entidades ni cachés de Hibernate.

Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32. Síntesis del perfil profesional, sin reproducir código ni datos del ejemplo.

## Relaciones, identificadores y normalización

Un identificador permite referenciar una entidad compartida sin repetir su representación legible. El libro lo muestra con regiones e industrias: si sus nombres están centralizados, un cambio no exige corregir cada perfil. La normalización reduce esa duplicación y el riesgo de actualizar solamente algunas copias.

Las relaciones muchos a uno y muchos a muchos aparecen cuando los perfiles comparten organizaciones o cuando un usuario recomienda a otro. Agrupar información en documentos no hace desaparecer esas conexiones. Debe decidirse cómo referenciarlas y cómo recuperar los datos relacionados.

Desnormalizar puede ahorrar combinaciones de datos, pero traslada trabajo a las escrituras y al mantenimiento de coherencia. La comparación depende de las relaciones y de las consultas, no de una preferencia abstracta por tablas u objetos.

Referencia: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 33-35.

## Consultas y caminos de acceso

En CODASYL, la aplicación recorría enlaces entre registros. Cambiar los caminos podía obligar a modificar el código de consulta. En el modelo relacional, el optimizador decide el orden de ejecución y los índices para resolver una consulta declarativa.

La distinción no implica que toda consulta declarativa sea rápida. Implica que el programa expresa el resultado y el motor dispone de margen para elegir cómo obtenerlo. El ejemplo de selección de tiburones del libro contrasta un recorrido imperativo con una condición declarativa; el segundo deja abiertas distintas estrategias de ejecución.

Referencias: cap. 2, “Are Document Databases Repeating History?”, pp. 36-38; “Query Languages for Data”, pp. 42-44. No se reproduce el código del ejemplo.

## Esquema y capacidades del motor

El libro describe soporte para datos anidados en motores relacionales y operaciones de combinación en algunos sistemas documentales. Por eso, tablas y documentos no son necesariamente capacidades excluyentes. Las menciones de productos y versiones son históricas, correspondientes a 2017.

Para estudiar el mapeo, conviene distinguir estructura lógica, acceso a los datos y restricciones. Un ORM automatiza parte de la traducción; no garantiza por sí mismo consultas eficientes, consistencia de invariantes ni protección frente a concurrencia. El libro advierte, por ejemplo, que un ciclo de lectura, modificación y escritura puede perder actualizaciones aunque se exprese cómodamente mediante objetos.

Referencias: cap. 2, “Convergence of document and relational databases”, pp. 41-42; cap. 7, “Preventing Lost Updates”, pp. 242-246.

## Continuación de la lectura

[Anterior: Fundamentos de persistencia](../01-fundamentos/README.md) · [Siguiente: Almacenamiento e índices](../03-almacenamiento-indices/README.md)
