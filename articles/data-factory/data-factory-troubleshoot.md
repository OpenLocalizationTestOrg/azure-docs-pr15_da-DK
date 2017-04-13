<properties 
    pageTitle="Fejlfinding i Azure Data Factory" 
    description="Lær at foretage fejlfinding af problemer med at bruge Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Foretage fejlfinding af problemer med Data Factory
I denne artikel indeholder fejlfindingstip til problemer, når du bruger Azure Data Factory. I denne artikel viser ikke alle de mulige problemer ved hjælp af tjenesten, men det dækker nogle problemer og generelle tip til fejlfinding.   

## <a name="troubleshooting-tips"></a>Tip til fejlfinding

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fejl: Abonnementet er ikke registreret for at bruge navneområde 'Microsoft.DataFactory'
Hvis du får vist denne fejl, er Azure Data Factory ressource udbyder ikke registreret på din computer. Benyt følgende fremgangsmåde: 

1. Start Azure PowerShell. 
2. Log på din Azure-konto ved hjælp af følgende kommando.
        Logon-AzureRmAccount 
3. Kør følgende kommando for at registrere Azure Data Factory-udbyder.
        Registrer AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Uautoriseret fejl, når du kører en Data Factory-cmdlet
Du bruger sandsynligvis ikke den højre Azure-konto eller et andet abonnement med Azure PowerShell. Brug følgende cmdletter for at vælge den højre Azure-konto og bruge med Azure PowerShell-abonnement. 

1. Logon-AzureRmAccount - Brug det rigtige bruger-ID og din adgangskode
2. Get-AzureRmSubscription - få vist alle abonnementer til kontoen. 
3. Vælg AzureRmSubscription &lt;abonnementsnavn&gt; -Vælg det rigtige abonnement. Du kan bruge det samme, som du kan bruge til at oprette en data fabrik på Azure-portalen.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Ikke Start Express konfiguration af Datastyringsgateway fra Azure-portalen
Express opsætningen af Datastyringsgateway kræver, at Internet Explorer eller en Microsoft ClickOnce kompatible webbrowser. Hvis konfigurationen Express kan ikke startes, kan du gøre et af følgende: 

- Bruge Internet Explorer eller en Microsoft ClickOnce kompatible webbrowser.

    Hvis du bruger Chrome, gå til [Chrome web store](https://chrome.google.com/webstore/), søge med "ClickOnce" nøgleord, vælge et af ClickOnce filtypenavne og installere den. 
    
    Gør det samme for Firefox (Installer tilføjelsesprogram). Klik på menuen Åbn knap på værktøjslinjen (tre vandrette linjer i øverste højre hjørne), skal du klikke på tilføjelsesprogrammer, søge med "ClickOnce" nøgleord, Vælg en af ClickOnce udvidelserne og installere den. 

- Brug linket **Manuel konfiguration** vises på den samme blade på portalen. Du kan bruge denne metode til at hente installationsfilen og køre den manuelt. Når installationen er fuldført, kan du se dialogboksen Data Management Gateway konfiguration. Kopiere den **nøgle** skærmbilledet portalen og bruge det i Konfigurationsstyring for manuelt at registrere gatewayen med tjenesten.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Ikke oprette forbindelse til lokal SQL Server 
Start **Konfigurationsstyring til Datastyringsgateway Data** på gatewaycomputeren og bruge fanen **fejlfinding** til at teste forbindelsen til SQL Server fra gatewaycomputeren. Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Input udsnit er i venter tilstand for nogensinde

Udsnit kan være i **venter** tilstand på grund af forskellige årsager. En af de almindelige årsager er, at den **eksterne** egenskab ikke er angivet til **Sand**. Et datasæt, der er produceret uden for omfanget af Azure Data Factory skal markeres med **eksterne** egenskab. Denne egenskab angiver, at dataene er ekstern og ikke understøttes af enhver rørledninger i data factory. Dataudsnit er markeret som **klar** , når dataene er tilgængelige i de respektive store. 

Se i følgende eksempel til brug af egenskaben **eksterne** . Du kan eventuelt angive **externalData*** når du angiver ekstern i forhold til sand.

Se [datasæt](data-factory-create-datasets.md) artikel for at få flere oplysninger om denne egenskab.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Føje **eksterne** egenskaben og sektionen valgfrit **externalData** til JSON definitionen af inputtabellen for at løse fejlen, og genskabe tabellen. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Hybrid kopieringen mislykkes
Se [fejlfinding af problemer med gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) til trinnene til fejlfinding af problemer med at kopiere til/fra et lokalt datalager ved hjælp af Datastyringsgateway. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: Efter behov HDInsight klargøring mislykkes
Når du bruger en sammenkædet tjeneste af typen HDInsightOnDemand, skal du angive en linkedServiceName, der peger på en Azure Blob-lager. Factory datatjeneste bruger denne lagerplads til at gemme logfiler og supplerende filer til din efter behov HDInsight klynge.  Nogle gange klargøring af en efter behov HDInsight klynge mislykkes med følgende fejl:

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Denne fejl skyldes normalt, placeringen af den angivne i linkedServiceName lagerplads konto ikke er i den samme data center placering, hvor HDInsight klargøring foregår. Eksempel:, hvis dine data factory i Vest USA og Azure opbevaring er i af USA, efter behov klargøring mislykkes i Vest USA.

Desuden, er der en anden JSON egenskaben additionalLinkedServiceNames hvor ekstra lagerplads konti kan angives i HDInsight efter behov. Disse ekstra sammenkædede lagerplads konti skal være på samme placering som HDInsight klynge, eller det lykkes ikke med den samme fejl.

### <a name="problem-custom-net-activity-fails"></a>Problem: Brugerdefineret .NET aktivitet mislykkes
Se [fejlfinding af en rørledning med tilpassede aktiviteter](data-factory-use-custom-activities.md#debug-the-pipeline) have en detaljeret vejledning. 

## <a name="use-azure-portal-to-troubleshoot"></a>Bruge Azure portal til at foretage fejlfinding af 

### <a name="using-portal-blades"></a>Ved hjælp af portalen blade
Du kan se [skærm pipeline](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) til trin. 

### <a name="using-monitor-and-manage-app"></a>Brug af skærm og administrere App
Se [skærm og administrere data factory rørledninger ved hjælp af skærm og administrer App](data-factory-monitor-manage-app.md) få mere at vide. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Bruge Azure PowerShell til at foretage fejlfinding af

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Bruge Azure PowerShell til at foretage fejlfinding af en fejl  
Du kan finde oplysninger i [skærm Data Factory rørledninger ved hjælp af Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) . 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 