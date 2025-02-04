---
title: Montaje de un volumen de Azure Files en Azure Container Instances
description: Obtenga información sobre cómo montar un volumen de Azure Files para conservar el estado con Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325499"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montaje de un recurso compartido de archivos de Azure en Azure Container Instances

De forma predeterminada, Azure Container Instances no tiene estado. Si el contenedor se bloquea o se detiene, se pierde todo su estado. Para conservar el estado más allá de la duración del contenedor, debe montar un volumen desde un almacén externo. En este artículo se explica cómo montar un recurso compartido de archivos de Azure con [Azure Files](../storage/files/storage-files-introduction.md) para su uso con Azure Container Instances. Azure Files ofrece recursos compartidos de archivos en la nube totalmente administrados, a los que se puede acceder mediante el protocolo de bloque de mensajes del servidor (SMB) estándar. El uso de un recurso compartido de archivos de Azure con Azure Container Instances ofrece características de uso compartido de archivos similares al uso de un recurso compartido de archivo de Azure con Azure Virtual Machines.

> [!NOTE]
> El montaje de un recurso compartido de Azure Files está actualmente restringido a los contenedores Linux. Aunque se está trabajando para incorporar todas las características a los contenedores Windows, puede consultar las diferencias actuales entre plataformas en [Información general](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure en Azure Container Instances, debe crearlo. Ejecute el script siguiente para crear una cuenta de almacenamiento para hospedar el recurso compartido de archivos y el propio recurso compartido. El nombre de la cuenta de almacenamiento debe ser único globalmente, por lo que el script agrega un valor aleatorio en la cadena base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obtención de las credenciales de almacenamiento

Para montar un recurso compartido de archivos de Azure como un volumen en Azure Container Instances, necesita tres valores: el nombre de la cuenta de almacenamiento, el nombre del recurso compartido y la clave de acceso de almacenamiento.

Si ha usado el script anterior, el nombre de la cuenta de almacenamiento se almacena en la variable $ACI_PERS_STORAGE_ACCOUNT_NAME. Para ver el nombre de la cuenta, escriba:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

El nombre del recurso compartido ya se conoce (es *acishare* en el script anterior), por lo tanto, solo queda la clave de cuenta de almacenamiento, que se encuentra con el siguiente comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Implementar contenedor y montar volumen: CLI

Para montar un recurso compartido de archivos de Azure como volumen en un contenedor mediante la CLI de Azure, especifique el recurso compartido y el punto de montaje del volumen al crear el contenedor con [az container create][az-container-create]. Si ha seguido los pasos anteriores, puede montar el recurso compartido que creó anteriormente mediante el comando siguiente para crear un contenedor:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

El valor `--dns-name-label` debe ser único dentro de la región de Azure en la que se crea la instancia de contenedor. Actualice el valor del comando anterior si recibe un mensaje de error de **etiqueta de nombre DNS** al ejecutar el comando.

## <a name="manage-files-in-mounted-volume"></a>Administración de archivos en el volumen montado

Una vez que se inicie el contenedor, puede usar la aplicación web sencilla que se implementó mediante la imagen [aci-hellofiles][aci-hellofiles] de Microsoft para crear archivos de texto pequeños en el recurso compartido de archivos de Azure de la ruta de montaje que especificó. Obtenga el nombre de dominio completo (FQDN) de la aplicación web con el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Tras guardar el texto con la aplicación, puede usar [Azure Portal][portal] o una herramienta como el [Explorador de Microsoft Azure Storage][storage-explorer] para recuperar e inspeccionar el archivo escrito en el recurso compartido de archivos.

## <a name="deploy-container-and-mount-volume---yaml"></a>Implementar contenedor y montar volumen: YAML

También puede implementar un grupo de contenedores y montar un volumen en un contenedor con la CLI de Azure y una [plantilla de YAML](container-instances-multi-container-yaml.md). La implementación mediante la plantilla YAML es el método preferido al implementar grupos de contenedores que constan de varios contenedores.

La siguiente plantilla de YAML define un grupo de contenedores con un contenedor creado con la imagen `aci-hellofiles`. El contenedor monta el recurso compartido de archivos de Azure *acishare* creado anteriormente como un volumen. Donde se indique, escriba la clave de almacenamiento y el nombre de la cuenta de almacenamiento que hospeda el recurso compartido de archivos. 

Al igual que en el ejemplo de la CLI, el valor `dnsNameLabel` debe ser único dentro de la región de Azure en la que se crea la instancia de contenedor. Si es necesario, actualice el valor en el archivo YAML.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implementar con la plantilla de YAML, guarde el anterior YAML en un archivo denominado `deploy-aci.yaml` y luego ejecute el comando [az container create][az-container-create] con el parámetro `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Implementar contenedor y montar volumen: Resource Manager

Además de la implementación mediante la CLI y YAML, puede implementar un grupo de contenedores y montar un volumen en un contenedor con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, rellene la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. 

Luego, en todos los contenedores en los que quiera montar el volumen, rellene la matriz `volumeMounts` de la sección `properties` de la definición del contenedor.

La siguiente plantilla de Resource Manager define un grupo de contenedores con un contenedor creado con la imagen `aci-hellofiles`. El contenedor monta el recurso compartido de archivos de Azure *acishare* creado anteriormente como un volumen. Donde se indique, escriba la clave de almacenamiento y el nombre de la cuenta de almacenamiento que hospeda el recurso compartido de archivos. 

Al igual que en los ejemplos anteriores, el valor `dnsNameLabel` debe ser único dentro de la región de Azure en la que se crea la instancia de contenedor. Si es necesario, actualice el valor en la plantilla.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Para implementar con la plantilla de Resource Manager, guarde el anterior JSON en un archivo denominado `deploy-aci.json` y luego ejecute el comando [az group deployment create][az-group-deployment-create] con el parámetro `--template-file`:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montaje de varios volúmenes

Para montar varios volúmenes en una instancia del contenedor, debe realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) o un archivo YAML. Para usar una plantilla o un archivo YAML, proporcione los detalles del recurso compartido y defina los volúmenes al rellenar la matriz `volumes` de la sección `properties` de la plantilla. 

Por ejemplo, si ha creado dos recursos compartidos de Azure Files denominados *share1* y *share2* en la cuenta de almacenamiento *myStorageAccount*, la matriz `volumes` de la plantilla de Resource Manager sería similar a la siguiente:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

A continuación, para cada contenedor del grupo de contenedores en el que desea montar los volúmenes, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor. Por ejemplo, esto monta los dos volúmenes, *myvolume1* y *myvolume2* definidos previamente:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un volumen de emptyDir en Azure Container Instances](container-instances-volume-emptydir.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montaje de un volumen secreto en Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create