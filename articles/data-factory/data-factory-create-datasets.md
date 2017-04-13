<properties 
    pageTitle="Oprette datasæt i Azure Data Factory | Microsoft Azure" 
    description="Lær, hvordan du opretter datasæt i Azure Data Factory med eksempler, der bruger egenskaber som forskydning og anchorDateTime."
    keywords="oprette datasæt, datasæt eksempel, forskydning eksempel"
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
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Datasæt i Azure Data Factory
I denne artikel beskriver datasæt i Azure Data Factory og indeholder eksempler som forskydning, anchorDateTime og forskydning/typografi databaser.

Når du opretter et datasæt, opretter du en markør til de data, du vil behandle. Data er behandlet (input/output) i en aktivitet og en aktivitet er indeholdt i en pipeline. Et input datasæt repræsenterer input til en aktivitet i pipeline og et output datasæt repræsenterer output til aktiviteten.

Datasæt identificere data i forskellige butikker, som tabeller, filer, mapper og dokumenter. Når du opretter et datasæt, kan du bruge det med aktiviteter i en pipeline. Et datasæt kan for eksempel være et input/output datasæt af en kopi aktivitet eller en HDInsightHive aktivitet. Portalen Azure giver dig et visuelt layout af alle dine rørledninger og data input og output. Et hurtigt overblik se du alle relationer og afhængigheder af din rørledninger på tværs af alle dine kilder så du altid ved, hvor data kommer fra, og hvor den skal.

I Azure Data Factory, kan du hente data fra et datasæt ved hjælp af kopi aktivitet i en pipeline.

> [AZURE.NOTE] Hvis du er ny bruger af Azure Data Factory, skal du se [Introduktion til Azure Data Factory](data-factory-introduction.md) for en oversigt over Azure Data Factory-tjenesten. Se [bygge din første data](data-factory-build-your-first-pipeline.md) for et selvstudium til at oprette din første data factory. Disse to artikler giver du baggrund oplysninger, du skal bruge i denne artikel for bedre at forstå. 

## <a name="define-datasets"></a>Definere datasæt
Et datasæt i Azure Data Factory defineres således: 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

I følgende tabel beskrives egenskaberne i ovenstående JSON:   

| Egenskaben | Beskrivelse | Påkrævet | Standard |
| -------- | ----------- | -------- | ------- |
| Navn | Navnet på datasættet. Se [Azure Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler. | Ja | IKKE.TILGÆNGELIG |
| type | Type af datasættet. Angive en af de typer, der understøttes af Azure Data Factory (for eksempel: AzureBlob, AzureSqlTable). <br/><br/>Du kan finde oplysninger i [Dataset Type](#Type) . | Ja | IKKE.TILGÆNGELIG |
| struktur | Skema for datasættet<br/><br/>Yderligere oplysninger finder du [Datasættet struktur](#Structure) sektion. | Nej. | IKKE.TILGÆNGELIG |
| typeProperties | Egenskaber, der svarer til den valgte type. Se [Datasæt Type](#Type) afsnittet Yderligere oplysninger om understøttede datatyper og deres egenskaber. | Ja | IKKE.TILGÆNGELIG |
| eksterne | Boolesk flag for at angive, om et datasæt eksplicit produceret af en data factory rørledning, eller ej.  | Nej | FALSK | 
| tilgængelighed | Definerer vinduet behandling eller slicing modellen til fremstilling datasæt. <br/><br/>Yderligere oplysninger finder du [Datasæt tilgængelighed](#Availability) sektion. <br/><br/>Få at vide om datasættet udskæring modellen, se [Planlægning af og udførelse af](data-factory-scheduling-and-execution.md) artikel. | Ja | IKKE.TILGÆNGELIG
| politik | Definerer kriterierne, eller den betingelse, der skal opfylde datasæt udsnit. <br/><br/>Yderligere oplysninger finder du [Datasæt politik](#Policy) sektion. | Nej | IKKE.TILGÆNGELIG |

## <a name="dataset-example"></a>Eksempel på datasæt
I følgende eksempel repræsenterer datasættet en tabel med navnet **MinTabel** i en **Azure SQL-database**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Bemærk følgende punkter: 

- type er indstillet til AzureSqlTable.
- Tabelnavn Typeegenskaben (specifik for AzureSqlTable type) er indstillet til MinTabel.
- linkedServiceName refererer til en sammenkædet tjeneste af typen AzureSqlDatabase. Se definitionen af følgende sammenkædede tjeneste. 
- tilgængelighed frekvens er indstillet til dag og interval er angivet til 1, hvilket betyder, at udsnittet produceret dagligt.  

AzureSqlLinkedService defineres således:

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

I den ovenstående JSON: 

- type er indstillet til AzureSqlDatabase
- Egenskaben connectionString type angiver oplysninger til at oprette forbindelse til en Azure SQL-database.  


Som du kan se, definerer tjenesten sammenkædede hvordan du opretter forbindelse til en Azure SQL-database. Datasættet definerer, hvilken tabel der bruges som input/output til aktivitet i en pipeline. Afsnittet aktivitet i din [pipeline](data-factory-create-pipelines.md) JSON angiver, om datasættet bruges som et input- eller datasæt.


> [AZURE.IMPORTANT] Medmindre et datasæt genereres af Azure Data Factory, skal den markeres som **eksterne**. Denne indstilling gælder generelt for input med første aktivitet i en rørledning.   

## <a name="Type"></a>Datasæt Type
Understøttede datakilder og datasæt typer er justeret. Se emner, der refereres til i artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md#supported-data-stores) oplysninger om datatyper og konfiguration af datasæt. Eksempelvis hvis du bruger data fra en Azure SQL-database, skal du klikke på Azure SQL-Database på listen over understøttede data butikker at se detaljerede oplysninger.  

## <a name="Structure"></a>Datasættet struktur
Sektionen **struktur** definerer skemaet for datasættet. Den indeholder en samling feltnavne og datatyper for kolonner.  I eksemplet nedenfor datasættet består af tre kolonner slicetimestamp-projektnavn og pageviews og de er af typen: streng, streng og Decimal henholdsvis.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Tilgængeligheden af datasæt
Sektionen **tilgængelighed** i et datasæt definerer vinduet behandling (time, dagligt, ugentligt, osv.) eller slicing modellen for datasættet. Se [Planlægning af og udførelse af](data-factory-scheduling-and-execution.md) artikel for at få flere oplysninger om datasæt udsnit og afhængighed modellen. 

Afsnittet nedenfor tilgængelighed angiver, at output datasættet er enten produceret hver time (eller) input datasæt er tilgængelige hver time:

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

I følgende tabel beskrives egenskaber, der kan bruges i sektionen mulighed: 

| Egenskaben | Beskrivelse | Påkrævet | Standard |
| -------- | ----------- | -------- | ------- |
| frekvens | Angiver tidsenheden til datasæt udsnit fremstilling.<br/><br/>**Understøttede frekvens**: minut, time, dag, uge, måned | Ja | IKKE.TILGÆNGELIG |
| interval | Angiver en multiplikator for frekvens<br/><br/>"Frekvens x intervallet" bestemmer, hvor ofte udsnittet er oprettet.<br/><br/>Hvis du har brug for datasættet til at blive opdelt på grundlag af hver time, kan du angive **frekvens** **time**og **interval** til **1**.<br/><br/>**Note:** Hvis du angiver frekvens som minut, anbefaler vi, at du indstiller intervallet til ikke mindre end 15 | Ja | IKKE.TILGÆNGELIG |
| typografi | Angiver, om udsnittet skal være produceret på start/slut i intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Hvis hyppighed er konfigureret til måned og typografi er indstillet til EndOfInterval, er udsnittet produceret på den sidste dag i måneden. Hvis formatet, der er angivet til StartOfInterval, er udsnittet produceret på den første dag i måneden.<br/><br/>Hvis hyppighed er indstillet til dag og typografi er indstillet til EndOfInterval, dannes udsnittet i den sidste timen på dagen.<br/><br/>Hvis hyppighed er indstillet til time og typografi er indstillet til EndOfInterval, produceret udsnittet i slutningen af timen. For et udsnit for 1 PM – 2 PM periode, er udsnittet for eksempel produceret på 2 PM. | Nej | EndOfInterval |
| anchorDateTime | Definerer den absolutte position i tid, der bruges af scheduler til at beregne datasæt udsnitsgrænser. <br/><br/>**Note:** Hvis AnchorDateTime har dele af en dato, der er mere detaljeret end hyppigheden ignoreres de mere detaljeret dele. <br/><br/>Eksempelvis hvis **interval** er **hver time** (frekvens: time og interval: 1) og **AnchorDateTime** indeholder **minutter og sekunder**, og klik derefter på **minutter og sekunder** dele af AnchorDateTime ignoreres. | Nej | 01/01/0001 |
| forskydning | TimeSpan som starten og slutningen af alle datasæt udsnit flyttes. <br/><br/>**Note:** Hvis både anchorDateTime og forskydning er angivet, er resultatet kombinerede Skift. | Nej | IKKE.TILGÆNGELIG |

### <a name="offset-example"></a>Forskydning eksempel

Daglig udsnit, der starter på 6 AM i stedet for standard midnat. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

**Frekvens** er indstillet til **dag** og **interval** er angivet til **1** (en gang om dagen): Hvis du vil have udsnittet skal være produceret på 6 AM i stedet for på tidspunkt, standard: kl. Husk, at problemet er en UTC-klokkeslæt. 

## <a name="anchordatetime-example"></a>Eksempel på anchorDateTime

**Eksempel:** 23 timer datasæt udsnit, der starter på 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>Forskydning/typografi eksempel

Hvis du skal bruge datasæt på månedlig basis på bestemte dato og klokkeslæt (Antag på 3 i hver måned på 8:00 AM) Brug mærket **forskydning** til at angive datoen og tidspunktet for afspilning bør kunne køre. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Politik for datasættet

Sektionen **politik** i dataset definition definerer kriterierne, eller den betingelse, der skal opfylde datasæt udsnit.

### <a name="validation-policies"></a>Validering politikker

| Navn på politik | Beskrivelse | Anvendt på | Påkrævet | Standard |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Kontrollerer, at dataene i en **Azure blob** opfylder mindste størrelse (i megabyte). | Azure Blob | Nej | IKKE.TILGÆNGELIG |
|minimumRows | Kontrollerer, at dataene i en **Azure SQL-database** eller en **Azure table** indeholder det mindste antal rækker. | <ul><li>Azure SQL-Database</li><li>Azure Table</li></ul> | Nej | IKKE.TILGÆNGELIG

#### <a name="examples"></a>Eksempler

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Eksterne datasæt

Eksterne datasæt er dem, der ikke er oprettet af en igangværende pipeline i data factory. Hvis datasættet er markeret som **eksterne**, kan politikken **ExternalData** defineres for at påvirke funktionsmåden for datasættet udsnit er ledige. 

Medmindre et datasæt genereres af Azure Data Factory, skal den markeres som **eksterne**. Denne indstilling gælder generelt for input med første aktivitet i en rørledning, medmindre aktivitet eller pipeline sammenkædning bruges. 

| Navn | Beskrivelse | Påkrævet | Standardværdi  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tid til at forsinke Søg på tilgængeligheden af de eksterne data til det angivne udsnit. Hvis dataene skal være tilgængelig hver time, kan der eksempelvis forsinkes Kontrollér for at se de eksterne data er tilgængelige, og det tilsvarende udsnit er klar ved hjælp af dataDelay.<br/><br/>Gælder kun for øjeblikket.  Hvis det er 1:00 PM lige nu, og denne værdi er 10 minutter, starter validering for eksempel på 1:10 PM.<br/><br/>Denne indstilling påvirker ikke udsnit tidligere (udsnit med udsnit sluttidspunkt + dataDelay < nu) behandles uden forsinkelser.<br/><br/>Tid, der er større end 23:59 timer skal angivet ved hjælp af formatet day.hours:minutes:seconds. For eksempel for at angive 24 timer skal ikke bruge 24:00:00 i stedet bruge 1.00:00:00. Hvis du bruger 24:00:00, fortolkes det som 24 dage (24.00:00:00). Angiv 1:04:00:00 for 1 dag og 4 timer. | Nej | 0 |
| retryInterval | Ventetiden mellem en fejl, og næste gentagne forsøg. Gælder for øjeblikket; Hvis den forrige forsøger mislykkedes, vi vente dette længe efter den sidste prøve. <br/><br/>Hvis det er 1:00 pm lige nu, starter vi første forsøg. Hvis varigheden at fuldføre den første validering check er 1 minut og mislykkedes handlingen, er næste forsøg igen på 1:00 + 1 min (varighed) + 1min (interval for gentagelse) = 1:02 pm. <br/><br/>Udsnit i fortiden er der ingen forsinkelse. Vil afsendelsen sker med det samme. | Nej | 00:01:00 (1 minut) | 
| retryTimeout | Timeout for hvert forsøg på at.<br/><br/>Hvis denne egenskab er indstillet til 10 minutter, skal valideringen udføres i 10 minutter. Hvis det tager længere tid end 10 minutter til at udføre validering, timeout vil afsendelsen.<br/><br/>Hvis alle forsøg til validering får timeout, er udsnittet markeret som har fået timeout. | Nej | 00:10:00 (10 minutter) |
| maximumRetry | Antallet af gange at kontrollere, om tilgængeligheden af de eksterne data. Den tilladte maksimale værdi er 10. | Nej | 3 | 

## <a name="scoped-datasets"></a>Relateret datasæt
Du kan oprette datasæt, der er fastsat til en rørledning ved hjælp af egenskaben **datasæt** . Disse datasæt kan kun bruges af aktiviteter inden for denne pipeline, men ikke af aktiviteter i andre rørledninger. I følgende eksempel definerer en rørledning med to datasæt - InputDataset-forbindelse til Fjernskrivebord og OutputDataset-forbindelse til Fjernskrivebord - kan bruges i pipeline:  

> [AZURE.IMPORTANT] Relateret datasæt understøttes kun med enkeltstående rørledninger (**pipelineMode** indstillet til **OneTime**). Du kan finde oplysninger i [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) .

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }