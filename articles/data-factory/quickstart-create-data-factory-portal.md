---
title: Creación de una factoría de datos de Azure con la interfaz de usuario de Azure Data Factory | Microsoft Docs
description: Cree una factoría de datos con una canalización que copie los datos de una ubicación de Azure Blob Storage a otra.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 5baa8c78ad581a00a3601706f31cf815359120c7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077048"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Inicio rápido: Creación de una factoría de datos con la interfaz de usuario de Azure Data Factory

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión actual](quickstart-create-data-factory-portal.md)

En esta guía de inicio rápido se describe cómo usar la interfaz de usuario de Azure Data Factory para crear y supervisar una factoría de datos. La canalización que ha creado en esta factoría de datos *copia* los datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo *transformar* datos mediante Azure Data Factory, consulte [Tutorial: Transformación de datos con Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introduction to Azure Data Factory](data-factory-introduction.md) antes de seguir los pasos de esta guía de inicio rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo le ayudará a conocer la interfaz de usuario de Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
1. Vaya a [Azure Portal](https://portal.azure.com). 
1. Seleccione **Crear un recurso** en el menú izquierdo, seleccione **Analytics** y, luego, elija **Data Factory**. 
   
   ![Selección de la factoría de datos en el panel Nuevo](./media/doc-common-process/new-azure-data-factory-menu.png)
1. En la página **Nueva factoría de datos**, escriba **ADFTutorialDataFactory** en **Nombre**. 
 
   El nombre de la instancia de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, **&lt;suNombre&gt;ADFTutorialDataFactory**) e intente crearlo de nuevo. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Mensaje de error cuando un nombre no está disponible](./media/doc-common-process/name-not-available-error.png)
1. En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos. 
1. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista. 
   - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
   Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
1. En **Versión**, seleccione **V2**.
1. En **Ubicación**, seleccione la ubicación de la factoría de datos.

   En la lista solo se muestran las ubicaciones que admite Data Factory y dónde se almacenarán los metadatos de Azure Data Factory. Los almacenes de datos asociados (como Azure Storage y Azure SQL Database) y los procesos (como Azure HDInsight) que usa Data Factory se pueden ejecutar en otras regiones.

1. Seleccione **Crear**.

1. Una vez completada la creación, verá la página **Data Factory**. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente.
   
   ![Página principal de la factoría de datos, con el icono Author & Monitor (Creación y supervisión)](./media/doc-common-process/data-factory-home-page.png)
1. En la página de **introducción**, cambie a la pestaña **Creador** del panel izquierdo. 

    ![Página de introducción](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado
En este procedimiento, creará un servicio vinculado para vincular la cuenta de Azure Storage con la factoría de datos. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a él.

1. Seleccione **Conexiones** y después el botón **Nuevo** en la barra de herramientas (el botón **Conexiones** se encuentra en la parte inferior de la columna izquierda debajo de **Factory Resources**). 

1. En la página **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y después **Continue** (Continuar). 

   ![Selección del icono Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. En la página Nuevo servicio vinculado (Azure Blob Storage), realice los siguientes pasos: 

   a. En **Name** (Nombre), escriba **AzureStorageLinkedService**.

   b. En **Storage account name** (Nombre de la cuenta de Storage), seleccione el nombre de la cuenta correspondiente.

   c. Seleccione **Test connection** (Probar conexión) para confirmar que el servicio Data Factory puede conectarse a la cuenta de almacenamiento. 

   d. Para guardar el servicio vinculado, seleccione **Finish** (Finalizar). 

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este procedimiento, creará dos conjuntos de datos: **InputDataset** y **OutputDataset**. Estos conjuntos de datos son de tipo **AzureBlob**. Hacen referencia al servicio vinculado de Azure Storage que creó en la sección anterior. 

El conjunto de datos de entrada representa los datos de origen en la carpeta de entrada. En la definición del conjunto de datos de entrada, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**input**) y el archivo (**emp.txt**) que contiene los datos de origen. 

El conjunto de datos de salida representa los datos que se copian en el destino. En la definición del conjunto de datos de salida, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**output**) y el archivo en el que se copian los datos. Cada ejecución de una canalización tiene un identificador único asociado a ella. Puede tener acceso a este identificador mediante el uso de la variable del sistema **RunId**. El nombre del archivo de salida se evalúa dinámicamente según el identificador de ejecución de la canalización.   

En la configuración del servicio vinculado se especifica la cuenta de Azure Storage que contiene los datos de origen. En la configuración del conjunto de datos de origen se especifica dónde residen exactamente los datos de origen (contenedor de blobs, carpeta y archivo). En la configuración del conjunto de datos receptor se especifica dónde se copian los datos (contenedor de blobs, carpeta y archivo). 
 
1. Haga clic en el botón **+** (Más) y seleccione **Dataset** (Conjunto de datos).

   ![Menú para crear un conjunto de datos](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. En la página **Nuevo conjunto de datos**, seleccione **Azure Blob Storage** y después **Continuar**. 

   ![Selección de Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. En la página **Seleccionar formato**, elija el tipo de formato de los datos y, después, seleccione **Continuar**. En este caso, seleccione **Binario** al copiar archivos tal cual sin analizar el contenido.

    ![Tipo de formato de datos](./media/doc-common-process/select-binary.png)

1. En la página **Establecer propiedades**, siga estos pasos:

    a. En **Nombre**, escriba **InputDataset**. 

    b. En **Linked service** (Servicio vinculado), seleccione **AzureStorageLinkedService**.

    c. En **File path** (Ruta del archivo), seleccione el botón **Browse** (Examinar).

    d. En la ventana **Choose a file or folder** (Elegir un archivo o carpeta), vaya a la carpeta **input** del contenedor **adftutorial**, seleccione el archivo **emp.txt** y seleccione **Finish** (Finalizar).
    
    e. Seleccione **Continuar**.   

    ![Definición de propiedades para InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Repita los pasos para crear el conjunto de datos de salida:  

    a. Haga clic en el botón **+** (Más) y seleccione **Dataset** (Conjunto de datos).

    b. En la página **Nuevo conjunto de datos**, seleccione **Azure Blob Storage** y después **Continuar**.

    c. En la página **Seleccionar formato**, elija el tipo de formato de los datos y, después, seleccione **Continuar**.

    d. En la página **Establecer propiedades**, especifique **OutputDataset** para el nombre. Seleccione **AzureStorageLinkedService** como servicio vinculado.

    e. En **Ruta de acceso del archivo**, escriba **adftutorial/output**. Si la carpeta **output** no existe, la actividad de copia la crea en tiempo de ejecución.

    f. Seleccione **Continuar**.   

## <a name="create-a-pipeline"></a>Crear una canalización 
En este procedimiento, va a crear y comprobar una canalización con una actividad de copia que utiliza los conjuntos de datos de entrada y de salida. La actividad de copia realiza una copia de los datos desde el archivo especificado en la configuración del conjunto de datos de entrada hasta el archivo especificado en la configuración del conjunto de datos de salida. Si el conjunto de datos de entrada especifica solo una carpeta (no el nombre de archivo), la actividad de copia realiza una copia de todos los archivos de la carpeta de origen al destino. 

1. Haga clic en el botón **+** (Más) y seleccione **Pipeline** (Canalización). 

1. En la pestaña **General**, especifique **CopyPipeline** en **Nombre**. 

1. En el cuadro de herramientas **Activities** (Actividades), expanda **Move & Transform** (Mover y transformar). Arrastre la actividad **Copiar datos** del cuadro de herramientas **Actividades** a la superficie del diseñador de canalizaciones. También puede buscar actividades en el cuadro de herramientas **Activities** (Actividades). Especifique **CopyFromBlobToBlob** en **Name** (Nombre).

1. Cambie a la pestaña **Source** (Origen) en la configuración de la actividad de copia y seleccione **InputDataset** para **Source Dataset** (Conjunto de datos de origen).

1. Cambie a la pestaña **Sink** (Receptor) en la configuración de la actividad de copia y seleccione **OutputDataset** para **Sink Dataset** (Conjunto de datos receptor).

1. Haga clic en **Validar** en la barra de herramientas de la canalización situada en la parte superior del lienzo para validar la configuración de la canalización. Confirme que la canalización se ha validado correctamente. Para cerrar la salida de la validación, haga clic en el botón **>>** (fecha derecha). 

## <a name="debug-the-pipeline"></a>Depuración de la canalización
En este paso va a depurar la canalización antes de implementarla en Data Factory. 

1. En la barra de herramientas de la canalización situada en la parte superior del lienzo, haga clic en **Depurar** para desencadenar una serie de pruebas. 
    
1. Confirme que ve el estado de ejecución de la canalización en la pestaña **Output** (Salida) de la configuración de la canalización situada en la parte inferior. 

1. Confirme que ve un archivo de salida en la carpeta **output** del contenedor **adftutorial**. Si no existe la carpeta de salida, el servicio Data Factory la crea automáticamente. 

## <a name="trigger-the-pipeline-manually"></a>Desencadenamiento manual de la canalización
En este procedimiento se implementan las entidades (servicios vinculados, conjuntos de datos, canalizaciones) en Azure Data Factory. A continuación, desencadenará manualmente una ejecución de la canalización. 

1. Antes de desencadenar una canalización, debe publicar las entidades en Data Factory. Seleccione **Publicar todo** en la parte superior para realizar la publicación. 

   ![Botón Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
1. Para desencadenar la canalización de forma manual, seleccione **Agregar desencadenador** en la barra de herramientas de la canalización y seleccione **Trigger Now** (Desencadenar ahora). En la página **Pipeline Run** (Ejecución de canalización), seleccione **Finish** (Finalizar).

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Use el botón **Refresh** (Actualizar) para actualizar la lista.

   ![Pestaña de supervisión de las ejecuciones de canalización](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en **Actions** (Acciones). En esta página puede ver el estado de la ejecución de la actividad de copia. 

1. Para más información sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (imagen de gafas) de la columna **Actions** (Acciones). Para más información sobre las propiedades, consulte [Introducción a la actividad de copia](copy-activity-overview.md). 

   ![Detalles de la operación de copia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Confirme que ve un archivo nuevo en la carpeta **output** (salida). 
1. Puede volver a la vista **Ejecuciones de la canalización** desde la vista **Ejecuciones de actividad**. Para ello, seleccione el vínculo **Ejecuciones de la canalización**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Desencadenamiento de la canalización de forma programada
Este procedimiento es opcional en este tutorial. Puede crear un *programador de desencadenador* para programar la ejecución de la canalización periódicamente (cada hora, a diario, y así sucesivamente). En este procedimiento, va a crear un desencadenador que se ejecutará cada minuto hasta la fecha y hora de finalización que se especifique. 

1. Cambie a la pestaña **Creador**. 

1. Vaya a la canalización, seleccione **Agregar desencadenador** en la barra de herramientas de la canalización y, después, seleccione **Nuevo/Editar**. 

1. En la página **Add Triggers** (Agregar desencadenadores), seleccione **Choose trigger** (Elegir desencadenador) y, después, seleccione **New** (Nuevo). 

1. En la página **New Trigger** (Nuevo desencadenador), para el campo **End** (Final), seleccione **On Date** (En fecha), especifique la hora de finalización unos minutos después de la hora actual y seleccione **Apply** (Aplicar). 

   Hay un costo asociado a cada ejecución de la canalización. Por lo tanto, especifique la hora de finalización tan solo unos minutos después de la hora de inicio. Asegúrese de que sea el mismo día. No obstante, asegúrese de que hay tiempo suficiente para que la canalización se ejecute entre la hora de publicación y la hora de finalización. El desencadenador entra en vigor después de publicar la solución en Data Factory, no cuando se guarda el desencadenador en la interfaz de usuario. 

1. En la página **Nuevo desencadenador**, active la casilla **Activado** y después seleccione **Siguiente**. 

   ![Opción Nuevo desencadenador](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Revise el mensaje de advertencia y seleccione **Finish** (Finalizar).

1. Seleccione **Publish All** (Publicar todo) para publicar los cambios en Data Factory. 

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Seleccione **Refresh** (Actualizar) para actualizar la lista. Verá que la canalización se ejecuta una vez cada minuto desde la hora de publicación hasta la hora de finalización. 

   Observe los valores de la columna **Triggered By** (Desencadenado por). La ejecución manual del desencadenador se realizó en el paso (**Trigger Now**) [Desencadenar ahora] que llevó a cabo antes. 

1. Cambie a la vista **Trigger Runs** (Ejecuciones de desencadenador). 

1. Confirme que se crea un archivo de salida para cada ejecución de la canalización hasta la fecha y hora de finalización especificadas en la carpeta **output** (salida). 

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Para más información sobre el uso de Data Factory en otros escenarios, consulte los siguientes [tutoriales](tutorial-copy-data-portal.md). 
