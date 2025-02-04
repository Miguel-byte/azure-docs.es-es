---
title: Conexión de datos de Symantec ICDx a la versión preliminar de Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos de Symantec ICDx a Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673748"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conexión de su dispositivo Symantec ICDx 

> [!IMPORTANT]
> Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

El conector de Symantec ICDx permite conectar fácilmente todos los registros de la solución de seguridad de Symantec con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación, de modo que dispondrá de más información sobre la red de la organización y de mejores capacidades de seguridad. La integración entre Symantec ICDx y Azure Sentinel usa la API REST.


> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-symantec-icdx"></a>Configuración y conexión con Symantec ICDx 

Symantec ICDx puede integrar y exportar los registros directamente a Azure Sentinel.

1. Abra la consola de administración de ICDx para agregar reenviadores de Microsoft Azure Sentinel (Log Analytics).
2. En la barra de navegación ICDX, haga clic en **Configuración**. 
3. En la parte superior de la pantalla **Configuración**, haga clic en **Reenviadores**.
4. En **Reenviadores**, junto a Microsoft Azure Sentinel (Log Analytics), haga clic en **Agregar**. 
4. En la ventana **Microsoft Azure Sentinel (Log Analytics)** , haga clic en **Mostrar opciones avanzadas**. 
5. En la parte superior de la ventana expandida a Microsoft Azure Sentinel (Log Analytics), haga lo siguiente:
    -   **Nombre**: escriba un nombre para el reenviador que no tenga más de 30 caracteres. Elija un nombre único y significativo. Este nombre aparece en la lista de reenviadores de la pantalla **Configuración** y en los paneles de la pantalla **Panel**. Por ejemplo:  Microsoft Azure Log Analytics East. Este campo es obligatorio.
    -   **Descripción**: escriba una descripción para el reenviador. Esta descripción también aparece en la lista de reenviadores de la pantalla **Configuración**. Incluya detalles como el tipo de evento que se reenvía y el grupo que necesita para inspeccionar los datos.
    -   **Tipo de inicio**: seleccione el método de inicio para la configuración del reenviador. Sus opciones son manuales y automáticas.<br>El valor predeterminado es Automatic. 
6. En **Eventos**, haga lo siguiente: 
    - **Origen**: seleccione uno o varios archivos desde los que reenviar eventos. Puede seleccionar archivos de recopiladores activos (incluido Common Archive), archivos de recopiladores huérfanos (es decir, archivos para los recopiladores que ha eliminado), archivos de receptor ICDX o System Archive. <br>El valor predeterminado es Common Archive.
      > [!NOTE]
      > Los archivos de receptor ICDX se muestran por separado, por nombre. 
 
    - **Filtro**: agregue un filtro que especifique el subconjunto de eventos que reenviar. Realice una de las operaciones siguientes:
        - Para seleccionar una condición de filtro, haga clic en un Tipo, Atributo, Operador y Valor. 
        - En el campo de filtro, revise su condición de filtro. Puede editarla directamente en el campo o eliminarla según sea necesario.
        - Haga clic en AND u OR para su incorporación a su condición de filtro.
        - También puede hacer clic en Consultas guardadas para aplicar una consulta guardada.
    - **Atributos incluidos**: escriba la lista delimitada por comas de atributos para incluir en los datos reenviados. Los atributos incluidos tienen prioridad sobre los atributos excluidos.
    - **Atributos excluidos**: escriba la lista delimitada por comas de atributos para excluir de los datos reenviados.
    - **Tamaño de lote**: seleccione el número de eventos que enviar por lote. Sus opciones son 10, 50, 100, 500 y 1000.<br>El valor predeterminado es 100. 
    - **Limite de frecuencia**: seleccione la frecuencia de reenvío de los eventos, expresada como eventos por segundo. Sus opciones son ilimitados, 500, 1000, 5000 y 10 000. <br> El valor predeterminado es 5000. 
7. En **Destino de Azure**, haga lo siguiente: 
    - **Id. del área de trabajo**: pegue el identificador del área de trabajo que aparece a continuación. Este campo es obligatorio.
    - **Clave principal**: pegue la clave principal que aparece a continuación. Este campo es obligatorio.
    - **Nombre de registro personalizado**: escriba el nombre de registro personalizado en el área de trabajo de Log Analytics de Microsoft Azure Portal a la que va a reenviar eventos. El valor predeterminado es SymantecICDx. Este campo es obligatorio.
8. Haga clic en *Guardar* para finalizar la configuración del reenviador. 
9. Para iniciar el reenviador, en **Opciones**, haga clic en **Más** y, a continuación, en **Iniciar**.
10. Para usar el esquema correspondiente en Log Analytics para encontrar los eventos de Symantec ICDx, busque **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar conectividad

Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir más de 20 minutos. 



## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Symantec ICDx a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](quickstart-get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](tutorial-detect-threats.md).

