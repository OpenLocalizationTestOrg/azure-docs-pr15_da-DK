<properties 
    pageTitle="Oprette/tidsplan rørledninger, sammenkæde aktiviteter i Data Factory | Microsoft Azure" 
    description="Lær at oprette en data rørledning i Azure Data Factory til at flytte og transformere data. Oprette en datadrevne arbejdsproces til at producere klar til at bruge oplysninger." 
    keywords="data pipeline, datadrevne arbejdsproces"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="pipelines-and-activities-in-azure-data-factory"></a>Rørledninger og aktiviteter i Azure Data Factory
I denne artikel hjælper dig med at forstå rørledninger og aktiviteter i Azure Data Factory og bruge dem til at oprette til slut datadrevne arbejdsprocesser til flytning af data og databehandling scenarier.  

> [AZURE.NOTE] Denne artikel forudsætter, at du har gennemgået [Introduktion til Azure Data Factory](data-factory-introduction.md). Hvis du ikke har praktiske-On-Screen-oplevelse med oprettelse af data fabrikker, ved at følge [bygge din første data](data-factory-build-your-first-pipeline.md) selvstudium vil bidrage til at forstå artiklen bedre.  

## <a name="what-is-a-data-pipeline"></a>Hvad er en data rørledning?
**Pipeline** er en gruppe af logisk relaterede **aktiviteter**. Det bruges til at gruppeaktiviteter i en enhed, som udfører en opgave. Rørledninger for bedre for at forstå, du har brug at forstå en aktivitet først. 

## <a name="what-is-an-activity"></a>Hvad er en aktivitet?
Aktiviteter definere handlinger til at udføre på dine data. Hver aktivitet tager nul eller flere [datasæt](data-factory-create-datasets.md) som input og giver en eller flere datasæt som output. 

For eksempel kan du bruge en kopi aktivitet til dirigerer kopiere data fra én datalager til en anden datalager. På samme måde, kan du bruge en HDInsight Hive aktivitet til at køre en Hive forespørgsel på en Azure HDInsight klynge til at transformere dataene. Azure Data Factory indeholder et bredt udvalg af [datatransformation](data-factory-data-transformation-activities.md)og [data bevægelse](data-factory-data-movement-activities.md) aktiviteter. Du kan også vælge at oprette en brugerdefineret .NET aktivitet for at køre din egen programkode. 

## <a name="sample-copy-pipeline"></a>Eksempel kopi pipeline
I følgende eksempel pipeline er der en aktivitet af typen **kopi** i sektionen **aktiviteter** . I dette eksempel kopierer [kopiere aktivitet](data-factory-data-movement-activities.md) data fra en Azure Blob-lager til Azure SQL-database. 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 

Bemærk følgende punkter:

- I sektionen aktiviteter er der kun en aktivitet, hvis **type** er indstillet til **kopi**.
- Input for aktiviteten er indstillet til **InputDataset** og afgang for aktiviteten er indstillet til **OutputDataset**.
- I sektionen **typeProperties** **BlobSource** er angivet som kildetypen og **SqlSink** er angivet som typen sink.

Finde en komplet gennemgang af oprettelse af denne pipeline [Selvstudium: kopiere data fra Blob-lager til SQL-Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Eksempel transformation pipeline
I følgende eksempel pipeline er der en aktivitet af typen **HDInsightHive** i sektionen **aktiviteter** . I dette eksempel transformerer [HDInsight Hive aktivitet](data-factory-hive-activity.md) data fra en Azure Blob-lager ved at køre en Hive script-fil på en Azure HDInsight Hadoop-klynge. 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
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
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

Bemærk følgende punkter: 

- I sektionen aktiviteter er der kun en aktivitet, hvis **type** er indstillet til **HDInsightHive**.
- Hive script-fil **partitionweblogs.hql**, er gemt i kontoen Azure-lager (angivet af scriptLinkedService, kaldet **AzureStorageLinkedService**) og **script** mappe i beholderen **adfgetstarted**.
- Sektionen **definerer** bruges til at angive runtime-indstillinger, der sendes til hive scriptet som Hive konfigurationsværdier (f.eks. ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

Finde en komplet gennemgang af oprettelse af denne pipeline [Selvstudium: oprette din første pipeline til proces data ved hjælp af Hadoop klynge](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Sammenkædning af aktiviteter
Hvis du har flere aktiviteter i en pipeline og output fra en aktivitet ikke er en indlæsning af en anden aktivitet, vil aktiviteterne kan køre parallelt, hvis inputdataene udsnit for aktiviteterne, der er klar. 

Du kan sammenkæde to aktiviteter ved at få output datasættet én aktivitet som input datasættet af anden aktiviteten. Aktiviteterne, der kan være i den samme rørledning eller i forskellige rørledninger. Den anden aktivitet udfører kun når den første opgave er fuldført. 

For eksempel kan du overveje følgende tilfælde:
 
1.  Pipeline P1 har aktivitet A1, der kræver eksterne input datasæt D1 og frembringe **output** datasæt **D2**.
2.  Pipeline P2 har aktivitet A2, der kræver **input** fra datasæt **D2**, og giver output datasæt D3.
 
I dette scenarie skal aktiviteten A1 køres, når de eksterne data er tilgængelig, og den planlagte tilgængelighed hyppighed nås.  Aktiviteten A2 køres, når de planlagte udsnit fra D2 bliver tilgængelige, og den planlagte tilgængelighed hyppighed nås. Hvis der er fejl i et af udsnittene i dataset D2, kører A2 ikke for udsnittet, indtil det bliver tilgængelig.

Diagramvisning:

![Sammenkædning af aktiviteter i to rørledninger](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Diagramvisning med begge aktiviteter i den samme rørledning: 

![Sammenkædning af aktiviteter i den samme rørledning](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Se [planlægnings- og udførelse af](#chaining-activities)kan finde flere oplysninger. 

## <a name="scheduling-and-execution"></a>Planlægning og udførelse af
Indtil nu har du forstået, hvad rørledninger og aktiviteter er. Du har også set hvordan de er defineret og en overordnet oversigt over aktiviteterne i Azure Data Factory. Nu Lad os se på, hvordan de får udføres. 

En rørledning er aktiv kun mellem dens starttidspunkt og sluttidspunkt. Den udføres ikke før starttidspunktet eller efter sluttidspunktet. Hvis rørledningen er stoppet midlertidigt, der det ikke udføres uanset dens klokkeslæt for start og slut. For en pipeline til at køre skal den ikke afbrydes midlertidigt. Det er faktisk ikke rørledning, der bliver udført. Det er de aktiviteter i pipeline, der udføres. Men de gør det ind i helheden af rørledninger. 

Se [Planlægning af og udførelse af](data-factory-scheduling-and-execution.md) at forstå, hvordan planlægnings- og udførelse af fungerer i Azure Data Factory.

## <a name="create-pipelines"></a>Oprette rørledninger
Azure Data Factory indeholder forskellige funktioner til at redigere og installere rørledninger (som også indeholder en eller flere aktiviteter i det). 

### <a name="using-azure-portal"></a>Portalen til Azure
Du kan bruge Data Factory editor i portalen Azure til at oprette en rørledning. Se [Introduktion til Azure Data Factory (Data Factory Editor)](data-factory-build-your-first-pipeline-using-editor.md) til et slutpunkt til gennemgang. 

### <a name="using-visual-studio"></a>Brug af Visual Studio 
Du kan bruge Visual Studio til at oprette og anvende rørledninger til Azure Data Factory. Se [Introduktion til Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) til et slutpunkt til gennemgang. 

### <a name="using-azure-powershell"></a>Brug af Azure PowerShell
Du kan bruge Azure PowerShell til at oprette rørledninger i Azure Data Factory. Sig, har du defineret pipeline JSON i en fil på c:\DPWikisample.json. Du kan overføre den til din Azure Data Factory-forekomst, som vist i følgende eksempel:

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Se [Introduktion til Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) til en til slut gennemgang til at oprette en data fabrik med en rørledning. 

### <a name="using-net-sdk"></a>Brug af .NET SDK
Du kan oprette og installere pipeline via .NET SDK for. Denne funktion kan bruges til at oprette rørledninger fra et program. Få mere at vide under [oprette, administrere, og overvåge data fabrikker fra et program](data-factory-create-data-factories-programmatically.md). 


### <a name="using-azure-resource-manager-template"></a>Ved hjælp af Azure ressourcestyring skabelon
Du kan oprette og installere pipeline ved hjælp af en skabelon til Azure ressourcestyring. Yderligere oplysninger finder du se [Introduktion til Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Brug af REST-API
Du kan oprette og installere pipeline ved hjælp af REST API'er for. Denne funktion kan bruges til at oprette rørledninger fra et program. Få mere at vide under [oprette eller opdatere en rørledning](https://msdn.microsoft.com/library/azure/dn906741.aspx). 


## <a name="monitor-and-manage-pipelines"></a>Overvåge og administrere rørledninger  
Når en rørledning er installeret, kan du administrere og overvåge rørledninger, udsnit og kører. Læs mere om den her: [skærm og administrere rørledninger](data-factory-monitor-manage-pipelines.md).


## <a name="pipeline-json"></a>Pipeline JSON   
Lad os tage et nærmere Kig på hvordan en rørledning er defineret i JSON-format. Generisk strukturen for en rørledning ser ud som følger:

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [
    
            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

Afsnittet **aktiviteter** kan have en eller flere aktiviteter, der er defineret i modellen. Hver aktivitet har følgende på øverste niveau struktur:

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {
    
        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

Følgende tabel beskrives egenskaberne i aktivitet og pipeline JSON definitioner:

Mærke | Beskrivelse | Påkrævet
--- | ----------- | --------
Navn | Navnet på aktiviteten eller pipeline. Angiv et navn, der repræsenterer handlingen, aktivitet eller pipeline er konfigureret til at gøre<br/><ul><li>Maksimale antal tegn: 260</li><li>Skal starte med et bogstav tal eller en understregningstegnet (_)</li><li>Følgende tegn er ikke tilladt: ".", "+", "?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> | Ja
Beskrivelse | En beskrivelse af, hvad den aktivitet eller pipeline skal bruges til | Ja
type | Angiver typen af aktiviteten. Se artiklerne [Data bevægelse](data-factory-data-movement-activities.md) og [Aktiviteter, Transformation som Data](data-factory-data-transformation-activities.md) for forskellige typer aktiviteter. | Ja
input | Indtast tabeller, der bruges af aktiviteten<br/><br/>tabel for et input<br/>"input": [{"navn": "inputtable1"}],<br/><br/>to input tabeller <br/>"input": [{"navn": "inputtable1"}, {"navn": "inputtable2"}], | Ja
output | Output tabeller, der bruges af tabellen activity.// én output<br/>"skriver": [{"navn": "outputtable1"}],<br/><br/>to output tabeller<br/>"skriver": [{"navn": "outputtable1"}, {"navn": "outputtable2"}], | Ja
linkedServiceName | Navnet på den sammenkædede tjeneste, der bruges af aktiviteten. <br/><br/>En aktivitet kan kræve, at du angiver den sammenkædede tjeneste, der linker til det nødvendige Beregn-miljø. | Ja til HDInsight aktivitet og Azure Machine Learning batchen vundne aktivitet <br/><br/>Nej for alle andre
typeProperties | Egenskaber i sektionen typeProperties afhænger af typen af aktiviteten. | Nej
politik | Politikker, der påvirker Runtime-funktionsmåden for aktiviteten. Hvis det ikke er angivet, bruges standardpolitikker. | Nej
Start | Start /-tidspunkt for pipelinen. Skal være i [ISO-formatet](http://en.wikipedia.org/wiki/ISO_8601). For eksempel: 2014-10-14T16:32:41Z. <br/><br/>Det er muligt at angive en lokal tid, for eksempel en EST tid. Her er et eksempel: "2016-02-27T06:00:00**-05: 00**", som er 6 AM EST.<br/><br/>Egenskaber for start og slut Angiv sammen aktive periode for pipelinen. Output udsnit er kun produceret med i denne aktive periode. | Nej<br/><br/>Hvis du angiver en værdi for egenskaben slutningen, skal du angive værdi for egenskaben start.<br/><br/>Start- og sluttidspunkter kan begge være tomt for at oprette en pipeline. Du skal angive begge værdier for at angive en aktive periode for pipeline til at køre. Hvis du ikke angiver start- og sluttidspunkter når du opretter en rørledning, kan du angive dem ved hjælp af cmdlet'en Set-AzureRmDataFactoryPipelineActivePeriod til senere.
Afslut | Afslutte dato og klokkeslæt for pipelinen. Hvis angivet, skal være i ISO-formatet. For eksempel: 2014-10-14T17:32:41Z <br/><br/>Det er muligt at angive en lokal tid, for eksempel en EST tid. Her er et eksempel: "2016-02-27T06:00:00**-05: 00**", som er 6 AM EST.<br/><br/>For at køre rørledningen på ubestemt tid, skal du angive 9999-09-09 som værdien for egenskaben slutningen. | Nej <br/><br/>Hvis du angiver en værdi for egenskaben start, skal du angive værdi for egenskaben slutningen.<br/><br/>Se noter for egenskaben **start** .
isPaused | Hvis indstillet til sand pipeline ikke kører. Standardværdi = falsk. Du kan bruge denne egenskab til at aktivere eller deaktivere. | Nej 
Opgavestyring | "scheduler" egenskaben bruges til at definere ønskede planlægning for aktiviteten. Dennes underegenskaber er den samme som dem i [tilgængelighed egenskab i et datasæt](data-factory-create-datasets.md#Availability). | Nej |   
| pipelineMode | Metoden til planlægning af kører for pipelinen. Tilladte værdier er: planlagt (standard), enkelt.<br/><br/>'Planlagt' angiver, at rørledningen kører på et angivet tidsinterval i overensstemmelse med den aktive periode (klokkeslæt for start og slut). 'Enkelt' angiver, at rørledningen køres kun én gang. Enkelt rørledninger når oprettet må ikke være ændret/opdateret i øjeblikket. Du kan finde oplysninger om, hvordan du enkelt i [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) . | Nej | 
| expirationTime | Varigheden af tiden efter oprettelse, pipeline er gyldig og skal forblive klargjort. Hvis den ikke har et aktivt, mislykkedes, eller afventer kører, slettes rørledningen automatisk en gang når udløbsdatoen. | Nej | 
| datasæt | Liste over datasæt, der skal bruges i aktiviteter, der er defineret i pipeline. Denne egenskab kan bruges til at definere datasæt, der er specifikke for denne pipeline og ikke er defineret i data factory. Datasæt, der er defineret i denne pipeline kan kun bruges i denne rørledninger og kan ikke deles. Du kan finde oplysninger i [fastsat datasæt](data-factory-create-datasets.md#scoped-datasets) .| Nej |  
 

### <a name="policies"></a>Politikker
Politikker påvirker Runtime-funktionsmåden for en aktivitet, specifikt, når udsnittet af en tabel behandles. Den følgende tabel indeholder oplysninger.

Egenskaben | Tilladte værdier | Standardværdi | Beskrivelse
-------- | ----------- | -------------- | ---------------
på dokumentsammenfald | Heltal <br/><br/>Maks værdi: 10 | 1 | Antallet af samtidige udførelser aktivitetens.<br/><br/>Den bestemmer antallet af parallelle aktivitet udførelser, der kan udføres på forskellige udsnit. Eksempelvis hvis en aktivitet skal gennemgå hurtigere en stor mængde tilgængelige data, har du en større på dokumentsammenfald værdi databehandlingen. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Bestemmer rækkefølgen af udsnittene i data, der behandles.<br/><br/>Eksempelvis hvis du har 2 udsnit (én sker kl og en anden kl), og begge er afventer udførelse af. Hvis du indstiller executionPriorityOrder skal være NewestFirst, behandles udsnittet kl først. På samme måde hvis du indstiller executionPriorityORder skal være OldestFIrst, behandles derefter udsnittet kl. 
Prøv igen | Heltal<br/><br/>Maks værdien kan være 10 | 3 | Antallet af gentagelser før databehandlingen for udsnittet er markeret som fejlen. Udførelse af aktivitet for en data udsnit er forsøgt igen op til antal angivne forsøg. Vil afsendelsen sker så tidligt som muligt efter fejlen.
timeout | TimeSpan | 00:00:00 | Timeout for aktiviteten. Eksempel: 00:10:00 (implicit timeout 10 minutter)<br/><br/>Hvis en værdi ikke er angivet eller er 0, er timeouten uendelig.<br/><br/>Hvis databehandling tid på et udsnit overskrider timeoutværdien, den annulleres, og systemet forsøger at prøve igen behandling. Antallet af forsøg, afhænger af egenskaben prøv igen. Når der opstår timeout, er status angivet til har fået timeout.
forsinkelse | TimeSpan | 00:00:00 | Angive en forsinkelse før data behandling af udsnit starter.<br/><br/>Udførelse af aktivitet for et udsnit af data er startet, når forsinkelsen har overskredet den forventede kørselstid.<br/><br/>Eksempel: 00:10:00 (implicit forsinkelse af 10 minutter)
longRetry | Heltal<br/><br/>Maks værdi: 10 | 1 | Antallet af lang forsøg forsøg før udførelse udsnit er mislykkedes.<br/><br/>longRetry forsøg er fordelte ved longRetryInterval. Hvis du vil angive et klokkeslæt mellem forsøg forsøg skal bruge så longRetry. Hvis både gentagne forsøg og longRetry er angivet, hver longRetry forsøg indeholder forsøg forsøg, og det maksimale antal forsøg er forsøg * longRetry.<br/><br/>For eksempel, hvis vi har følgende indstillinger i politikken aktivitet:<br/>Prøv igen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Antag, at der er kun ét udsnit til at udføre (status venter) og aktivitet udførelsen mislykkes, hver gang. Først skal der være 3 udførelse af gentagne forsøg. Efter hvert forsøg på vil udsnit status være prøv igen. Når først 3 forsøg overskrider, der udsnit status ville være LongRetry.<br/><br/>Efter en time (det vil sige, Longretryinteval's værdi), vil der være en anden række 3 udførelse af gentagne forsøg. Udsnit status ville være mislykkedes bagefter og vil blive forsøgt ikke gøres flere forsøg. Derfor er overordnede 6 forsøg foretaget.<br/><br/>Hvis en hvilken som helst udførelse af lykkes, udsnit status ville være klar og ikke gøres flere forsøg forsøges.<br/><br/>longRetry kan bruges i situationer, hvor afhængige data ankommer til ikke-deterministisk gange eller miljøet generelt er flaky under hvilke databehandling opstår. Nye forsøg efter hinanden ikke til at gøre, og at gøre dette efter et interval af tid i så fald resultater i det ønskede resultat.<br/><br/>Lille advarsel: Angiv ikke høje værdier for longRetry eller longRetryInterval. Høje værdier, indikerer andre systematiske problemer. 
longRetryInterval | TimeSpan | 00:00:00 | Forsinkelsen mellem lange forsøg forsøg 


## <a name="next-steps"></a>Næste trin

- Forstå [planlægnings- og udførelse af i Azure Data Factory](data-factory-scheduling-and-execution.md).  
- Læs mere om [flytning af data](data-factory-data-movement-activities.md) og [data transformation funktioner](data-factory-data-transformation-activities.md) i Azure Data Factory
- Forstå [Administration og overvågning i Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Opbygge og anvende din hånden pipeline](data-factory-build-your-first-pipeline.md). 
