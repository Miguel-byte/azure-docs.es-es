---
title: 'Adición de varias conexiones sitio a sitio de una instancia de VPN Gateway a una red virtual: Azure Portal: Resource Manager| Microsoft Docs'
description: Agregar conexiones S2S de varios sitios a VPN Gateway con una conexión existente
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60760509"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Agregar una conexión de sitio a sitio a una red virtual con una conexión de VPN Gateway existente

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clásico)](vpn-gateway-multi-site.md)
>
> 

Los pasos de este artículo le ayudan a agregar conexiones de sitio a sitio (S2S) a VPN Gateway con una conexión existente mediante Azure Portal. Este tipo de conexión se denomina con frecuencia, configuración "multisitio". Puede agregar una conexión S2S a una red virtual que ya tiene una conexión S2S, una conexión de punto a sitio o una conexión entre dos redes virtuales. Existen algunas limitaciones al agregar conexiones. Vea la sección [Antes de empezar](#before) de este artículo para comprobarlas antes de iniciar la configuración. 

Este artículo se aplica a redes virtuales de Resource Manager que tienen una puerta de enlace de VPN RouteBased. Estos pasos no se aplican a las configuraciones de conexión coexistentes de ExpressRoute/sitio a sitio. Consulte [conexiones coexistentes de ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obtener información sobre las conexiones coexistentes.

### <a name="deployment-models-and-methods"></a>Modelos de implementación y métodos
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Esta tabla se actualiza cada vez que hay nuevos artículos y nuevas herramientas disponibles para esta configuración. Cuando aparezca un artículo, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Antes de empezar
Compruebe los siguientes aspectos:

* No está creando una conexión coexistente ExpressRoute/S2S.
* Tiene una red virtual que se ha creado con el modelo de implementación de Resource Manager con una conexión existente.
* La puerta de enlace de red virtual para su red virtual es RouteBased. Si tiene una VPN Gateway PolicyBased, necesita eliminar la puerta de enlace de red virtual y crear una VPN Gateway nueva como RouteBased.
* Ninguno de los intervalos de direcciones se superpone con ninguna de las redes virtuales a la que se está conectando esta red virtual.
* Tiene un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no conoce la configuración de su dispositivo VPN o los intervalos de direcciones IP ubicados en la configuración de la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles.
* Tiene una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.

## <a name="part1"></a>Parte 1: Configuración de una conexión
1. Desde un explorador, vaya [Azure Portal](https://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Todos los recursos**, localice su **puerta de enlace de red virtual** en la lista de recursos y haga clic en ella.
3. En la página **Puerta de enlace de red virtual**, haga clic en **Conexiones**.
   
    ![Página de conexiones](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Página de conexiones")<br>
4. En la página **Conexiones**, haga clic en **+Agregar**.
   
    ![Botón Agregar conexión](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Botón Agregar conexión")<br>
5. En la página **Agregar conexión**, rellene los campos siguientes:
   
   * **Nombre**: el nombre que quiere darle al sitio para el que está creando la conexión.
   * **Tipo de conexión**: seleccione **De sitio a sitio (IPsec)** .
     
     ![Agregar página de conexión](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection page")<br>

## <a name="part2"></a>Parte 2: Agregar una puerta de enlace de red local
1. Haga clic en **Puerta de enlace de red local** ***Elegir una puerta de enlace de red local***. Se abre la página **Elegir puerta de enlace de red local**.
   
    ![Elegir puerta de enlace de red local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Elegir puerta de enlace de red local")<br>
2. Haga clic en **Crear nueva** para abrir la página **Crear puerta de enlace de red local**.
   
    ![Página Crear puerta de enlace de red local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>
3. En la página **Crear puerta de enlace de red local**, rellene los campos siguientes:
   
   * **Nombre**: el nombre que quiere darle al recurso de puerta de enlace de red local.
   * **Dirección IP**: la dirección IP pública del dispositivo VPN del sitio al que quiere conectarse.
   * **Espacio de direcciones**: el espacio de direcciones al que quiere que se enrute el nuevo sitio de red local.
4. Haga clic en **Aceptar** en la página **Crear puerta de enlace de red local** para guardar los cambios.

## <a name="part3"></a>Parte 3: Agregar la clave compartida y crear la conexión
1. En la página **Agregar conexión**, agregue la clave compartida que quiere usar para crear la conexión. Puede obtener la clave compartida de su dispositivo VPN o crear una aquí y, después, configurar su dispositivo VPN para que use la misma clave compartida. Lo importante es que las claves sean exactamente iguales.
   
    ![Clave compartida](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Clave compartida")<br>
2. En la parte inferior de la página, haga clic en **Aceptar** para crear la conexión.

## <a name="part4"></a>Parte 4: Comprobar la conexión VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [ruta de aprendizaje](/learn/paths/deploy-a-website-with-azure-virtual-machines/) de las máquinas virtuales para más información.
