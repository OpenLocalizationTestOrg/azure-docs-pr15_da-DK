<properties 
    pageTitle="Overvåge og administrere Azure Data Factory rørledninger" 
    description="Lær, hvordan du bruger Azure Portal og Azure PowerShell til at overvåge og administrere Azure data fabrikker og rørledninger, du har oprettet." 
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
    ms.date="09/06/2016" 
    ms.author="spelluru"/>


# <a name="monitor-and-manage-azure-data-factory-pipelines"></a>Overvåge og administrere Azure Data Factory rørledninger
> [AZURE.SELECTOR]
- [Brug af Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Brug af overvågning og administration af App](data-factory-monitor-manage-app.md)

Tjenesten Data Factory giver pålideligt og fuldstændigt visning af dine lagerplads, behandling og data bevægelse tjenester. Tjenesten giver dig en overvågning dashboard hjælper med at, som du kan bruge til at udføre følgende opgaver: 

- Hurtigt at vurdere til slut data pipeline tilstand.
- Identificere problemer med, og løs problemet, hvis det er nødvendigt. 
- Registrere data om datakildeafstamning. 
- Spore relationerne mellem dine data på tværs af en af dine kilder.
- Vis fuld historiske revision af udførelse af jobbet, systemtilstand og afhængigheder.

I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding af din rørledninger. Den indeholder også oplysninger om, hvordan du Opret beskeder og få besked på følgende mislykkede forsøg.

## <a name="understand-pipelines-and-activity-states"></a>Forstå rørledninger og aktivitet tilstande
Ved hjælp af portalen Azure, kan du:

- Få vist dine data factory som et diagram
- Få vist aktiviteter i en pipeline
- Få vist input- og outputområder datasæt
- og meget mere. 

Dette afsnit indeholder også, hvordan et udsnit overgange fra én tilstand til en anden tilstand.   

### <a name="navigate-to-your-data-factory"></a>Gå til dine data factory
1.  Log på [Azure-portalen](https://portal.azure.com).
2.  Klik på **Data fabrikker** i menuen til venstre. Hvis du ikke se den, skal du klikke på **flere tjenester >** , og klik på **Data fabrikker** under **INTELLIGENCE + ANALYTICS** kategori. 

    ![Gennemse alle -> Data fabrikker](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

    Du bør se alle data virksomheder i bladet **Data fabrikker** . 
4. Vælg den data factory, du er interesseret i, i bladet Data fabrikker.

    ![vælge data factory](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)  
5.  og du bør se startsiden (**Data factory** blade) for data factory.

    ![Data factory blade](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramvisning af dine data factory
Diagramvisning af en fabrik data indeholder en enkelt rude for at overvåge og administrere data fabrik og dens aktiver.

Klik på **Diagram** på hjemmesiden data factory for at se diagramvisning af dine data factory skal.

![Diagramvisning](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Du kan zoome ind, zoome ud og zoome for at tilpasse, zoom til 100%, låse layoutet af diagrammet, og Placer automatisk rørledninger og tabeller. Du kan også få vist oplysninger om datakildeafstamning i data (vise foregående og efterfølgende elementer for de markerede elementer).
 

### <a name="activities-inside-a-pipeline"></a>Aktiviteterne i en rørledning 
1. Højreklik på rørledningen, og klik på **Åbn pipeline** for at få vist alle aktiviteter i pipeline sammen med input- og outputområder datasæt for aktiviteterne. Denne funktion er nyttig, når din pipeline består af mere end én aktivitet, og du ønsker at forstå de funktionsdygtige om datakildeafstamning af en enkelt rørledning.

    ![Åbn pipeline menu](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)  
2. I eksemplet nedenfor kan du se to aktiviteter i rørledning med deres input og output. Aktiviteten med titlen **JoinData** af typen HDInsight Hive aktivitet og **EgressDataAzure** af typen kopi aktivitet er i dette eksempel pipeline. 
    
    ![Aktiviteterne i en rørledning](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Du kan gå tilbage til startsiden for Data Factory ved at klikke på Data factory link i brødkrummen i det øverste venstre hjørne.

    ![Gå tilbage til data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-state-of-each-activity-inside-a-pipeline"></a>Vis tilstanden for hver aktivitet i en rørledning
Du kan få vist den aktuelle status for en aktivitet ved at få vist status for en af de datasæt, der er oprettet med aktiviteten. 

For eksempel: i eksemplet nedenfor **BlobPartitionHiveActivity** kørte korrekt og produceret et datasæt med navnet **PartitionedProductsUsageTable**, som er i **klar** tilstand.

![Tilstanden for pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Hvis du dobbeltklikker på **PartitionedProductsUsageTable** i diagramvisning viser alle de udsnit, der er oprettet med forskellige aktivitet kører i en rørledning. Du kan se, at **BlobPartitionHiveActivity** blev kørt hver måned for seneste otte måneder og produceret udsnittene i **klar** tilstand.

Datasæt udsnittene i data factory kan have en af følgende statusser:

<table>
<tr>
    <th align="left">Stat</th><th align="left">Undertilstand</th><th align="left">Beskrivelse</th>
</tr>
<tr>
    <td rowspan="8">Venter</td><td>ScheduleTime</td><td>Tid, der har ikke komme for udsnit til at køre.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Før afhængigheder er ikke klar.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beregn ressourcer er ikke tilgængelige.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle aktivitet forekomster er optaget kører andre udsnit.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivitet er afbrudt midlertidigt og kan ikke køre udsnittene, indtil den genoptages.</td>
</tr>
<tr>
<td>Prøv igen</td><td>Udførelse af aktivitet skal gentages.</td>
</tr>
<tr>
<td>Datavalidering</td><td>Datavalidering er ikke startet endnu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Venter på validering forsøges igen.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validere</td><td>Validering i gang.</td>
</tr>
<td></td>
<td>Udsnittet behandles.</td>
</tr>
<tr>
<td rowspan="4">Mislykkedes</td><td>Der opstod timeout for</td><td>Udførelse af tog længere tid end, der er tilladt i aktiviteten.</td>
</tr>
<tr>
<td>Annulleret</td><td>Annulleret af brugerhandling.</td>
</tr>
<tr>
<td>Datavalidering</td><td>Validering mislykkedes.</td>
</tr>
<tr>
<td></td><td>Kunne ikke generere og/eller validere udsnittet.</td>
</tr>
<td>Klar</td><td></td><td>Udsnittet er klar til forbrug.</td>
</tr>
<tr>
<td>Ignoreret</td><td></td><td>Udsnittet behandles ikke.</td>
</tr>
<tr>
<td>Ingen</td><td></td><td>Et udsnit, der bruges til at findes med en anden status, men er blevet nulstillet.</td>
</tr>
</table>



Du kan få vist oplysninger om et udsnit ved at klikke på et udsnit post i bladet **Senest opdateret udsnit** .

![Udsnit detaljer](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Hvis udsnittet er blevet udført flere gange, vises der flere rækker på listen **aktivitet kører** . Du kan få vist oplysninger om en aktivitet køre ved at klikke på Kør posten på listen **aktivitet kører** . Listen viser alle logfilerne sammen med en fejlmeddelelse, hvis en hvilken som helst. Denne funktion er praktisk til at få vist og foretage fejlfinding af logfiler uden at forlade factory dine data.

![Aktivitet køre detaljer](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Hvis udsnittet ikke er i tilstanden **klar** , kan du se de foregående udsnit, der ikke er klar og blokerer det aktuelle udsnit fra udførelse af på listen **før udsnit, der er ikke klar** . Denne funktion er nyttig, når din udsnit er i **venter** tilstand, og du ønsker at forstå de tidligere afhængigheder, venter på udsnittet.

![Før udsnittene ikke klar](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Datasæt tilstandsdiagram
Når du installerer en data fabrik og rørledningerne har en gyldig aktive periode, udsnit datasættet overgang fra én tilstand til en anden. Udsnit status følger i øjeblikket følgende tilstandsdiagram:

![Tilstandsdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Dataset tilstand overgang strømmen i data factory: vente -> fremdrift/i-igangværende (validerer) -> klar/mislykket

Udsnittene starter i tilstanden **venter** for foreløbig betingelser opfyldes, før du udfører. Derefter aktiviteten starter afholde og udsnittet går i **Igangværende** tilstand. Aktivitet udførelsen muligvis lykkes eller mislykkes. Udsnittet er markeret som **klar**' eller **mislykket** baseret på resultatet af kørslen. 

Du kan nulstille udsnit for at gå tilbage fra **klar** eller **mislykket** tilstand **venter** tilstand. Du kan også markere tilstanden udsnit til **Spring**, som forhindrer aktiviteten køres og ikke behandle udsnittet.


## <a name="manage-pipelines"></a>Administrere rørledninger
Du kan administrere dine rørledninger ved hjælp af Azure PowerShell. Du kan for eksempel stoppe og fortsætte rørledninger ved at køre Azure PowerShell-cmdlet'er. 

### <a name="pause-and-resume-pipelines"></a>Afbryde og genoptage rørledninger
Du kan pause/afbryde rørledninger ved hjælp af **Pause-AzureRmDataFactoryPipeline** Powershell-cmdlet. Denne cmdlet er nyttig, når du ikke vil køre din rørledninger, indtil en problemet er løst.

For eksempel: på følgende skærmbillede, der er identificeret et problem med **PartitionProductsUsagePipeline** på **productrecgamalbox1dev** data fabrik og vi ønsker at annullere rørledningen.

![Pipeline afbrydes](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Hvis du vil annullere en rørledning, du køre følgende PowerShell-kommando:

    Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Eksempel:

    Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Når problemet er løst med **PartitionProductsUsagePipeline**, kan den afbrudte rørledning genoptages ved at køre følgende PowerShell-kommando:

    Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Eksempel:

    Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## <a name="debug-pipelines"></a>Fejlfinding af rørledninger
Azure Data Factory indeholder mange funktioner via Azure portal og Azure PowerShell til at udføre fejlfinding og foretage fejlfinding af rørledninger.

### <a name="find-errors-in-a-pipeline"></a>Finde fejl i en pipeline
Hvis aktivitet Kør mislykkes i en pipeline, er det datasæt, der er oprettet af pipeline i en fejltilstand på grund af fejlen. Du kan udføre fejlfinding og foretage fejlfinding i Azure Data Factory ved hjælp af følgende netværksadgang.

#### <a name="use-azure-portal-to-debug-an-error"></a>Bruge Azure portal til at foretage fejlfinding af en fejl:

3.  Klik på problem udsnittet med **STATUS** er indstillet til **mislykket**i bladet **tabel** .

    ![Tabel blade med problemet udsnit](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.  Klik på den aktivitet, køre, mislykkedes i bladet **DATA UDSNIT** .
    
    ![Dataslice med en fejl](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.  Du kan hente de filer, der er knyttet til HDInsight behandlingen bladet **Aktivitet køre detaljer** . Klik på Hent for Status/stderr at hente fejllogfilen, der indeholder oplysninger om fejlen.

    ![Aktivitet køre detaljer blade med fejl](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)  

#### <a name="use-the-powershell-to-debug-an-error"></a>Bruge PowerShell til at foretage fejlfinding af en fejl
1.  Start **Azure PowerShell**.
3.  Kør **Get-AzureRmDataFactorySlice** kommando for at se udsnittene og deres status. Skal du se afsnittet et udsnit med status: **mislykkedes**.       

            Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    
    Eksempel:
        
            Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

    Erstat **StartDateTime** med den StartDateTime værdi, du angav for Set-AzureRmDataFactoryPipelineActivePeriod.
4. Nu skal køre **Get-AzureRmDataFactoryRun** cmdlet for at få mere at vide om aktivitet køre til udsnittet.

        Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
        <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    
    Eksempel:

        Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

    Værdien af StartDateTime er starttidspunktet for det fejl/problem udsnit, du skrev ned fra det forrige trin. Dato og klokkeslæt skal være omsluttet af dobbelte anførselstegn.
5.  Du burde se output med oplysninger om fejlen (svarende til følgende):

            Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
            ResourceGroupName       : ADF
            DataFactoryName         : LogProcessingFactory3
            TableName               : EnrichedGameEventsTable
            ProcessingStartTime     : 10/10/2014 3:04:52 AM
            ProcessingEndTime       : 10/10/2014 3:06:49 AM
            PercentComplete         : 0
            DataSliceStart          : 5/5/2014 12:00:00 AM
            DataSliceEnd            : 5/6/2014 12:00:00 AM
            Status                  : FailedExecution
            Timestamp               : 10/10/2014 3:04:52 AM
            RetryAttempt            : 0
            Properties              : {}
            ErrorMessage            : Pig script failed with exit code '5'. See wasb://     adfjobs@spestore.blob.core.windows.net/PigQuery
                                            Jobs/841b77c9-d56c-48d1-99a3-
                        8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                        more details.
            ActivityName            : PigEnrichLogs
            PipelineName            : EnrichGameLogsPipeline
            Type                    :
    
    
6.  Du kan køre **Gem AzureRmDataFactoryLog** cmdlet med id-værdi, du kan se fra outputtet og hente de logfiler, ved hjælp af **-DownloadLogsoption** til cmdlet.

            Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"


## <a name="rerun-failures-in-a-pipeline"></a>Kør følgende mislykkede forsøg i en pipeline

### <a name="using-azure-portal"></a>Portalen til Azure

Når du fejlfinding i forbindelse med og foretage fejlfinding af fejl i en rørledning, kan du køre følgende mislykkede forsøg ved at navigere til fejl udsnittet og klikke på knappen **Kør** på kommandolinjen.

![Kør et mislykkedes udsnit](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

I tilfælde af udsnittet mislykkes validering på grund af en politik fejl (for ex: data, der er ikke tilgængelig), du kan rette fejlen og validere igen ved at klikke på knappen **Valider** på kommandolinjen.
![Ret fejl og validere](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="using-azure-powershell"></a>Brug af Azure PowerShell

Du kan køre fejl ved hjælp af cmdlet'en Set-AzureRmDataFactorySliceStatus til. Se [Konfigurere AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) emnet for syntaksen og andre oplysninger om cmdlet. 

**Eksempel:** I følgende eksempel indstiller status for alle udsnit for tabellen 'DAWikiAggregatedData' til venter på Azure data fabrik 'WikiADF'.

UpdateType er indstillet til UpstreamInPipeline, hvilket betyder, at status for hvert udsnit for tabellen og alle de afhængige (mod) tabeller er indstillet til "Venter". Andre mulige værdi for denne parameter er "Person".

    Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## <a name="create-alerts"></a>Opret beskeder
Azure logge brugerhændelser, når en Azure ressource (for eksempel en fabrik data) er oprettet, opdateres eller slettes. Du kan oprette beskeder på disse hændelser. Data Factory gør det muligt at registrere forskellige former for statistik og oprette beskeder på målepunkter. Vi anbefaler, at du bruger begivenheder til realtid overvågning og målepunkter til historiske formål. 

### <a name="alerts-on-events"></a>Beskeder om begivenheder
Azure begivenheder giver nyttige indsigt i hvad sker der i dine Azure ressourcer. Azure logge brugerhændelser, når en Azure ressource (for eksempel en fabrik data) er oprettet, opdateres eller slettes. Hændelser oprettes, når du bruger Azure Data Factory, når:

- Azure Data Factory er oprettet/opdateret/slettet.
- Databehandling (kaldet som kører) er i gang/fuldført.
- En efter behov HDInsight klynge oprettes og fjernet.

Du kan oprette beskeder på disse brugerhændelser, der og konfigurere dem for at sende besked via mail til administratoren og medadministratorer af abonnementet. Desuden kan du angive yderligere mailadresser af brugere, der skal modtage besked via mail, når betingelserne er opfyldt. Denne funktion er nyttig, når du vil få besked på fejl og ikke hele tiden vil overvåge dine data factory.

> [AZURE.NOTE] På portalen viser i øjeblikket ikke beskeder på begivenheder. Bruge [overvågnings- og administration af App](data-factory-monitor-manage-app.md) til at se alle vigtige beskeder.

#### <a name="specifying-an-alert-definition"></a>Angive en definition af beskeder om:
For at angive en besked om definition skal oprette du en JSON-fil, der beskriver de handlinger, du vil have besked om på. I følgende eksempel sender beskeden besked via mail for handlingen RunFinished. Hvis du vil være specifik, besked via mail sendes, når en Kør i data factory er fuldført, og kør mislykkes (Status = FailedExecution).

    {
        "contentVersion": "1.0.0.0",
         "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters": {},
        "resources": 
        [
            {
                "name": "ADFAlertsSlice",
                "type": "microsoft.insights/alertrules",
                "apiVersion": "2014-04-01",
                "location": "East US",
                "properties": 
                {
                    "name": "ADFAlertsSlice",
                    "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                    "isEnabled": true,
                    "condition": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                        "dataSource": 
                        {
                            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                            "operationName": "RunFinished",
                            "status": "Failed",
                            "subStatus": "FailedExecution"   
                        }
                    },
                    "action": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails": [ "<your alias>@contoso.com" ]
                    }
                }
            }
        ]
    }

Fra definition JSON kan **understatus** fjernes, hvis du ikke vil have besked på en bestemt fejl.

I dette eksempel konfigurerer beskeden for alle data fabrikker i dit abonnement. Hvis du vil den vigtige besked til konfigureres til en bestemt data fabrik, kan du angive data factory **resourceUri** i **datakilden**:

    "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

Den følgende tabel indeholder listen over tilgængelige handlinger og statusser (og underordnede statusser).

Handlingsnavn | Status | Sub status
-------------- | ------ | ----------
RunStarted | I gang | Starte
RunFinished | Mislykkedes / lykkedes | FailedResourceAllocation<br/><br/>Lykkedes<br/><br/>FailedExecution<br/><br/>Der opstod timeout for<br/><br/>< annulleret<br/><br/>FailedValidation<br/><br/>Efterladt uden vedligeholdelse
OnDemandClusterCreateStarted | I gang
OnDemandClusterCreateSuccessful | Lykkedes
OnDemandClusterDeleted | Lykkedes

Du kan finde oplysninger om JSON elementer, der bruges i eksemplet i [Oprette reglen](https://msdn.microsoft.com/library/azure/dn510366.aspx) . 

#### <a name="deploying-the-alert"></a>Implementere påmindelsen 
Hvis du vil installere den vigtige besked, Brug Azure PowerShell-cmdlet'en: **Ny AzureRmResourceGroupDeployment**, som vist i følgende eksempel:

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Når ressource gruppe installationen er fuldført, skal se du følgende meddelelser:

    VERBOSE: 7:00:48 PM - Template is valid.
    WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
    Please update scripts to remove this parameter.
    VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
    VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
    
    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :

> [AZURE.NOTE] Du kan bruge [Opret besked regel](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST-API til at oprette en besked om. JSON data ligner eksemplet JSON.  

#### <a name="retrieving-the-list-of-azure-resource-group-deployments"></a>Hentning af listen over Azure ressource gruppe installationer
Hvis du vil hente listen over installerede Azure ressourcegruppe installationer, Brug cmdlet'en: **Get-AzureRmResourceGroupDeployment**, som vist i følgende eksempel:

    Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
    
    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :


#### <a name="troubleshooting-user-events"></a>Fejlfinding i forbindelse med brugerrelaterede hændelser


1. Du kan se alle de hændelser, der er oprettet ved klik på feltet **målepunkter og handlinger** .

    ![Målepunkter og drift felt](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)

2. Klik på **begivenheder** felt for at få vist hændelserne. 

    ![Hændelser felt](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Du kan se detaljer om begivenheder i bladet **begivenheder** , filtrere begivenheder og så videre. 

    ![Hændelser blade](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Klik på en **handling** på listen handlinger, der medfører en fejl.
    
    ![Vælg en handling](./media/data-factory-monitor-manage-pipelines/select-operation.png) 
5. Klik på hændelsen **fejl** for at se oplysninger om fejlen.

    ![Begivenhed fejl](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)
  

Se [Azure indsigt cmdletter](https://msdn.microsoft.com/library/mt282452.aspx) artikel til PowerShell-cmdletter, som du kan bruge til at tilføje/få/Fjern beskeder. Her er nogle eksempler på brug af cmdlet'en **Get-AlertRule** til: 


    PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
        
            Properties :
            Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
            Condition   :
            DataSource :
            EventName             :
            Category              :
            Level                 :
            OperationName         : RunFinished
            ResourceGroupName     :
            ResourceProviderName  :
            ResourceId            :
            Status                : Failed
            SubStatus             : FailedExecution
            Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
            Condition   :
            Description : One or more of the data slices for the Azure Data Factory has failed processing.
            Status      : Enabled
            Name:       : ADFAlertsSlice
            Tags       :
            $type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
            Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
            Location   : West US
            Name       : ADFAlertsSlice
    
    PS C:\> Get-AlertRule -res $resourceGroup

            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0
    
            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
            Location   : West US
            Name       : FailedExecutionRunsWest3

    PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
    
            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0

Kør følgende kommandoer i få hjælp til at se detaljerne for og eksempler på cmdlet'en Get-AlertRule til. 

    get-help Get-AlertRule -detailed 
    get-help Get-AlertRule -examples


- Hvis du se beskeder om generering af begivenheder på bladet portalen, men du ikke modtager besked via mail, skal du kontrollere, om angivne e-mail-adresse er indstillet til at modtage mails fra eksterne afsendere. De e-mails med en påmindelse kan være blevet blokeret af indstillingerne for e-mail.

### <a name="alerts-on-metrics"></a>Beskeder på målepunkter
Data Factory gør det muligt at registrere forskellige former for statistik og oprette beskeder på målepunkter. Du kan overvåge og oprette beskeder på følgende omfanget af udsnittene i dine data factory.
 
- Mislykkedes kører
- Vellykket kører

Denne statistik er praktiske, så du kan få en oversigt over samlede mislykkedes og vellykkede kører i deres data factory. Målepunkter der udledes, hver gang der opstår et udsnit Kør. Oven på timetallet, er disse målepunkter sammenlagt og overføres til kontoen lagerplads. Derfor for at aktivere målepunkter skal konfigurere en lagerplads konto.


#### <a name="enabling-metrics"></a>Aktivering af målepunkter:
Klik på følgende fra Data Factory blade for at aktivere målepunkter skal:

**Overvågning af** -> **metrisk** -> **diagnostik indstillinger** -> **diagnostik**

![Diagnosticerings-link](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Klik **på** på bladet **diagnostik** , og Vælg kontoen, lagring, og Gem.

![Diagnosticering blade](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Når du har gemt, kan det kan tage op til en time på målepunkter skal være synlige på bladet overvågning, fordi målepunkter sammenlægning sker der, hver time.


### <a name="setting-up-alert-on-metrics"></a>Konfigurere en besked for målepunkter:

Klik på **Data Factory målepunkter** blade: 

![Data factory målepunkter felt](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Klik på bladet **metrisk** **+ Tilføj påmindelse** på værktøjslinjen. 
![Data factory metriske blade - tilføje besked](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Gør følgende på siden **Tilføj en besked om** , og klik på **OK**.
 
- Angiv et navn for den vigtige besked (eksempel: mislykkedes besked).
- Angiv en beskrivelse for den vigtige besked (eksempel: sende en mail, når der opstår fejl).
- Vælg en metrikværdi (mislykkedes kører kontra vellykket kører).
- Angiv en betingelse og en grænseværdi.   
- Angiv perioden. 
- Angiv, om der skal sendes en mail til ejere, bidragydere og læsere.
- og meget mere. 

![Data factory metriske blade - tilføje besked](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Når reglen er tilføjet, bladet lukkes, og du får vist den nye besked på siden **metrisk** . 

![Data factory metriske blade - tilføje besked](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Du skal også se antallet af beskeder på feltet **beskeder** . Klik på **beskeder** felt.

![Data factory metriske blade - regler for påmindelser](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

I bladet **beskeder** , kan du se eksisterende beskeder. Hvis du vil tilføje en påmindelse, skal du klikke på **Tilføj påmindelse** på værktøjslinjen.

![Regler for påmindelser blade](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Påmindelser:
Når reglen opfylder betingelsen, skal du få en besked om aktiveret mail. Når problemet er løst, og giv betingelsen ikke stemmer overens med mere, får du en besked om løst mail.

Denne funktionalitet er anderledes end begivenheder hvor der sendes en meddelelse ved hver fejl hvilke reglen berettiger.

### <a name="deploying-alerts-using-powershell"></a>Implementere advarsler ved hjælp af PowerShell
Du kan installere beskeder for målepunkter på samme måde som dig om begivenheder. 

**Beskeder om definition:**

    {
        "contentVersion" : "1.0.0.0",
        "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters" : {},
        "resources" : [
        {
                "name" : "FailedRunsGreaterThan5",
                "type" : "microsoft.insights/alertrules",
                "apiVersion" : "2014-04-01",
                "location" : "East US",
                "properties" : {
                    "name" : "FailedRunsGreaterThan5",
                    "description" : "Failed Runs greater than 5",
                    "isEnabled" : true,
                    "condition" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                        "dataSource" : {
                            "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                            "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                            "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
    >/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                            "metricName" : "FailedRuns"
                        },
                        "threshold" : 5.0,
                        "windowSize" : "PT3H",
                        "timeAggregation" : "Total"
                    },
                    "action" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails" : ["abhinav.gpt@live.com"]
                    }
                }
            }
        ]
    }
 
Erstat subscriptionId, resourceGroupName og dataFactoryName i stikprøven med relevante værdier.

*metricName* allerede nu understøtter to værdier:
- FailedRuns
- SuccessfulRuns

**Implementering af den vigtige besked:**

Hvis du vil installere den vigtige besked, Brug Azure PowerShell-cmdlet'en: **Ny AzureRmResourceGroupDeployment**, som vist i følgende eksempel:

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Du bør se nedenstående meddelelse efter vellykket installation:

    VERBOSE: 12:52:47 PM - Template is valid.
    VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
    VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
    
    
    DeploymentName    : FailedRunsGreaterThan5
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 7/27/2015 7:52:56 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           


Du kan også bruge **Tilføj AlertRule** cmdlet til at installere en besked om. Se [Tilføje AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) emnet for detaljer og eksempler.  

## <a name="move-data-factory-to-a-different-resource-group-or-subscription"></a>Flytte data factory til en anden ressourcegruppe eller et andet abonnement
Du kan flytte en fabrik data til en anden ressourcegruppe eller et andet abonnement ved hjælp af knappen **Flyt** kommandoen liggende på startsiden for dine data factory. 

![Flyt data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Du kan også flytte alle relaterede ressourcer (såsom beskeder, der er knyttet til data factory) sammen med data factory.

![Dialogboksen Flyt ressourcer](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
