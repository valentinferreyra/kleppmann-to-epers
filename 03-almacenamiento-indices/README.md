# Almacenamiento e índices

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Un índice cambia el costo del acceso](#un-índice-cambia-el-costo-del-acceso)
- [Índices hash y compactación](#índices-hash-y-compactación)
  - [Seguir una clave a través del archivo](#seguir-una-clave-a-través-del-archivo)
  - [Por qué un borrado necesita dejar una marca](#por-qué-un-borrado-necesita-dejar-una-marca)
  - [El costo del acceso directo](#el-costo-del-acceso-directo)
- [SSTables y LSM-trees](#sstables-y-lsm-trees)
  - [Recorrido de una escritura y de una lectura](#recorrido-de-una-escritura-y-de-una-lectura)
  - [Índices dispersos y filtros de Bloom](#índices-dispersos-y-filtros-de-bloom)
  - [Compactar también consume capacidad](#compactar-también-consume-capacidad)
- [B-trees y recuperación](#b-trees-y-recuperación)
  - [Una división de página afecta varias escrituras](#una-división-de-página-afecta-varias-escrituras)
- [Compromisos y otros índices](#compromisos-y-otros-índices)
  - [Comparar el trabajo físico](#comparar-el-trabajo-físico)
  - [El orden de un índice compuesto forma parte del diseño](#el-orden-de-un-índice-compuesto-forma-parte-del-diseño)
- [Diagrama de apoyo](#diagrama-de-apoyo)

## Un índice cambia el costo del acceso

El ejemplo inicial del libro almacena pares clave-valor agregándolos al final de un archivo. Escribir resulta sencillo; encontrar el valor vigente requiere buscar la última aparición de la clave. El ejemplo muestra por qué una representación persistente necesita estructuras auxiliares para acelerar lecturas.

Un índice conserva información adicional que ayuda a localizar datos. Puede mejorar una consulta, pero cada escritura debe mantener los índices afectados. No existe un beneficio gratuito: se intercambian tiempo de lectura, trabajo de escritura y espacio.

Referencia: cap. 3, “Data Structures That Power Your Database”, pp. 70-72. Síntesis del pequeño almacén del libro, sin reproducir su implementación.

## Índices hash y compactación

El índice hash descrito mantiene en memoria la posición de cada clave en el archivo. Al agregar un valor, actualiza su posición; al leer, busca esa posición y recupera el dato. En este diseño, las claves del índice deben caber en memoria y las búsquedas por rango no aprovechan un orden.

El archivo se divide en segmentos para evitar su crecimiento indefinido. La compactación descarta versiones antiguas; la combinación de segmentos reúne los valores vigentes. Los registros de borrado deben impedir que reaparezcan valores antiguos al combinar segmentos.

El libro usa contadores de reproducciones de videos para mostrar muchas actualizaciones sobre un conjunto relativamente pequeño de claves. Ese patrón ayuda a entender tanto el índice en memoria como la utilidad de compactar.

Referencia: cap. 3, “Hash Indexes”, pp. 72-76.

### Seguir una clave a través del archivo

En el ejemplo del libro, actualizar una clave agrega otro registro al final. El valor anterior sigue en el archivo, pero deja de ser el vigente. El índice apunta a la posición de la versión nueva. La lectura evita recorrer todos los registros porque ya sabe dónde comenzar.

La distinción entre el archivo y el índice permite entender la recuperación. Los valores sobreviven en disco; el mapa en memoria desaparece al reiniciar el proceso. El motor puede reconstruirlo recorriendo el archivo y conservando la última posición de cada clave. Esa reconstrucción consume tiempo. Guardar una copia del índice por segmento puede acelerar el arranque, como describe el libro para Bitcask.

Cuando existen varios segmentos, cada uno tiene su propio mapa. Una lectura busca primero en el segmento más reciente y continúa hacia los anteriores hasta encontrar la clave. Por eso, agregar segmentos evita un archivo único ilimitado, pero también aumenta el trabajo de búsqueda si no se combinan periódicamente.

Referencia: cap. 3, “Hash Indexes”, pp. 72-75.

### Por qué un borrado necesita dejar una marca

Compactar conserva la última versión de una clave y descarta las anteriores. En los contadores de reproducciones del libro, las actualizaciones repetidas dejan muchos registros que ya no hacen falta para responder una lectura del contador actual.

Un borrado plantea otro problema. Si el motor eliminara únicamente la versión reciente, una búsqueda podría encontrar una versión antigua en otro segmento y devolverla como vigente. El tombstone registra que la clave fue borrada. La combinación debe tenerlo en cuenta antes de descartar las versiones anteriores; quitarlo prematuramente puede hacer reaparecer el dato.

Mientras un proceso combina segmentos, los lectores pueden seguir usando los anteriores. El motor cambia a los archivos nuevos cuando están completos. La inmutabilidad de los segmentos simplifica ese trabajo, aunque exige espacio temporal para mantener archivos viejos y nuevos durante la operación.

Referencia: cap. 3, “Hash Indexes”, pp. 73-76.

### El costo del acceso directo

| Decisión | Qué permite | Qué cuesta o limita |
| --- | --- | --- |
| Mantener una posición por clave en memoria | Localizar un valor sin recorrer el archivo completo. | Todas las claves del mapa deben caber en memoria. |
| Agregar registros en vez de sobrescribir | Escribir secuencialmente y conservar segmentos que los lectores pueden consultar. | Acumular versiones obsoletas y recuperarlas mediante compactación. |
| Usar un mapa hash | Resolver búsquedas por una clave concreta. | Recorrer rangos de claves sin un orden útil en el índice. |

La pregunta sobre la consulta importa tanto como la cantidad de datos. Un mapa adecuado para recuperar un contador por identificador no ofrece la misma ayuda para obtener todas las claves entre dos límites.

Referencia: cap. 3, “Hash Indexes”, pp. 75-76.

## SSTables y LSM-trees

Una SSTable mantiene pares ordenados por clave. El orden facilita combinar archivos y permite usar un índice disperso: no hace falta guardar en memoria la posición de cada clave para acotar dónde buscar.

En el diseño del libro, las escrituras llegan primero a una estructura ordenada en memoria, la memtable. Un registro en disco permite recuperarla después de un fallo. Cuando alcanza cierto tamaño, se vuelca como SSTable. Las lecturas consultan la memoria y después los segmentos, empezando por los recientes; la compactación combina segmentos en segundo plano.

Esta familia de motores organiza buena parte del trabajo como escrituras secuenciales. Buscar una clave inexistente puede obligar a revisar varios archivos; los filtros de Bloom permiten descartar muchos de ellos. Un filtro puede dar falsos positivos, por lo que no sustituye la consulta del dato.

Referencia: cap. 3, “SSTables and LSM-Trees”, pp. 76-79.

### Recorrido de una escritura y de una lectura

El libro construye el motor por etapas. La memtable mantiene las claves ordenadas aunque lleguen escrituras en cualquier orden. Su contenido todavía necesita protección frente a un reinicio.

1. El motor registra la escritura en un log de disco para recuperar los cambios que siguen en memoria.
2. Actualiza la memtable con el valor nuevo.
3. Al alcanzar el umbral de tamaño, convierte esa estructura ordenada en una SSTable.
4. Una nueva memtable recibe escrituras mientras se completa el volcado de la anterior.
5. Cuando el archivo persistente está disponible, el log correspondiente a esa memtable ya puede descartarse.

El log de recuperación no necesita ordenar las claves: su función es reconstruir la memoria. La SSTable sí las ordena, porque ese orden sirve para buscar y combinar archivos.

Una lectura empieza por la memtable. Si no encuentra allí la clave, revisa las SSTables recientes antes de las antiguas. Esta prioridad permite encontrar una actualización antes que la versión que reemplazó. También explica por qué una clave ausente puede resultar costosa: no hay un valor que permita detener la búsqueda temprano.

Referencia: cap. 3, “Constructing and maintaining SSTables”, pp. 77-78.

### Índices dispersos y filtros de Bloom

En una SSTable, conocer algunas posiciones basta para acotar la búsqueda. Si el índice guarda una clave anterior y otra posterior a la buscada, el motor examina el bloque entre ellas. El orden del archivo permite ahorrar memoria a cambio de leer y buscar dentro de ese bloque.

El filtro de Bloom responde otra pregunta: si una clave puede estar en un archivo. Una respuesta negativa permite omitirlo. Una respuesta positiva exige consultar la SSTable, porque el filtro puede equivocarse con un falso positivo. No devuelve el valor ni reemplaza el índice.

Estas estructuras reducen trabajos diferentes. El índice disperso ubica una zona del archivo; el filtro ayuda a evitar archivos que no contienen la clave. Su utilidad depende del patrón de lectura y de cuántos segmentos sería necesario examinar.

Referencia: cap. 3, “SSTables and LSM-Trees” y “Performance optimizations”, pp. 76-79.

### Compactar también consume capacidad

Combinar archivos ordenados permite recorrerlos secuencialmente. Cuando una clave aparece en más de un archivo, la combinación conserva la versión pertinente. Los tombstones participan en esa decisión, igual que en el almacén de segmentos anterior.

La compactación produce escrituras físicas que no corresponden a nuevas escrituras de la aplicación. Además, lee archivos existentes. Si comparte el disco con las consultas, puede demorar solicitudes aunque el promedio de latencia parezca aceptable. El libro destaca el efecto sobre las solicitudes más lentas.

Si las escrituras entran más rápido de lo que la compactación puede procesarlas, se acumulan archivos. Eso exige más espacio y puede aumentar el trabajo de lectura. Un motor con buen rendimiento de escritura durante un intervalo corto no necesariamente sostiene ese ritmo de manera continua.

El libro distingue compactación por tamaños y por niveles. La primera combina archivos pequeños con otros mayores; la segunda organiza rangos de claves en niveles y reparte el trabajo de forma más incremental. Las políticas cambian el espacio ocupado y las reescrituras. No hay una política ganadora para cualquier carga.

Referencias: cap. 3, “Performance optimizations”, p. 79; “Downsides of LSM-trees”, pp. 84-85. Las observaciones sobre implementaciones corresponden a 2017.

## B-trees y recuperación

Los B-trees mantienen claves ordenadas en páginas de tamaño fijo. Las páginas internas dirigen la búsqueda hacia rangos y las hojas contienen valores o referencias. Insertar puede dividir páginas; actualizar suele modificar una página existente.

Un registro de escritura anticipada, WAL, permite recuperar cambios después de un fallo. La estructura también requiere control de concurrencia porque distintas operaciones pueden acceder a las mismas páginas. La implementación física y sus mecanismos de recuperación son parte del costo del índice.

Referencia: cap. 3, “B-Trees”, pp. 79-83.

### Una división de página afecta varias escrituras

Una página tiene capacidad limitada. Cuando una inserción la llena, el B-tree divide su contenido y agrega al padre una referencia a la página nueva. La operación puede modificar varias páginas, por lo que un fallo entre esas modificaciones necesita recuperación.

El WAL conserva primero la información necesaria para rehacer cambios. Así, la recuperación puede restablecer una estructura consistente después de una interrupción. Los latches protegen las estructuras internas mientras distintos hilos las modifican. Cumplen una función distinta de los bloqueos usados para aislar transacciones de la aplicación.

El libro también presenta copy-on-write: escribir páginas nuevas y actualizar las referencias en vez de sobrescribir las anteriores. Conservar versiones de páginas puede servir para snapshots. El mecanismo cambia el trabajo de actualización y la gestión del espacio; mantener una página previa tampoco elimina la necesidad de decidir cuándo puede recuperarse su espacio.

Referencia: cap. 3, “Making B-trees reliable” y “B-tree optimizations”, pp. 82-83.

## Compromisos y otros índices

Los LSM-trees pueden favorecer cargas de escritura, pero la compactación consume recursos y puede introducir demoras. Los B-trees pueden ofrecer lecturas más previsibles, pero actualizar páginas también genera trabajo adicional. Ambos pueden presentar amplificación de escritura: una escritura lógica causa varias escrituras físicas. Kleppmann advierte que el resultado depende de la implementación y la carga.

Un índice secundario permite buscar por campos distintos de la clave primaria. Un índice agrupado almacena los datos asociados; uno de cobertura guarda información suficiente para responder ciertas consultas sin recuperar la fila completa. Esa duplicación acelera algunas lecturas y aumenta el costo de mantenimiento.

Un índice compuesto ordena por una secuencia de campos. El ejemplo de la guía telefónica muestra que ordenar por apellido y luego nombre ayuda a buscar por apellido, pero no ofrece la misma ventaja al buscar solamente por nombre. Índices multidimensionales, de texto completo y difusos responden a necesidades diferentes.

Referencias: cap. 3, “Comparing B-Trees and LSM-Trees”, pp. 83-85; “Other Indexing Structures”, pp. 85-90. Las observaciones sobre motores concretos corresponden a 2017.

### Comparar el trabajo físico

| Aspecto | B-tree | LSM-tree |
| --- | --- | --- |
| Actualización habitual | Modifica páginas y registra información de recuperación. | Agrega cambios y luego combina archivos. |
| Reescrituras | WAL, páginas completas y posibles divisiones. | Log, volcado y sucesivas compactaciones. |
| Lectura por clave | Recorre el árbol hacia la página correspondiente. | Puede revisar memoria y varios archivos, con ayuda de índices y filtros. |
| Trabajo de mantenimiento | Modificaciones de páginas y de la estructura del árbol. | Compactación que compite por recursos con las operaciones activas. |

La amplificación de escritura importa porque el disco procesa más bytes que los enviados por la aplicación. En un B-tree, cambiar unos pocos bytes puede escribir una página completa. En un LSM-tree, un valor puede reescribirse en varias compactaciones. Cuál amplifica menos depende de la implementación, la configuración y la carga.

El libro vincula la escritura secuencial y la densidad de los archivos con ventajas posibles de los LSM-trees. También advierte que el mantenimiento puede producir picos de latencia. Para comparar, hace falta observar rendimiento sostenido, espacio y latencia de las consultas, además de la tasa de inserciones.

Referencia: cap. 3, “Comparing B-Trees and LSM-Trees”, pp. 83-85.

### El orden de un índice compuesto forma parte del diseño

En la guía telefónica del libro, el apellido es el primer criterio y el nombre el segundo. Los registros con el mismo apellido quedan juntos. Buscar ese apellido permite recorrer una región contigua del índice. Buscar solamente un nombre exige examinar entradas repartidas entre muchos apellidos.

Un índice de cobertura agrega campos para responder una consulta sin buscar después la fila completa. La ganancia es evitar accesos adicionales; el costo es almacenar y mantener otra copia de esos campos. Si cambian, también debe actualizarse el índice que los contiene.

Estas decisiones dependen de las consultas que se quieren acelerar. Agregar índices a cada campo puede reducir búsquedas, pero cada inserción o actualización debe mantener las estructuras afectadas. El costo aparece en las escrituras incluso cuando esa operación no necesita consultar los índices.

Referencia: cap. 3, “Other Indexing Structures”, pp. 85-88.

## Diagrama de apoyo

![De la memoria a los segmentos ordenados](diagramas/lsm.svg)

[Fuente editable](diagramas/lsm.drawio). Ruta de escritura simplificada. Se omiten WAL, lectura, borrados y políticas de compactación. Referencia: Cap. 3, “SSTables and LSM-Trees”, pp. 76-79.

## Continuación de la lectura

[Anterior: Modelo relacional y mapeo de objetos](../02-modelo-relacional/README.md) · [Siguiente: Transacciones y ACID](../04-transacciones-acid/README.md)
