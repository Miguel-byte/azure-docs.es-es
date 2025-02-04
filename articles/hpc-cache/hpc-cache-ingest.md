---
title: Traslado de datos a un contenedor en la nube de Azure HPC Cache
description: Cómo rellenar Azure Blob Storage para usarlo con Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: v-erkell
ms.openlocfilehash: 0a71efdc0479a69aed8fecc22a6c89c506279d57
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105307"
---
# <a name="move-data-to-azure-blob-storage-for-azure-hpc-cache"></a>Traslado de datos a Azure Blob Storage en Azure HPC Cache

Si el flujo de trabajo incluye el movimiento de datos a Azure Blob Storage, asegúrese de que usa una estrategia eficaz para copiar los datos mediante Azure HPC Cache.

En este artículo se explican las mejores formas de mover los datos a Blob Storage para usarlos con Azure HPC Cache.

Tenga en cuenta estos factores:

* Azure HPC Cache usa un formato de almacenamiento especializado para organizar los datos en Blob Storage. Este es el motivo de que un destino de Blob Storage deba ser un contenedor nuevo vacío o un contenedor de blobs que se usara anteriormente con los datos de Azure HPC Cache. ([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) también usa este sistema de archivos en la nube).

* La copia de datos mediante Azure HPC Cache es la mejor opción cuando se usan varios clientes y operaciones en paralelo. Un comando de copia sencillo desde un cliente moverá los datos lentamente.

Existe una utilidad basada en Python para cargar contenido en un contenedor de Blob Storage. Para más información, lea [Carga previa de datos en Blob Storage](#pre-load-data-in-blob-storage-with-clfsload).

Si no quiere usar la utilidad de carga o si quiere agregar contenido a un destino de almacenamiento existente, siga las sugerencias de ingesta de datos en paralelo en [Copia de datos mediante Azure HPC Cache](#copy-data-through-the-azure-hpc-cache). 

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Carga previa de datos en Blob Storage con CLFSLoad

Puede usar la <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> utilidad Avere CLFSLoad para copiar los datos en un nuevo contenedor de Blob Storage antes de agregarlo como destino de almacenamiento. Esta utilidad se ejecuta en un sistema Linux único y escribe los datos en el formato de propiedad necesario para Azure HPC Cache. CLFSLoad es la manera más eficaz de rellenar un contenedor de Blob Storage y usarlo con la caché.

La utilidad Avere CLFSLoad está disponible a petición de su equipo de Azure HPC Cache. Pida contacto a su equipo o abra una incidencia de soporte técnico para solicitar ayuda.

Esta opción solo funciona con contenedores nuevos vacíos. Cree el contenedor antes de usar Avere CLFSLoad.

Se incluye información detallada en la distribución de Avere CLFSLoad, que está disponible a petición del equipo de Azure HPC Cache. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Información general del proceso:

1. Prepare un sistema Linux (máquina virtual o físico) con la versión 3.6 o posterior de Python. (Se recomienda Python 3.7 para mejorar el rendimiento).
1. Instale el software Avere-CLFSLoad en el sistema Linux.
1. Ejecute la transferencia desde la línea de comandos de Linux.

La utilidad Avere CLFSLoad necesita la siguiente información:

* El identificador de la cuenta de almacenamiento que incluye el contenedor de Blob Storage.
* El nombre del contenedor vacío de Blob Storage.
* Un token de firma de acceso compartido (SAS) que permite a la utilidad escribir en el contenedor
* Una ruta de acceso local al origen de datos, ya sea un directorio local que contiene los datos que se van a copiar o una ruta de acceso local a un sistema remoto montado con los datos.

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Copia de datos mediante Azure HPC Cache

Si no quiere usar la utilidad Avere CLFSLoad o si quiere agregar una gran cantidad de datos a un destino existente de Blob Storage, puede copiarlos mediante la caché. Azure HPC Cache está diseñado para atender a varios clientes a la vez, así que para copiar datos mediante la caché, debe usar escrituras en paralelo desde varios clientes.

![Diagrama que muestra el movimiento de datos de varios clientes de múltiples subprocesos: en la parte superior izquierda, hay un icono para el almacenamiento de hardware local que tiene varias flechas que salen de él. Las flechas apuntan a cuatro equipos cliente diferentes. De cada máquina cliente tres flechas apuntan a Azure HPC Cache. De Azure HPC Cache, varias flechas apuntan a Blob Storage.](media/hpc-cache-parallel-ingest.png) 

Los comandos ``cp`` o ``copy`` que se usan habitualmente para transferir datos de un sistema de almacenamiento a otro son comandos de subproceso único que copian solo un archivo a la vez. Esto significa que el servidor de archivos solo puede ingerir un archivo a la vez, lo que es un desperdicio de los recursos del clúster.

En esta sección se explican las estrategias para crear un sistema de copia de archivos de varios subprocesos y varios clientes para mover datos a Blob Storage con Azure HPC Cache. Asimismo, se explican los conceptos de transferencia de archivos y los puntos de decisión que se pueden usar para copiar datos de manera eficiente mediante varios clientes y comandos de copia simples.

Por supuesto, también se explican algunas utilidades que pueden serle de ayuda. La utilidad ``msrsync`` se puede usar para automatizar parcialmente el proceso de dividir un conjunto de datos en cubos y usar los comandos rsync. El script ``parallelcp`` es otra utilidad que lee el directorio de origen y emite comandos de copia automáticamente.

### <a name="strategic-planning"></a>Plan estratégico

Al crear una estrategia para copiar datos en paralelo, debe comprender las ventajas y desventajas que acarrea el tamaño del archivo, el recuento de archivos y la profundidad del directorio.

* Cuando los archivos son pequeños, la métrica de interés se basa en los archivos por segundo.
* Cuando los archivos son grandes (de 10 MiBi o más), la métrica de interés se mide en función de los bytes por segundo.

Cada proceso de copia tiene una tasa de rendimiento y una tasa de transferencia de archivos que puede medirse en función de la longitud del comando de copia y factorizando el tamaño y número de archivos. La explicación referente a cómo medir estas tasas no se encuentra en este documento, pero es imperativo que sepa si usará archivos pequeños o grandes.

Las estrategias para la ingesta de datos en paralelo con Azure HPC Cache son las siguientes:

* Copia manual: puede crear manualmente una copia de varios subprocesos en un cliente mediante la ejecución de más de un comando de copia a la vez en segundo plano con los conjuntos predefinidos de archivos o rutas. Para más información, lea [Ingesta de datos de Azure HPC Cloud: método de copia manual](hpc-cache-ingest-manual.md).

* Copia automatizada parcial con ``msrsync``:  - ``msrsync`` es una utilidad de contenedor que ejecuta varios procesos ``rsync`` en paralelo. Para más información, lea [Ingesta de datos de Azure HPC Cache: método msrsync](hpc-cache-ingest-msrsync.md).

* Copia de scripts con ``parallelcp``: aprenda a crear y ejecutar un script de copia en paralelo en [Ingesta de datos de Azure HPC Cache: método de script de copia parcial](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Pasos siguientes

Después de configurar el almacenamiento, conozca cómo los clientes pueden montar la caché.

* [Acceso al sistema de Azure HPC Cache](hpc-cache-mount.md)
