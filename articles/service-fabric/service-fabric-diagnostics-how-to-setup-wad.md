<properties
   pageTitle="Indsamle logge ved hjælp af Azure diagnosticering | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Azure diagnosticering til at indsamle logge fra en tjeneste-strukturen klynge kører i Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Indsamle logge ved hjælp af Azure diagnosticering

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Når du kører en Azure Service strukturen klynge, er det en god ide at indsamle logge fra alle knuderne i et centralt sted. Har du logge på en central placering, kan du analysere og foretage fejlfinding af problemer i din klynge eller problemer i til programmer og tjenester, der kører i pågældende klynge.

En metode til at overføre og indsamle logge er at bruge filtypenavnet Azure diagnosticering, som overfører logfiler til Azure-lager. Logfiler er ikke det nyttige direkte i lagerplads. Men du kan bruge en ekstern proces til at læse hændelserne fra lager og sætte dem i et produkt som [Log Analytics](../log-analytics/log-analytics-service-fabric.md), [Elastiske Søg](service-fabric-diagnostic-how-to-use-elasticsearch.md)eller en anden parsing af log løsning.

## <a name="prerequisites"></a>Forudsætninger
Du skal bruge disse værktøjer til at udføre nogle af handlingerne i dette dokument:

* [Azure diagnosticering](../cloud-services/cloud-services-dotnet-diagnostics.md) (relateret til Azure Cloud Services, men har gode oplysninger og eksempler)
* [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure ressourcestyring-klient](https://github.com/projectkudu/ARMClient)
* [Azure ressourcestyring skabelon](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Log kilder, du vil indsamle
- **Tjenesten strukturen logfiler**: udledes fra platformen til standard begivenhed sporing for Windows (ETW) og EventSource kanal. Logfiler kan være en af flere typer:
  - Funktionsdygtige hændelser: logfiler for handlinger, som udfører Service-strukturen platform. Som eksempler kan nævnes oprettelse af programmer og tjenester, node tilstandsændringer og oplysninger om opgradering.
  - [Pålidelige aktører programming model begivenheder](service-fabric-reliable-actors-diagnostics.md)
  - [Pålidelige Services programming model begivenheder](service-fabric-reliable-services-diagnostics.md)
- **Programhændelser**: begivenheder udledes fra din tjeneste kode og skrevet ud ved hjælp af klassen EventSource hjælpeprogram, findes i Visual Studio-skabeloner. Finde flere oplysninger om, hvordan du kan skrive logfiler fra dit program [skærm og diagnosticere tjenester i en lokal computer udvikling opsætning](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Installere filtypenavnet diagnosticering
Det første trin i indsamling af logfiler er at installere filtypenavnet diagnosticering på hver af FOS i tjenesten strukturen klynge. Filtypenavnet Diagnostics indsamler logge på hver VM og overfører dem til kontoen lagerplads, du angiver. Trinnene varierer en smule afhængigt af om du bruger Azure portal eller Azure ressourcestyring. Trinnene varierer afhængigt af om installationen er en del af oprettelse af klynge eller til en klynge, der allerede findes. Lad os se på trinnene for hvert scenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Installere filtypenavnet diagnosticering som en del af klynge oprettelse til portalen
Du skal installere filtypenavnet Diagnostics på FOS i klyngen som en del af oprettelse af klynge, kan du bruge panelet Diagnostics indstillinger vist på følgende billede. For at aktivere pålidelig aktører eller pålidelige Services begivenhed af websteder skal du sikre dig, diagnosticering er indstillet til **på** (standardindstillingen). Når du opretter klyngen, kan du ikke ændre disse indstillinger ved hjælp af portalen.

![Indstillinger for Azure diagnosticering i portalen til oprettelse af klynge](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Fra Azure support team *kræver* support logfiler for at løse eventuelle anmodninger om support, som du opretter. Disse logfiler indsamles i realtid og gemmes i en af lagerplads konti, der er oprettet i ressourcegruppen. Indstillingerne for diagnosticering konfigurere programmet niveau begivenheder. Disse hændelser omfatter [Pålidelig aktører](service-fabric-reliable-actors-diagnostics.md) begivenheder, [Pålidelig Services](service-fabric-reliable-services-diagnostics.md) begivenheder og nogle system-niveau Service-strukturen hændelser, der skal være gemt på Azure-lager.

Produkter som [Elastiske Søg](service-fabric-diagnostic-how-to-use-elasticsearch.md) eller din egen proces kan åbne hændelserne kontoen lagerplads. Der findes i øjeblikket ingen måde til at filtrere eller soignere de hændelser, der sendes til tabellen. Hvis du ikke har implementeret en proces for at fjerne begivenheder fra tabellen, fortsat tabellen vokse.

Når du opretter en klynge ved hjælp af portalen, skal det anbefales, at du henter skabelon *før du klikker på * *OK*** til at oprette klyngen. Se [konfigurere en tjeneste-strukturen klynge ved hjælp af en skabelon til Azure ressourcestyring](service-fabric-cluster-creation-via-arm.md)få mere at vide. Du skal bruge skabelonen til at foretage ændringer senere, fordi du ikke har foretaget ændringer ved hjælp af portalen.

Du kan eksportere skabeloner fra portalen ved at benytte følgende fremgangsmåde. Skabelonerne kan dog være sværere at bruge, fordi de kan have null-værdier, der mangler nødvendige oplysninger.

1. Åbn din ressourcegruppe.
2. Vælg **Indstillinger for** at få vist panelet indstillinger.
3. Vælg **installationer** til at vise panelet installation oversigt.
4. Vælg en installation for at få vist detaljerne for installationen.
5. Vælg **Eksporter skabelon** til at vise panelet skabelon.
6. Vælg **Gem fil** eksportere en .zip-fil, der indeholder den skabelon, parameter og PowerShell filer.

Når du eksporterer filerne, skal du foretager en ændring af. Redigere filen parameters.json, og fjern **adminPassword** elementet. Dette medfører en påmindelse til adgangskoden, når der køres scriptet installation. Når du kører scriptet installation, kan det være nødvendigt at løse null parameterværdier.

Bruge den hentede skabelon til at opdatere en konfiguration:

1. Udtrække indholdet til en mappe på din lokale computer.
2. Ændre oplysningerne afspejler den nye konfiguration.
3. Start PowerShell, og Skift til den mappe, hvor du udpakkede indholdet.
4. Kør **deploy.ps1** , og Udfyld abonnement-ID, ressource gruppenavn (Brug samme navn at opdatere konfigurationen) og et entydigt installation navn.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Installere filtypenavnet diagnosticering som en del af oprettelse af klynge ved hjælp af Azure ressourcestyring
Hvis du vil oprette en klynge ved hjælp af Ressourcestyring, skal du føje diagnosticering konfigurationen JSON til fuld klynge ressourcestyring skabelon, før du opretter klyngen. Vi giver dig et eksempel på fem VM klynge ressourcestyring en skabelon med diagnosticering konfiguration, der er føjet til den som en del af vores ressourcestyring skabelon eksempler. Du kan se det på denne placering i galleriet Azure eksempler: [klynge fem noder med diagnosticering ressourcestyring skabelon eksempel](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

For at se indstillingen diagnosticering i skabelonen ressourceleder, der skal du åbne azuredeploy.json filen og søge efter **IaaSDiagnostics**. Vælg knappen **Implementer til Azure** findes på det forrige link for at oprette en klynge ved hjælp af denne skabelon skal.

Alternativt kan du kan hente eksemplet Ressourcestyring, foretage ændringer i den og oprette en klynge med skabelonen ændret ved hjælp af den `New-AzureRmResourceGroupDeployment` kommando i en Azure PowerShell-vinduet. Se følgende kode for de parametre, du overfører i kommandoen. Du kan finde detaljerede oplysninger om, hvordan du installerer en ressourcegruppe ved hjælp af PowerShell, i artiklen [Implementer en ressourcegruppe med skabelonen Azure ressourcestyring](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Installere filtypenavnet diagnosticering til en eksisterende klynge
Hvis du har en eksisterende klynge, der ikke har installeret diagnosticering, eller hvis du vil ændre en eksisterende konfiguration, kan du tilføje eller opdatere den. Rediger skabelonen Ressourcestyring, der bruges til at oprette den eksisterende klynge eller hente skabelonen fra portalen, som beskrevet tidligere. Redigere filen template.json ved at udføre følgende opgaver.

Tilføje en ny ressource lagerplads til skabelonen ved at tilføje i afsnittet ressourcer.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Derefter skal du tilføje til sektionen parametre lige efter lagerplads konto definitioner, mellem `supportLogStorageAccountName` og `vmNodeType0Name`. Erstat den pladsholder til tekst *kontonavn lager her* med navnet på kontoen, lagerplads.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Derefter opdatere den `VirtualMachineProfile` afsnit i filen template.json ved at tilføje følgende kode i matrixen filtypenavne. Sørg for at tilføje et komma i starten eller slutningen, afhængigt af hvor den er indsat.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Når du redigere filen template.json, som beskrevet, genpublicere skabelonen ressourcestyring. Hvis skabelonen er eksporteret, genudgiver kører filen deploy.ps1 skabelonen. Når du installerer, skal du sikre, at **ProvisioningState** er **lykkedes**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Opdatere diagnosticering for at indsamle og overføres fra nye EventSource kanaler
Opdatere diagnosticering for at indsamle logge fra nye EventSource kanaler, der repræsenterer et nyt program, du er ved at installere, skal du udføre de samme trin som i [forrige afsnit](#deploywadarm) til opsætning af diagnosticering til en eksisterende klynge.

Opdatere den `EtwEventSourceProviderConfiguration` sektion i filen template.json til at føje poster til de nye EventSource kanaler, før du anvender konfigurationen opdatere ved hjælp af den `New-AzureRmResourceGroupDeployment` PowerShell-kommando. Navnet på begivenhed kilden er defineret som en del af din kode i Visual Studio oprettede ServiceEventSource.cs filen.

Hvis din begivenhed kilde hedder Mine hændelseskilde, tilføje følgende kode for at placere begivenheder fra Mine Eventsource i en tabel med navnet MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Rediger skabelonen Ressourcestyring for at indsamle tællere i ydeevne eller -hændelseslogge, ved hjælp af de eksempler, der er angivet i [oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af en skabelon til Azure ressourcestyring](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Udgive skabelonen ressourcestyring derefter igen.

## <a name="next-steps"></a>Næste trin
Hvilke hændelser, du skal søge efter, når du foretager fejlfinding af problemer kan se de diagnosticering hændelser, der udsendes for [Pålidelig aktører](service-fabric-reliable-actors-diagnostics.md) og [Pålidelig tjenester](service-fabric-reliable-services-diagnostics.md)for at forstå mere detaljeret.


## <a name="related-articles"></a>Relaterede artikler
* [Lær at indsamle tællere i ydeevne eller de logfiler ved hjælp af filtypenavnet diagnosticering](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Tjenesten strukturen løsning i Log Analytics](../log-analytics/log-analytics-service-fabric.md)
