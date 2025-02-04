---
title: 'Tutorial: Hospedaje del dominio y los subdominios en Azure DNS'
description: Este tutorial le muestra cómo configurar Azure DNS para hospedar las zonas DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: c0c5c5fe899c9b9b898973a88c7dac4256959ee4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779783"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Tutorial: Hospedaje del dominio en Azure DNS

Puede usar Azure DNS para hospedar el dominio DNS y administrar los registros correspondientes. Al hospedar dominios en Azure, puede administrar los registros de DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure.

Suponga que adquiere el dominio contoso.net de un registrador de nombres de dominio y que crea una zona con el nombre contoso.net en Azure DNS. Como es el propietario del dominio, el registrador le ofrecerá la opción de configurar los registros del servidor de nombres (NS) del dominio. El registrador almacena los registros NS en la zona principal, .net. A los usuarios de Internet de todo el mundo se les remitirá entonces al dominio en cuestión en la zona de Azure DNS cuando tratan de resolver registros DNS en contoso.net.


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una zona DNS.
> * Recuperar una lista de servidores de nombres.
> * Delegar el dominio.
> * Comprobar que la delegación funciona.


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Tiene que tener un nombre de dominio disponible que pueda hospedar en Azure DNS para realizar las pruebas. Debe tener control total de este dominio. El control total incluye la capacidad de establecer los registros de nombre de servidor (NS) para el dominio.

El dominio en el ejemplo que se usa en este tutorial es contoso.net, pero debe usar su propio nombre de dominio.

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

1. Inicie sesión en el Portal de Azure.
1. En la parte superior izquierda, seleccione **Crear un recurso** > **Redes** > **Zona DNS** para abrir la página **Crear zona DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. En la página **Crear zona DNS**, escriba los valores siguientes y seleccione **Crear**:

   | **Configuración** | **Valor** | **Detalles** |
   |---|---|---|
   |**Nombre**|[el nombre de dominio] |El nombre de dominio que compró. Este tutorial usa como ejemplo contoso.net.|
   |**Suscripción**|[Su suscripción]|Seleccione una suscripción en la que crear la zona.|
   |**Grupos de recursos**|**Crear nuevo:** contosoRG|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción que ha seleccionado.<br>La ubicación del grupo de recursos no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es "global" y no se muestra.|
   |**Ubicación**|Este de EE. UU||

## <a name="retrieve-name-servers"></a>Recuperación de los servidores de nombres

Antes de poder delegar la zona DNS a Azure DNS, primero debe conocer el servidor de nombres de la zona. El DNS de Azure asigna los servidores de nombres de un grupo cada vez que se crea una zona.

1. Con la zona DNS creada, en el panel **Favoritos** de Azure Portal, seleccione **Todos los recursos**. En la página **Todos los recursos**, seleccione la zona DNS. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir el nombre de dominio en el cuadro **Filtrar por nombre** para acceder fácilmente a la puerta de enlace de aplicaciones. 

1. Recupere los servidores de nombres de la página Zona DNS. En este ejemplo, a la zona contoso.net se le han asignado los servidores de nombres *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* y *ns4-01.azure-dns.info*:

   ![Lista de servidores de nombres](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS crea automáticamente los registros NS autoritativos en la zona para los servidores de nombres asignados.

## <a name="delegate-the-domain"></a>Delegación del dominio

Ahora que se crea la zona DNS y que tiene los servidores de nombres, debe actualizar el dominio primario con los servidores de nombres de Azure DNS. Cada registrador dispone de sus propias herramientas de administración de DNS para cambiar los registros de servidores de nombres de un dominio. 

1. En la página de administración de DNS del registrador, edite los registros NS y reemplácelos por los servidores de nombres de Azure DNS.

1. Cuando delega un dominio a Azure DNS, debe usar los servidores de nombres proporcionados por Azure DNS. Use los cuatro servidores de nombres, independientemente del nombre de su dominio. La delegación de dominios no requiere que el servidor de nombres use el mismo dominio de primer nivel que su dominio.

> [!NOTE]
> Cuando copie cada dirección del servidor de nombres, asegúrese de copiar el punto final al final de la dirección. El punto indica el final de un nombre de dominio completo. Algunos registradores anexan el punto si el nombre NS no lo tiene al final. Para cumplir con la RFC de DNS, incluya el período de seguimiento.

De momento, no se admiten en Azure DNS las delegaciones que usan servidores de nombres en su propia zona (a veces denominados *servidores DNS personalizados*).

## <a name="verify-the-delegation"></a>Comprobación de la delegación

Cuando finalice la delegación, puede comprobar que funciona mediante una herramienta como *nslookup* para consultar el registro Inicio de autoridad (SOA) para su zona. El registro SOA se crea automáticamente cuando se crea la zona. Puede que necesite esperar 10 minutos o más después de completar la delegación para poder comprobar correctamente que funciona. Puede que los cambios tarden unos minutos en propagarse por el sistema DNS.

No es necesario especificar los servidores de nombres de Azure DNS. Si la delegación está configurada correctamente, el proceso de resolución DNS normal busca automáticamente los servidores de nombres.

1. Desde un símbolo del sistema, escriba un comando nslookup similar al ejemplo siguiente:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Compruebe que su respuesta sea similar a la salida del comando nslookup siguiente:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede conservar el grupo de recursos **contosoRG** si va a realizar el tutorial siguiente. De lo contrario, elimine el grupo de recursos **contosoRG** para eliminar los recursos creados en este tutorial.

- Seleccione el grupo de recursos **contosoRG** y, luego, **Eliminar grupo de recursos**. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado una zona DNS para el dominio y la ha delegado a Azure DNS. Para más información acerca de Azure DNS y sobre las aplicaciones web, continúe con el tutorial sobre estas.

> [!div class="nextstepaction"]
> [Creación de registros DNS para una aplicación web en un dominio personalizado](./dns-web-sites-custom-domain.md)