<properties
    pageTitle="Oprette din første data factory (RESTEN) | Microsoft Azure"
    description="I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af Data Factory REST-API."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Selvstudium: Oprette din første Azure data factory ved hjælp af Data Factory REST-API
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-build-your-first-pipeline.md)
- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

I denne artikel kan bruge du Data Factory REST-API til at oprette din første Azure data factory.

## <a name="prerequisites"></a>Forudsætninger
- Læse [Selvstudium oversigt](data-factory-build-your-first-pipeline.md) artikel, og Fuldfør trinnene **betingelse** .
- Installere [Curl](https://curl.haxx.se/dlwiz/) på din computer. Du kan bruge værktøjet KRØLLET med RESTEN kommandoer til at oprette en data fabrik. 
- Følg instruktionerne i [denne artikel](../resource-group-create-service-principal-portal.md) for at: 
    1. Oprette et webprogram, der hedder **ADFGetStartedApp** i Azure Active Directory.
    2. Få **klient-ID** og **hemmeligt nøgle**. 
    3. Få **lejer-ID**. 
    4. Tildele programmet **ADFGetStartedApp** til rollen **Data Factory bidragyder** .  
- Installer [Azure PowerShell](../powershell-install-configure.md).  
- Start **PowerShell** og køre følgende kommando. Hold Azure PowerShell åbent, indtil slutningen af dette selvstudium. Hvis du lukker og åbner, skal du køre kommandoerne igen.
    1. Kør **Logon-AzureRmAccount** og angive brugernavn og adgangskode, du bruger til at logge på portalen Azure.  
    2. Kør **Get-AzureRmSubscription** for at få vist alle abonnementer til denne konto.
    3. Køre **Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription | Angiv AzureRmContext** til at markere det abonnement, du vil arbejde med. Erstat **NameOfAzureSubscription** med navnet på dit Azure-abonnement. 
3. Oprette en Azure ressourcegruppe med navnet **ADFTutorialResourceGroup** ved at køre følgende kommando i PowerShell:  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Nogle af trinnene i dette selvstudium forudsætter, at du bruger den med navnet ADFTutorialResourceGroup ressourcegruppe. Hvis du bruger en anden ressourcegruppe, skal du bruge navnet på din ressourcegruppe i stedet for ADFTutorialResourceGroup i dette selvstudium.

## <a name="create-json-definitions"></a>Oprette JSON definitioner
Oprette efter JSON-filer i den mappe, hvor curl.exe er placeret. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Navnet skal være globalt entydige, så du kan eventuelt præfiks/suffiks ADFCopyTutorialDF for at gøre det et entydigt navn. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Erstatte **kontonavn** og **accountkey** med navn og tast til kontoen Azure-lager. Få [vist, Kopiér og Regenerer lagerplads access-taster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)for at lære at få din lagerplads hurtigtast.

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

Den følgende tabel indeholder en beskrivelse til de JSON egenskaber, der bruges i kodestykket:

| Egenskaben | Beskrivelse |
| :------- | :---------- |
| Version | Angiver, at versionen af HDInsight oprettede til at være 3,2. | 
| ClusterSize | Størrelsen på HDInsight klyngen. | 
| TimeToLive | Angiver, at indstillingen Inaktivitetstid for HDInsight-klyngen, før det bliver slettet. |
| linkedServiceName | Angiver lagerplads-konto, der bruges til at gemme logfiler, der genereres af HDInsight |

Bemærk følgende punkter: 

- Data Factory opretter en **Windows-baseret** HDInsight klynge for dig med det ovenfor JSON. Du kan også få dem oprette en **Linux-baserede** HDInsight klynge. Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Du kan bruge **din egen HDInsight klynge** i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- HDInsight klynge opretter en **standard objektbeholder** i den blob-lager, du har angivet i JSON (**linkedServiceName**). HDInsight sletter ikke denne beholder, når klyngen slettes. Det skyldes design. Med efter behov HDInsight sammenkædet tjeneste, en HDInsight klynge oprettes, hver gang et udsnit behandles, medmindre der er en eksisterende live klynge (**timeToLive**) og slettes, når behandlingen er færdig.

    Efterhånden som flere udsnit er behandlet, kan du se mange beholdere i Azure blob-lager. Hvis du ikke skal bruge dem til fejlfinding af job, kan du vil slette dem for at reducere omkostningerne lagerplads. Navnene på disse beholdere følge et mønster: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Bruge funktioner såsom [Microsoft lagerplads Explorer](http://storageexplorer.com/) til at slette beholdere i Azure blob-lager.

Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


JSON definerer et datasæt med navnet **AzureBlobInput**, som repræsenterer indtastede data for en aktivitet i pipeline. Desuden angiver det, at inputdataene er placeret i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **inputdata**.

Den følgende tabel indeholder en beskrivelse til de JSON egenskaber, der bruges i kodestykket:

| Egenskaben | Beskrivelse |
| :------- | :---------- |
| type | Typeegenskaben er indstillet til AzureBlob, fordi dataene opbevares i Azure blob-lager. |  
| linkedServiceName | refererer til den StorageLinkedService, du oprettede tidligere. |
| Filnavn | Denne egenskab er valgfrit. Hvis du udelader denne egenskab, er alle filerne fra mappesti valgte. I dette tilfælde behandles kun input.log. |
| type | Logfilerne er i tekstformat, så vi bruge tekstformat. | 
| columnDelimiter | kolonner i logfilerne er adskilt af et kommategn () |
| frekvens/interval | hyppighed, der er angivet til måned og interval er 1, hvilket betyder, at de input udsnit er tilgængelige hver måned. | 
| eksterne | Denne egenskab er indstillet til sand, hvis de indtastede data ikke er oprettet af tjenesten Data Factory. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

JSON definerer et datasæt med navnet **AzureBlobOutput**, som repræsenterer outputdata til en aktivitet i pipeline. Desuden angiver det, at resultatet er gemt i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **partitioneddata**. Afsnittet **tilgængelighed** angiver, at output datasættet produceret på månedsbasis.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Erstat **storageaccountname** med navnet på kontoen Azure-lager. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

I JSON kodestykket opretter du en rørledning, der består af en enkelt aktivitet, der bruger Hive til proces data på en HDInsight klynge.

Hive script-fil **partitionweblogs.hql**, er gemt i kontoen Azure-lager (angivet af scriptLinkedService, kaldet **StorageLinkedService**) og **script** mappe i beholderen **adfgetstarted**.

Afsnittet **definerer** angiver runtime-indstillinger, der sendes til hive scriptet som Hive konfigurationsværdier (f.eks. ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

**Start** og **Stop** egenskaberne for rørledningen angiver den aktive periode af rørledninger.

I aktiviteten JSON angiver du, at Hive scriptet kører på Beregn angivet af **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Du kan finde oplysninger om JSON egenskaber, der bruges i det foregående eksempel i [Anatomien af en rørledning](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 

## <a name="set-global-variables"></a>Angive globale variabler

Udføre følgende kommandoer i Azure PowerShell, når du erstatter værdierne med dine egne:

> [AZURE.IMPORTANT] Se [forudsætninger](#prerequisites) afsnittet vejledning til at få klient-ID, klienten hemmeligt lejer-ID og abonnement-ID.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Godkende med AAD

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Oprette data factory

I dette trin skal oprette du en Azure Data Factory-med navnet **FirstDataFactoryREST**. En data fabrik kan have en eller flere rørledninger. En rørledning kan have en eller flere aktiviteter i den. For eksempel en kopi aktivitet at kopiere data fra en kilde til en destination datalager og en HDInsight Hive aktivitet til at køre Hive script for at transformere data. Kør følgende kommandoer til at oprette data factory: 

1. Tildele kommandoen til navngivne **cmd**-variabel. 

    Bekræft, at navnet på data factory du angiver her (ADFCopyTutorialDF) stemmer overens med navnet, der er angivet i **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.

        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis data factory er blevet oprettet, kan du se JSON for data factory i **resultater** Ellers kan se du en fejlmeddelelse.  

        Write-Host $results

Bemærk følgende punkter:
 
- Navnet på Azure Data Factory skal være globalt entydige. Hvis du får vist fejlen i resultater: **Data factory navnet "FirstDataFactoryREST" er ikke tilgængelig**, Benyt følgende fremgangsmåde:  
    1. Ændre navnet (for eksempel yournameFirstDataFactoryREST) i filen **datafactory.json** . Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer.
    2. Erstatte FirstDataFactoryREST med det nye navn i den første kommando, hvor variablen **$cmd** er tildelt en værdi, og Kør kommandoen. 
    3. Køre de næste to kommandoer til at aktivere REST-API for at oprette data fabrik og udskrive resultatet af handlingen. 
- Hvis du vil oprette Data Factory forekomster, skal du være en bidragyder administrator af Azure-abonnement
- Navnet på data factory kan registreres som en DNS-navn på et senere tidspunkt og dermed bliver offentligt synlige.
- Hvis du får fejl: "**dette abonnement er ikke registreret til at bruge navneområde Microsoft.DataFactory**", skal du gøre et af følgende og prøv at publicere igen: 

    - Kør følgende kommando for at registrere Data Factory provideren i Azure PowerShell: 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan køre følgende kommando for at bekræfte, at den Data Factory udbyder er registreret: 
    
            Get-AzureRmResourceProvider
    - Logon med Azure-abonnement til [Azure-portalen](https://portal.azure.com) , og gå til en Data Factory blade (eller) oprette en data fabrik i portalen Azure. Denne handling registrerer automatisk provideren for dig.

Før du opretter en rørledning, skal du først oprette nogle Data Factory-enheder. Du først oprette sammenkædede services for at sammenkæde data butikker/beregner med dit datalager, definere input og output datasæt for at repræsentere data i sammenkædede data butikker. 

## <a name="create-linked-services"></a>Oprette sammenkædede tjenester 
I dette trin skal knytte du kontoen Azure-lager og en efter behov Azure HDInsight klynge til dine data factory. Kontoen Azure-lager indeholder input- og outputområder dataene for rørledningen i dette eksempel. Tjenesten HDInsight sammenkædet bruges til at køre Hive-script, der er angivet i aktiviteten af rørledning i dette eksempel. 

### <a name="create-azure-storage-linked-service"></a>Oprette Azure-lager, der er sammenkædet service
I dette trin kan sammenkæde du kontoen Azure-lager til dine data factory. Med dette selvstudium bruger du den samme konto Azure-lager til lagring input/output data og HQL script-fil.

1. Tildele kommandoen til navngivne **cmd**-variabel. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis sammenkædede tjenesten er blevet oprettet, kan du se JSON for tjenesten sammenkædede i **resultater** Ellers kan se du en fejlmeddelelse.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Oprette Azure HDInsight sammenkædet service
I dette trin kan sammenkæde du en efter behov HDInsight klynge til dine data factory. HDInsight klynge oprettes på kørselstidspunktet og slettet, når det er gjort kataloger og inaktiv for det angivne tidsrum automatisk. Du kan bruge din egen HDInsight klynge i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [Beregne sammenkædede tjenester](data-factory-compute-linked-services.md) .  

1. Tildele kommandoen til navngivne **cmd**-variabel.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.

        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis sammenkædede tjenesten er blevet oprettet, kan du se JSON for tjenesten sammenkædede i **resultater** Ellers kan se du en fejlmeddelelse.  

        Write-Host $results

## <a name="create-datasets"></a>Oprette datasæt
I dette trin skal oprette du datasæt for at repræsentere input og eksportere data til Hive behandling. Disse datasæt referere til **StorageLinkedService** , du har oprettet tidligere i dette selvstudium. Den sammenkædede service for peger på en Azure-lager konto og datasæt angive objektbeholder, mappe, filnavn i opbevaring, der indeholder input og output-data.   

### <a name="create-input-dataset"></a>Oprette input datasæt
I dette trin skal oprette du input datasættet for at repræsentere indtastede data, der er gemt i Azure Blob-lager.

1. Tildele kommandoen til navngivne **cmd**-variabel. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.

        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis datasættet er blevet oprettet, kan du se JSON for datasættet i **resultater** Ellers kan se du en fejlmeddelelse.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Oprette output datasæt
I dette trin skal oprette du output datasæt for at repræsentere outputdata, der er gemt i Azure Blob-lager.

1. Tildele kommandoen til navngivne **cmd**-variabel.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.

        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis datasættet er blevet oprettet, kan du se JSON for datasættet i **resultater** Ellers kan se du en fejlmeddelelse.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Oprette pipeline
I dette trin skal oprette du din første pipeline med en **HDInsightHive** aktivitet. Indtast udsnit findes månedligt (frekvens: måned, interval: 1), output udsnit produceret månedligt og egenskaben scheduler for aktiviteten også er indstillet til månedlig. Indstillingerne for output datasættet og aktivitet planlæggeren skal svare til. Output datasæt er i øjeblikket, hvad drev tidsplanen, så du skal oprette et datasæt output, selvom aktiviteten ikke medfører noget output. Hvis aktiviteten ikke tager alt input, kan du springe over oprettelse af input datasættet.  

Bekræft, at du kan se **input.log** fil i mappen **adfgetstarted/inputdata** i Azure blob-lager og køre følgende kommando for at installere rørledningen. Da ** **start** - og** sluttidspunkter angives tidligere og **isPaused** er indstillet til falsk, kører pipeline (aktivitet i pipeline) umiddelbart efter at du installerer. 

1. Tildele kommandoen til navngivne **cmd**-variabel.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Kør kommandoen ved hjælp af **Aktiver-kommando**.

        $results = Invoke-Command -scriptblock $cmd;
3. Få vist resultaterne. Hvis datasættet er blevet oprettet, kan du se JSON for datasættet i **resultater** Ellers kan se du en fejlmeddelelse.  

        Write-Host $results
5. Tillykke, du har oprettet din første pipeline ved hjælp af PowerShell Azure!

## <a name="monitor-pipeline"></a>Overvåge pipeline
I dette trin skal bruge du Data Factory REST-API til at overvåge udsnit, oprettet af pipeline.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Oprettelse af en efter behov HDInsight klynge tager normalt et tidspunkt (cirka 20 minutter). Derfor Forvent pipeline til at tage **cirka 30 minutter** at behandle udsnittet.  

Kør kommandoen Aktiver-Command og den næste, indtil du ser på udsnittet i **klar** stater eller **fejltilstand** . Når udsnittet er i klar tilstand, skal du kontrollere mappen **partitioneddata** i objektbeholderen **adfgetstarted** i din blob storage for datatypen output.  Oprettelse af en efter behov HDInsight klynge tager normalt noget tid.

![outputdata](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] Indtast filen bliver slettet, når udsnittet er behandlet. Derfor, hvis du vil køre udsnittet eller gøre selvstudiet igen, overføre input filen (input.log) til mappen inputdata for objektbeholderen adfgetstarted.

Du kan også bruge Azure portal til at overvåge udsnit og foretage fejlfinding af eventuelle problemer. Se [overvåge rørledninger Azure portalen](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) oplysninger.  

## <a name="summary"></a>Oversigt 
I dette selvstudium, du har oprettet en Azure data factory til proces data ved at køre Hive script på en HDInsight hadoop klynge. Du har brugt Data Factory-Editor i portalen Azure til at gøre følgende:  

1.  Oprettet en Azure **data factory**.
2.  Oprettet to **sammenkædede tjenester**:
    1.  **Azure-lager** sammenkædet service sammenkæde din Azure blob-lager, der indeholder input/output filer til fabriksindstillingerne data.
    2.  **Azure HDInsight** efter behov sammenkædede service en efter behov HDInsight Hadoop klynge sammenkædes data factory. Azure Data Factory opretter en HDInsight Hadoop klynge just-in-time for at behandle inputdataene og frembringe outputdata. 
3.  Oprettet to **datasæt**, der beskriver input- og data for HDInsight Hive aktivitet i pipeline. 
4.  Oprettet en **rørledning** med en **HDInsight Hive** aktivitet. 

## <a name="next-steps"></a>Næste trin
Du har oprettet en rørledning med en transformation aktivitet (HDInsight aktivitet), der kører et Hive-script på en efter behov Azure HDInsight klynge i denne artikel. Hvis du vil se, hvordan du bruger en kopi aktivitet til at kopiere data fra en Azure Blob til Azure SQL, se [Selvstudium: kopiere data fra en Azure Blob til Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Se også
| Emne | Beskrivelse |
| :---- | :---- |
| [Reference til data Factory RESTEN API](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Se dokumentationen til omfattende om Data Factory-cmdletter |
| [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) | Denne artikel indeholder en liste over data transformation aktiviteter (såsom HDInsight Hive transformation, du har brugt i dette selvstudium) understøttes af Azure Data Factory. |
| [Planlægning og udførelse af](data-factory-scheduling-and-execution.md) | Denne artikel forklares planlægnings- og udførelse af aspekter af Azure Data Factory programmet model. |
| [Rørledninger](data-factory-create-pipelines.md) | I denne artikel hjælper dig med at forstå rørledninger og aktiviteter i Azure Data Factory og hvordan du kan bruge dem til at oprette til slut datadrevne arbejdsprocesser for scenarie eller business. |
| [Datasæt](data-factory-create-datasets.md) | I denne artikel hjælper dig med at forstå datasæt i Azure Data Factory.
| [Overvåge og administrere rørledninger ved hjælp af Azure portalen blade](data-factory-monitor-manage-pipelines.md) | I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding af din rørledninger ved hjælp af Azure portalen blade. |
| [Overvåge og administrere rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) | I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding rørledninger med kommandoen overvågning og administration af App. 

