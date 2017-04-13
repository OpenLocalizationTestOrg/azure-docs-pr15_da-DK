<properties
   pageTitle="Installere flere forekomster af ressourcer | Microsoft Azure"
   description="Brug kopieringen og matrixer i en Azure ressourcestyring skabelon til at navigere flere gange ved implementering af ressourcer."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Oprette flere forekomster af ressourcer i Azure ressourcestyring.

Dette emne viser, hvordan du forbedrer i Azure ressourcestyring skabelonen til at oprette flere forekomster af en ressource.

## <a name="copy-copyindex-and-length"></a>Kopier, copyIndex og længde

Du kan angive en **Kopier** objekt, der angiver antallet gange til forbedrer inden for ressourcen som du opretter flere gange. Kopiér tager følgende format:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Du kan få adgang til den aktuelle gentagelse værdi med funktionen **copyIndex()** , som vist nedenfor i funktionen kontaktformular.

    [concat('examplecopy-', copyIndex())]

Når du opretter flere ressourcer fra en matrix med værdier, kan du bruge funktionen **længde** til at angive antallet. Du kan angive matrixen som parameter til funktionen længde.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Bruge indeksværdi i navnet

Du kan bruge kopien handlingen Opret flere forekomster af en ressource, der identificerer navngives baseret på stigende indekset. For eksempel kan du føje et entydigt tal til slutningen af hvert ressourcenavn, der er installeret. Installere tre websteder med navnet:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Brug af skabelonen følgende:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Forskydning indeksværdi

Du vil bemærke i det forrige eksempel, der indeksværdien går fra nul til 2. Hvis du vil forskydning indeksværdien, kan du sende en værdi i funktionen **copyIndex()** , som **copyIndex(1)**. Antallet af gentagelser til at udføre stadig er angivet i elementet kopi, men værdien af copyIndex forskydes ved den angivne værdi. Ved hjælp af skabelonen samme som i forrige eksempel, men der angiver **copyIndex(1)** ville så installere tre websteder med navnet:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Bruge kopi med matrix
   
Kopieringen er særligt nyttige, når du arbejder med matrixer, fordi du kan navigere gennem hvert element i matrixen. Installere tre websteder med navnet:

- examplecopy Contoso
- examplecopy Fabrikam
- examplecopy Coho

Brug af skabelonen følgende:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Naturligvis angive du antal kopier til en anden værdi end længden af matrixen. For eksempel kan du oprette en matrix med mange værdier og derefter overføre i en parameterværdi, der angiver, hvor mange af matrix elementer for at installere. Det er tilfældet, angive antal kopier, som vist i det første eksempel. 

## <a name="depending-on-resources-in-a-loop"></a>Afhængigt af ressourcer i en løkke

Du kan angive, at en ressource være installeret efter en anden ressource ved hjælp af elementet **dependsOn** . Når du skal installere en ressource, der afhænger af samlingen af ressourcer i en løkke, kan du bruge levere navnet på kopi løkken i elementet **dependsOn** . I følgende eksempel viser, hvordan du kan installere 3 lagerplads konti før du anvender den virtuelle maskine. Fuld virtuelt definitionen vises ikke. Bemærk, at elementet kopi har **navn** , der er angivet til **storagecopy** og **dependsOn** elementet til de virtuelle maskiner også er indstillet til **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Løkker på en indlejret ressource

Du kan ikke bruge en kopi løkke for en indlejret ressource. Hvis du vil oprette flere forekomster af en ressource, som du typisk definerer som indlejret i en anden ressource, skal du i stedet oprette ressourcen som en ressource på øverste niveau og definere relation med den overordnede ressource via egenskaber for **typen** af og **navnet** .

Lad os antage, at du typisk definerer et datasæt som en indlejret ressource i en Data fabrik.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Hvis du vil oprette flere forekomster af datasæt, du har brug at ændre din skabelon, som vist nedenfor. Meddelelse om typen fuldt kvalificeret og navnet indeholder data factory navn.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Oprette flere forekomster, når kopi ikke virker

Du kan kun bruge **kopi** på ressourcetyper, ikke på Egenskaber i en ressourcetype. Det kan give problemer for dig, når du vil oprette flere forekomster af noget, som er en del af en ressource. Et almindeligt scenarie er at oprette flere data til en virtuel maskine. Du kan ikke bruge **kopi** med datadisce, fordi **dataDisks** er en egenskab på den virtuelle maskine, ikke sin egen ressourcetype. I stedet skal oprette du en matrix med så mange datadisce, som du har brug for, og gå i det faktiske antal datadisce til at oprette. I definitionen af virtuel maskine, kan du bruge funktionen **tage** til at hente kun antallet af elementer, du rent faktisk vil fra matrixen.

En komplet eksempel på dette mønster er Vis i dialogboksen [Opret en VM med et dynamisk udvalg af datadisce](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) skabelon.

De relevante afsnit i skabelonen installation er vist nedenfor. En masse skabelonen er blevet fjernet for at markere de sektioner, der er involveret i dynamisk oprettelse af et antal datadisce. Bemærk parameter **numDataDisks** , der gør det muligt at overføre i feltet Antal disk, der opretter. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Næste trin
- Hvis du vil vide mere om sektionerne i en skabelon, skal du se [Redigering Azure ressourcestyring skabeloner](./resource-group-authoring-templates.md).
- Se [Azure ressourcestyring skabelon funktioner](./resource-group-template-functions.md)for alle de funktioner, du kan bruge i en skabelon.
- For at lære at installere skabelonen, skal du se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md).
