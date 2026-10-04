# Transacciones y ACID

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [La transacción como unidad de trabajo](#la-transacción-como-unidad-de-trabajo)
- [Atomicidad y consistencia](#atomicidad-y-consistencia)
- [Aislamiento y durabilidad](#aislamiento-y-durabilidad)
- [Operaciones sobre uno o varios objetos](#operaciones-sobre-uno-o-varios-objetos)
- [Abortar y reintentar](#abortar-y-reintentar)

## La transacción como unidad de trabajo

Una transacción agrupa lecturas y escrituras para que la aplicación pueda razonar sobre éxito, aborto y concurrencia. Kleppmann la presenta como una abstracción que concentra parte del manejo de errores en la base. El término puede describir garantías distintas, por lo que debe examinarse qué significa en cada sistema.

El libro cuestiona tanto afirmar que las transacciones impiden escalar como asumir que cualquier sistema etiquetado ACID resuelve todos los problemas de corrección. Los mecanismos tienen costos y límites; las invariantes de la aplicación siguen necesitando un diseño correcto.

Referencia: cap. 7, “The Slippery Concept of a Transaction”, pp. 222-223.

## Atomicidad y consistencia

La atomicidad de ACID permite abortar una transacción sin conservar una parte de sus escrituras. Trata el resultado ante errores; el control de lo que otras transacciones observan corresponde al aislamiento. Por eso no debe confundirse con otros usos de la palabra atómico en programación concurrente.

La consistencia de ACID se refiere a invariantes de los datos. En el ejemplo contable del libro, las operaciones deben conservar el balance entre débitos y créditos. La base puede exigir algunas restricciones, pero no conoce todas las reglas de negocio. La aplicación debe definir transacciones que preserven esas reglas.

Esta consistencia no significa que todas las réplicas muestren inmediatamente lo mismo. Las garantías de lectura distribuida se estudian en [Transacciones distribuidas y consistencia](../12-transacciones-distribuidas/README.md).

Referencia: cap. 7, “The Meaning of ACID”, subsecciones “Atomicity” y “Consistency”, pp. 223-225.

## Aislamiento y durabilidad

El aislamiento determina cómo interactúan transacciones concurrentes. La serializabilidad busca un efecto equivalente a ejecutarlas una después de otra; niveles más débiles protegen frente a algunas anomalías y permiten otras. La etiqueta ACID no basta para identificar esas diferencias.

La durabilidad promete conservar los datos de una transacción confirmada ante los fallos contemplados. En un nodo suele apoyarse en almacenamiento no volátil y recuperación mediante registros. En sistemas replicados también depende de qué réplicas recibieron la escritura antes de confirmarla.

Ningún mecanismo tolera todos los eventos. Una escritura en disco puede quedar inaccesible al perder la máquina; réplicas en memoria pueden sufrir un fallo común; una réplica asíncrona puede no haber recibido una escritura confirmada. La garantía debe interpretarse junto con sus supuestos.

Referencia: cap. 7, “Isolation”, “Durability” y “Replication and Durability”, pp. 225-228. Las configuraciones de productos citadas en la edición no se presentan como valores actuales.

## Operaciones sobre uno o varios objetos

Una operación sobre un objeto puede ser atómica sin que una secuencia de operaciones sobre varios objetos lo sea. Tampoco agrupar comandos en una única solicitud demuestra por sí mismo atomicidad transaccional.

El libro usa un correo y un contador de mensajes no leídos: actualizar ambos por separado puede dejar el contador desfasado o permitir una observación parcial. También menciona datos desnormalizados e índices secundarios, que requieren mantener varias representaciones relacionadas.

Las transacciones sobre varios objetos simplifican ese mantenimiento. Sin ellas, la aplicación necesita afrontar escrituras incompletas y observaciones concurrentes por otros medios. Elegir un documento como unidad no elimina automáticamente las relaciones que atraviesan documentos.

Referencia: cap. 7, “Single-Object and Multi-Object Operations”, pp. 228-231. Síntesis del ejemplo del correo y su contador.

## Abortar y reintentar

Un aborto permite repetir una unidad de trabajo que no quedó aplicada parcialmente. El reintento exige distinguir un error transitorio de uno permanente y evitar que una sobrecarga produzca más sobrecarga por repetición indiscriminada.

Si el servidor confirmó internamente pero se perdió la respuesta, el cliente puede desconocer el resultado. Repetir en ese caso puede duplicar la operación. Los efectos externos tampoco se deshacen automáticamente al abortar la base: el libro lo ilustra con el envío de un correo. La transacción local cubre sus propios datos, no cualquier acción de la aplicación.

Referencia: cap. 7, “Handling errors and aborts”, pp. 231-232.

## Continuación de la lectura

[Anterior: Almacenamiento e índices](../03-almacenamiento-indices/README.md) · [Siguiente: Aislamiento y serializabilidad](../05-aislamiento/README.md)
