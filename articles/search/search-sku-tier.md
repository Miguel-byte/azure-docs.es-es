---
title: 'Selección de SKU o plan de tarifa del servicio Azure Search: Azure Search'
description: 'Azure Search puede aprovisionarse en estas SKU: Gratis, Básico o Estándar; este último está disponible en varias configuraciones de recursos y niveles de capacidad.'
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 1c86649a989b16d928a46d322af3d805b6fbf832
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647346"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Selección de un plan de tarifa de Azure Search

Al crear un servicio Azure Search, [se crea un recurso](search-create-service-portal.md) en el plan de tarifa (o SKU) que se ha fijado para el período de vigencia del servicio. Los niveles incluyen Gratis, Básico, Estándar y Almacenamiento optimizado. Estándar y Almacenamiento optimizado están disponibles con varias configuraciones y capacidades.

La mayoría de los clientes empiezan con el nivel Gratis para poder evaluar el servicio. Después de la evaluación, es habitual crear un segundo servicio en uno de los niveles más altos para las implementaciones de desarrollo y producción.

Aunque todos los niveles, incluido el nivel Gratis, suelen ofrecer paridad de características, es posible que las cargas de trabajo más grandes necesiten niveles más altos. Por ejemplo, el [enriquecimiento de IA con Cognitive Services](cognitive-search-concept-intro.md) tiene funciones de ejecución prolongada que agotan el tiempo de espera en un servicio gratuito, a menos que el conjunto de datos sea pequeño.

> [!NOTE] 
> La excepción a la paridad de características reside en los [indizadores](search-indexer-overview.md), que no están disponibles en S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Niveles disponibles

Los niveles reflejan las características del hardware que hospeda el servicio (en lugar de las características) y se diferencian por:

+ Cantidad de índices e indizadores que se pueden crear
+ Tamaño y velocidad de particiones (almacenamiento físico)

El nivel que se seleccione determina la tasa facturable. En la siguiente captura de pantalla de Azure Portal se muestran los niveles disponibles, menos los precios (que puede encontrar en el portal y en la [página de precios](https://azure.microsoft.com/pricing/details/search/). **Gratis**, **Básico** y **Estándar** son los niveles más comunes.

**Gratis** crea un servicio de búsqueda limitado en un clúster, que se comparte con otros suscriptores. Puede realizar proyectos pequeños, incluidos inicios rápidos y tutoriales, pero no puede escalar el servicio ni ejecutar cargas de trabajo considerables. **Básico** y **Estándar** son los niveles facturables usados con mayor frecuencia, mientras que el valor predeterminado es **Estándar**.

![Planes de tarifa de Azure Search](media/search-sku-tier/tiers.png "Planes de tarifa de Azure Search")

Algunos niveles están optimizados para determinados tipos de trabajo. Por ejemplo, **Estándar 3 Alta densidad (S3 HD)** es un *modo de hospedaje* para S3, donde el hardware subyacente está optimizado para un gran número de índices más pequeños y está pensado para escenarios de varios inquilinos. S3 HD tiene el mismo cargo por unidad que S3, pero el hardware está optimizado para lecturas de archivos rápidas en un gran número de índices más pequeños.

Los niveles **Almacenamiento optimizado** ofrecen mayor capacidad de almacenamiento a un precio menor por TB que los niveles Estándar. El principal inconveniente es una mayor latencia de consulta, lo cual debe validar para sus requisitos de aplicación específica.  Para saber más sobre las consideraciones de rendimiento de este nivel, vea [Estrategias de implementación y procedimientos recomendados para optimizar el rendimiento en Azure Search](search-performance-optimization.md).

Encontrará más información sobre los distintos niveles en la [página de precios](https://azure.microsoft.com/pricing/details/search/) del artículo [Límites de servicio en Azure Search](search-limits-quotas-capacity.md) y en la página del portal cuando vaya a aprovisionar un servicio.

## <a name="billable-events"></a>Eventos facturables

Una solución basada en Azure Search puede incurrir en costos de las siguientes maneras:

+ Costo base del servicio con la configuración mínima (crear un servicio)
+ Costo incremental al escalar (agregar réplicas o particiones)
+ Cargos de ancho de banda (transferencia de datos salientes) 
+ Búsqueda cognitiva (asociar Cognitive Services para enriquecimiento de inteligencia artificial, Azure Storage para almacén de conocimiento)

### <a name="service-costs"></a>Costos de servicio

A diferencia de las máquinas virtuales u otros recursos que se pueden "pausar" para evitar cargos, un servicio de Azure Search siempre está disponible en el hardware dedicado para su uso exclusivo. Así, la creación de un servicio es un evento facturable que se inicia cuando se crea el servicio y termina cuando se elimina este. 

El cargo mínimo es la primera unidad de búsqueda (una réplica x una partición) a la tasa facturable. Este valor mínimo es fijo durante la vigencia del servicio porque el servicio no se puede ejecutar en ninguna otra configuración menor que esta. Más allá del mínimo, puede agregar réplicas y particiones por separado. Los aumentos incrementales de la capacidad mediante réplicas y particiones aumentan la factura en función de la siguiente fórmula: [(réplicas x particiones x tarifa)](#search-units), donde la tarifa que se le cobra depende del plan de tarifa que seleccione.

Cuando esté calculando el costo de una solución de búsqueda, tenga en cuenta que los precios y la capacidad no son lineales (cuanto más se duplica la capacidad, más se duplica el costo). Para ver un ejemplo de cómo funciona la fórmula, eche un vistazo a [Cómo asignar particiones y réplicas](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Cargos de ancho de banda

El uso de [indexadores de Azure Search](search-indexer-overview.md) podría afectar a la facturación, según la ubicación de los servicios. Puede eliminar por completo los cargos de salida de datos si crea el servicio Azure Search en la misma región que los datos. Para saber más, eche un vistazo a la [página de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft no cobra por los datos de entrada a ningún servicio de Azure, ni por los datos de salida de Azure Search.
+ En soluciones de varios servicios, no hay ningún cargo por los datos que atraviesan la conexión cuando todos los servicios están en la misma región.

Si hay servicios en regiones diferentes, se aplican cargos por los datos de salida. Estos cargos realmente no forman parte de la factura de Azure Search. Se mencionan aquí porque si usa datos o indexadores enriquecidos con inteligencia artificial para extraer datos de distintas regiones, verá los costos reflejados en su factura general.

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Enriquecimiento de inteligencia artificial de búsqueda cognitiva con Cognitive Services

Para el [enriquecimiento de inteligencia artificial con Cognitive Services](cognitive-search-concept-intro.md), debe planear [asociar un recurso facturable de Azure Cognitive Services](cognitive-search-attach-cognitive-services.md), en la misma región que Azure Search, en el plan de tarifa S0 para el procesamiento de pago por uso. No hay ningún costo fijo por asociar Cognitive Services. Solo se paga por el procesamiento que necesita.

| Operación | Impacto en la facturación |
|-----------|----------------|
| Descifrado de documentos, extracción de texto | Gratuito |
| Descifrado de documentos, extracción de imágenes | Se factura según el número de imágenes extraídas de los documentos. En una [configuración de indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** es el parámetro que desencadena la extracción de imágenes. Si **imageAction** está establecido en "none" (valor predeterminado), no se le cobrará por la extracción de imágenes. La tarifa por extracción de imágenes está documentada en la página de [detalles de precios](https://azure.microsoft.com/pricing/details/search/) de Azure Search.|
| [Aptitudes cognitivas pregeneradas](cognitive-search-predefined-skills.md) | Se facturan a la misma tarifa que si se hubiera realizado la tarea mediante Cognitive Services directamente. |
| Aptitudes personalizadas | Una aptitud personalizada es funcionalidad que proporciona el usuario. El costo de usar una aptitud personalizada depende completamente de si el código personalizado está llamando a otros servicios de uso medido. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de facturación (R x P = SU)

El concepto de facturación más importante que se debe entender para las operaciones de Azure Search es la *unidad de búsqueda* (SU). Dado que Azure Search depende de las réplicas y las particiones para la indexación y las consultas, no tiene sentido facturar solo por una o la otra. En su lugar, la facturación se basa en una composición de ambas.

Una SU es el producto de las *réplicas* y las *particiones* usadas por un servicio: **(R x P = SU)** .

Cada servicio se inicia como mínimo con una SU (una réplica multiplicada por una partición). El máximo para cualquier servicio es 36 unidades de búsqueda. Este valor máximo se puede conseguir de varias maneras: por ejemplo, 6 particiones por 6 réplicas, o 3 particiones por 12 réplicas. Es habitual usar menos capacidad total (por ejemplo, un servicio de 3 réplicas y 3 particiones facturado como 9 SU). Vea el gráfico [Combinaciones de particiones y réplicas](search-capacity-planning.md#chart) para ver las combinaciones válidas.

La tarifa de facturación es de una hora por unidad. Cada nivel tiene una tarifa mayor de forma progresiva. Los niveles superiores incluyen particiones mayores y más rápidas, y esto contribuye a un total superior para ese nivel. Puede ver las tarifas de cada nivel en la página [detalles de precios](https://azure.microsoft.com/pricing/details/search/).

La mayoría de los clientes solo ponen en línea una parte de la capacidad total, manteniendo el resto en la reserva. Para la facturación, el número de particiones y réplicas que tiene en línea, calculado mediante la fórmula de SU, determina lo que se paga por hora.

## <a name="how-to-manage-and-reduce-costs"></a>Cómo administrar y reducir costos

Además de las sugerencias siguientes, visite [Administración de costos y facturación](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Cree todos los recursos en la misma región, o en el menor número de regiones posible, para minimizar o eliminar los cargos de ancho de banda.

- Consolide todos los servicios en un grupo de recursos, como Azure Search, Cognitive Services y cualquier otro servicio de Azure que se use en la solución. En Azure Portal, busque el grupo de recursos y use los comandos de **Administración de costos** para obtener información sobre el gasto real y previsto.

- Considere Azure Web App como aplicación de front-end para que las solicitudes y las respuestas permanezcan dentro del límite del centro de datos.

- Escale verticalmente las operaciones que consumen muchos recursos, como la indización, y vuelva a ajustar hacia abajo para las cargas de trabajo de consulta normales. Comience con la configuración mínima de Azure Search (una SU compuesta de una partición y una réplica) y luego supervise la actividad de los usuarios para identificar patrones de uso que indiquen la necesidad de más capacidad. Si hay un patrón predecible, es posible que pueda sincronizar la escala con la actividad (necesitaría escribir código para automatizar esto).

No se puede apagar un servicio de búsqueda para reducir la factura. Los recursos dedicados siempre están operativos y se asignan para su uso exclusivo mientras el servicio esté vigente. En lo que respecta al propio servicio, la única manera de reducir la factura es reducir las réplicas y las particiones a un nivel que aún proporcione rendimiento aceptable y [cumplimiento del SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), o crear un servicio en un nivel inferior (las tarifas por hora de S1 son inferiores a las de S2 o S3). Supongamos que aprovisiona su servicio con la menor de las previsiones de carga; si se supera el servicio, puede crear un segundo servicio con un nivel mayor, regenerar los índices en el segundo servicio y eliminar el primero.

## <a name="how-to-evaluate-capacity-requirements"></a>Cómo evaluar los requisitos de capacidad

En Azure Search, la capacidad se estructura como *réplicas* y *particiones*.

+ Las réplicas son instancias del servicio de búsqueda. Cada réplica hospeda una copia de carga equilibrada de un índice. Por ejemplo, un servicio con seis réplicas tiene seis copias de todos los índices cargados en el servicio.

+ Las particiones almacenan índices y dividen automáticamente los datos utilizables en búsquedas. Dos particiones dividen el índice por la mitad; tres particiones lo dividen en tercios y así sucesivamente. En términos de capacidad, el *tamaño de partición* es la principal característica diferenciadora entre niveles.

> [!NOTE]
> Todos los niveles Estándar y Almacenamiento optimizado admiten [combinaciones flexibles de réplicas y particiones](search-capacity-planning.md#chart) para que pueda [optimizar el sistema para velocidad o para almacenamiento](search-performance-optimization.md) con solo cambiar el equilibrio. El nivel Básico ofrece hasta tres réplicas para alta disponibilidad, pero solo tiene una partición. El nivel Gratis no proporciona recursos dedicados: los recursos de cálculo se comparten entre varios suscriptores.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Evaluar la capacidad

La capacidad y los costos de ejecución del servicio están relacionados. Los niveles imponen límites tanto en el almacenamiento como en los recursos. Debe tener en cuenta estos dos aspectos, porque el límite real será el que se alcance primero.

Los requisitos empresariales suelen imponer el número de índices que se necesitará. Por ejemplo, podría necesitar un índice global para un repositorio de documentos de gran tamaño. O podría necesitar varios índices en función de la región, la aplicación o el nicho de negocio.

Para determinar el tamaño de un índice, tendrá que [crear uno](search-create-index-portal.md). La estructura de datos de Azure Search es principalmente una estructura de [índice invertido](https://en.wikipedia.org/wiki/Inverted_index), que tiene características diferentes que los datos de origen. Para un índice invertido, el tamaño y la complejidad vienen determinados por el contenido, y no necesariamente por la cantidad de datos que se incorporan. Un origen de datos de gran tamaño con mucha redundancia podría dar lugar a un índice más pequeño que un conjunto de datos más pequeño que incluya contenido muy variable. Así que es poco probable deducir el tamaño del índice en función del tamaño del conjunto de datos original.

> [!NOTE] 
> Aunque el cálculo de las necesidades futuras de índices y almacenamiento se hace a partir de suposiciones, vale la pena hacerlo. Si la capacidad de un nivel resulta demasiado baja, tendrá que aprovisionar un nuevo servicio en un nivel superior y luego [volver a cargar los índices](search-howto-reindex.md). No existe ninguna actualización local de un servicio de una SKU a otra.
>

### <a name="estimate-with-the-free-tier"></a>Estimación con el nivel Gratis

Un enfoque para calcular la capacidad es empezar con el nivel Gratis. Recuerde que el servicio Gratis ofrece hasta 3 índices, 50 MB de almacenamiento y 2 minutos de tiempo de indexación. Puede resultar complicado calcular un tamaño de índice proyectado con estas restricciones, pero estos son los pasos:

+ [Cree un servicio gratuito](search-create-service-portal.md).
+ Prepare un conjunto de datos pequeño y representativo.
+ [Genere un índice inicial en el portal](search-create-index-portal.md) y anote su tamaño. Las características y los atributos afectan al almacenamiento. Por ejemplo, al agregar proveedores de sugerencias (escritura anticipada), aumentan los requisitos de almacenamiento. Con el mismo conjunto de datos, puede intentar crear varias versiones de un índice, con atributos diferentes en cada campo, para ver cómo varían los requisitos de almacenamiento. Para obtener más información, vea ["Implicaciones de almacenamiento" en Creación de un índice básico](search-what-is-an-index.md#storage-implications).

Con una estimación aproximada, podría doblar esa cantidad a fin de presupuestar dos índices (desarrollo y producción) y luego elegir el nivel en consecuencia.

### <a name="estimate-with-a-billable-tier"></a>Estimación con un nivel facturable

Los recursos dedicados pueden adaptarse a mayores tiempos de muestreo y procesamiento para realizar estimaciones más realistas de cantidad de índices, tamaño y volúmenes de consulta durante el desarrollo. Algunos clientes pasan directamente a un nivel facturable y luego reevalúan a medida que el proyecto de desarrollo madura.

1. [Revise los límites del servicio en cada nivel](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar si los niveles más bajos pueden admitir la cantidad de índices que necesita. En los niveles Básico, S1 y S2, los límites de índices son 15, 50 y 200 respectivamente. El nivel Almacenamiento optimizado tiene un límite de 10 índices porque se ha diseñado para admitir un número reducido de índices muy grandes.

1. [Cree un servicio en un nivel facturable](search-create-service-portal.md):

    + Comience por abajo, en Básico o S1, si no está seguro de la carga proyectada.
    + Empiece por arriba, con un nivel S2 o incluso S3, si sabe que va a tener cargas de consulta e indexación a gran escala.
    + Empiece con Almacenamiento optimizado, en L1 o L2, si va a indexar una gran cantidad de datos y la carga de consultas es relativamente baja, como con una aplicación empresarial interna.

1. [Genere un índice inicial](search-create-index-portal.md) para determinar cómo se traducen los datos de origen a un índice. Esta es la única manera de calcular el tamaño del índice.

1. [Supervise el almacenamiento, los límites del servicio, el volumen de consultas y la latencia](search-monitor-usage.md) en el portal. El portal muestra las consultas por segundo, las consultas limitadas y la latencia de búsqueda. Todos estos valores pueden ayudarle a decidir si ha seleccionado el nivel correcto. También puede habilitar el [análisis del tráfico de búsqueda](search-traffic-analytics.md) para configurar la supervisión detallada de valores como el análisis del porcentaje de clics.

El tamaño y la cantidad de índices son igualmente importantes para el análisis. Esto es así porque se alcanzan los límites máximos al usar por completo el almacenamiento (particiones) o al agotar todos los recursos (índices, indexadores, etc.), lo que ocurra primero. El portal le ayuda a realizar un seguimiento de ambos, mostrando el uso actual y los límites máximos en paralelo en la página Información general.

> [!NOTE]
> Los requisitos de almacenamiento pueden parecer excesivos si los documentos contienen datos extraños. Lo ideal es que los documentos contengan solo los datos que necesita para la experiencia de búsqueda. No es posible realizar búsquedas en datos binarios, así que deben almacenarse por separado (quizás en Azure Blob Storage o en una tabla de Azure). Después hay que agregar un campo en el índice para mantener una referencia de URL a los datos externos. El tamaño máximo de un documento individual es 16 MB (o menos si va a cargar de forma masiva varios documentos en una sola solicitud). Para más información, consulte [Límites de servicio en Azure Search](search-limits-quotas-capacity.md).
>

**Consideraciones del volumen de consultas**

Las consultas por segundo (QPS) son una medida importante durante la optimización del rendimiento, pero solo suele ser una consideración de nivel si espera un volumen elevado de consultas desde el principio.

El nivel Estándar puede proporcionar un equilibrio entre réplicas y particiones. Puede aumentar el tiempo de respuesta de las consultas mediante la adición de réplicas para el equilibrio de carga o agregar particiones para el procesamiento paralelo. Luego puede ajustar el rendimiento después de aprovisionar el servicio.

Si prevé grandes volúmenes de consultas sostenidos desde el principio, considere la opción de los niveles Estándar superiores, respaldados por un hardware más potente. Luego puede desconectar particiones y réplicas, o incluso cambiar a un servicio de nivel inferior, si no llega a tener los volúmenes de consultas previstos. Para más información sobre cómo calcular el rendimiento de las consultas, consulte [Rendimiento y optimización de Azure Search](search-performance-optimization.md).

El nivel Almacenamiento optimizado es útil para cargas de trabajo de datos de gran tamaño, ya que admite más almacenamiento de índice general disponible para cuando los requisitos de latencia de consulta sean menos importantes. Tendrá que seguir usando réplicas adicionales para el equilibrio de carga y particiones adicionales para el procesamiento paralelo. Luego puede ajustar el rendimiento después de aprovisionar el servicio.

**Contratos de nivel de servicio**

El nivel Gratis y las características en vista previa no ofrecen [contratos de nivel de servicio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todos los niveles facturables, los SLA tomarán efecto cuando se aprovisione suficiente redundancia para el servicio. Necesitará dos o más réplicas para los SLA de consulta (lectura). Necesitará tres o más réplicas para los SLA de consulta e indización (lectura-escritura). El número de particiones no afecta a los SLA.

## <a name="tips-for-tier-evaluation"></a>Sugerencias para la evaluación de nivel

+ Aprenda a crear índices eficientes y conozca qué métodos de actualización tendrán un impacto menor. Use el [análisis del tráfico de búsqueda](search-traffic-analytics.md) para obtener información sobre la actividad de consultas.

+ Cree métricas sobre las consultas y recopile datos sobre los patrones de uso (consultas durante el horario laboral, indexación durante las horas de menor uso). Use estos datos para respaldar las decisiones de aprovisionamiento del servicio. Aunque no resulta práctico en una cadencia horaria o diaria, puede ajustar dinámicamente las particiones y los recursos para adaptar los cambios planeados en el volumen de consultas. También puede adaptar los cambios no planeados pero sostenidos si los niveles se mantienen lo suficiente para garantizar que se puedan realizar acciones.

+ Recuerde que la única desventaja de un aprovisionamiento inferior es que es posible que deba anular un servicio si los requisitos reales son mayores que los que había previsto. Para evitar la interrupción del servicio, se crea un nuevo servicio en la misma suscripción en un nivel superior y se ejecuta en paralelo hasta que todas las solicitudes y las aplicaciones se dirijan al nuevo punto de conexión.

## <a name="next-steps"></a>Pasos siguientes

Comience con un nivel Gratis y cree un índice inicial usando un subconjunto de los datos para comprender sus características. La estructura de datos de Azure Search es una estructura de índice invertido. El tamaño y la complejidad de un índice invertido vienen determinados por el contenido. Recuerde que un contenido de gran redundancia tiende a producir un índice más pequeño que el contenido de alta irregularidad. Por tanto, son las características del contenido más que el tamaño del conjunto de datos lo que determina los requisitos de almacenamiento de índices.

Una vez que tenga un cálculo inicial del tamaño del índice, [aprovisione un servicio facturable](search-create-service-portal.md) en uno de los niveles que hemos descrito en este artículo: Básico, Estándar o Almacenamiento optimizado. Rebaje cualquier restricción artificial sobre el tamaño de los datos y [recompile el índice](search-howto-reindex.md) para que incluya todos los datos sobre los que quiera realizar búsquedas.

[Asigne particiones y réplicas](search-capacity-planning.md) según sea necesario para obtener el rendimiento y la escala que necesite.

Si la capacidad y el rendimiento son válidos, ya estará listo. En caso contrario, vuelva a crear un servicio de búsqueda en otro nivel que se adapte más estrechamente a sus necesidades.

> [!NOTE]
> Si tiene alguna duda, puede publicarla en [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) o [ponerse en contacto con el Soporte técnico de Azure](https://azure.microsoft.com/support/options/).
