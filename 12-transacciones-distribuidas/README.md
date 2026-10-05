# Transacciones distribuidas y consistencia

[Índice general](../README.md) · [Glosario](../glosario/README.md)

Fuente exclusiva: Kleppmann, primera edición de 2017. Páginas impresas del libro.

## Índice

- [Distinguir las garantías](#distinguir-las-garantías)
  - [Cada garantía responde una pregunta](#cada-garantía-responde-una-pregunta)
- [Linealizabilidad y particiones de red](#linealizabilidad-y-particiones-de-red)
  - [Seguir la lectura del resultado deportivo](#seguir-la-lectura-del-resultado-deportivo)
  - [Unicidad y coordinación entre servicios](#unicidad-y-coordinación-entre-servicios)
  - [El costo durante una partición de red](#el-costo-durante-una-partición-de-red)
- [Causalidad y orden total](#causalidad-y-orden-total)
  - [Dependencias y operaciones concurrentes](#dependencias-y-operaciones-concurrentes)
  - [Numerar no equivale a entregar en orden](#numerar-no-equivale-a-entregar-en-orden)
- [Compromiso en dos fases, 2PC](#compromiso-en-dos-fases-2pc)
  - [Recorrido completo de una decisión](#recorrido-completo-de-una-decisión)
  - [Por qué no alcanza con abortar al vencer un timeout](#por-qué-no-alcanza-con-abortar-al-vencer-un-timeout)
  - [Atomicidad a cambio de una dependencia operativa](#atomicidad-a-cambio-de-una-dependencia-operativa)
- [Operación y consenso tolerante a fallos](#operación-y-consenso-tolerante-a-fallos)
  - [Un mensaje y sus efectos en la base](#un-mensaje-y-sus-efectos-en-la-base)
  - [Seguridad y progreso en consenso](#seguridad-y-progreso-en-consenso)
  - [Todos los participantes frente a una mayoría](#todos-los-participantes-frente-a-una-mayoría)
- [Diagrama de apoyo](#diagrama-de-apoyo)

## Distinguir las garantías

La consistencia eventual permite diferencias temporales entre réplicas; la convergencia por sí sola no dice qué observa una lectura durante ese intervalo. Linealizabilidad, serializabilidad, compromiso atómico y consenso responden a preguntas diferentes.

La linealizabilidad busca que cada operación parezca actuar en un único punto entre su inicio y su respuesta, respetando el orden real de operaciones no superpuestas. La serializabilidad compara transacciones con algún orden serial; no exige por sí sola que ese orden respete el tiempo real. El compromiso atómico determina un resultado conjunto de confirmación o aborto. El consenso permite acordar una decisión propuesta.

Referencias: cap. 9, “Consistency Guarantees”, pp. 322-324; “Linearizability”, pp. 324-330; “Distributed Transactions and Consensus”, pp. 352-354.

### Cada garantía responde una pregunta

| Garantía | Pregunta que responde | Límite |
| --- | --- | --- |
| Linealizabilidad | ¿Una operación puede ubicarse en un punto entre su inicio y su respuesta, respetando el tiempo real? | Por sí sola no describe el aislamiento de una transacción con varias operaciones. |
| Serializabilidad | ¿El resultado equivale a ejecutar las transacciones en algún orden serial? | Ese orden no tiene que respetar el tiempo real. |
| Compromiso atómico | ¿Los participantes acuerdan confirmar o abortar la transacción? | No determina por sí solo qué pueden leer transacciones concurrentes. |
| Consenso | ¿Los participantes acuerdan una decisión propuesta? | Sus garantías de progreso dependen del algoritmo y de sus supuestos de fallos y comunicación. |

Una transacción puede tener un resultado conjunto sin ofrecer aislamiento serializable. También puede existir un registro linealizable sin una transacción que modifique varios registros. Separar las preguntas evita atribuir al protocolo una garantía que corresponde a otro mecanismo.

El libro llama serializabilidad estricta a la combinación de serializabilidad y linealizabilidad. En ese caso, el orden serial también debe respetar el orden real de las transacciones que no se superponen.

Referencia: cap. 9, “Linearizability Versus Serializability”, pp. 329-330; “Distributed Transactions and Consensus”, pp. 352-354.

## Linealizabilidad y particiones de red

El ejemplo del resultado deportivo muestra un lector que ve el resultado final y otro que, después de conocer esa observación, lee una réplica que aún presenta el partido en curso. La copia atrasada no ofrece la ilusión de un único dato vigente.

La garantía resulta relevante también para restricciones de unicidad, bloqueos y relaciones entre servicios. Bajo una interrupción de red, un sistema que necesita coordinar copias para preservar linealizabilidad puede dejar de atender ciertas solicitudes. Permitir que ambos lados respondan sin esa coordinación puede debilitar la garantía.

Kleppmann explica CAP como una limitación ante particiones de red, no como una elección libre y permanente de dos propiedades entre tres. La partición de red es un fallo de comunicación; la partición del capítulo 6 divide datos deliberadamente.

Referencias: cap. 9, “Relying on Linearizability”, pp. 330-332; “Implementing Linearizable Systems”, pp. 332-335; “The Cost of Linearizability”, pp. 335-339. El ejemplo deportivo pertenece a 2014.

### Seguir la lectura del resultado deportivo

En el ejemplo del libro, una persona lee que terminó el partido y comunica el resultado. Otra persona, después de recibir esa información, consulta una réplica atrasada y encuentra que el partido todavía está en curso.

La segunda lectura empieza después de que la primera ya devolvió el resultado final. Una representación linealizable debe respetar ese orden. No puede ubicar la segunda lectura antes de la actualización solamente para justificar que encontró el valor anterior.

Si dos lecturas se superponen con una escritura, hay más libertad para ordenarlas dentro de sus intervalos. La garantía no exige que todas las operaciones ocurran instantáneamente en la implementación; exige que sus resultados admitan una explicación compatible con esos intervalos y con el orden real pertinente.

Referencia: cap. 9, “What Makes a System Linearizable?”, pp. 325-329. El ejemplo deportivo pertenece a 2014.

### Unicidad y coordinación entre servicios

Para registrar un nombre de usuario único, comprobar por separado dos copias atrasadas puede llevar a aceptar el mismo nombre dos veces. La comprobación y la decisión necesitan una garantía que evite que ambas operaciones tengan éxito incompatiblemente.

El libro relaciona ese problema con bloqueos y restricciones de unicidad. También presenta un procesamiento de imágenes donde la información de una escritura y el mensaje que dispara otra tarea recorren sistemas distintos. Si el consumidor observa el mensaje antes de poder leer el dato necesario, la tarea puede fallar pese a que el productor ya realizó la escritura.

Conservar el orden dentro de un componente no conserva automáticamente las dependencias entre componentes. El diseño debe explicar cómo sabe el consumidor que el estado requerido está disponible para su lectura.

Referencia: cap. 9, “Relying on Linearizability”, pp. 330-332.

### El costo durante una partición de red

Si dos lados de una partición no pueden coordinarse y ambos responden libremente, pueden producir observaciones incompatibles con un único estado linealizable. Preservar esa garantía puede exigir que un lado rechace operaciones o espere hasta recuperar comunicación.

| Prioridad durante el fallo de comunicación | Consecuencia |
| --- | --- |
| Mantener linealizabilidad | Algunas operaciones no pueden completarse mientras falta la coordinación necesaria. |
| Responder desde copias que no pueden coordinarse | Las respuestas pueden debilitar la garantía de un único dato vigente. |

CAP usa una noción específica de disponibilidad; no es una medida general del porcentaje de uptime ni una receta para elegir bases. La tensión aparece ante la partición de red. Sin esa partición siguen existiendo costos de latencia, porque coordinar nodos requiere mensajes.

El libro también explica que la consistencia causal puede permitir operaciones que no necesitan el mismo orden total global. Ofrece una garantía diferente, con restricciones diferentes. La elección depende de las observaciones que debe impedir el sistema.

Referencia: cap. 9, “The Cost of Linearizability”, pp. 335-339; “The causal order is not a total order”, pp. 341-343.

## Causalidad y orden total

Un orden causal conserva dependencias: un efecto debe seguir a las operaciones de las que depende. Operaciones concurrentes pueden no tener un orden causal entre sí. Los relojes lógicos ayudan a expresar orden sin depender exclusivamente de relojes físicos sincronizados.

La difusión con orden total entrega a los participantes los mensajes en un mismo orden y de forma fiable. Asignar números de orden no basta: también hace falta saber qué mensajes anteriores existen y garantizar su entrega. El libro relaciona ese problema con consenso y con la construcción de operaciones linealizables.

Referencia: cap. 9, “Ordering Guarantees”, “Ordering and Causality”, “Sequence Number Ordering” y “Total Order Broadcast”, pp. 339-352.

### Dependencias y operaciones concurrentes

Si una operación lee un valor y luego produce otro a partir de él, existe una dependencia causal. En cambio, dos operaciones que no conocen los efectos de la otra pueden ser concurrentes. Un orden causal conserva la dependencia sin exigir que esas operaciones concurrentes tengan una precedencia causal inventada.

Un orden total sí permite comparar cualquier par. Puede extender el orden causal asignando una posición arbitraria a operaciones concurrentes, siempre que respete las dependencias que ya existen.

La diferencia tiene un costo práctico. Rastrear todas las dependencias puede requerir mucha información. El libro presenta números de secuencia y relojes lógicos como una representación compacta de un orden compatible con causalidad. Una marca física, por sí sola, no garantiza ese orden.

Referencia: cap. 9, “Ordering and Causality” y “Sequence Number Ordering”, pp. 339-346.

### Numerar no equivale a entregar en orden

Los relojes de Lamport permiten construir un orden compatible con causalidad usando contadores e identificadores de nodos. Si A causó B, el número de A precede al de B. El inverso no vale: un número menor no demuestra que hubo una dependencia causal.

Tener números tampoco permite decidir siempre de inmediato cuál mensaje es el siguiente. Un nodo puede desconocer una operación con un número anterior que todavía no recibió. Para ejecutar decisiones en un orden compartido hacen falta garantías sobre entrega, además de una regla para comparar números.

La difusión con orden total combina entrega fiable con el mismo orden de entrega para todos los participantes pertinentes. El libro muestra su utilidad para replicar una máquina de estados: si las réplicas parten del mismo estado y ejecutan operaciones deterministas en el mismo orden, producen el mismo resultado.

Referencia: cap. 9, “Lamport timestamps”, pp. 345-347; “Total Order Broadcast”, pp. 348-352.

## Compromiso en dos fases, 2PC

Una transacción distribuida puede necesitar modificar varios participantes. Confirmar cada uno independientemente permite que unos confirmen y otros aborten. 2PC agrega un coordinador y divide la decisión en preparación y resolución.

Durante la preparación, cada participante comprueba que puede confirmar y conserva el estado necesario para cumplir su voto afirmativo. El coordinador decide confirmar si todos pueden hacerlo; de lo contrario, decide abortar. Registra durablemente la decisión antes de comunicarla. Un participante que ya prometió confirmar no puede decidir por su cuenta otro resultado.

Si el coordinador falla después de la preparación, un participante puede quedar en duda y retener recursos mientras espera conocer la decisión. Un timeout no autoriza simplemente a abortar después de votar sí, porque otros participantes podrían haber confirmado. El registro del coordinador forma parte del estado necesario para recuperarse.

Referencia: cap. 9, “Atomic Commit and Two-Phase Commit (2PC)”, “A system of promises” y “Coordinator failure”, pp. 354-359. 2PC aporta compromiso atómico; no reemplaza un mecanismo de aislamiento como 2PL.

### Recorrido completo de una decisión

La preparación es una promesa durable. Antes de votar sí, el participante debe comprobar las condiciones que podrían impedir la confirmación y conservar la información necesaria para completarla incluso después de reiniciar.

1. La aplicación realiza operaciones locales dentro de una transacción distribuida identificada.
2. El coordinador pide preparar a los participantes.
3. Cada participante verifica restricciones y conflictos. Si no puede comprometerse, responde no; si responde sí, registra el estado necesario y pierde la libertad de abortar unilateralmente.
4. El coordinador reúne los votos. Decide confirmar únicamente si todos son afirmativos; un rechazo o una falta de respuesta durante esta fase conduce al aborto.
5. Registra la decisión en almacenamiento durable antes de comunicarla.
6. Envía la decisión y reintenta su entrega a los participantes que todavía no respondieron.

Hay dos puntos distintos que requieren persistencia: el voto afirmativo del participante y la decisión del coordinador. El primero permite cumplir una promesa tras un reinicio. El segundo permite repetir la misma decisión tras recuperar al coordinador.

Referencia: cap. 9, “A system of promises”, pp. 357-358.

### Por qué no alcanza con abortar al vencer un timeout

El caso de fallo del libro ocurre después de que los participantes votaron sí. Uno recibe la decisión de confirmar; otro todavía no la recibe. El coordinador falla antes de completar la comunicación.

| Participante | Información local |
| --- | --- |
| El que recibió la decisión | Sabe que debe confirmar. |
| El que solo votó sí | Sabe que prometió cumplir la decisión, pero todavía no conoce cuál fue. |

Si el segundo aborta por su cuenta al vencer un timeout, el resultado deja de ser atómico: uno confirmó y otro abortó. Esperar conserva la posibilidad de cumplir la decisión común, pero retiene recursos y puede bloquear otras transacciones.

El coordinador recuperado consulta su registro para conocer la decisión durable. Puede continuar enviando confirmaciones que no llegaron. En el procedimiento descrito, si no existe una decisión de confirmación registrada, puede resolver el aborto. Esa autoridad no se obtiene adivinando a partir del tiempo transcurrido.

Referencia: cap. 9, “Coordinator failure”, pp. 358-359.

### Atomicidad a cambio de una dependencia operativa

2PC evita que cada participante decida de manera independiente, pero introduce dependencia del coordinador. Un participante preparado puede conservar bloqueos durante la espera. Entonces el fallo de una parte afecta operaciones que necesitan los mismos datos, aunque el proceso de la base siga funcionando.

El libro comenta 3PC y sus supuestos de demoras acotadas. No lo presenta como una solución general para redes con pausas y retrasos arbitrarios. El modelo de fallos es parte de la validez del protocolo.

También hay que distinguir 2PC de 2PL. El primero coordina confirmar o abortar entre participantes. El segundo usa bloqueos para aislamiento. Pueden coexistir; sus dos fases se refieren a tareas diferentes.

Referencias: cap. 9, “Coordinator failure” y “Three-phase commit”, pp. 358-360; cap. 7, “Two-Phase Locking (2PL)”, pp. 257-261.

## Operación y consenso tolerante a fallos

El libro distingue transacciones distribuidas dentro de una tecnología de transacciones entre sistemas heterogéneos. El ejemplo de un mensaje y una escritura combina confirmar el procesamiento del mensaje con persistir sus efectos. La coordinación cubre participantes que soportan el protocolo; no incorpora automáticamente efectos externos arbitrarios.

XA establece una interfaz de coordinación, pero el estado del coordinador, las transacciones en duda y los bloqueos afectan la operación. Si el coordinador vive en el proceso de la aplicación, esa parte deja de ser completamente descartable: sus registros son necesarios para recuperar decisiones.

El consenso busca acuerdo, integridad, validez y terminación bajo los supuestos del algoritmo. Protocolos como Paxos, Raft y otros descritos en el libro usan épocas y quorums para evitar decisiones incompatibles y avanzar cuando existe una mayoría adecuada. Preservar seguridad puede implicar detener el progreso si falta esa mayoría.

Consenso y 2PC están relacionados, pero no son nombres intercambiables. El compromiso atómico requiere poder confirmar todos los participantes; una decisión ordinaria de consenso puede escoger una propuesta. Los servicios de coordinación usan consenso para sostener decisiones, liderazgo y metadatos, con costos de comunicación y límites de disponibilidad.

Referencias: cap. 9, “Distributed Transactions in Practice”, pp. 360-364; “Fault-Tolerant Consensus”, pp. 364-370; “Membership and Coordination Services”, pp. 370-373. La evaluación de productos corresponde a 2017.

### Un mensaje y sus efectos en la base

El ejemplo del libro combina procesar un mensaje con escribir en una base. Si se confirma el consumo antes de persistir el efecto y luego el proceso falla, el mensaje puede dejar de estar disponible sin que exista la escritura. Si se escribe primero y se falla antes de confirmar el consumo, el mensaje puede volver a entregarse y su efecto ejecutarse otra vez.

Una transacción distribuida puede coordinar el reconocimiento del mensaje y la escritura cuando ambos participantes soportan el protocolo. El resultado conjunto cubre esas operaciones participantes. No incorpora automáticamente cualquier acción externa que la aplicación realice durante el procesamiento.

XA ofrece una interfaz para que el coordinador solicite preparar, confirmar o abortar a través de los controladores. El libro distingue esa interfaz de un protocolo de red único. La implementación del coordinador sigue siendo responsable de conservar las decisiones.

Si el coordinador vive en el proceso de la aplicación y su registro está en disco local, reemplazar ese servidor sin recuperar el registro puede impedir resolver transacciones en duda. La aplicación tiene entonces estado durable necesario para la recuperación.

Referencia: cap. 9, “Distributed Transactions in Practice” y “XA transactions”, pp. 360-363. La lista de tecnologías que soportaban XA corresponde a 2017.

### Seguridad y progreso en consenso

Acuerdo exige que los nodos no decidan resultados incompatibles. Integridad evita decidir varias veces; validez vincula la decisión con propuestas permitidas. Terminación exige llegar a decidir bajo los supuestos del algoritmo. Mantener seguridad durante un fallo no implica poder continuar operando durante ese fallo.

Las épocas distinguen períodos de liderazgo. Un líder anterior puede seguir creyendo que dirige el sistema después de perder comunicación. Los protocolos necesitan reglas que impidan aceptar sus propuestas cuando los participantes ya reconocen una época superior.

Los quorums de elección y de decisión deben intersectar de la manera que exige el algoritmo. Esa intersección permite que un nuevo líder encuentre y respete decisiones anteriores. Elegir a un nodo sin recuperar el historial pertinente no basta para obtener consenso.

Con tres nodos, una mayoría requiere dos; con cinco, requiere tres. Esos tamaños permiten tolerar respectivamente uno o dos nodos no disponibles, siempre que los restantes puedan comunicarse y se cumplan los supuestos de progreso. La existencia abstracta de suficientes nodos no garantiza mensajes oportunos ni estabilidad del liderazgo.

Referencia: cap. 9, “Fault-Tolerant Consensus” y “Single-leader replication and consensus”, pp. 364-369.

### Todos los participantes frente a una mayoría

| Mecanismo | Condición que destaca el libro | Costo ante fallos |
| --- | --- | --- |
| 2PC | Todos los participantes deben poder confirmar para decidir commit. | Un participante preparado puede quedar en duda ante el fallo del coordinador. |
| Consenso tolerante a fallos | Una mayoría adecuada permite acordar decisiones y recuperar liderazgo según el protocolo. | Sin el quorum requerido se conserva seguridad deteniendo el progreso. |

Una mayoría de consenso no habilita a confirmar una transacción ignorando el rechazo de un participante que debe modificar datos. Replicar la decisión del coordinador y comprobar que todos los participantes pueden confirmar son problemas relacionados, pero diferentes.

Los protocolos de consenso agregan rondas de comunicación y trabajo de replicación. Reducir dependencia de un coordinador único requiere sostener un conjunto coordinado de nodos. Los servicios basados en consenso del libro se usan para liderazgo y metadatos compartidos, sin convertir toda la carga de la aplicación en operaciones de coordinación.

Referencia: cap. 9, “Fault-Tolerant Consensus”, pp. 364-370; “Membership and Coordination Services”, pp. 370-373.

## Diagrama de apoyo

![2PC: recorrido de una confirmación exitosa](diagramas/2pc.svg)

[Fuente editable](diagramas/2pc.drawio). Se muestra solo el camino exitoso. Aborto, fallos y participantes en duda se explican en el texto. Referencia: Cap. 9, “A system of promises”, pp. 357-358.

## Continuación de la lectura

[Anterior: Partición](../11-particion/README.md) · [Siguiente: Persistencia para análisis de datos](../13-analitica/README.md)
