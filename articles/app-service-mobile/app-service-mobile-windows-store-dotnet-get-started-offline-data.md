---
title: Habilitación de la sincronización sin conexión para aplicaciones de la Plataforma universal de Windows (UWP) con Mobile Apps | Microsoft Docs
description: Obtenga información acerca de cómo usar una Aplicación móvil de Azure para almacenar en caché y sincronizar datos sin conexión en la aplicación de la Plataforma universal de Windows (UWP)
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 4970a80b911a1efbc308d48ac4b8a50f774b4d04
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "67551925"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Activación de la sincronización sin conexión para la aplicación de Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está invirtiendo en servicios nuevos e integrados fundamentales para el desarrollo de aplicaciones móviles. Los desarrolladores pueden usar los servicios de **compilación**, **prueba** y **distribución** para configurar la canalización de integración y entrega continuas. Una vez que se ha implementado la aplicación, los desarrolladores pueden supervisar el estado y el uso de su aplicación con los servicios de **análisis** y **diagnóstico**, e interactuar con los usuarios que utilizan el servicio de **Push** (inserción). Además, los desarrolladores pueden aprovechar **Auth** para autenticar a los usuarios y el servicio de **datos** para almacenar y sincronizar los datos de la aplicación en la nube. Consulte [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-offline-data) hoy mismo.
>

## <a name="overview"></a>Información general
Este tutorial muestra cómo agregar compatibilidad sin conexión a una aplicación de la Plataforma universal de Windows (UWP) mediante un back-end de aplicación móvil de Azure. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no haya conexión de red. Los cambios se almacenan en una base de datos local. Una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

En este tutorial, actualizará el proyecto de aplicación de UWP del tutorial [Creación de una aplicación para Windows] a fin de conseguir compatibilidad con las características sin conexión de Azure Mobile Apps. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="requirements"></a>Requisitos  
Este tutorial requiere los siguientes requisitos previos:

* Visual Studio 2013 funcionando en Windows 8.1 o versiones posteriores.
* Finalización del tutorial [Creación de una aplicación para Windows][Creación de una aplicación para Windows].
* [Almacén de SQLite de Azure Mobile Services][sqlite store nuget]
* [SQLite for Universal Windows Platform development](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Actualización de la aplicación cliente para que admita características sin conexión
Las características sin conexión de Aplicaciones móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión. Para usar estas características en una aplicación, inicialice una interfaz de [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite se utiliza como almacén local en el dispositivo.

1. Instale el [entorno en tiempo de ejecución de SQLite para la plataforma universal de Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. En Visual Studio, abra el Administrador de paquetes NuGet en el proyecto de aplicación de UWP que completó en el tutorial [Creación de una aplicación para Windows].
    Busque e instale el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** > **Agregar referencia...** >**Universal de Windows**>**Extensiones** y después habilite tanto **SQLite para la Plataforma universal de Windows** como **Tiempo de ejecución de Visual C++ 2015 para Aplicaciones de la Plataforma universal de Windows**.

    ![Incorporación de referencia de SQLite UWP][1]
4. Abra el archivo MainPage.xaml.cs y quite la definición `#define OFFLINE_SYNC_ENABLED`.
5. En Visual Studio, presione la tecla **F5** para volver a compilar y ejecutar la aplicación cliente. La aplicación funciona igual que lo hacía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos se rellena con datos que pueden utilizarse en un escenario sin conexión.

## <a name="update-sync"></a>Actualización de la aplicación para desconectarla del back-end
En esta sección, se interrumpe la conexión con el back-end de aplicación móvil para simular un escenario sin conexión. Al agregar elementos de datos, el controlador de excepciones le indicará que la aplicación está en modo sin conexión. En este estado, se agregan nuevos elementos al almacén local y se sincronizarán con el back-end de aplicación móvil cuando se vuelva a ejecutar la inserción en estado conectado.

1. Edite App.xaml.cs en el proyecto compartido. Convierta en comentario la inicialización de **MobileServiceClient** y agregue las siguientes líneas, que usan una dirección URL de una aplicación móvil no válida:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Además, puede mostrar el comportamiento sin conexión deshabilitando las redes Wi-Fi y móvil en el dispositivo o usar el modo avión.
2. Presione **F5** para compilar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inició la aplicación.
3. Escriba nuevos elementos y observe que la operación de inserción produce un error con un estado de [CancelledByNetworkError] cada vez que hace clic en **Guardar**. No obstante, los nuevos elementos Todo están en el almacén local hasta que se puedan insertar en el back-end de aplicación móvil.  En una aplicación de producción, si suprime estas excepciones, la aplicación cliente se comporta como si aún estuviera conectada al back-end de aplicación móvil.
4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.
5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**SQL Databases**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido. Compruebe que no han cambiado los datos de la base de datos back-end.
6. (Opcional) Use una herramienta REST como Fiddler o Postman para consultar el back-end móvil mediante una consulta GET con la forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el back-end de la aplicación móvil
En esta sección se vuelve a conectar la aplicación al back-end de aplicación móvil. Estos cambios simulan una reconexión de red en la aplicación.

La primera vez que se ejecuta la aplicación, el controlador de eventos `OnNavigatedTo` llama a `InitLocalStoreAsync`. Este método, a su vez, llama a `SyncAsync` para sincronizar el almacén local con la base de datos back-end. La aplicación intenta sincronizar al inicio.

1. Abra App.xaml.cs en el proyecto compartido y quite la marca de comentario a la inicialización anterior de `MobileServiceClient` para usar la dirección URL de aplicación móvil correcta.
2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. La aplicación sincroniza los cambios locales con el back-end de Azure Mobile App mediante las operaciones de inserción y extracción una vez que se ejecuta el controlador de eventos `OnNavigatedTo`.
3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos del back-end de la aplicación móvil de Azure y el almacén local.
4. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

   `UpdateCheckedTodoItem` llama a `SyncAsync` para sincronizar cada elemento completado con el back-end de Mobile App. `SyncAsync` llama a las operaciones de inserción y extracción. Sin embargo, **cada vez que se ejecuta una incorporación de cambios en una tabla en la que el cliente ha realizado cambios, siempre se ejecuta automáticamente una inserción** . De este modo se garantiza la coherencia de todas las tablas del almacén local, junto con sus relaciones. Este comportamiento puede provocar una inserción inesperada.  Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="api-summary"></a>Resumen de la API
Para admitir las características sin conexión de Mobile Services, se usa la interfaz [IMobileServiceSyncTable] y se inicializa [MobileServiceClient.SyncContext][synccontext] con una base de datos SQLite local. En el modo sin conexión, las operaciones CRUD normales para Mobile Apps funcionan como si la aplicación siguiera conectada y todas las operaciones se producen en el almacén local. Cuando se quiera sincronizar el almacén local con el servidor, se usan los métodos siguientes:

* **[PushAsync]** : como este método es miembro de [IMobileServicesSyncContext], los cambios de todas las tablas se insertan en el back-end. Solo se envían al servidor los registros con cambios locales.
* **[PullAsync]** : se inicia una extracción desde [IMobileServiceSyncTable]. Cuando haya cambios marcados en la tabla, se ejecuta una inserción implícita para asegurarse de que todas las tablas del almacén local junto con las relaciones siguen siendo coherentes. El parámetro *pushOtherTables* controla si se insertan otras tablas del contexto en una inserción implícita. El parámetro *query* toma la cadena de consulta de OData o [IMobileServiceTableQuery\<T>][IMobileServiceTableQuery] para filtrar los datos devueltos. El parámetro *queryId* se utiliza para definir la sincronización incremental. Para más información, consulte [Sincronización de datos sin conexión en Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** : su aplicación debe llamar periódicamente a este método para purgar datos obsoletos del almacén local. Utilice el parámetro *force* cuando necesite purgar todos los cambios que aún no se hayan sincronizado.

Para obtener más información sobre estos conceptos, consulte [Sincronización de datos sin conexión en Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Más información
En los temas siguientes se ofrece información de fondo adicional sobre la característica de sincronización sin conexión de Mobile Apps:

* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]
* [Procedimiento del SDK de .NET de Azure Mobile Apps][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Creación de una aplicación para Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
