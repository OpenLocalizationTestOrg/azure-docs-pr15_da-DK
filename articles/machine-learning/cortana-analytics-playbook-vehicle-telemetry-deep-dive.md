<properties 
    pageTitle="Biltype telemetri analytics løsning playbook: undersøgelse løsningen | Microsoft Azure" 
    description="Bruge funktionerne i Cortana Intelligence til at få realtid og skønnet viden på biltype tilstand og bil vaner." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Biltype telemetri analytics løsning playbook: undersøgelse løsningen

I denne **menu** links i dele af denne playbook: 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

I denne sektion drills ned i hver af de faser, der er vist i løsningsarkitektur med instruktioner og markører for tilpasning. 

## <a name="data-sources"></a>Datakilder

Løsningen bruger to forskellige datakilder:

- **simuleret biltype signaler og diagnosticering datasæt** og 
- **biltype katalog**

Biltype telematik simulator er inkluderet som en del af denne løsning. Det udsender diagnostiske oplysninger og signaler svarer til tilstanden for biltype og fører mønstret på et bestemt tidspunkt. Klik på [Biltype telematik Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) for at hente **Biltype telematik Simulator Visual Studio-løsning** for tilpasninger, der er baseret på dine krav. Biltype kataloget indeholder en reference datasæt med en VIN til model-tilknytning.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figur 2 – biltype telematik Simulator*

Dette er et JSON-formateret datasæt, der indeholder følgende skema.

Kolonne | Beskrivelse | Værdier 
 ------- | ----------- | --------- 
VIN | Tilfældigt biltype id-nummer | Dette er hentet fra en overordnet liste over 10.000 tilfældigt biltype id-numre.
Eksterne temperaturen | Eksterne temperaturen, hvor der har indflydelse på biltype | Tilfældigt tal mellem 0-100
Program temperaturen | Program temperaturen af biltype | Tilfældigt tal mellem 0-500
Hastighed | Program hastigheden, hvor der har indflydelse på biltype | Tilfældigt tal mellem 0-100
Brændstof | Brændstof niveauet for biltype | Tilfældigt tal mellem 0-100 (angiver brændstof niveau procent)
EngineOil | Program oil niveauet for biltype | Tilfældigt tal mellem 0-100 (angiver program oil niveau procent)
Dæk tryk | Dæk Tryk på biltype | Tilfældige tal fra 0-50 (angiver dæk tryk niveau procent)
Kilometerstand | Kilometerstand læsning af biltype | Tilfældigt tal mellem 0-200000
Accelerator_pedal_position | Genvejstast vha placeringen af biltype | Tilfældigt tal mellem 0-100 (angiver genvejstast niveau procent)
Parking_brake_status | Angiver, om biltype parkeret eller ej | SAND eller FALSK
Headlamp_status | Angiver, hvor forlygten er på eller ej | SAND eller FALSK
Brake_pedal_status | Angiver, om der trykkes pedalen påkrævet eller ej | SAND eller FALSK
Transmission_gear_position | Overførslen tandhjul placeringen af biltype | Tilstande: første, anden, tredje, fjerde, femte, sjette, syvende, ottende
Ignition_status | Angiver, om biltype er kører eller er stoppet | SAND eller FALSK
Windshield_wiper_status | Angiver, om at windshield viskerarmen er slået til eller ej | SAND eller FALSK
ABS | Angiver, om ABS er involveret | SAND eller FALSK
Tidsstempel | Tidsstemplet for, hvornår datapunktet oprettes | Dato
By | Placeringen af biltype | 4 byer i denne løsning: Storeby, Redmond, Sammamish, Seattle


Biltype model reference datasæt indeholder VIN til model-tilknytning. 

VIN | Model |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Mangler |
8J0U8XCPRGW4Z3NQE | Hybrid |
WORG68Z2PLTNZDBI7 | Family Saloon |
JTHMYHQTEPP4WBMRN | Mangler |
W9FTHG27LZN1YWO0Y | Hybrid |
MHTP9N792PHK08WJM | Family Saloon |
EI4QXI2AXVQQING4I | Mangler |
5KKR2VB4WHQH97PF8 | Hybrid |
W9NSZ423XZHAONYXB | Family Saloon |
26WJSGHX4MA5ROHNL | Konvertible |
GHLUB6ONKMOSI7E77 | Station samt |
9C2RHVRVLMEJDBXLP | Lille bil |
BRNHVMZOUJ6EOCP32 | Lille SUV |
VCYVW0WUZNBTM594J | Sports bil |
HNVCE6YFZSA5M82NY | Mellemlangt SUV |
4R30FOR7NUOBL05GJ | Station samt |
WYNIIY42VKV6OQS1J | Store SUV |
8Y5QKG27QET1RBK7I | Store SUV |
DF6OX2WSRA6511BVG | Coupé |
Z2EOZWZBXAEW3E60T | Mangler |
M4TV6IEALD5QDS3IR | Hybrid |
VHRA1Y2TGTA84F00H | Family Saloon |
R0JAUHT1L1R3BIKI0 | Mangler |
9230C202Z60XX84AU | Hybrid |
T8DNDN5UDCWL7M72H | Family Saloon |
4WPYRUZII5YV7YA42 | Mangler |
D1ZVY26UV2BFGHZNO | Hybrid |
XUF99EW9OIQOMV7Q7 | Family Saloon
8OMCL3LGI7XNCC21U | Konvertible |
…….  |   |


### <a name="to-generate-simulated-data"></a>Til at oprette simuleret data
1.  For at hente data simulator pakke, skal du klikke på pilen øverst til højre for noden biltype telematik Simulator. Gem og udtrække filer lokalt på din computer. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png)*Figur 3 – biltype Telemetri Analytics løsning grundrids*

2.  Gå til den mappe, hvor du udpakkede pakken biltype telematik Simulator på din lokale computer. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Figur 4 – biltype telematik Simulator mappe*

3.  Køre programmet **CarEventGenerator.exe**.

### <a name="references"></a>Referencer

[Biltype telematik Simulator Visual Studio-løsning](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Azure begivenhed Hub](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## <a name="ingestion"></a>Indtagelse
Kombinationer af Azure begivenhed hubber, Stream analyser og Data Factory er opgraderede den for at indtager biltype signaler, diagnosticering begivenheder og notetagning i realtid og batch analyser. Alle disse komponenter er oprettet og konfigureret som en del af løsning installationen. 

### <a name="real-time-analysis"></a>Realtid analyse
De hændelser, der genereres af biltype telematik Simulator publiceres til begivenhed hubben ved hjælp af begivenhed Hub SDK. Stream Analytics jobbet ingests disse begivenheder fra begivenhed Hub og behandler dataene i realtid for at analysere biltype tilstand. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png) 

*Figur 5 - begivenhed Hub dashboard*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figur 6 - Stream analytics job behandling af data*

Stream analytics-job.

- ingests data fra den begivenhed Hub 
- udfører en sammenkædning med referencedata tilknyttes biltype VIN tilsvarende modellen 
- fortsætter dem til Azure blob-lager for omfattende batchen analyser. 

Følgende stream analytics forespørgsel bruges til at bevare dataene til Azure blob-lager. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figur 7 - Stream analytics job forespørgsel til data indtagelse*

### <a name="batch-analysis"></a>Batchanalyse
Vi også genererer en yderligere mængde simuleret biltype signaler og diagnosticering datasæt for bedre batchen analyser. Dette er påkrævet for at sikre en god repræsentant data lydstyrken for batchbehandling. Hertil bruger vi en rørledning med navnet "PrepareSampleDataPipeline" i Azure Data Factory arbejdsprocessen til at generere et år værd simuleret biltype signaler og diagnosticering datasæt. Klik på [Data Factory tilpassede aktiviteter](http://go.microsoft.com/fwlink/?LinkId=717077) for at hente den Data Factory brugerdefinerede DotNet aktivitet Visual Studio-løsning for tilpasninger, der er baseret på dine krav. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figur 8 - Klargøre eksempeldata for batchen behandling arbejdsproces*

Pipeline består af en brugerdefineret ADF .net aktivitet, Vis her:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figur 9 - PrepareSampleDataPipeline*

Når rørledningen udføres korrekt og "RawCarEventsTable" dataset er markeret "Klar", etårige værd af simuleret biltype signaler og diagnosticering er data oprettet. Du se følgende mappe og filer, der er oprettet i din lagerplads konto under objektbeholderen "connectedcar":

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figur 10 - PrepareSampleDataPipeline Output*

### <a name="references"></a>Referencer

[Azure begivenhed Hub SDK for stream indtagelse](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Azure Data Factory data bevægelse funktioner](../data-factory/data-factory-data-movement-activities.md)
[Azure Data Factory DotNet aktivitet](../data-factory/data-factory-use-custom-activities.md)

[Azure Data Factory DotNet aktivitet visual studio-løsning til forberedelse af eksempeldata](http://go.microsoft.com/fwlink/?LinkId=717077) 


## <a name="partition-the-dataset"></a>Partition datasættet

Rå semistrukturerede biltype signaler og diagnosticering datasæt er opdelt i trinnet data forberedelse til et år/måned-format. Denne partitionering hæver mere effektive forespørgsels- og SVG langsigtede lager ved at aktivere et over fra én blob-konto til næste, som den første konto fylder. 

>[AZURE.NOTE] Dette trin i løsningen gælder kun for batchbehandling.

Input og output data datastyring:

- **Outputdata** (mærket *PartitionedCarEventsTable*) der skal forblive i en lang tid som finde grundlæggende / "rawest" form af data i kundens "Data sø". 
- De **indtastede data** til denne rørledning vil typisk kassere, som outputdata har fuld pålidelighed til input - det er kun gemt (opdelt) bedre til senere brug.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Figur 11 – Partition bil begivenheder arbejdsproces*

Den rækkedata er opdelt ved hjælp af en Hive HDInsight aktivitet i "PartitionCarEventsPipeline". De eksempeldata, der er oprettet i trin 1 for et år er opdelt efter år/måned. Partitionerne, der bruges til at generere biltype signaler og diagnosticering data for hver måned (samlede 12 partitioner) i et år. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figur 12 - PartitionCarEventsPipeline*

Følgende Hive-script, med navnet "partitioncarevents.hql", der bruges til partitionering og er placeret i mappen "\demo\src\connectedcar\scripts" af det hentede zip. 


    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string,
                YearNo                          int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figur 13 - PartitionConnectedCarEvents Hive Script*

Når rørledningen er udført, kan du se de følgende partitioner, der er oprettet i din lagerplads konto under objektbeholderen "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figur 14 - partitioneret Output*

Dataene er nu optimeret, administrere og klar til videre behandling for at få omfattende batchen indsigt. 

## <a name="data-analysis"></a>Dataanalyse

I dette afsnit, skal se du, hvordan du kombinere Azure Stream Analytics, Azure Machine Learning, Azure Data Factory og Azure HDInsight for RTF avanceret analyse på biltype tilstand og bil vaner. Der er tre underafsnit her:

1.  **Machine Learning**: Dette afsnit indeholder oplysninger om anomali registrering forsøget, som vi har brugt i denne løsning til at forudsige køretøjer, der kræver vedligeholdelse vedligeholdelse og køretøjer, der kræver tilbagekaldelser på grund af problemer med sikkerhed.
2.  **Realtid analyse**: Dette afsnit indeholder oplysninger om realtid analyser ved hjælp af Stream Analytics forespørgselssprog og operationalizing machine learning forsøget i realtid ved hjælp af et brugerdefineret program.
3.  **Batchanalyse**: Dette afsnit indeholder oplysninger om den at transformere og behandling af batchen dataene ved hjælp af Azure HDInsight og Azure Machine Learning opgivet af Azure Data Factory.

### <a name="machine-learning"></a>Machine Learning

Her vores mål er at forudsige køretøjerne, der kræver vedligeholdelse eller tilbagekaldelsen baseret på bestemte områder statistik. Vi giver følgende forudsætninger

- Hvis et af følgende tre betingelser er opfyldt, kræver køretøjerne **vedligeholdelse vedligeholdelse**:
    - Dæk tryk mangler
    - Program oil niveau mangler
    - Program temperaturen er højt

- Hvis et af følgende betingelser er opfyldt, kan køretøjerne har et **sikkerhedsproblem, der er** og kræver **tilbagekaldelsen**:
    - Program temperaturen er høj, men uden for temperaturen er lav
    - Program temperaturen er lav, men uden for temperaturen er høj

Baseret på de forrige krav, har vi oprettet to separate modeller at registrere afvigelser, én til biltype vedligeholdelse registrering og én til biltype tilbagekaldelsen registrering. I begge disse modeller bruges den indbyggede vigtigste komponent analyse (PCA) algoritme til anomali registrering. 

**Vedligeholdelse registrering model**

Hvis en af tre indikatorer - dæk tryk, program oil eller program temperatur - opfylder dens respektive betingelse, rapporterer vedligeholdelse registrering modellen en fejl. Derfor skal vi kun Overvej disse tre variabler i opbygning af modellen. I vores forsøg i Azure Machine Learning bruger vi først et **Vælg kolonner i Dataset** -modul til at udtrække disse tre variabler. Derefter bruger vi modulet PCA-baserede anomali registrering til at opbygge anomali registrering modellen. 

Hovedstolen komponent analyse (PCA) er en eksisterende teknik maskine mere, der kan anvendes til valg af funktion, klassifikation og anomali registrering. PCA konverterer et sæt af store og små bogstaver, der indeholder muligvis forbundne variabler i et sæt af værdier, der hedder vigtigste komponenter. Vigtige ideen med PCA-baserede modellering er til project-data til et lavere flerdimensionale område, så funktioner og afvigelser kan identificeres nemmere.
 
For hver ny input til registrering af modellen i anomali registrering ressourcer først beregner dens projicering på eigenvectors, og derefter beregner fejlen standardiseret gendannelse. Fejlen standardiseret er anomali resultat. Jo højere fejlen, mere uoverensstemmende er forekomsten. 

I vedligeholdelse registrering problemet, kan hver post betragtes som et punkt i et 3-flerdimensionale mellemrum, der er defineret af dæk tryk, program oil og program temperatur koordinater. For at registrere disse afvigelser, kan vi projekt de oprindelige data i det 3-flerdimensionale område til et 2-flerdimensionale område ved hjælp af PCA. Derfor indstille vi parameteren antallet af komponenter til brug i PCA til at være 2. Denne parameter spiller en vigtig rolle i anvende PCA-baserede anomali registrering. Efter projicere data ved hjælp af PCA, kan vi identificere disse afvigelser nemmere.

**Tilbagekalde anomali registrering model** I tilbagekaldelsen anomali registrering af modellen, vi bruge Vælg kolonnerne i datasættet og PCA-baserede anomali registrering moduler på samme måde. Nærmere betegnet kan udtrække vi først tre variabler - program temperaturen, eksterne temperaturen og hastighed - bruger modulet **Vælg kolonner i datasættet** . Vi kan også indeholde variablen hastighed, da program temperaturen mellem typisk til hastigheden. Derefter bruger vi PCA-baserede anomali registrering modul til project-data fra den 3-flerdimensionale plads til et 2-flerdimensionale område. Tilbagekaldelsen kriterier er opfyldt, og så biltype kræver tilbagekaldelsen når program temperatur og uden for temperaturen meget negativt mellem. Bruger PCA-baserede anomali registreringsalgoritmen, kan vi registrere afvigelser når du har udført PCA. 

Når uddannelse enten model, har vi brug at anvende normal data, som ikke kræver vedligeholdelse eller tilbagekaldelsen som inputdataene til at undervise PCA-baserede anomali registrering modellen. I vurdering forsøget bruger vi erfaren anomali registrering modellen til at undersøge, om biltype kræver vedligeholdelse eller tilbagekaldelsen. 


### <a name="real-time-analysis"></a>Realtid analyse

Følgende Stream Analytics SQL-forespørgslen bruges til at finde gennemsnittet af alle vigtige biltype parametre som biltype hastighed, brændstof niveau, program temperaturen, kilometerstand læsning, dæk tryk, program oil niveau og andre. Gennemsnit bruges til at registrere afvigelser, udstede beskeder, og bestemme de overordnede sundhedsbetingelser af køretøjer drevet i bestemte område og koordinere den til demografiske data. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figur 15-Stream analytics-forespørgsel til realtid behandling

Alle gennemsnit beregnes over en 3 sekunders TumblingWindow. Vi bruger TubmlingWindow i dette tilfælde da vi kræver ikke overlapper og sammenhængende tidsintervaller. 

Hvis du vil vide mere om alle "Windowing"-funktioner i Azure Stream Analytics, skal du klikke på [Windowing (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Realtid forudsigelse**

Et program er inkluderet som en del af løsningen til giver muligheder for machine learning modellen i realtid. Dette program kaldet "RealTimeDashboardApp" er oprettet og konfigureret som en del af løsning installationen. Programmet udfører følgende:

1.  Lytter til en begivenhed Hub-forekomst, hvor Stream Analytics publicerer begivenheder i et mønster løbende. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figur 16-Stream analytics-forespørgsel til at publicere dataene til en output begivenhed Hub forekomst* 

2.  For hver begivenhed, der modtager dette program: 

    - Behandler data med Machine Learning anmodning om svar vundne (Ressourceposter) slutpunkt. Ressourceposter slutpunktet udgives automatisk som en del af installationen.
    - Ressourceposter output publiceres til et PowerBI datasæt ved hjælp af opslagsnål API'er.

Dette mønster gælder også for scenarier, hvor du vil integrere et Line of Business (LoB) program med realtid analytics-strømmen for scenarier som beskeder, beskeder og beskeder.

Klik på [RealtimeDashboardApp Hent](http://go.microsoft.com/fwlink/?LinkId=717078) for at hente RealtimeDashboardApp Visual Studio-løsning for tilpasninger. 

**Til at køre realtid Dashboard-programmet**

1.  Klik på noden PowerBI på diagramvisning, og klik på linket "Hent realtid Dashboard programmet" i egenskabsruden. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png)*Figur 17 – PowerBI dashboard konfigurationsvejledning*
2.  Udtrække og gemme lokalt ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *figur 18 – RealtimeDashboardApp mappe*
3.  Køre programmet RealtimeDashboardApp.exe
4.  Gyldige legitimationsoplysninger til Power BI, logge på og klik på Acceptér ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figur 19 – RealtimeDashboardApp: Log på PowerBI*

>[AZURE.NOTE] Hvis du vil rydde PowerBI datasættet, skal du udføre RealtimeDashboardApp med parameteren "flushdata": 

    RealtimeDashboardApp.exe -flushdata

### <a name="batch-analysis"></a>Batchanalyse

Mål her får vist, hvordan Contoso konstruktion anvender funktionerne Azure Beregn til at organisere big data for at få omfattende viden om bil mønster, brugen funktionsmåde og biltype tilstand. Dette gør det muligt at:

- Forbedre kundeoplevelsen og gøre det billigere ved at indsende indsigt på bil spisevaner og brændstof effektiv fører funktionsmåder
- Lære proaktiv kunder og deres fører patters styrer forretningsbeslutninger og angive bedst i klassen produkter og tjenester

I denne løsning målretter vi følgende målepunkter:

1.  **Tilstanden fører funktionsmåde**: identificerer tendensen for den modeller, placeringer, fører betingelser og klokkeslættet for året til at få indsigt i tilstanden fører mønstre. Contoso konstruktion kan bruge disse indsigt til marketingkampagner, bil nye tilpassede funktioner og brugen-baserede forsikring.
2.  **Brændstof effektiv fører funktionsmåde**: identificerer tendensen for den modeller, placeringer, fører betingelser og klokkeslættet for året til at få indsigt på brændstof effektiv fører mønstre. Contoso konstruktion kan bruge disse indsigt til markedsføringskampagner, til at bil nye funktioner og proaktiv rapportering til driverne til omkostninger effektiv og miljø fulde fører vaner. 
3.  **Tilbagekalde modeller**: identificerer modeller, der kræver tilbagekaldelser ved operationalizing anomali registrering machine learning-forsøg

Lad os se flere detaljer i hver af disse målepunkter


**Tilstanden fører mønster**

Partitioneret biltype signaler og diagnosticering data behandles i pipeline med navnet "AggresiveDrivingPatternPipeline" ved hjælp af Hive til at bestemme modellerne, placering, biltype, fører betingelser og andre parametre, der opfører tilstanden fører mønster.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) 
*Figur 20 – aggressiv bil mønster arbejdsproces*

Hive scriptet med navnet "aggresivedriving.hql" bruges til at analysere tilstanden fører betingelse mønster er placeret på "\demo\src\connectedcar\scripts" mappe med hentede zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                vin                         string, 
                model                       string,
                timestamp                   string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,
                brake_pedal_status          string,
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figur 21 – aggressiv bil mønster Hive forespørgsel*

Kombination af biltypes overførslen tandhjul position, målinger vha status og hastigheden bruges til at registrere reckless/tilstanden fører funktionsmåde, der er baseret på bremsning mønster med høj hastighed. 

Når rørledningen er udført, kan du se de følgende partitioner, der er oprettet i din lagerplads konto under objektbeholderen "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Figur 22 – AggressiveDrivingPatternPipeline output*


**Brændstof effektiv fører mønster**

Partitioneret biltype signaler og diagnosticering data behandles i pipeline med navnet "FuelEfficientDrivingPatternPipeline". Hive bruges til at bestemme modellerne, placering, biltype, fører betingelser og andre egenskaber, der udviser brændstof effektiv fører mønster.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figur 23 – brændstof effektiv fører mønster arbejdsproces*

Hive scriptet med navnet "fuelefficientdriving.hql" bruges til at analysere tilstanden fører betingelse mønster er placeret på "\demo\src\connectedcar\scripts" mappe med hentede zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                vin                         string, 
                model                       string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,                
                brake_pedal_status          string,            
                accelerator_pedal_position  string,                             
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figur 24 – brændstof effektiv fører mønster Hive-forespørgsel*

Den anvender en kombination af biltypes overførslen tandhjul position, målinger vha status, hastighed og genvejstast pedalrefleksanordninger stand til at registrere brændstof effektiv fører funktionsmåde baseret på hardwareacceleration, bremsning, og hastighed mønstre. 

Når rørledningen er udført, kan du se de følgende partitioner, der er oprettet i din lagerplads konto under objektbeholderen "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figur 25 – FuelEfficientDrivingPatternPipeline output*


**Tilbagekalde prognoser**

Maskinen læ forsøg er klargjort og publicerede som en webtjeneste som en del af løsning installationen. Batchen vundne slutpunkt opgraderede i arbejdsprocessen er registreret som en fabrik sammenkædet datatjeneste og opgivet ved hjælp af data factory batchen vundne aktivitet.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png) 

*Figur 26 – Machine learning slutpunkt, der er registreret som en sammenkædet tjeneste i data factory*

Den registrerede sammenkædede service bruges i DetectAnomalyPipeline til at give data med anomali registrering modellen. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png) 

*Figur 27 – Azure Machine Learning batchen vundne aktivitet i data factory* 

Der er få trin udføres i denne pipeline til forberedelse af data, så det kan være opgivet med batchen vundne webtjeneste. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figur 28 – DetectAnomalyPipeline til at forudsige køretøjer, der kræver tilbagekaldelser* 

Når den vundne er fuldført, er en HDInsight aktivitet bruges til at behandle og samle de data, der er kategoriseret som afvigelser af modellen med en sandsynlighed resultat af 0.60 eller nyere.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                         string,
                model                       string,
                gendate                     string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                fuel                        string,
                engineoil                   string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position  string,
                parking_brake_status        string,
                headlamp_status             string,
                brake_pedal_status          string,
                transmission_gear_position  string,
                ignition_status             string,
                windshield_wiper_status     string,
                abs                         string,
                maintenanceLabel            string,
                maintenanceProbability      string,
                RecallLabel                 string,
                RecallProbability           string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                         string,
                model                       string,
                city                        string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                Year                        string,
                Month                       string              
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Når rørledningen er udført, kan du se de følgende partitioner, der er oprettet i din lagerplads konto under objektbeholderen "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Figur 30 – figur 30-DetectAnomalyPipeline output*


## <a name="publish"></a>Publicere

### <a name="real-time-analysis"></a>Realtid analyse

En af forespørgsler i stream analytics jobbet publiceres begivenheder til output begivenhed Hub forekomst. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figur 31-Stream analytics jobbet publiceres til output begivenhed Hub forekomst*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figur 32 – Stream analytics forespørgsel for at publicere output begivenhed Hub forekomst*

Denne strøm af hændelser, der bruges af RealTimeDashboardApp findes i løsningen. Dette program bruger webtjenesten Machine Learning anmodning-svar til realtid vundne og udgiver den resulterende data til et PowerBI datasæt til forbrug. 

### <a name="batch-analysis"></a>Batchanalyse

Resultaterne af den batch og realtid behandling publiceres til Azure SQL-databasetabeller til forbrug. Azure SQL Server, Database og tabellerne, der oprettes automatisk som en del af konfigurationen scriptet. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figur 33 – batchbehandling resultater kopi til data datamarkedet arbejdsproces*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figur 34 – Stream analytics jobbet publiceres til datamarkedet*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figur 35 – datamarkedet indstilling i stream analytics job*


## <a name="consume"></a>Bruge

Power BI giver denne løsning et omfattende dashboard for realtidsdata og skønnet analytics visualiseringer. 

Klik her for at få detaljerede anvisninger til konfiguration af PowerBI rapporter og dashboard. Den endelige dashboard ser sådan ud:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figur 36 - PowerBI Dashboard*

## <a name="summary"></a>Oversigt

Dette dokument indeholder en detaljeret analysere ned biltype Telemetri Analytics løsningens. Dette viser et lambda arkitektur mønster for realtid og batch analytics med prognoser og handlinger. Dette mønster gælder for en lang række use cases, der kræver varmt sti (realtid) og koldtvandssystemer sti (batchen) analyser. 
