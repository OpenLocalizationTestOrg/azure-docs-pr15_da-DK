<properties
    pageTitle="Aktivere automatisk diagnosticeringsindstillinger ved hjælp af en ressourcestyring skabelon | Microsoft Azure"
    description="Lær at bruge en ressourcestyring skabelon til at oprette diagnosticering indstillinger, der gør det muligt at streame dine diagnosticeringslogfiler til begivenhed hubber eller gemme dem i en lagerplads konto."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Aktivere automatisk diagnosticeringsindstillinger på ressource oprettelse af ved hjælp af en ressourcestyring skabelon
I denne artikel viser vi, hvordan du kan bruge en [Azure ressourcestyring skabelon](../resource-group-authoring-templates.md) til at konfigurere indstillinger for diagnosticering af en ressource, når den er oprettet. Dette gør det muligt at starte automatisk streaming dine logge til diagnosticering og målepunkter til begivenhed hubber, arkiveres med en konto med lagerplads eller sende dem til Log Analytics, når der oprettes en ressource.

Metode til at aktivere diagnosticeringslogfiler ved hjælp af en ressourcestyring skabelon afhænger af ressourcetypen.

- **Ikke-Beregn** ressourcer (for eksempel netværk sikkerhedsgrupper, logik Apps automatisering) Brug [Indstillinger for diagnosticering beskrevet i denne artikel](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Beregne** (WAD/LAD-baseret) ressourcer bruge [WAD/LAD konfigurationsfil, der er beskrevet i denne artikel](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

I denne artikel beskriver vi hvordan du konfigurerer diagnosticering ved hjælp af begge metoder.

De grundlæggende trin er som følger:

1. Oprette en skabelon som en JSON-fil, der beskriver, hvordan du kan oprette ressourcen og aktivere diagnosticering.
2. [Implementer skabelonen ved hjælp af en hvilken som helst installationsmetode](../resource-group-template-deploy.md).

Nedenfor får et eksempel på skabelonfilen JSON du har brug for til at generere til ikke-Beregn og Beregn ressourcer.

## <a name="non-compute-resource-template"></a>Ikke-Beregn ressourceskabelon
For ikke-Beregn ressourcer skal du gøre to ting:

1. Tilføj parametre i parametre blob storage kontonavn, tjenesten bus regel-ID'ET og/eller OMS Log Analytics arbejdsområde-ID (aktivere arkivering af diagnosticeringslogfiler med en konto med lagerplads, streaming af logfiler til begivenhed hubber og/eller sende logfiler til Log Analytics).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Føje en ressource af typen i matrixen ressourcer på den ressource, som du vil aktivere diagnosticeringslogfiler `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

Egenskaber for blob for indstillingen diagnosticering følger [formatet, der er beskrevet i denne artikel](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Her er en fuld eksempel, der opretter en sikkerhedsgruppe netværk og slår streaming til begivenhed Hubs og lager med en konto med lagerplads.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Beregne ressourceskabelon
Hvis du vil aktivere diagnosticering på en Beregn ressource, for eksempel en virtuel computer eller tjeneste strukturen klynge, skal du:

1. Føje filtypenavnet Azure diagnosticering til VM ressource definition.
2. Angive en lagerplads firma og/eller begivenhed hub som en parameter.
3. Tilføje indholdet af din WADCfg XML-fil i egenskaben XMLCfg, slippe alle XML-tegn korrekt.

> [AZURE.WARNING] Det sidste trin kan være svære at Kom hurtigt i gang. [Se denne artikel](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) til et eksempel, der opdeles diagnosticering konfiguration skemaet i variabler, som er escape-tegn og formateret korrekt.

Hele processen, herunder eksempler, er beskrevet [i dette dokument](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Næste trin
- [Få mere at vide om Azure diagnosticeringslogfiler](./monitoring-overview-of-diagnostic-logs.md)
- [Streame Azure diagnosticeringslogfiler til begivenhed hubber](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
