<properties
    pageTitle="Oprette en begivenhed Hubs navneområde med begivenhed Hub og aktivere arkiv ved hjælp af en skabelon til Azure ressourcestyring | Microsoft Azure"
    description="Oprette en begivenhed Hubs navneområde med begivenhed Hub og aktivere arkiv ved hjælp af Azure ressourcestyring skabelon"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Oprette en begivenhed Hubs navneområde med begivenhed Hub og aktivere arkiv ved hjælp af en skabelon til Azure ressourcestyring

I denne artikel viser, hvordan du bruger en Azure ressourcestyring skabelon, der opretter en begivenhed Hubs navneområde med en begivenhed Hub og muliggør arkiv på din begivenhed Hub. Du Lær, hvordan du definerer hvilke ressourcer er installeret, og hvordan du kan definere parametre, der er angivet, når installationen er udført. Du kan bruge denne skabelon til dine egne installationer eller tilpasse den for at opfylde dine behov

Du kan finde flere oplysninger om oprettelse af skabeloner, [redigering Azure ressourcestyring skabeloner][].

Yderligere oplysninger om praksis og mønstre på Azure ressourcer se navngivningsmetode, skal du [Azure regler for navngivning af ressourcer][].

Se [begivenhed Hub og Aktivér arkiv skabelon][] på GitHub for skabelonen fuldført.

>[AZURE.NOTE]
>For at søge efter de seneste skabeloner, skal du besøge skabelongalleriet [Azure Hurtig start][] , og Søg efter begivenhed Hubs.

## <a name="what-you-deploy"></a>Hvad du installerer?

Med denne skabelon kan du installere en begivenhed Hubs navneområde med en begivenhed Hub og aktiverer arkiv.

[Begivenhed Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) er en begivenhed, behandling af tjeneste, der bruges til at give begivenheden og telemetri vandindtrængen til Azure massive skaleres lav ventetid og høj pålidelighed. Begivenhed Hubs arkiv giver dig til automatisk at levere streaming dataene i din begivenhed Hubs til Azure Blob-lager efter eget valg i et bestemt tidspunkt eller størrelse interval efter eget valg.

For at køre installationen automatisk, skal du klikke på knappen følgende:

[![Installere på Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parametre

Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder afsnittet `Parameters` , der indeholder alle parameterværdier. Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der altid forbliver de samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret.

Skabelonen definerer følgende parametre.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Navnet på begivenhed Hubs navneområdet til at oprette.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Navnet på den begivenhed Hub oprettet i Hubs navneområde.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Antallet af dage, der skal bevares meddelelserne i din begivenhed Hub. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Antallet af partitioner, du vil i begivenhed-hubben.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Aktivere arkiv på begivenheden centrum.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

Kodningsformatet du angive, at sekventielt begivenhed dataene.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

Et tidsinterval, hvormed arkivet starter arkivering af dataene i Azure blob-lager.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize

Det størrelse interval, hvormed arkivet starter arkivering af dataene i Azure blob-lager.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Arkivet kræver en lagerplads konto ressource-id til at aktivere arkiv til dit ønskede Azure-lager.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Objektbeholderen blob, hvor du vil dataene begivenhed arkiveres.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

API-version af skabelonen.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Ressourcer til at udrulle

Opretter et navneområde af typen **EventHubs**, med en begivenhed-Hub, og gør det muligt for arkiv.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Kommandoer til at køre Installation

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Redigering Azure ressourcestyring skabeloner]: ../resource-group-authoring-templates.md
[Azure Hurtig start skabeloner]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Azure ressourcer navngivningskonventioner]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Begivenhed-Hub, og Aktivér arkiv skabelon]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
