---
title: Solución de errores habituales de la API de Azure Cosmos DB para MongoDB
description: En este documento se describen las formas de solucionar los problemas habituales detectados en la API de Azure Cosmos DB para MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735712"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Solución de errores habituales de la API de Azure Cosmos DB para MongoDB

Azure Cosmos DB implementa los protocolos de conexión de bases de datos NoSQL habituales, como MongoDB. Gracias a la implementación del protocolo de conexión, puede interactuar transparentemente con Azure Cosmos DB mediante los SDK de cliente existentes, los controladores y las herramientas que funcionen con las bases de datos NoSQL. Azure Cosmos DB no utiliza código fuente de las bases de datos para proporcionar API compatibles con la conexión a las bases de datos NoSQL. Cualquier controlador de cliente de MongoDB que reconozca estas versiones del protocolo de conexión debería poder conectarse a Azure Cosmos DB.

Aunque la API de Azure Cosmos DB para MongoDB es compatible con la versión 3.2 del protocolo de conexión de MongoDB (los operadores de consulta y las características agregadas en la versión 3.4 están disponibles actualmente en versión preliminar), hay algunos códigos de error personalizados que corresponden a errores específicos de Azure Cosmos DB. En este artículo se explican los diferentes errores, códigos de error y pasos para resolver esos errores.

## <a name="common-errors-and-solutions"></a>Errores habituales y soluciones

| Error               | Código  | DESCRIPCIÓN  | Solución  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | El número total de unidades de solicitud consumidas es mayor que la tasa de unidades de solicitud aprovisionadas para la colección y se ha limitado. | Considere la posibilidad de escalar el rendimiento asignado a un contenedor o un conjunto de contenedores desde Azure Portal o vuelva a intentarlo. |
| ExceededMemoryLimit | 16501 | Como se trata de un servicio de varios inquilinos, la operación ha superado la asignación de memoria del cliente. | Reduzca el ámbito de la operación a través de criterios de consulta más restrictivos o póngase en contacto con soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ejemplo: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problemas de versión de la conexión de MongoDB | - | Las versiones anteriores de los controladores de MongoDB son no detectan el nombre de la cuenta de Azure Cosmos en las cadenas de conexión. | Anexe *appName = @**nombreDeLaCuenta**@* al final de la API de Cosmos DB para la cadena de conexión de MongoDB, donde ***nombreDeLaCuenta*** es el nombre de la cuenta de Cosmos DB. |


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Studio 3T](mongodb-mongochef.md) con la API de Azure Cosmos DB para MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](mongodb-samples.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.

