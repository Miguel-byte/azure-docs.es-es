---
title: 'Alta disponibilidad: servicio Azure SQL Database | Microsoft Docs'
description: Obtenga información acerca de las funcionalidades y características de alta disponibilidad de Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 226b0c1cb11fc872cb7759e0d0e49275b9c2d9bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568142"
---
# <a name="high-availability-and-azure-sql-database"></a>Alta disponibilidad y Azure SQL Database

El objetivo de la arquitectura de alta disponibilidad en Azure SQL Database es garantizar que la base de datos funciona el 99,99 % de las veces, sin preocuparse del impacto de las operaciones de mantenimiento y las interrupciones. Azure controla automáticamente tareas de mantenimiento críticas, como aplicación de revisiones, copias de seguridad, actualizaciones de Windows y SQL, así como los eventos no planeados, como los errores subyacentes de hardware, software o red.  Cuando se conmute por error la instancia de SQL subyacente o se le apliquen revisiones, no se notará el tiempo de inactividad si [usa la lógica de reintento](sql-database-develop-overview.md#resiliency) de su aplicación. Azure SQL Database puede recuperarse rápidamente, incluso en las circunstancias más críticas, asegurando que los datos estén siempre disponibles.

La solución de alta disponibilidad está diseñada para garantizar que los datos confirmados nunca se pierden debido a errores, que las operaciones de mantenimiento no afectan a la carga de trabajo y que la base de datos no será un único punto de error en la arquitectura de software. No hay ventanas de mantenimiento ni tiempos de inactividad que puedan requerir que detenga la carga de trabajo mientras se actualiza o se mantiene la base de datos. 

Existen dos modelos de arquitectura de alta disponibilidad que se usan en Azure SQL Database:

- El modelo de disponibilidad estándar que se basa en la separación del proceso y el almacenamiento.  Depende de la alta disponibilidad y la confiabilidad de la capa de almacenamiento remoto. Esta arquitectura va dirigida a las aplicaciones de negocio preocupadas por la economía que pueden permitirse una cierta degradación del rendimiento durante las actividades de mantenimiento.
- El modelo de disponibilidad premium que se basa en un clúster de procesos del motor de base de datos. Depende del hecho de que siempre hay un quórum de nodos del motor de base de datos disponibles. Esta arquitectura va dirigida a aplicaciones críticas con alto rendimiento de E/S y elevada tasa de transacciones, y garantiza un impacto mínimo sobre el rendimiento de la carga de trabajo durante las actividades de mantenimiento.

Azure SQL Database se ejecuta en la versión estable más reciente del Motor de base de datos de SQL Server y del sistema operativo Windows, y la mayoría de los usuarios no advierten que las actualizaciones se realizan continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidad de los niveles de servicio Básico, Estándar y Uso general

Estos niveles de servicio aprovechan la arquitectura de disponibilidad estándar. En la siguiente ilustración se muestran cuatro nodos diferentes con las capas de proceso y almacenamiento separadas.

![Separación de proceso y almacenamiento](media/sql-database-high-availability/general-purpose-service-tier.png)

El modelo de disponibilidad estándar incluye dos capas:

- Una capa de proceso sin estado que ejecuta el proceso `sqlserver.exe` y que solo contiene datos transitorios y almacenado en caché en la unidad SSD asociada, como TempDB, base de datos modelo, caché de planes, grupo de búferes y grupo de almacén de columnas. Este nodo sin estado lo opera Azure Service Fabric, que inicializa `sqlserver.exe`, controla el estado del nodo y realiza la conmutación por error a otro nodo si es necesario.
- Una capa de datos con estado con archivos de base de datos (.mdf o .ldf) que se almacenan en Azure Blob Storage. Azure Blob Storage presenta características integradas de redundancia y disponibilidad de los datos. Garantiza que todos los registros del archivo de registro o de la página del archivo de datos se conservarán incluso si se bloquea el proceso de SQL Server.

Siempre que se actualice el motor de base de datos o el sistema operativo, o se detecte un error, Azure Service Fabric moverá el proceso sin estado de SQL Server a otro nodo de proceso sin estado son capacidad suficiente disponible. Los datos de Azure Blob Storage no resultan afectados por esta operación y los archivos de registro o de datos se asocian al proceso de SQL Server recién inicializado. Aunque este proceso garantiza el 99,99 % de disponibilidad, una carga de trabajo pesada podría experimentar una degradación del rendimiento durante la transición dado que la nueva instancia de SQL Server se inicia con la caché inactiva.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidad de los niveles de servicio Premium y Crítico para la empresa

Los niveles de servicio premium y crítico para la empresa aprovechan el modelo de disponibilidad premium, que integra recursos de proceso (proceso del Motor de base de datos de SQL Server) y almacenamiento (SSD conectado localmente) en un único nodo. Para conseguir alta disponibilidad se replica el proceso y el almacenamiento en nodos adicionales a fin de crear un clúster formado por tres o cuatro nodos. 

![Clúster de nodos del motor de base de datos](media/sql-database-high-availability/business-critical-service-tier.png)

Los archivos de base de datos subyacentes (.mdf o .ldf) se colocan en el almacenamiento SSD conectado para proporcionar una latencia muy baja de E/S para la carga de trabajo. Para implementar alta disponibilidad se usa una tecnología parecida a la de los [grupos de disponibilidad AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server. El clúster incluye una única réplica principal (proceso de SQL Server) que es accesible para las cargas de trabajo de cliente de lectura y escritura, y hasta tres réplicas secundarias (proceso y almacenamiento) que contienen copias de los datos. El nodo principal inserta constantemente los cambios en los nodos secundarios en orden y garantiza que los datos se sincronizan con al menos una réplica secundaria antes de confirmar cada transacción. Este proceso garantiza que si el nodo principal se bloquea por cualquier motivo, siempre hay un nodo totalmente sincronizado al que conmutar por error. La conmutación por error se inicia en Azure Service Fabric. Una vez que la réplica secundaria se convierte en el nuevo nodo principal, se crea otra réplica secundaria para garantizar que el clúster tiene suficientes nodos (conjunto de quórum). Cuando finaliza la conmutación por error, las conexiones SQL se redirigen automáticamente al nuevo nodo principal.

Como ventaja adicional, el modelo de disponibilidad premium incluye la posibilidad de redirigir las conexiones SQL de solo lectura a una de las réplicas secundarias. Esta característica se denomina [escalado horizontal de lectura](sql-database-read-scale-out.md) y proporciona el 100 % de capacidad de proceso adicional al mismo costo para descargar operaciones de solo lectura, como cargas de trabajo de análisis, desde la réplica principal.

## <a name="zone-redundant-configuration"></a>Configuración de redundancia de zona

De forma predeterminada, el clúster de nodos del modelo de disponibilidad premium se crea en el mismo centro de datos. Con la incorporación de [Azure Availability Zones](../availability-zones/az-overview.md), SQL Database puede colocar diferentes réplicas del clúster en diferentes zonas de disponibilidad de la misma región. Para eliminar un punto de error único, también se duplica el anillo de control en varias zonas como tres anillos de puerta de enlace. El enrutamiento a un anillo de puerta de enlace específico se controla mediante [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Como la configuración de redundancia de zona de los niveles de servicio premium y crítico para la empresa no crean redundancia adicional de base de datos, puede habilitarla sin costo adicional. Si selecciona una configuración de redundancia de zona, puede hacer que las bases de datos de los niveles premium o crítico para la empresa sean resistentes a un número mucho mayor de errores, como interrupciones catastróficas de los centros de datos, sin necesidad de cambiar la lógica de la aplicación. También puede aplicar la configuración de redundancia de zona a cualquier grupo o base de datos existente del nivel Premium o Crítico para la empresa.

Como las bases de datos con redundancia de zona tienen réplicas en distintos centros de datos situados a cierta distancia entre ellos, la mayor latencia de red puede aumentar el tiempo de confirmación y, por lo tanto, afectar al rendimiento de algunas cargas de trabajo OLTP. Siempre puede volver a la configuración de zona única; para ello, deshabilite la configuración de redundancia de zona. Este proceso es una operación en línea similar a la actualización normal del nivel de servicio. Al final del proceso, la base de datos o el grupo se migra desde un anillo con redundancia de zona a un anillo de zona única, o viceversa.

> [!IMPORTANT]
> Los grupos elásticos y las bases de datos con redundancia de zona solo se admiten actualmente en los niveles de servicio premium y crítico para la empresa de algunas regiones. Cuando se usa el nivel crítico para la empresa, la configuración de redundancia de zona solo está disponible cuando se selecciona el hardware de proceso Gen5. Para información actualizada sobre las regiones que admiten bases de datos con redundancia de zona, consulte [Soporte técnico de servicios por región](../availability-zones/az-overview.md#services-support-by-region).  

En el diagrama siguiente se ilustra la versión con redundancia de zona de la arquitectura de alta disponibilidad:

![arquitectura de alta disponibilidad con redundancia de zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperación de base de datos acelerada (ADR)

[Recuperación de base de datos acelerada (ADR)](sql-database-accelerated-database-recovery.md) es una nueva característica del motor de base de datos SQL que mejora considerablemente la disponibilidad de la base de datos, en especial en presencia de transacciones de larga duración. ADR está actualmente disponible para bases de datos únicas, grupos elásticos y Azure SQL Data Warehouse.

## <a name="conclusion"></a>Conclusión

Azure SQL Database presenta una solución de alta disponibilidad incorporada, que está totalmente integrada con la plataforma Azure. Depende de Service Fabric para la detección y la recuperación de errores, de Azure Blob Storage para la protección de datos y de Availability Zones para la mayor tolerancia a errores. Al mismo tiempo, Azure SQL Database aprovecha la tecnología Grupo de disponibilidad AlwaysOn de SQL Server para la replicación y la conmutación por error. La combinación de estas tecnologías permite que las aplicaciones obtengan todos los beneficios de un modelo de almacenamiento mixto y puedan admitir los SLA más exigentes.

## <a name="next-steps"></a>Pasos siguientes

- Obtener más información acerca de las [zonas de disponibilidad de Azure](../availability-zones/az-overview.md).
- Obtener más información sobre [Service Fabric](../service-fabric/service-fabric-overview.md).
- Obtener más información acerca de [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Para conocer más opciones de alta disponibilidad y recuperación ante desastres, consulte [Continuidad del negocio](sql-database-business-continuity.md).
