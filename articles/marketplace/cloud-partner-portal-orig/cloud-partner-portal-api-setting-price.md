---
title: Precios de ofertas de máquinas virtuales | Azure Marketplace
description: Explica los tres métodos para especificar el precio de las ofertas de máquinas virtuales.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: e398b43e679fb6420c2256e77d34359ae537ac1c
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868743"
---
<a name="pricing-for-virtual-machine-offers"></a>Precios de ofertas de máquinas virtuales
==================================

Hay tres maneras de especificar los precios de ofertas de máquina virtual: precios por núcleo personalizados, precios por núcleo y precios por hoja de cálculo.


<a name="customized-core-pricing"></a>Precios por núcleo personalizados
-----------------------

Los precios son específicos para cada combinación de región y núcleo. Se deben especificar todas las regiones en la lista de países de venta en la sección **virtualMachinePricing**/**regionPrices** de la definición.  Use los códigos de divisa correctos para cada [región](#regions) en la solicitud.  En el siguiente ejemplo se muestran estos requisitos:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Precios por núcleo
----------------

En este caso, los publicadores especifican un precio en USD para su SKU y todos los demás precios se generan automáticamente. El precio por núcleo se especifica en el **único** parámetro de la solicitud.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Precios por hoja de cálculo
-------------------

El editor también puede cargar la hoja de cálculo de precios en una ubicación de almacenamiento temporal y, a continuación, incluir el URI en la solicitud, como otros artefactos del archivo. A continuación, la hoja de cálculo se carga, se traduce para evaluar la previsión de precios y, finalmente, actualiza la oferta con la información sobre precios. Las solicitudes GET posteriores de la oferta devuelven el URI de la hoja de cálculo y los precios evaluados para la región.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Nuevos tamaños de núcleo agregados el 2/7/2019
---------------------------

A los publicadores de máquinas virtuales se les notificó el 2 de julio de 2019 la incorporación de nuevos precios para los nuevos tamaños de máquina virtual de Azure (en función del número de núcleos).  Los nuevos precios son para los tamaños de núcleo 10, 44, 48, 60, 120, 208 y 416.  Para ofertas de máquinas virtuales existentes, los nuevos precios para estos tamaños de núcleo se han calculado automáticamente en función de los precios actuales.  Los publicadores tienen hasta el 1 de agosto de 2019 para revisar los precios adicionales y realizar los cambios deseados.  Después de esta fecha, si el publicador no los ha vuelto a publicar, se aplicarán los precios calculados automáticamente para estos nuevos tamaños de núcleo.


<a name="regions"></a>Regions
-------

En la tabla siguiente se muestran las distintas regiones que se pueden especificar para precios por núcleo y sus códigos de moneda correspondientes.

| **Región** | **Nombre**             | **Código de divisa** |
|------------|----------------------|-------------------|
| DZ         | Argelia              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahréin              | BHD               |
| BY         | Belarús              | RUB               |
| BE         | Bélgica              | EUR               |
| BR         | Brasil               | USD               |
| BG         | Bulgaria             | BGN               |
| CA         | Canadá               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Colombia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croacia              | HRK               |
| CY         | Chipre               | EUR               |
| CZ         | República Checa       | CZK               |
| DK         | Dinamarca              | DKK               |
| DO         | República Dominicana   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egipto                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| VF         | Francia               | EUR               |
| DE         | Alemania              | EUR               |
| GR         | Grecia               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | RAE de Hong Kong        | HKD               |
| HU         | Hungría              | HUF               |
| IS         | Islandia              | ISK               |
| IN         | India                | INR               |
| id         | Indonesia            | IDR               |
| IE         | Irlanda              | EUR               |
| IL         | Israel               | ILS               |
| IT         | Italia                | EUR               |
| JP         | Japón                | JPY               |
| JO         | Jordania               | JOD               |
| KZ         | Kazajistán           | KZT               |
| KE         | Kenia                | KES               |
| KR         | Corea                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Letonia               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituania            | EUR               |
| LU         | Luxemburgo           | EUR               |
| MK         | Macedonia del Norte      | MKD               |
| MY         | Malasia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marruecos              | MAD               |
| NL         | Países Bajos          | EUR               |
| NZ         | Nueva Zelanda          | NZD               |
| NG         | Nigeria              | NGN               |
| NO         | Noruega               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pakistán             | PKR               |
| PA         | Panamá               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Perú                 | PEN               |
| PH         | Filipinas          | PHP               |
| PL         | Polonia               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Rumania              | RON               |
| RU         | Rusia               | RUB               |
| SA         | Arabia Saudí         | SAR               |
| RS         | Serbia               | RSD               |
| SG         | Singapur            | SGD               |
| SK         | Eslovaquia             | EUR               |
| SI         | Eslovenia             | EUR               |
| ZA         | Sudáfrica         | ZAR               |
| ES         | España                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Suecia               | SEK               |
| CH         | Suiza          | CHF               |
| TW         | Taiwán               | TWD               |
| TH         | Tailandia             | THB               |
| TT         | Trinidad y Tobago  | TTD               |
| TN         | Túnez              | TND               |
| TR         | Turquía               | TRY               |
| UA         | Ucrania              | UAH               |
| AE         | Emiratos Árabes Unidos | EUR               |
| GB         | Reino Unido       | GBP               |
| US         | Estados Unidos        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
