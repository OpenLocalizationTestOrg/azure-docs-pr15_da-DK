<properties
    pageTitle="Streame Analytics output: indstillinger for lagring, analyse | Microsoft Azure"
    description="Få mere at vide om målretning af Stream Analytics data output indstillinger, herunder Power BI til analyseresultaterne igen."
    keywords="transformation af data, analyseresultaterne igen, indstillinger for lagring af data"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Streame Analytics output: indstillinger for lagring, analyse

Ved oprettelse af et Stream Analytics-job, kan du overveje, hvordan resulterende dataene skal blive brugt. Hvordan skal du se resultatet af kørslen Stream Analytics, og hvor vil du gemme den?

For at aktivere en række forskellige programmet mønstre, har Azure Stream Analytics forskellige indstillinger til lagring af output og visning af analyseresultaterne igen. Dette gør det nemt at få vist jobbet output og giver dig fleksibilitet i forbrug og opbevaring af job output til data, opbevaring og andre formål. Noget output, der er konfigureret i jobbet skal være før jobbet startes og begivenheder starte der flyder. Eksempelvis hvis du bruger Blob-lager som output, kan jobbet ikke oprette en lagerplads konto automatisk. Det skal være oprettet af brugeren, før ASA jobbet startes.

## <a name="azure-data-lake-store"></a>Azure sø datalager

Du kan streame Analytics understøtter [Azure sø datalager](https://azure.microsoft.com/services/data-lake-store/). Denne lagerplads gør det muligt at gemme data fra en hvilken som helst størrelse, type og indtagelse hastigheden for drift og undersøgende analyser. På nuværende tidspunkt understøttes oprettelse og konfiguration af sø datalager output kun i portalen Azure klassisk. Desuden skal Stream Analytics have tilladelse til at få adgang til sø datalager. Få at vide om godkendelse, og hvordan til at tilmelde sig sø til Store datavisning (hvis det er nødvendigt) er beskrevet i [Data sø output artikel](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Godkend en Azure datalager sø

Når sø datalagring er markeret som output i portalen Azure Management, bliver du bedt om at Godkend en forbindelse til en eksisterende Data sø butik.  

![Godkend sø datalager](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Udfyld derefter egenskaberne for sø datalager output, som vist nedenfor:

![Godkend sø datalager](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse, der er behov for at oprette en sø datalager output.

<table>
<tbody>
<tr>
<td><B>EGENSKABSNAVN</B></td>
<td><B>BESKRIVELSE</B></td>
</tr>
<tr>
<td>Output Alias</td>
<td>Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne sø datalager.</td>
</tr>
<tr>
<td>Kontonavn</td>
<td>Navnet på kontoen sø datalager, hvor du sender din output. Du der præsenteres med en rulleliste over sø datalager konti, som den bruger, der er logget på portalen har adgang til.</td>
</tr>
<tr>
<td>Sti præfiks mønster [<I>valgfrit</I>]</td>
<td>Filstien bruges til at skrive dine filer i den angivne Data sø Store konto. <BR>{date}, {time}<BR>Eksempel 1: folder1/logfiler / {dato} / {tid}<BR>Eksempel 2: folder1/logfiler / {dato}</td>
</tr>
<tr>
<td>Datoformat [<I>valgfrit</I>]</td>
<td>Hvis dato tokenet bruges i præfiks stien, kan du vælge datoformatet, hvor dine filer er organiseret. Eksempel: Åååå/MM/DD</td>
</tr>
<tr>
<td>Klokkeslætsformat [<I>valgfrit</I>]</td>
<td>Hvis tokenet tid der bruges i præfiks stien, kan du angive det klokkeslætsformat, hvor dine filer er organiseret. Den eneste understøttede værdi er i øjeblikket HH.</td>
</tr>
<tr>
<td>Begivenhed serialiseringsformatet</td>
<td>Serialiseringsformatet til outputdata. JSON, csv- og Avro understøttes.</td>
</tr>
<tr>
<td>Kode</td>
<td>Hvis csv- eller JSON formaterer, skal en kodning angives. UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt.</td>
</tr>
<tr>
<td>Afgrænser</td>
<td>Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere til serialisering csv-data. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg.</td>
</tr>
<tr>
<td>Formatér</td>
<td>Gælder kun for JSON serialisering. Linje adskilt angiver, at der skal formateres output ved at få de enkelte JSON-objekter, der er adskilt af en ny linje. Matrix angiver, at der skal formateres output som en matrix med JSON objekter.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Forny sø datalager godkendelse

Du skal godkende kontoen sø datalager igen, hvis adgangskoden er ændret siden tingene blev oprettet eller senest godkendt.

![Godkend sø datalager](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>SQL-Database

[Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) kan bruges som output, for data, som er relationelle karakter eller programmer, der er afhænger af indhold, der er tilknyttet i en relationel database. Stream Analytics job skriver til en eksisterende tabel i en Azure SQL-Database.  Bemærk, at tabellen skemaet skal svare nøjagtigt til felterne og deres type, der output fra dit arbejde. En [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kan også angives som output via SQL-Database output indstillingen samt (dette er en funktion, preview). Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse for at oprette en SQL-Database output.

| Egenskabsnavn | Beskrivelse |
|---------------|-------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne database. |
| Database | Navnet på den database, hvor du sender din output |
| Servernavn | Navnet på SQL-databaseserveren |
| Brugernavn | Det brugernavn, der har adgang til at skrive til databasen |
| Adgangskode | Adgangskode til at oprette forbindelse til databasen |
| Tabel | Tabelnavnet hvor output skal skrives. Der skelnes mellem tabelnavnet og skemaet for denne tabel skal svare nøjagtigt til antallet af felter og deres type, der genereres af din sag output. |

> [AZURE.NOTE] Aktuelt understøttes tilbud Azure SQL-Database til et job output i Stream analyser. Men en Azure virtuel maskine, der kører SQL Server med en database, der er vedhæftet understøttes ikke. Dette er underlagt ændring i fremtidige versioner.

## <a name="blob-storage"></a>BLOB-lager

BLOB-lager tilbyder en økonomisk og SVG løsning til lagring af store mængder ustrukturerede data i skyen.  Du kan finde en introduktion på Azure Blob-lager og dens brugen på [Sådan bruger du BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse til oprettelse af en blob output.

<table>
<tbody>
<tr>
<td>EGENSKABSNAVN</td>
<td>BESKRIVELSE</td>
</tr>
<tr>
<td>Output Alias</td>
<td>Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne blob-lager.</td>
</tr>
<tr>
<td>Lagerplads konto</td>
<td>Navnet på kontoen lagerplads, hvor du sender din output.</td>
</tr>
<tr>
<td>Lagerplads Kontonøgle</td>
<td>Hemmeligt nøglen der er knyttet til kontoen lagerplads.</td>
</tr>
<tr>
<td>Objektbeholder til lagring</td>
<td>Beholdere giver en logisk gruppering for blob, der er gemt i tjenesten Microsoft Azure Blob. Når du overfører en blob til Blob-tjenesten, skal du angive en objektbeholder til pågældende blob.</td>
</tr>
<tr>
<td>Sti præfiks mønster [Valgfrit]</td>
<td>Filstien bruges til at skrive din BLOB inden for den angivne beholder.<BR>I sti, kan du vælge at bruge en eller flere forekomster af de følgende 2 variabler til at angive den hyppighed, der er skrevet BLOB:<BR>{date}, {time}<BR>Eksempel 1: cluster1/logfiler / {dato} / {tid}<BR>Eksempel 2: cluster1/logfiler / {dato}</td>
</tr>
<tr>
<td>Datoformat [Valgfrit]</td>
<td>Hvis dato tokenet bruges i præfiks stien, kan du vælge datoformatet, hvor dine filer er organiseret. Eksempel: Åååå/MM/DD</td>
</tr>
<tr>
<td>Klokkeslætsformat [Valgfrit]</td>
<td>Hvis tokenet tid der bruges i præfiks stien, kan du angive det klokkeslætsformat, hvor dine filer er organiseret. Den eneste understøttede værdi er i øjeblikket HH.</td>
</tr>
<tr>
<td>Begivenhed serialiseringsformatet</td>
<td>Serialiseringsformatet til outputdata.  JSON, csv- og Avro understøttes.</td>
</tr>
<tr>
<td>Kode</td>
<td>Hvis csv- eller JSON formaterer, skal en kodning angives. UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt.</td>
</tr>
<tr>
<td>Afgrænser</td>
<td>Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere til serialisering csv-data. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg.</td>
</tr>
<tr>
<td>Formatér</td>
<td>Gælder kun for JSON serialisering. Linje adskilt angiver, at der skal formateres output ved at få de enkelte JSON-objekter, der er adskilt af en ny linje. Matrix angiver, at der skal formateres output som en matrix med JSON objekter.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Begivenhed Hub

[Begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/) er en meget SVG udgivelse abonnement begivenhed ingestor. Det kan indsamle millioner af begivenheder sekundet.  En anvendelse af en begivenhed Hub som output er hvornår output fra et Stream Analytics-job bliver input fra et andet streaming job.

Der er et par parametre, der skal bruges til at konfigurere begivenhed Hub datastreams som output.

| Egenskabsnavn | Beskrivelse |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne begivenhed Hub. |
| Tjenesten Bus Namespace | Et Service Bus navneområde er en beholder for et sæt messaging enheder. Når du har oprettet en ny begivenhed-Hub, skal du også har oprettet et Service Bus navneområde |
| Begivenhed Hub | Navnet på din begivenhed Hub output |
| Navn på begivenhed Hub politik | Politikken delt adgang, som kan oprettes under fanen begivenhed Hub konfigurere. Hver delt access-politik skal have et navn, tilladelser, du har angivet, og hurtigtasterne |
| Nøgle til begivenhed Hub politik | Den delte adgangsnøgle, der bruges til at godkende adgang til navneområdet Service Bus |
| Partition nøglekolonne [Valgfrit] | Kolonnen skal indeholde tasten partition til begivenhed Hub output. |
| Begivenhed serialiseringsformatet | Serialiseringsformatet til outputdata.  JSON, csv- og Avro understøttes. |
| Kode | Csv-og JSON ved er UTF-8 kun understøttet kodningsformatet på nuværende tidspunkt |
| Afgrænser | Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere serialisering data i en CSV-fil. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg. |
| Formatér | Kun tilgængelig for JSON type. Linje adskilt angiver, at der skal formateres output ved at få de enkelte JSON-objekter, der er adskilt af en ny linje. Matrix angiver, at der skal formateres output som en matrix med JSON objekter. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) kan bruges som output for en Stream Analytics sag til at levere til en omfattende visualisering oplevelse af analyseresultaterne igen. Denne funktion kan bruges til driften dashboards, oprettelse af rapporter og metrisk indsatsbaserede rapportering.

### <a name="authorize-a-power-bi-account"></a>Godkend en Power BI-konto

1.  Når Power BI er markeret som output i portalen Azure Management, bliver du spurgt, Godkend en eksisterende Power BI-bruger eller oprette en ny Power BI-konto.  

    ![Godkend Power BI bruger](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Oprette en ny konto, hvis du ikke, men har en og derefter klikke på Godkend nu.  Et skærmbillede i stil med følgende præsenteres.  

    ![Azure-konto Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  I dette trin skal du angive den arbejds- eller skolekonto konto for godkendelse af Power BI-output. Hvis du ikke allerede er logget til Power BI, Vælg Log nu. Den arbejde- eller skolekonto konto, du bruger til Power BI kan være forskellige fra kontoen Azure-abonnement, som du er logget på med.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurere Power BI output egenskaber

Når du har den godkendte Power BI-konto, kan du konfigurere egenskaberne for dit Power BI-output. Tabellen nedenfor er listen over egenskabsnavne, og deres beskrivelse for at konfigurere din Power BI-output.

| Egenskabsnavn | Beskrivelse |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne PowerBI output. |
| Gruppen arbejdsområde | Du kan vælge grupper i din Power BI-konto eller Vælg "Mine arbejdsområde", hvis du ikke vil skrive til en gruppe for at aktivere Deling af data med andre brugere af Power BI.  Opdatere en eksisterende gruppe kræver forny Power BI-godkendelse. | 
| Navn på datasæt | Angiv et navn til datasættet, som det ønskes for Power BI-output skal bruges |
| Tabelnavn | Angiv et tabelnavn under datasæt på Power BI-output. I øjeblikket, kan Power BI output fra Stream Analytics job kun have én tabel i et datasæt |

En gennemgang af konfiguration af en Power BI output og dashboard, skal du se artiklen [Azure Stream analyser og Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Eksplicit Opret ikke datasæt og tabel i Power BI-dashboard. Datasæt og tabel udfyldes automatisk når jobbet startes og jobbet starter Pumpende output til Power BI. Bemærk, at hvis forespørgslen job ikke genererer nogen resultater, datasæt og tabel ikke oprettes. Også være opmærksom på, at hvis Power BI allerede har en dataset og en tabel med det samme navn som den, der er angivet i dette Stream Analytics-job skal de eksisterende data vil blive overskrevet.

### <a name="renew-power-bi-authorization"></a>Forny Power BI-godkendelse

Du skal godkende kontoen Power BI igen, hvis adgangskoden er ændret siden tingene blev oprettet eller senest godkendt. Hvis Multi-Factor Authentication (MFA) er konfigureret på din Azure Active Directory (AAD) lejer, skal du også forny Power BI godkendelse hver 2 uger. Et symptom på dette problem er ingen job output og "Godkend brugerfejl" i loggene handling:

  ![Power BI Opdater token fejl](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Stop igangværende tingene for at løse dette problem, og gå til din Power BI-output.  Klik på linket "Forny godkendelse", og genstart tingene fra den sidste ikke længere tid at undgå tab af data.

  ![Power BI Forny godkendelse](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage

[Azure Table storage](../storage/storage-introduction.md) giver høj tilgængelighed, stort omfang SVG lagerplads, så programmet kan automatisk skaleres til at opfylde brugernes behov. Tabellagring er Microsofts NoSQL nøgle/attribut store hvilken af skærmene du kan udnytte til strukturerede data med mindre begrænsninger i skemaet. Azure-tabellager kan bruges til at gemme data for bevarelse og effektivt hentning.

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse for at oprette en tabel output.

| Egenskabsnavn | Beskrivelse |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne tabellager. |
| Lagerplads konto | Navnet på kontoen lagerplads, hvor du sender din output. |
| Lagerplads Kontonøgle | Hurtigtast, der er knyttet til kontoen lagerplads. |
| Tabelnavn | Navnet på tabellen. Tabellen vil få oprettet, hvis den ikke findes. |
| Partitionsnøgle | Navnet på outputkolonnen, der indeholder tasten partition. Partitionsnøglen er et entydigt id for partitionen i en given tabel, der udgør den første del af et objekt primær nøgle. Det er en strengværdi, der kan være op til 1 KB i størrelse. |
| Række-tasten | Navnet på outputkolonnen, der indeholder tasten række. Den række nøgle er et entydigt id for et objekt i en given partition. Udgør den anden del af et objekt primær nøgle. Nøglen række er en strengværdi, der kan være op til 1 KB i størrelse. |
| Batchstørrelse | Antallet af poster for en batchhandling af. Typisk standard er tilstrækkelige til de fleste job skal du henvise til [tabel batchhandlingen specifikation](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) for flere oplysninger om at ændre denne indstilling. |

## <a name="service-bus-queues"></a>Service Bus køer

[Bus servicekøer](https://msdn.microsoft.com/library/azure/hh367516.aspx) tilbyder en First In, først ud (FIFU) levering af meddelelser til en eller flere konkurrerende forbrugere. Typisk forventede meddelelser er modtaget og behandlet af modtagere i den tidsmæssige rækkefølge, hvori de blev føjet til køen, og alle meddelelser er modtaget og behandles som en enkelt meddelelse forbruger.

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse for at oprette en kø output.

| Egenskabsnavn | Beskrivelse |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne tjeneste Bus kø. |
| Tjenesten Bus Namespace | Et Service Bus navneområde er en beholder for et sæt messaging enheder. |
| Kø navn | Navnet på tjenesten Bus køen. |
| Navn på kø politik | Når du opretter en kø, kan du også oprette politikker for delt adgang under fanen kø konfigurere. Hver delt access-politik skal have et navn, tilladelser, du har angivet, og hurtigtasterne. |
| Kø politiknøgle | Den delte adgangsnøgle, der bruges til at godkende adgang til navneområdet Service Bus |
| Begivenhed serialiseringsformatet | Serialiseringsformatet til outputdata.  JSON, csv- og Avro understøttes. |
| Kode | Csv-og JSON ved er UTF-8 kun understøttet kodningsformatet på nuværende tidspunkt |
| Afgrænser | Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere serialisering data i en CSV-fil. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg. |
| Formatér | Kun tilgængelig for JSON type. Linje adskilt angiver, at der skal formateres output ved at få de enkelte JSON-objekter, der er adskilt af en ny linje. Matrix angiver, at der skal formateres output som en matrix med JSON objekter. |

## <a name="service-bus-topics"></a>Tjenesten Bus emner

Mens Bus servicekøer leverer en en til en kommunikationsmetode fra afsender til modtageren, indeholder [Service Bus emner](https://msdn.microsoft.com/library/azure/hh367516.aspx) en en-til-mange form for kommunikation.

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse for at oprette en tabel output.

| Egenskabsnavn | Beskrivelse |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Output Alias | Dette er et brugervenligt navn, der bruges i forespørgsler til at dirigere forespørgslens output til denne tjeneste Bus emne. |
| Tjenesten Bus Namespace | Et Service Bus navneområde er en beholder for et sæt messaging enheder. Når du har oprettet en ny begivenhed-Hub, skal du også har oprettet et Service Bus navneområde |
| Emnenavn | Emner messaging enheder, svarende til begivenhed hubs og køer. De er udviklet til at indsamle begivenhed streams fra en række forskellige enheder og -tjenester. Når der oprettes et emne, er det også givet et bestemt navn. De meddelelser, som sendes til et emne, vil ikke være tilgængelig, medmindre et abonnement er oprettet, så kontrollere, at der er en eller flere abonnementer under emnet |
| Emne politikkens navn | Når du opretter et emne, kan du også oprette politikker for delt adgang under fanen emne konfigurere. Hver delt access-politik skal have et navn, tilladelser, du har angivet, og hurtigtasterne |
| Emne politiknøgle | Den delte adgangsnøgle, der bruges til at godkende adgang til navneområdet Service Bus |
| Begivenhed serialiseringsformatet | Serialiseringsformatet til outputdata.  JSON, csv- og Avro understøttes. |
| Kode | Hvis csv- eller JSON formaterer, skal en kodning angives. UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt |
| Afgrænser | Gælder kun for csv-serialisering. Stream Analytics understøtter et antal almindelige afgrænsere serialisering data i en CSV-fil. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg. |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) er en fuldt administreret NoSQL dokument database tjeneste, der tilbyder forespørgsel og transaktioner over skema ledig data, forudsigelige og pålidelige ydeevne og hurtig udvikling.

Tabellen nedenfor viser Egenskabsnavnene og deres beskrivelse til oprettelse af en DocumentDB output.

<table>
<tbody>
<tr>
<td>EGENSKABSNAVN</td>
<td>BESKRIVELSE</td>
</tr>
<tr>
<td>Kontonavn</td>
<td>Navnet på DocumentDB-kontoen.  Det kan også være slutpunkt for kontoen.</td>
</tr>
<tr>
<td>Kontonøgle</td>
<td>Tasten delt adgang til kontoen DocumentDB.</td>
</tr>
<tr>
<td>Database</td>
<td>Databasenavnet DocumentDB.</td>
</tr>
<tr>
<td>Samling navn mønster</td>
<td>Samling navn mønster for samlinger der skal bruges. Navneformatet af websteder kan opbygget ved hjælp af tokenet valgfrit {partition}, hvor partitioner starter fra 0.<BR>F.eks. Følgende er gyldige input:<BR>MyCollection {partition}<BR>MyCollection<BR>Bemærk, at af websteder skal findes, før Stream Analytics jobbet er startet, og vil ikke blive oprettet automatisk.</td>
</tr>
<tr>
<td>Partitionsnøgle</td>
<td>Navnet på feltet i output hændelser, der bruges til at angive nøgle for partitionering output på tværs af websteder.</td>
</tr>
<tr>
<td>Dokument-ID</td>
<td>Navnet på feltet i output-hændelser, der bruges til at angive den primære nøgle, indsætte eller opdatere handlinger er baseret på.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin
Du har indført til Stream Analytics, en administreret tjeneste til streaming analytics på data fra internettet ting. Hvis du vil vide mere om denne tjeneste skal du se:

- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
