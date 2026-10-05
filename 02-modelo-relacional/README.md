# Modelo relacional y mapeo de objetos

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Representaciones en distintas capas](#representaciones-en-distintas-capas)
  - [Separar el modelo de la forma de guardarlo](#separar-el-modelo-de-la-forma-de-guardarlo)
- [El desajuste objeto-relacional](#el-desajuste-objeto-relacional)
  - [Desarmar y reconstruir el perfil profesional](#desarmar-y-reconstruir-el-perfil-profesional)
  - [Qué automatiza un ORM y qué decisiones conserva](#qué-automatiza-un-orm-y-qué-decisiones-conserva)
- [Relaciones, identificadores y normalización](#relaciones-identificadores-y-normalización)
  - [Identidad estable y nombre que puede cambiar](#identidad-estable-y-nombre-que-puede-cambiar)
  - [Cuando una organización deja de ser un texto](#cuando-una-organización-deja-de-ser-un-texto)
  - [Normalizar y desnormalizar redistribuyen trabajo](#normalizar-y-desnormalizar-redistribuyen-trabajo)
- [Consultas y caminos de acceso](#consultas-y-caminos-de-acceso)
  - [El acoplamiento de un camino de acceso](#el-acoplamiento-de-un-camino-de-acceso)
  - [La selección de tiburones y la libertad del motor](#la-selección-de-tiburones-y-la-libertad-del-motor)
- [Esquema y capacidades del motor](#esquema-y-capacidades-del-motor)
  - [Un esquema explícito y una transición gradual](#un-esquema-explícito-y-una-transición-gradual)
  - [Comparar capacidades sin confundirlas con garantías](#comparar-capacidades-sin-confundirlas-con-garantías)
- [Diagrama de apoyo](#diagrama-de-apoyo)

## Representaciones en distintas capas

Una aplicación representa entidades con estructuras de su lenguaje; una base representa datos mediante su modelo; el motor transforma ese modelo en bytes. Kleppmann plantea estas capas como abstracciones: cada una oculta parte de la implementación inferior, pero cada traducción impone decisiones.

El modelo relacional organiza datos en relaciones, representadas habitualmente como tablas de filas. Permite expresar qué datos se necesitan sin exigir que la aplicación recorra una estructura física específica. Esa separación es central al compararlo con modelos basados en caminos de acceso.

Referencia: cap. 2, introducción y “Relational Model Versus Document Model”, pp. 27-29.

### Separar el modelo de la forma de guardarlo

Las capas no describen necesariamente servicios separados. Son representaciones de los mismos datos. El código trabaja con objetos o estructuras; la base expone un modelo que permite guardar y consultar; el almacenamiento organiza registros, páginas o archivos. Un cambio en una representación puede exigir una traducción sin cambiar el significado del dato.

En una tabla, una fila describe una instancia y sus columnas contienen atributos. Una colección de empleos no necesita convertirse en una cantidad fija de columnas dentro de la fila del usuario. Puede representarse con varias filas vinculadas por un identificador. La cantidad de elementos queda expresada por la cantidad de filas relacionadas.

Separar estructura lógica y disposición física también evita interpretar un join como una instrucción de ir a un archivo concreto. La consulta relaciona datos según una condición; el motor decide qué estructuras físicas usar. Esa decisión puede cambiar cuando existen otros índices o cambian los datos, sin que la aplicación deba describir un recorrido nuevo.

Referencia: cap. 2, introducción, pp. 27-28; “The Object-Relational Mismatch”, pp. 29-32; “The relational model”, pp. 37-38.

## El desajuste objeto-relacional

Un objeto con colecciones anidadas no se convierte automáticamente en una única fila. En el perfil profesional del libro, nombre y apellido son atributos simples, pero los empleos y estudios admiten cantidades variables. Una representación normalizada utiliza tablas relacionadas; otra puede agrupar la estructura como documento.

Un ORM como Hibernate reduce el código necesario para traducir entre objetos y tablas. No elimina las diferencias: los límites de los objetos, las relaciones y las consultas siguen dependiendo del modelo persistente. Esta sección da contexto conceptual a EPERS; no describe configuraciones, estados de entidades ni cachés de Hibernate.

Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32. Síntesis del perfil profesional, sin reproducir código ni datos del ejemplo.

### Desarmar y reconstruir el perfil profesional

El perfil del libro tiene un identificador, atributos del usuario y colecciones de empleos, estudios y contactos. Los atributos simples aparecen una vez; las colecciones pueden tener cantidades diferentes para cada persona.

| Parte del perfil | Representación relacional normalizada del ejemplo | Trabajo al reconstruirlo |
| --- | --- | --- |
| Datos del usuario | Una fila identificada por usuario. | Recuperar sus atributos simples. |
| Empleos | Varias filas que referencian al usuario. | Reunir los empleos correspondientes. |
| Estudios | Varias filas relacionadas con el usuario. | Reunir su historia educativa. |
| Contactos | Registros asociados al usuario. | Incorporarlos a la estructura que espera la aplicación. |

La lectura completa necesita reunir esas partes mediante consultas o combinaciones. El resultado que recibe el código no tiene que coincidir con una fila individual: la aplicación puede construir un objeto que contenga las colecciones.

Un documento presenta una alternativa. Agrupa las colecciones dentro del perfil y hace visible el árbol que la aplicación usa. Esa cercanía puede simplificar el caso de leer todo el perfil. El costo de la traducción relacional se vuelve especialmente visible cuando la operación principal siempre requiere reconstruir el mismo árbol.

Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32. La tabla sintetiza la estructura del ejemplo sin reproducir su esquema ni sus datos personales.

### Qué automatiza un ORM y qué decisiones conserva

El ORM reduce el código repetitivo de traducción entre objetos y registros. Puede ayudar a expresar el vínculo entre un objeto y los datos que lo representan. Aun así, la estructura persistente sigue teniendo reglas diferentes de las de una colección en memoria.

La aplicación debe decidir qué constituye una entidad compartida, qué datos se repiten y qué necesita una consulta. Si dos perfiles referencian una organización, guardar dos objetos en memoria no resuelve si ambos representan la misma organización persistente o dos copias de su información.

El tradeoff del mapeo aparece al elegir cuánto acercar el almacenamiento a la estructura que usa el código. Reconstruir objetos desde tablas exige traducción. Almacenar el árbol como documento puede simplificar esa reconstrucción, pero las referencias que cruzan árboles siguen necesitando tratamiento.

La comodidad del código es un criterio del modelo; no sustituye el análisis de lecturas y escrituras. El libro mantiene separados el problema del mapeo y las garantías de concurrencia, que desarrolla en capítulos posteriores.

Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32; “Which data model leads to simpler application code?”, pp. 38-39.

## Relaciones, identificadores y normalización

Un identificador permite referenciar una entidad compartida sin repetir su representación legible. El libro lo muestra con regiones e industrias: si sus nombres están centralizados, un cambio no exige corregir cada perfil. La normalización reduce esa duplicación y el riesgo de actualizar solamente algunas copias.

Las relaciones muchos a uno y muchos a muchos aparecen cuando los perfiles comparten organizaciones o cuando un usuario recomienda a otro. Agrupar información en documentos no hace desaparecer esas conexiones. Debe decidirse cómo referenciarlas y cómo recuperar los datos relacionados.

Desnormalizar puede ahorrar combinaciones de datos, pero traslada trabajo a las escrituras y al mantenimiento de coherencia. La comparación depende de las relaciones y de las consultas, no de una preferencia abstracta por tablas u objetos.

Referencia: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 33-35.

### Identidad estable y nombre que puede cambiar

En el perfil, la región y la industria se guardan como identificadores. La diferencia se entiende siguiendo un cambio de nombre. Si cada perfil contiene el texto legible, corregir el nombre requiere actualizar todos esos perfiles. Si contienen un identificador común, la representación legible puede cambiar en la entidad referenciada.

El identificador separa identidad de presentación. El mismo dato puede mostrarse con nombres localizados para diferentes idiomas sin cambiar las referencias. El libro también destaca la ortografía uniforme, la reducción de ambigüedades y la posibilidad de buscar usando relaciones entre regiones.

Esa separación introduce una tarea en la lectura: resolver el identificador para mostrar la información. Guardar el texto evita esa resolución en algunas consultas, pero agrega copias que las escrituras deben mantener. La normalización reduce esa duplicación; no elimina la necesidad de recuperar datos relacionados.

Referencia: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 33-34.

### Cuando una organización deja de ser un texto

El libro propone extender el perfil con páginas para organizaciones y escuelas. Un nombre dentro de un empleo alcanza para mostrar una cadena de texto. Una organización con logo, noticias y página propia ya tiene información que distintos perfiles comparten.

Representarla como entidad permite que cada empleo la referencie. Muchas personas pueden haber trabajado en la misma organización, y una persona puede haber trabajado en varias. El modelo necesita conservar ambas conexiones, además de los datos del empleo particular.

Las recomendaciones agregan otra relación. El perfil recomendado muestra información de quien la escribió. Si esa persona cambia su foto, las recomendaciones deben mostrar la información actual. Copiar la foto dentro de cada recomendación obliga a propagar ese cambio; referenciar al autor permite resolver su perfil al consultar.

Lo que al principio parecía un conjunto de perfiles independientes se convierte en datos conectados. Elegir el modelo solo por la primera pantalla puede ocultar el trabajo que introducen esas funciones.

Referencia: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 34-35.

### Normalizar y desnormalizar redistribuyen trabajo

| Decisión | Beneficio para el ejemplo | Costo que debe considerarse |
| --- | --- | --- |
| Referenciar una región compartida | Actualizar su nombre en un lugar y conservar identidad común. | Resolver la referencia para mostrar el nombre. |
| Copiar el nombre en cada perfil | Obtenerlo junto con el perfil. | Actualizar copias y afrontar resultados incoherentes si algunas quedan atrás. |
| Referenciar al autor de una recomendación | Recuperar su nombre y foto actuales desde su perfil. | Consultar o combinar datos del autor al mostrarla. |

El libro suspende una elección universal entre normalizar y desnormalizar. En esta comparación importa qué se consulta junto, qué se comparte y con qué frecuencia cambia. Ahorrar un join traslada parte del trabajo a mantener las representaciones duplicadas.

Referencia: cap. 2, “Many-to-One and Many-to-Many Relationships”, pp. 33-35; “Which data model leads to simpler application code?”, pp. 38-39.

## Consultas y caminos de acceso

En CODASYL, la aplicación recorría enlaces entre registros. Cambiar los caminos podía obligar a modificar el código de consulta. En el modelo relacional, el optimizador decide el orden de ejecución y los índices para resolver una consulta declarativa.

La distinción no implica que toda consulta declarativa sea rápida. Implica que el programa expresa el resultado y el motor dispone de margen para elegir cómo obtenerlo. El ejemplo de selección de tiburones del libro contrasta un recorrido imperativo con una condición declarativa; el segundo deja abiertas distintas estrategias de ejecución.

Referencias: cap. 2, “Are Document Databases Repeating History?”, pp. 36-38; “Query Languages for Data”, pp. 42-44. No se reproduce el código del ejemplo.

### El acoplamiento de un camino de acceso

CODASYL permitía conectar un registro con varios padres. La aplicación consultaba siguiendo enlaces, con un cursor y un recorrido definido. Si diferentes caminos llegaban al mismo registro, el programa debía manejar esa navegación.

El beneficio histórico era controlar el acceso en hardware con restricciones fuertes. El costo aparecía al cambiar el modelo o necesitar otra consulta. Si faltaba un camino adecuado, agregarlo podía exigir reescribir código que dependía de los recorridos anteriores.

En el modelo relacional, la consulta expresa condiciones sobre datos y relaciones. El optimizador elige un plan de ejecución. Crear un índice puede ofrecer otra estrategia sin exigir que cada consulta reemplace un recorrido manual por otro.

La independencia es parcial: el diseño de tablas y la consulta siguen importando. El cambio es que el programa no tiene que fijar todos los detalles del acceso físico. El motor puede concentrar ese trabajo y compartirlo entre aplicaciones.

Referencia: cap. 2, “The network model” y “The relational model”, pp. 36-38.

### La selección de tiburones y la libertad del motor

El ejemplo del libro compara recorrer una lista de especies con expresar la condición de pertenecer a la familia de los tiburones. En el recorrido imperativo, el código determina cómo iterar, cuándo evaluar la condición y cómo acumular resultados.

La expresión declarativa define el conjunto buscado. Deja al motor elegir si recorre registros, usa un índice o distribuye trabajo cuando su implementación lo permite. La consulta puede conservarse mientras mejora el mecanismo que la ejecuta.

La ausencia de un orden exigido también da libertad. Si el programa necesita resultados ordenados, debe expresar ese requisito. Una consulta que solo selecciona filas no convierte su disposición física en un contrato de orden.

El tradeoff consiste en ceder control de la secuencia exacta de operaciones a cambio de optimización y menor dependencia de la implementación. Eso no prueba que cualquier consulta será rápida; permite que el motor busque estrategias sin modificar su significado.

Referencia: cap. 2, “Query Languages for Data”, pp. 42-43. Síntesis del ejemplo, sin reproducir el código.

## Esquema y capacidades del motor

El libro describe soporte para datos anidados en motores relacionales y operaciones de combinación en algunos sistemas documentales. Por eso, tablas y documentos no son necesariamente capacidades excluyentes. Las menciones de productos y versiones son históricas, correspondientes a 2017.

Para estudiar el mapeo, conviene distinguir estructura lógica, acceso a los datos y restricciones. Un ORM automatiza parte de la traducción; no garantiza por sí mismo consultas eficientes, consistencia de invariantes ni protección frente a concurrencia. El libro advierte, por ejemplo, que un ciclo de lectura, modificación y escritura puede perder actualizaciones aunque se exprese cómodamente mediante objetos.

Referencias: cap. 2, “Convergence of document and relational databases”, pp. 41-42; cap. 7, “Preventing Lost Updates”, pp. 242-246.

### Un esquema explícito y una transición gradual

El esquema al escribir hace explícita una estructura y exige que los datos la respeten. El libro lo compara con el chequeo estático de tipos. El esquema al leer deja parte de esa interpretación al código que consume cada registro, como un chequeo dinámico.

Cambiar un nombre completo por campos separados no exige siempre reescribir de inmediato toda una tabla. El libro describe agregar el campo nuevo y completar datos después, incluso durante la lectura si una actualización masiva resulta demasiado costosa. La evolución gradual también puede existir en una representación relacional.

Las afirmaciones del libro sobre tiempos de ALTER TABLE y diferencias entre productos corresponden a 2017. El punto conceptual es distinguir cambiar la definición de un campo de transformar todas las filas que ya existen. Son trabajos diferentes y pueden tener costos diferentes.

Referencia: cap. 2, “Schema flexibility in the document model”, pp. 39-41.

### Comparar capacidades sin confundirlas con garantías

Una base relacional puede admitir datos anidados. Una base documental puede ofrecer mecanismos para resolver referencias. El nombre de la familia no describe por completo sus capacidades, y el libro observa una convergencia entre ambos modelos.

El soporte para documentos no indica por sí solo cómo se realizan joins ni qué aislamiento existe. Un ORM tampoco decide esas garantías por el solo hecho de permitir modificar objetos. La pérdida de actualizaciones aparece cuando varias operaciones leen, modifican y escriben sin un mecanismo apropiado de concurrencia.

La comparación de modelos puede mantenerse separada de ese segundo análisis: primero, qué estructura y relaciones se expresan; después, cómo se consultan y qué garantías tienen sus modificaciones. [Bases documentales](../07-bases-documentales/README.md) desarrolla agrupamiento y evolución, y [Aislamiento](../05-aislamiento/README.md) explica las anomalías de operaciones concurrentes.

Referencias: cap. 2, “Convergence of document and relational databases”, pp. 41-42; cap. 7, “Preventing Lost Updates”, pp. 242-246.

## Diagrama de apoyo

![Perfil profesional representado como tablas y como documento](../06-introduccion-nosql/diagramas/perfil-relacional-documental.svg)

[Fuente editable](../06-introduccion-nosql/diagramas/perfil-relacional-documental.drawio). Vista propia simplificada de las colecciones del perfil. No representa una arquitectura de Hibernate ni las relaciones compartidas entre perfiles. Referencia: cap. 2, “The Object-Relational Mismatch”, pp. 29-32.

## Continuación de la lectura

[Anterior: Fundamentos de persistencia](../01-fundamentos/README.md) · [Siguiente: Almacenamiento e índices](../03-almacenamiento-indices/README.md)
