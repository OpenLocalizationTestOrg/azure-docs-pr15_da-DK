<properties
    pageTitle="Kopiere aktivitet ydeevne og justering vejledning | Microsoft Azure"
    description="Få mere at vide om vigtige faktorer, der påvirker ydeevnen for flytning af data i Azure Data Factory, når du bruger kopi aktivitet."
    services="data-factory"
    documentationCenter=""
    authors="linda33wj"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="jingwang"/>


# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiere aktivitet ydeevne og justering vejledning
Azure Data Factory kopi aktivitet leverer en førsteklasses sikkert, pålideligt og høj ydeevne indlæsning af data løsning. Det gør det muligt at kopi mange af TB data hver dag på tværs af en bred vifte af skyen og lokale data butikker. Forrygende hurtig indlæsning ydeevnen af data er nede for at sikre, at du kan fokusere på core "big data" problemet: opbygning af løsninger avanceret analyse og få deep indsigt fra alle data.

Azure indeholder et sæt af professionel og data lager og data lagerløsning, og Kopiér aktivitet tilbyder en meget optimerede indlæsning oplevelse, som er nem at konfigurere og konfigurere af data. Med blot en enkelt kopi aktivitet, kan du opnå:

- Indlæse data i **Azure SQL Data Warehouse** på **1,2 GBps**
- Indlæse data i **1,0 GBps** **Azure Blob-lager**
- Indlæse data i **Azure sø datalager** på **1,0 GBps**


I denne artikel beskrives:

- [Ydeevnen referencenumre](#performance-reference) for understøttede datakilder og sink data gemmer kan hjælpe dig med at planlægge dit projekt
- Funktioner, der kan gavne kopi overførselshastighed i forskellige scenarier, herunder [parallelle kopi](#parallel-copy), [skyen data bevægelse enheder](#cloud-data-movement-units)og [midlertidigt kopi](#staged-copy);
- [Ydeevnen justering vejledning](#performance-tuning-steps) i at finjustere ydeevnen og de vigtigste faktorer, der kan påvirke ydeevnen kopi.

> [AZURE.NOTE] Hvis du ikke kender med kopi aktiviteter Generelt, se [flytte data ved hjælp af kopi aktivitet](data-factory-data-movement-activities.md) inden du læse denne artikel.

## <a name="performance-reference"></a>Ydeevnen reference

![Ydeevnen matrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] Du kan opnå højere overførselshastighed ved at udnytte flere data bevægelse enheder (DMUs) end standard maksimalt DMUs, som er 8 for en kopi til skyen i skyen aktivitet køre. For eksempel med 100 DMUs, kan du kopiere data fra Azure Blob til Azure sø datalager til en sats på 1 GB sekundet. I afsnittet [skyen data bevægelse enheder](#cloud-data-movement-units) for at få oplysninger om denne funktion. Kontakt [support Azure](https://azure.microsoft.com/support/) til at anmode om flere DMUs.

Peger på Bemærk:

- Overførselshastighed beregnes ved hjælp af følgende formel: [datastørrelse læse fra kilde] / [kopi aktivitet køre varighed].
- Referencenumre ydeevne i tabellen blev målt ved hjælp af [TPC-H](http://www.tpc.org/tpch/) datasæt i en enkelt kopi aktivitet køre.
- For at kopiere mellem skyen data butikker, skal du angive **cloudDataMovementUnits** 1 og 4 (eller 8) for sammenligning. **parallelCopies** er ikke angivet. I afsnittet [parallelle kopi](#parallel-copy) få mere at vide om disse funktioner.
- I Azure data butikker er kilde- og sink i samme Azure område.
- Til hybrid (lokale til skyen eller skyen til en lokal installation) flytning af data, en enkelt forekomst af gatewayen kørte på en computer, der er adskilt fra det lokale datalager. Konfigurationen er angivet i den næste tabel. Når en enkelt aktivitet kørte på gateway, consumed kopieringen kun en lille del af test computerens CPU, hukommelse eller netværksbåndbredde.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 processorkerner der bruges 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Hukommelse</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Netværk</td>
        <td>Internet interface: 10 Gbps; intranet interface: 40 Gbps</td>
    </tr>
    </table>

## <a name="parallel-copy"></a>Parallelle kopi
Du kan læse data fra kilden eller skrive data til destinationen **i en kopi aktivitet køre parallelt**. Denne funktion forbedrer overførselshastigheden for kopiering og reducerer den tid, det tager for at flytte data.

Denne indstilling er forskellig fra egenskaben **på dokumentsammenfald** i definitionen af aktivitet. Egenskaben **på dokumentsammenfald** angiver antallet af **samtidige kopi aktivitet kører** til at behandle data fra forskellige aktivitet windows (AM 1 til 2 AM, 2 AM til 3 AM, 3 AM til 4 AM og osv.). Denne funktion er nyttigt, når du udfører en historiske belastning. Muligheden for parallel kopi gælder for en **enkelt aktivitet køre**.

Lad os se på et eksempel på et scenario. Flere udsnit fra tidligere skal behandles i eksemplet nedenfor. Data Factory kører en forekomst af kopi aktivitet (en aktivitet Kør) for hvert udsnit:

- Dataudsnittet fra den første aktivitet vindue (AM 1 til 2 AM) == > aktivitet køre 1
- Dataudsnittet fra det andet aktivitet vindue (2 AM til 3 AM) == > aktivitet køre 2
- Dataudsnittet fra det andet aktivitet vindue (3 AM til 4 AM) == > aktivitet køre 3

Og så videre.

I dette eksempel, når værdien **på dokumentsammenfald** er angivet til 2, kopiere **aktivitet køre 1** og **aktivitet køre 2** data fra to aktivitet windows **samtidig** at forbedre ydeevnen for bevægelse af data. Men hvis flere filer er knyttet til aktivitet køre 1, bevægelse datatjeneste kopierer filerne fra kilden til en destinationsfilen ad gangen.

### <a name="parallelcopies"></a>parallelCopies
Du kan bruge egenskaben **parallelCopies** til at angive den parallelitet, du vil kopiere aktivitet, der skal bruge. Du kan betragte denne egenskab som det maksimale antal tråde i kopi aktivitet, der kan læse fra din kilde eller skrive til dine sink data butikker parallelt.

For hver kopi aktivitet, køre, bestemmer Data Factory antallet af parallelle kopier, der skal bruge til at kopiere data fra kilden data gemme og gemme til destination dataene. Standardantallet af parallelle kopier, der bruges, afhænger af typen kilde- og sink, du bruger.  

Kilde- og sink |   Antal standard parallelle Kopier bestemmes af tjenesten
------------- | -------------------------------------------------
Kopiere data mellem fil-baserede lagre (Blob storage; Sø datalager; Amazon S3; en lokal filsystemet. en lokal HDFS) | Mellem 1 og 32. Afhænger af størrelsen på filerne og antallet af skyen data bevægelse enheder (DMUs) bruges til at flytte data mellem to skyen data butikker eller den fysiske konfiguration af gatewaycomputeren bruges til en hybrid kopi (for at kopiere data til eller fra et lokalt datalager).
Kopiér data fra **en hvilken som helst kildedata gemme til Azure-tabellager** | 4
Alle andre kilde- og sink par | 1

Standardfunktionsmåden bør normalt, give dig den bedste overførsel. Men for at styre belastningen på computere, der hoster dine data gemmes, eller for at finjustere ydeevnen i kopi, du kan vælge at tilsidesætte standardværdien og angive en værdi for egenskaben **parallelCopies** . Værdien skal være mellem 1 og 32 (begge inklusive). På kørselstidspunktet bruger til den bedste ydeevne kopi aktivitet en værdi, der er mindre end eller lig med værdien, som du fastsætter.

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "parallelCopies": 8
            }
        }
    ]

Peger på Bemærk:

- Når du kopierer data mellem fil-baserede lagre, sker parallelitet på filniveau. Der er ingen samle inden for en enkelt fil. Det faktiske antal parallelle Kopier bevægelse datatjeneste bruger til kopieringen på kørselstidspunktet er ikke mere end antallet filer, du har. Hvis kopi funktionsmåden er **mergeFile**, kan ikke kopiere aktivitet drage fordel af parallelitet.
- Når du angiver en værdi for egenskaben **parallelCopies** , skal du overveje Indlæs stigningen på dine kilde- og sink data butikker og til gatewayen, hvis det er en hybrid kopi. Dette sker især når du har flere aktiviteter eller samtidige kører af de samme aktiviteter, der kører i forhold til den samme datalager. Hvis du opdager, at den datalager eller Gateway er overvældet af afkrydsningsfeltet Indlæs, formindske **parallelCopies** værdien for at lette arbejdet afkrydsningsfeltet Indlæs.
- Når du kopierer data fra butikker, der ikke er baseret på filer til butikker, der er baseret på filer, ignorerer bevægelse datatjeneste egenskaben **parallelCopies** . Selvom parallelitet er angivet, anvendes den ikke i dette tilfælde.

> [AZURE.NOTE] Du skal bruge Data Management Gateway version 1.11 eller nyere til at bruge funktionen **parallelCopies** , når du gør en hybrid kopi.

### <a name="cloud-data-movement-units"></a>Skyen data bevægelse enheder
En **skyen data bevægelse enhed (DMU)** er en måling, der repræsenterer en potens (en kombination af CPU, hukommelse og netværk ressourceallokering) på en enkelt enhed i Data Factory. En DMU kan bruges i skyen i skyen kopiering, men ikke i en hybrid kopi.

Som standard bruger Data Factory en enkelt sky DMU til at udføre en enkelt kopi aktivitet køre. Hvis du vil tilsidesætte denne standard, skal du angive en værdi for egenskaben **cloudDataMovementUnits** på følgende måde. Du kan finde oplysninger om niveauet af ydeevnen, du kan få, når du konfigurerer flere enheder til en enkelt kopi kilde- og sink, [ydeevne reference](#performance-reference).

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "cloudDataMovementUnits": 4
            }
        }
    ]

Den **tilladte værdier** for egenskaben **cloudDataMovementUnits** er 1 (standard), 2, 4 og 8. Det **faktiske antal skyen DMUs** , der bruger kopieringen på kørselstidspunktet er lig med eller mindre end værdien af konfigurerede, afhængigt af dine datamønster. 

> [AZURE.NOTE] Hvis du har brug for mere skyhybridinstallation DMUs til et højere overførselshastighed, skal du kontakte [support Azure](https://azure.microsoft.com/support/). Indstilling af 8 og over aktuelt fungerer kun, når du kopierer flere filer fra Blob-lager til Blob-lager, sø datalager eller Azure SQL-Database og filstørrelsen er større end eller lig med 16 MB enkeltvis.

Bedre bruge disse to egenskaber, og til at forbedre din bevægelse dataoverførsel, se [eksempel use cases](#case-study-use-parallel-copy). Du behøver ikke at konfigurere **parallelCopies** for at kunne udnytte standardfunktionsmåden. Hvis du konfigurerer og **parallelCopies** er for lille, er flere skyen DMUs muligvis ikke fuldt ud.  

Det er **vigtigt** at huske, at du betaler baseret på den samlede tid af kopieringen. Hvis en Kopier sag bruges til at tage en time med én sky enhed, og nu det tager 15 minutter med fire skyen enheder, forbliver den overordnede faktura næsten på samme måde. For eksempel kan du bruge fire skyen enheder. Den første enhed i skyen tilbringer 10 minutter, den anden opgave 10 minutter, den tredje, fem minutter, og den fjerde, fem minutter, køres alle i én kopi aktivitet. Du betaler for det tidspunkt, samlet antal copy (data bevægelse), som er 10 + 10 + 5 + 5 = 30 minutter. Brug af **parallelCopies** påvirker ikke fakturering.

## <a name="staged-copy"></a>Faseinddelt kopi
Når du kopierer data fra en kilde datalager til et sink datalager, kan du vælge at bruge Blob-lager som en midlertidig midlertidige store. Midlertidige er især nyttig i følgende tilfælde:

1.  **Du vil indtager data fra forskellige data butikker i SQL Data Warehouse via PolyBase**. SQL Data Warehouse bruger PolyBase som en metode til høj overførselshastighed indlæse en stor mængde data til SQL Data Warehouse. Dog kildedataene skal være i Blob-lager og den skal opfylder flere kriterier. Når du indlæser data fra et datalager end Blob-lager, kan du aktivere data kopiere via midlertidig midlertidige Blob-lager. I så fald udfører Data Factory de påkrævede data transformeringer for at sikre, at den opfylder kravene i PolyBase. Derefter bruges PolyBase for at indlæse data til SQL Data Warehouse. Du kan finde flere detaljer, [Brug PolyBase indlæse data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
2.  **Nogle gange det tager lang tid at udføre en hybrid data bevægelse (det vil sige, for at kopiere mellem en lokale data store og et skyen data store) via en langsom netværksforbindelse**. For at forbedre ydeevnen, kan du komprimere de data i det lokale miljø, så det tager mindre tid til at flytte data i det midlertidige datalager i skyen. Du kan derefter dekomprimere dataene i den midlertidige store, før du indlæse den i data destinationslageret.
3.  **Du ikke kan åbne porte end port 80 og port 443 i din firewall, på grund af IT firmapolitikker**. For eksempel, når du kopierer data fra et lokalt datalager til en sink Azure SQL-Database eller en Azure SQL Data Warehouse sink, skal du aktivere udgående TCP-kommunikation på port 1433 til både Windows firewall og din virksomhedens firewall. I dette scenarie skal du drage fordel af gatewayen til den første kopier data i en midlertidig forekomst Blob-lager over HTTP eller HTTPS port 443. Indlæse dataene i SQL-Database eller SQL Data Warehouse fra Blob storage midlertidige. I dette flow behøver du ikke at aktivere port 1433.

### <a name="how-staged-copy-works"></a>Hvordan faseinddelt Kopiér værker
Når du aktiverer funktionen midlertidige, først dataene er kopieret fra datalager kilde til arrangering datalager (tage dine egne). Dernæst skal kopieres dataene fra den midlertidige datalager til datalager sink. Data Factory styrer automatisk to faser strømmen for dig. Data Factory rydder også op midlertidige data fra den midlertidige lagerplads efter flytning af data er fuldført.

Gatewayen bruges ikke i skyen kopi scenarie (både kilde- og sink data butikker er i skyen). Tjenesten Data Factory udfører handlingerne kopi.

![Midlertidigt kopi: skyen scenarie](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I scenariet hybrid kopi (kilde er i det lokale miljø og sink er i skyen), gatewayen flytter data fra datalager kilde til et midlertidige datalager. Factory datatjeneste flytter data fra den midlertidige datalager til sink datalager. Kopierer data fra en skybaseret datalager til et lokalt datalager via midlertidige også understøttes med omvendt strømmen.

![Midlertidigt kopi: Hybrid scenarie](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Når du aktiverer flytning af data ved hjælp af en midlertidig store, kan du angive, om du vil dataene, der skal komprimeres før du flytter data fra datalager kilde til en midlertidig eller midlertidige datalager, og derefter dekomprimeret, før du flytter data fra en foreløbig eller arrangere datalager til sink datalager.

Du kan ikke i øjeblikket, kopiere data mellem to lokale data butikker ved hjælp af en midlertidig butik. Vi forventer denne indstilling for at snart være tilgængelige.

### <a name="configuration"></a>Konfiguration
Konfigurere indstillingen **enableStaging** i kopi aktivitet til at angive, om du vil dataene, der gemmes i Blob-lager, før du indlæse den i en destination datalager. Når du indstiller **enableStaging** til sand, kan du angive yderligere egenskaber er angivet i den næste tabel. Hvis du ikke har en, skal du også oprette en Azure-lager eller lagerplads delt access signatur sammenkædet tjeneste til midlertidige.

Egenskaben | Beskrivelse | Standardværdi | Påkrævet
--------- | ----------- | ------------ | --------
**enableStaging** | Angiv, om du vil kopiere data via en midlertidige arrangere store. | FALSK | Nej
**linkedServiceName** | Angiv navnet på en [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) sammenkædede tjenesten, som refererer til forekomsten af lagerplads, som du kan bruge som en midlertidig midlertidige butik. <br/><br/> Du kan ikke bruge lagerplads med en delt adgang signatur indlæse data til SQL Data Warehouse via PolyBase. Du kan bruge det i alle andre scenarier. | I/T. | Ja, når **enableStaging** er indstillet til sand
**sti** | Angiv den Blob storage sti, som der skal indeholde faseinddelt dataene. Hvis du ikke angiver en sti, opretter tjenesten en objektbeholder for at gemme midlertidige data. <br/><br/> Angive en sti, kun, hvis du bruger Storage med en delt adgang signatur, eller du har brug for midlertidige data skal være i en bestemt placering. | I/T. | Nej
**enableCompression** | Angiver, om dataene skal komprimeres, før den kopieres til destinationen. Denne indstilling reducerer mængden af data, der overføres. | FALSK | Nej

Her er et eksempel definition af kopi aktivitet med de egenskaber, der er beskrevet i ovenstående tabel:

    "activities":[  
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [{ "name": "OnpremisesSQLServerInput" }],
        "outputs": [{ "name": "AzureSQLDBOutput" }],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob",
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
    ]


### <a name="billing-impact"></a>Fakturering virkning
Du betaler på basis af to trin: kopiere varighed og kopiere type. 

- Når du bruger betaler arrangere under en skyen kopi (kopierer data fra en skybaseret datalager til en anden skybaseret datalager), du [summen af kopi varighed for trin 1 og 2] x [skyen kopi Enhedspris].
- Når du bruger arrangere under en hybrid kopi (kopierer data fra et lokalt datalager til en skybaseret datalager), du betaler for [hybrid kopi varighed] x [hybrid kopi Enhedspris] + [skyen kopi varighed] x [skyen kopi Enhedspris].


## <a name="performance-tuning-steps"></a>Ydeevnen justering trin
Vi anbefaler, at du følger disse trin til at finjustere ydeevnen for din Data Factory-tjeneste med kopi aktiviteter:

1.  **Opret en oprindelig plan**. Teste din pipeline ved hjælp af kopi aktivitet mod en stikprøve repræsentant data i udviklingsfasen. Du kan bruge Data Factory [udskæring model](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) til at begrænse mængden data, du arbejder med.

    Indsamle kørselstid og ydeevne egenskaber ved hjælp af **overvågnings- og Management App**. Vælg **skærm og administrer** på startsiden Data Factory. Vælg **output datasæt**i træstrukturen. Vælg den kopi aktivitet, der kører på listen **Aktivitet Windows** . **Aktivitet Windows** vises kopi Aktivitetens varighed og størrelsen på de data, der er kopieret. Overførselshastigheden er angivet i **Aktivitet vinduet Stifinder**. Du kan få mere at vide om appen, [skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning og administration af App](data-factory-monitor-manage-app.md).

    ![Aktivitet køre detaljer](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

    Senere i denne artikel kan du sammenligne den ydeevne og konfiguration af scenariet til kopi aktivitet [ydeevne reference](#performance-reference) fra vores test.
2. **Diagnosticer og optimere ydeevnen**. Hvis den ydeevne, du ser ikke opfylder dine forventninger, skal du identificere flaskehalse for performance. Derefter optimere ydeevnen for at fjerne eller reducere effekten af flaskehalse. En fuldstændig beskrivelse af ydeevnen diagnosticering er ikke medtaget i denne artikel, men her er nogle almindelige overvejelser:
    - Funktioner:
        - [Parallelle kopi](#parallel-copy)
        - [Skyen data bevægelse enheder](#cloud-data-movement-units)
        - [Faseinddelt kopi](#staged-copy)   
    - [Kilde](#considerations-for-the-source)
    - [Synkronisere](#considerations-for-the-sink)
    - [Serialisering og deserialisering](#considerations-for-serialization-and-deserialization)
    - [Komprimering](#considerations-for-compression)
    - [Kolonnetilknytning](#considerations-for-column-mapping)
    - [Datastyringsgateway](#considerations-for-data-management-gateway)
    - [Andre overvejelser](#other-considerations)

3. **Udvid konfigurationen til hele datasættet**. Når du er tilfreds med udførelse af resultaterne og ydeevnen, kan du udvide for at definere og pipeline aktive periode dækker hele datasættet.

## <a name="considerations-for-the-source"></a>Overvejelser i forbindelse med kilden
### <a name="general"></a>Generelt
Sørg for, at den underliggende datalager ikke er overvældet af andre arbejdsbelastninger, der kører på eller imod den. 

Se [overvågning og justering af emner](#performance-reference) , der er specifikke for data butikker og Hjælp du forstår data gemme ydeevne egenskaber, minimere svar gange og maksimere overførselshastighed for Microsoft data butikker.

Hvis du kopierer data fra Blob-lager til SQL Data Warehouse, kan du overveje at bruge **PolyBase** for at øge ydeevnen. Du kan finde oplysninger i [Brug PolyBase indlæse data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) .


### <a name="file-based-data-stores"></a>Fil-baserede data butikker
*(Inkluderer Blob-lager, sø datalager, Amazon S3, lokale filer systemer og lokale HDFS)*

- **Gennemsnitlig filstørrelse og antal filer**: Kopiér aktiviteten overfører data én fil ad gangen. Med den samme mængde data, der skal flyttes, er overordnede overførselshastigheden lavere, hvis data, der består af mange små filer i stedet for et par store filer på grund af bootstrap fasen for hver fil. Derfor, hvis det er muligt kombinere små filer i større filer til at få højere overførselshastighed.
- **Filformat og komprimering**: flere måder at forbedre ydeevnen, du i afsnittene [overvejelser i forbindelse med serialisering og deserialisering](#considerations-for-serialization-and-deserialization) og [overvejelser i forbindelse med komprimering](#considerations-for-compression) .
- Til det **lokale filer** scenario, hvor **Datastyringsgateway** er påkrævet, skal du se afsnittet [overvejelser i forbindelse med Datastyringsgateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relationelle data butikker
*(Inkluderer SQL-Database SQL datawarehouse; Amazon Redshift; SQL Server-databaser og Oracle, MySQL, DB2, Teradata, Sybase og PostgreSQL databaser osv.)*

- **Datamønster**: din tabelskema påvirker kopi overførselshastighed. En stor rækkestørrelse giver dig en bedre ydeevne end small rækkestørrelse til at kopiere den samme mængde data. Det skyldes, at databasen mere effektivt kan hente færre batches med data, der indeholder færre rækker.
- **Forespørgsel eller en lagret procedure**: optimere logik om den forespørgsel eller lagrede procedure, du angiver i kopi aktivitet kilden til at hente data mere effektivt.
- For en **lokal relationsdatabaser**, såsom SQL Server og Oracle, som kræver brug af **Datastyringsgateway**, finder du i afsnittet [overvejelser i forbindelse med Datastyringsgateway](#considerations-on-data-management-gateway) .

## <a name="considerations-for-the-sink"></a>Overvejelser i forbindelse med sink

### <a name="general"></a>Generelt
Sørg for, at den underliggende datalager ikke er overvældet af andre arbejdsbelastninger, der kører på eller imod den. 

For Microsoft data butikker henvise til [overvågning og justering af emner](#performance-reference) , der er specifikke for data butikker. Følgende emner kan hjælpe dig med at forstå data store ydeevne egenskaber, og hvordan du minimere svar gange og maksimere overførselshastighed.

Hvis du kopierer data fra **Blob-lager** til **SQL Data Warehouse**, kan du overveje at bruge **PolyBase** for at øge ydeevnen. Du kan finde oplysninger i [Brug PolyBase indlæse data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) .


### <a name="file-based-data-stores"></a>Fil-baserede data butikker
*(Inkluderer Blob-lager, sø datalager, Amazon S3, lokale filer systemer og lokale HDFS)*

- **Kopiér funktionsmåde**: Hvis du kopierer data fra en anden fil-baserede datalager, Kopiér aktivitet har tre indstillinger via egenskaben **copyBehavior** . Den bevarer hierarki, samkopieres hierarki eller flettes filer. Enten bevarelse eller udjævne hierarki har begrænset eller ingen ydeevne omkostninger, men fletning af filer får ydeevne omkostninger at øge.
- **Filformat og komprimering**: Se afsnittene [overvejelser i forbindelse med serialisering og deserialisering](#considerations-for-serialization-and-deserialization) og [overvejelser i forbindelse med komprimering](#considerations-for-compression) for flere måder at forbedre ydeevnen.
- **BLOB-lager**: i øjeblikket Blob storage understøtter kun blokere BLOB for optimerede dataoverførsel og overførselshastighed.
- **Lokal filer systemer** scenarier, der kræver brug af **Datastyringsgateway**, i afsnittet [overvejelser i forbindelse med Datastyringsgateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relationelle data butikker
*(Inkluderer SQL-Database, SQL Data Warehouse, SQL Server-databaser og Oracle-databaser)*

- **Kopiere funktionsmåde**: afhængigt af de egenskaber, du har angivet for **sqlSink**, Kopiér aktivitet skriver data til destinationsdatabasen på forskellige måder.
    - Som standard føjer data bevægelse tjenesten optager flere kopi API til at indsætte data i tilstand, hvilket giver den bedste ydeevne.
    - Hvis du konfigurerer en lagret procedure i sink, gælder databasen datarækken én ad gangen i stedet for som en flere belastning. Ydeevnen falder væsentligt. Hvis datasættet er stort, når det er relevant, kan du overveje at skifte til ved hjælp af egenskaben **sqlWriterCleanupScript** .
    - Hvis du konfigurerer egenskaben **sqlWriterCleanupScript** for hver kopi aktivitet, køre, tjenesten udløser scriptet, og derefter du bruge flere kopier API til at indsætte dataene. Hvis du vil overskrive hele tabellen med de nyeste data, kan du angive et script for at først slette alle poster før indsætning de nye data fra kilden.
- **Datastørrelse mønster og batchen**:
    - Din tabelskema påvirker kopi overførselshastighed. Hvis du vil kopiere den samme mængde data, giver en stor rækkestørrelse dig bedre ydeevne end en lille rækkestørrelse fordi databasen mere effektivt kan bekræfte færre batches med data.
    - Kopiér aktivitet indsætter data i en række batches. Du kan angive antallet rækker i en gruppe ved hjælp af egenskaben **writeBatchSize** . Hvis dine data har små rækker, kan du angive egenskaben **writeBatchSize** med en højere værdi for at få glæde af nederste batchen omkostninger og højere overførselshastighed. Hvis række størrelsen på dine data er stor, være forsigtig, når du øger **writeBatchSize**. En høj værdi kan føre til en kopi fejl skyldes overbelastning databasen.
- **Lokale relationsdatabaser** som SQL Server og Oracle, som kræver brug af **Datastyringsgateway**, i afsnittet [overvejelser i forbindelse med Datastyringsgateway](#considerations-for-data-management-gateway) .


### <a name="nosql-stores"></a>NoSQL butikker
*(Inkluderer Table storage og Azure DocumentDB)*

- For **Table storage**:
    - **Partition**: skrive data til sekvensopdelt partitioner dramatisk forringer ydeevnen. Sortere kildedataene efter Partitionsnøgle, så data, der er indsat effektivt i én partition efter den anden, eller Juster logik for at skrive dataene til en enkelt partition.
- For **DocumentDB**:
    - **Batchstørrelse**: egenskaben **writeBatchSize** angiver antallet af parallelle anmodninger til DocumentDB service for at oprette dokumenter. Du kan forvente bedre ydeevne, når du øger **writeBatchSize** , fordi flere parallelle forespørgsler sendes til DocumentDB. Men pas (throttling), når du skriver til DocumentDB (fejlmeddelelsen er "Anmode om rente er stort"). Forskellige faktorer kan forårsage (throttling), herunder dokumentstørrelse, antallet af betingelserne i dokumenterne, og den destinationswebsteder indeksering politik. For at opnå højere kopi overførselshastighed skal du overveje at bruge en bedre samling, for eksempel S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Overvejelser i forbindelse med serialisering og deserialisering
Serialisering og deserialisering kan opstå, når din Skriv datasæt eller output datasæt er en fil. Kopiér aktivitet understøtter i øjeblikket, Avro og tekst (for eksempel csv- og TSV) dataformater.

**Kopiere funktionsmåde**:

-   Kopiere filer mellem fil-baserede datalagre:
    - Når input- og outputområder datasæt begge har den samme eller ingen indstillinger for format, bevægelse datatjeneste udfører en binær kopi uden nogen serialisering eller deserialisering. Du ser en højere overførselshastighed sammenlignet med dette scenario, hvor kilde- og sink fil format-indstillingerne er forskellige fra hinanden.
    - Når input og output datasæt begge er i tekstformat, og kun den kode, er forskellige, bevægelse datatjeneste fungerer kun kodning konvertering. Den ikke gøre en hvilken som helst serialisering og deserialisering, som medfører, at nogle ydeevne indirekte sammenlignet med en binær kopi.
    - Når input- og outputområder datasæt begge har forskellige filformater eller forskellige konfigurationer, som afgrænsere, bevægelse datatjeneste deserializes kildedataene for at streame, transformere og sekventielt det til det outputformat, du har angivet. Denne handling resulterer i en meget vigtigere indirekte sammenlignet med andre scenarier.
- Når du kopierer filer til/fra et datalager, ikke der er baseret på filer (for eksempel fra en fil-baserede store til en relationel store), er serialisering eller deserialisering trinnet er påkrævet. Dette trin giver betydeligt ydeevne omkostninger.

**Filformat**: det filformat, du vælger kan påvirke ydeevnen kopi. Avro er for eksempel en kompakt binære filformat, som lagrer metadata med data. Den har omfattende understøttelse i Hadoop økosystemet for at behandle og forespørgsler. Avro er dog mere omfattende for serialisering og deserialisering, hvilket resulterer i nederste kopi overførselshastighed sammenlignet med tekstformat. Gøre ønskede filformat i hele behandling strømmen holistically. Start med hvad formulardata er gemt i kilde data butikker eller der skal udtrækkes fra eksterne systemer det bedste format til lagerplads, analytical processing og forespørgsler; og i hvilket format skal eksporteres data til data marts for rapportering og visualisering værktøjer. Nogle gange et filformat, der er ikke-optimal for læse og skrive ydeevnen kan være et godt valg, når du overvejer overordnede analytical processen.

## <a name="considerations-for-compression"></a>Overvejelser i forbindelse med komprimering
Når din input- eller datasættet er en fil, kan du angive kopi aktivitet til at udføre komprimering eller dekomprimering, som den skriver data til destinationen. Når du vælger komprimering, du foretager en fordeling input/output (I/O) og CPU. Komprimere de ekstra i Beregn ressourcer data omkostninger. Men som svar det reducerer netværk I/O og lager. Afhængigt af dine data, kan du se en boost i overordnede kopi overførselshastighed.

**Codec**: Kopiér aktivitet understøtter gzip, bzip2 og Deflate komprimeringstyper. Azure HDInsight kan bruge alle tre typer til behandling. Hver komprimering codec er nogle fordele ved. For eksempel bzip2 har den laveste kopi overførselshastighed, men du får den bedste ydeevne for Hive forespørgsel med bzip2, fordi kan du opdele den til behandling. Gzip er den mest ikke-opgjorte indstilling, og det bruges mest ofte. Vælg den codec, der passer bedst scenariet til slut.

**Niveau**: Du kan vælge mellem to muligheder for hver komprimering codec: hurtigst komprimeret og optimalt komprimeret. Den hurtigste komprimerede indstilling komprimerer dataene så hurtigt som muligt, selvom den resulterende fil ikke er komprimeret optimalt. Indstillingen optimalt komprimeret tilbringer mere tid på komprimering og giver en minimale mængde data. Du kan teste begge for at se, hvilket giver bedre overordnede ydeevne i din sag.

**A overvejelser**: Hvis du vil kopiere en stor mængde data mellem et lokalt lager og i skyen, skal du overveje at bruge midlertidig blob-lager med komprimering. Ved hjælp af midlertidig lagerplads er nyttigt, når båndbredden for virksomhedens netværk og Azure tjenester er den begrænse faktor, og du vil input datasættet og output datasæt begge i dekomprimerede form. Mere specifikt kan du opdele en enkelt kopi aktivitet i to kopi aktiviteter. Den første kopi aktivitet kopierer fra kilden til en midlertidig eller midlertidige blob i komprimeret form. Den anden kopi aktivitet kopierer den komprimerede data fra midlertidige, og derefter dekomprimerer mens den skriver til sink.

## <a name="considerations-for-column-mapping"></a>Overvejelser i forbindelse med kolonnetilknytning
Du kan angive egenskaben **columnMappings** i kopi aktivitet til alle kort eller et undersæt af kolonnerne input til outputkolonner. Når bevægelse datatjeneste læser data fra kilden, skal den udføre kolonnetilknytning på dataene, før den skriver dataene til sink. Denne ekstra behandling reducerer kopi overførselshastighed.

Hvis kildedataene gemmer er forespørgselsmulige, f.eks, hvis det er en relationel store som SQL-Database eller SQL Server, eller hvis det er en NoSQL store som tabellagring eller DocumentDB, overveje at trykke på filtrering af kolonne og ændring af rækkefølgen af logik til egenskaben **forespørgsel** i stedet for ved hjælp af kolonnetilknytning. Denne måde prognosen opstår, mens bevægelse datatjeneste læser data fra datalager kilde, hvor det er meget mere effektiv.

## <a name="considerations-for-data-management-gateway"></a>Overvejelser i forbindelse med Data Management Gateway
Du kan finde Gateway konfiguration anbefalinger [Overvejelser ved brug af Datastyringsgateway](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Gatewayen maskine miljø**: Vi anbefaler, at du bruger en dedikeret maskine vært Datastyringsgateway. Brug funktioner som PerfMon undersøge CPU, hukommelse og båndbredde brug under kopiering på computeren Gateway. Skifte til en mere effektiv maskine, hvis CPU, hukommelse eller netværksbåndbredde bliver en flaskehals.

**Samtidige kopi aktivitet kører**: en enkelt forekomst af Datastyringsgateway kan udskrive flere kopier aktivitet kører ad gangen eller samtidigt. Det maksimale antal samtidige job er beregnes ud fra Gateway-computerens hardwarekonfiguration. Ekstra kopi job er i kø, indtil de valgte ved Gateway, eller indtil timeout ved et andet job. For at undgå ressourcekonflikter på gatewaycomputeren, kan du fase tidsplanen kopi aktivitet at reducere antallet af kopier job i køen ad gangen, eller Overvej at opdele belastning på flere Gateway-computere.


## <a name="other-considerations"></a>Andre overvejelser
Hvis størrelsen af data, du vil kopiere er stor, kan du tilpasse din forretningslogik til yderligere partition data med den slicing ordning i Data Factory. Planlæg derefter Kopiér aktivitet til at køre ofte for at reducere størrelsen på dataene for hver kopi aktivitet køre.

Vær forsigtig med antallet af datasæt og Kopiér aktiviteter, der kræver Data Factory til forbindelse i det samme datalager på samme tid. Mange samtidige kopi job kan begrænse et datalager og føre til forringet ydelse, Kopiér job interne forsøg, og i nogle tilfælde fejl under udførelse af.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Eksempel på et scenario: Kopiér fra en lokal SQL Server til Blob-lager
**Scenarie**: en rørledning er udviklet til at kopiere data fra en lokal SQL Server til Blob-lager i CSV-format. Hvis du vil gøre Kopier sag hurtigere, skal CSV-filer komprimeres til bzip2 format.

**Test og analyse**: gennemløb af kopi aktivitet er mindre end 2 MBps, hvilket er meget langsommere end ydeevne benchmark.

**Analyse af ydeevnen og justering af**: for at foretage fejlfinding af problemet med ydeevnen, Lad os se på hvordan dataene behandles og flyttes.

1.  **Yderligere oplysninger**: Gateway åbner en forbindelse til SQL Server og sender forespørgslen. SQL Server reagerer ved at sende datastrømmen til Gateway via intranettet.
2.  **Serialiseret og Komprimer data**: Gateway serializes datastrømmen til en CSV-fil og komprimerer dataene til en bzip2 stream.
3.  **Skrive data**: gatewayen uploader bzip2 strømmen til Blob-lager via internettet.

Som du kan se, dataene er blevet behandlet og flyttet på en måde, streaming sekventielle: SQL Server > LAN > Gateway > WAN > Blob storage. **Overordnede ydeevne gated ved mindste overførselshastigheden på tværs af pipeline**.

![Dataflow](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

En eller flere af følgende faktorer kan medføre ydeevne flaskehals:

-   **Kilde**: SQL Server selve har lav overførselshastighed på grund af stor belastning.
-   **Datastyringsgateway**:
    -   **LAN**: Gateway er placeret langt fra SQL Server-computer, og jeg har en forbindelse med lav båndbredde.
    -   **Gatewayen**: Gateway er nået frem til dens Indlæs begrænsninger for at udføre følgende handlinger:
        -   **Serialisering**: serialisering datastrømmen til en CSV-fil er langsom overførselshastighed.
        -   **Komprimering**: du har valgt en langsom komprimering codec (for eksempel bzip2, som er 2,8 MBps med Core i7).
    -   **WAN**: båndbredde mellem virksomhedens netværk og Azure tjenester er lav (for eksempel T1 = 1,544 kbps. T2 = 6,312 kbps).
-   **Synkronisere**: Blob-lager har lav overførselshastighed. (Dette scenario er sandsynligvis ikke, fordi dens SLA garanterer mindst 60 MBps).

I dette tilfælde kan bzip2 datakomprimering langsommere hele pipeline. Skifte til et gzip komprimering codec kan lette denne flaskehals.


## <a name="sample-scenarios-use-parallel-copy"></a>Eksempel på scenarier: bruge parallelle kopi  

**Scenarie I:** Kopiere 1.000 1 MB filer fra filsystemet i det lokale miljø til Blob-lager.

**Analyse og justering af ydeevnen**: et eksempel, hvis du har installeret gateway på en firdobbelt core maskine Data Factory bruger 16 parallelle Kopier til at flytte filer fra filsystemet til Blob-lager samtidigt. Denne parallel udførelse skal resultere i høj overførselshastighed. Du kan også eksplicit angive parallelle Kopier antallet. Når du kopierer mange små filer, hjælpe parallelle Kopier dramatisk overførselshastighed ved hjælp af ressourcer mere effektivt.

![Scenarie 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenarie II**: kopiere 20 BLOB 500 MB fra Blob-lager til Data sø Store Analytics, og derefter finjustere ydeevnen.

**Analyse og justering af ydeevnen**: I dette scenarie skal Data Factory kopierer data fra Blob-lager til sø datalager ved hjælp af single-kopi (**parallelCopies** angivet til 1) og enkelt skyen data bevægelse enheder. Overførselshastigheden du ser, tæt, der beskrives i den [ydeevne reference sektion](#performance-reference).   

![Scenarie 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenarie III**: individuelle filstørrelsen er større end massevis af MB og samlede lydstyrken er stort.

**Analyse og ydeevne aktivering**: øget **parallelCopies** ikke angives i bedre kopi ydeevne på grund af en enkelt skyen DMU ressource begrænsninger. I stedet skal du angive flere skyen DMUs at få yderligere ressourcer til at udføre flytning af data. Ikke Angiv en værdi for egenskaben **parallelCopies** . Data Factory håndterer parallelitet for dig. I dette tilfælde, hvis du angiver **cloudDataMovementUnits** til 4, forekommer en overførsel af om fire gange.

![Scenarie 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Reference
Her er ydeevnen overvågning og tilpasning af referencer til nogle af de understøttede data butikker:

- Azure-lager (herunder Blob-lager og Table storage): [Azure-lager skalerbarhed mål](../storage/storage-scalability-targets.md) og [Azure-lager ydeevne og skalerbarhed tjekliste](../storage//storage-performance-checklist.md)
- Azure SQL-Database: Du kan [overvåge ydeevnen](../sql-database/sql-database-service-tiers.md#monitoring-performance) og kontrollere databasen transaktion enhed (DTU) procentdel
- Azure SQL datawarehouse: Sin kompetence måles i data warehouse enheder (DWUs) Se [administrere beregne power i Azure SQL Data Warehouse (oversigt)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
- Azure DocumentDB: [ydeevneniveauer i DocumentDB](../documentdb/documentdb-performance-levels.md)
- Lokal SQL Server: [skærm og finjustere ydeevne](https://msdn.microsoft.com/library/ms189081.aspx)
- Lokale filer server: [justering af ydeevnen for filservere](https://msdn.microsoft.com/library/dn567661.aspx)
