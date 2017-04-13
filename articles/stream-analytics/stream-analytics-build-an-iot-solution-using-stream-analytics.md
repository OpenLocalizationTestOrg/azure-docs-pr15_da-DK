<properties
    pageTitle="Opbygge en IoT løsning ved hjælp af Stream Analytics | Microsoft Azure"
    description="Introduktion-– selvstudium til Stream Analytics IoT opløsning af et scenarie med afgiftsbås"
    keywords="iot løsning, vinduet funktioner"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Opbygge en IoT løsning ved hjælp af Stream Analytics

## <a name="introduction"></a>Introduktion

I dette selvstudium lærer du at bruge Azure Stream Analytics til at få realtid indsigt fra dine data. Udviklere kan nemt kombinere streams af data, som Klik streams, logfiler og enhed oprettede begivenheder med historiske poster eller referencedata til at udlede business indsigt. Som en tjeneste fuldt administreret, realtid stream beregning, der er placeret i Microsoft Azure, indeholder Azure Stream Analytics indbyggede fleksibilitet, lav ventetid og skalerbarhed til får du op at køre i minutter.

Når du har fuldført dette selvstudium, vil du kunne:

-   Lære at kende på Azure Stream Analytics-portalen.
-   Konfigurere og installere et streaming job.
-   Formidle mest reelle problemer og løse dem ved hjælp af forespørgselssprog Stream analyser.
-   Udvikle streaming løsninger til dine kunder ved hjælp af Stream Analytics uden bekymringer.
-   Brug den overvågning og registrering oplevelse til at foretage fejlfinding af problemer.

## <a name="prerequisites"></a>Forudsætninger

Du skal de følgende forudsætninger til at udføre dette selvstudium:

-   Den seneste version af [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 eller gratis [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Et [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/)
-   Administratorrettigheder på computeren
-   Download af [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) fra Microsoft Download Center
-   Valgfrit: Kildekode for TollApp begivenhed generator i [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Scenarie-Introduktion: "Hej, kun!"


En kun station er et almindelige dette. Du støder på dem på mange expressways, broer og tunneler over hele verden. Hver kun station har flere kun boder. Ved manuel boder stoppe du for at betale kun til en omstilling. På automatiseret boder scanner et føler øverst på hver standen et RFID kort, der er fastgjort til windshield af din biltype, som du overfører kun standen. Det er nemt at visualisere overgang af gennem disse kun stationer som en begivenhed stream som interessante handlinger kan udføres over.

![Billede af biler på kun boder](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Indgående data

Dette selvstudium fungerer med to streams af data. Sensorer installeret i indgang og Afslut af kun stationer landbrugsprodukter første strømmen. Den anden stream er et statisk opslagsdatasæt, der indeholder biltype registreringsoplysninger.

### <a name="entry-data-stream"></a>Posten data værdistrøm

Posten datastrømmen indeholder oplysninger om biler, når de ankommer kun stationer.


| TollID | EntryTime               | LicensePlate | Stat | Gøre   | Model   | VehicleType | VehicleWeight | Kun | Mærke       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 2014-09-10 12:01:00.000 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 2014-09-10 12:02:00.000 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 2014-09-10 12:02:00.000 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 2014-09-10 12:03:00.000 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 2014-09-10 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 2014-09-10 12:05:00.000 | CVU 1007     | KØBENHAVN    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Her er en kort beskrivelse af kolonnerne:

| Kolonne | Beskrivelse |
|--------------|--------------------------------------------------------------------|
| TollID       | Kun booth-ID, der entydigt identificerer en kun stand            |
| EntryTime    | Dato og klokkeslæt for post kan kun standen i UTC |
| LicensePlate | Licens trykplade antallet af biltype                            |
| Stat        | En tilstand i USA                                           |
| Gøre         | Producenten af bil                                 |
| Model        | Model antallet af bil                                 |
| VehicleType  | Enten 1 til personbiler eller 2 til erhvervskøretøjer       |
| WeightType   | Biltype vægt i ton; 0 for personbiler                   |
| Kun         | Kun værdien i kr.                                              |
| Mærke          | E-mærket på bil, som kan automatisere betaling. tom, hvor betalingen blev foretaget manuelt |


### <a name="exit-data-stream"></a>Afslut data værdistrøm

Afslut datastrømmen indeholder oplysninger om at forlade kun stationen biler.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CVU 1007         |

Her er en kort beskrivelse af kolonnerne:


| Kolonne | Beskrivelse |
|--------------|-----------------------------------------------------------------|
| TollID       | Kun booth-ID, der entydigt identificerer en kun stand         |
| ExitTime     | Dato og klokkeslæt for Afslut af biltype fra kun standen i UTC |
| LicensePlate | Licens trykplade antallet af biltype                         |

### <a name="commercial-vehicle-registration-data"></a>Kommerciel biltype registreringsoplysninger

Dette selvstudium bruger et statisk billede af en kommerciel biltype registreringsdatabase.


| LicensePlate | RegistrationId | Udløbet |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Her er en kort beskrivelse af kolonnerne:


| Kolonne | Beskrivelse |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Licens trykplade antallet af biltype                         |
| RegistrationId     | Den biltype registrering-ID |
| Udløbet | Registreringsstatus for biltype: 0, hvis biltype registrering er aktivt, 1, hvis registreringen er udløbet                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurere miljøet for Azure Stream analyser

For at fuldføre dette selvstudium, skal du et Microsoft Azure-abonnement. Microsoft tilbyder gratis prøveversion for Microsoft Azure-tjenester.

Hvis du ikke har en Azure-konto, kan du [anmodning om en gratis prøveversion](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] For at tilmelde dig en gratis prøveversion, du skal bruge en mobilenhed, der kan modtage tekstmeddelelser og et gyldigt kreditkort.

Sørg for at følge trinnene i afsnittet "Rydde op i din Azure konto" i slutningen af denne artikel, så du kan foretage bedste brugen af $-200 ledig Azure kreditkontrol.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Klargøre Azure ressourcer, der kræves til selvstudiet

Dette selvstudium kræver to begivenhed hubs modtage *posten* , og *Afslut* datastreams. Azure SQL-Database viser resultater for Stream Analytics-job. Azure Storage gemmer referencedata om biltype registreringer.

Du kan bruge Setup.ps1 scriptet i mappen TollApp på GitHub til at oprette alle de nødvendige ressourcer. Af hensyn til tid anbefaler vi, at du kører den. Hvis du vil have mere at vide om hvordan du konfigurerer disse ressourcer i portalen Azure, kan du referere til "Konfiguration af selvstudium ressourcer i Azure portal" tillægget.

Hente og gemme de understøttende [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) mapper og filer.

Åbn et **Microsoft Azure PowerShell** -vinduet _som administrator_. Hvis du endnu ikke har Azure PowerShell, skal du følge vejledningen i [installere og konfigurere Azure PowerShell](../powershell-install-configure.md) for at installere den.

Da Windows blokerer automatisk .ps1, dll- og .exe-filer, skal du indstiller politikken udførelse af, før du kører scriptet. Kontrollér, at vinduet Azure PowerShell kører _som administrator_. Køre **sæt Udførselspolitikken ubegrænset**. Når du bliver bedt om det, kan du skrive **Y**.

![Skærmbillede af "Sæt-Udførselspolitikken ubegrænset" kører i Azure PowerShell-vinduet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Kør **Get-Udførselspolitikken** at sikre dig, at kommandoen lykkedes.

![Skærmbillede af "Get-Udførselspolitikken" kører i Azure PowerShell-vinduet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Gå til den mappe, der indeholder de scripts og generator programmet.

![Skærmbillede af "cd .\TollApp\TollApp" kører i vinduet Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Skriv **.\\ Setup.ps1** for at konfigurere din Azure-konto, oprette og konfigurere alle de nødvendige ressourcer og begynde at generere begivenheder. Scriptet henter tilfældigt et område til at oprette dine ressourcer. Hvis du vil angive eksplicit et område, kan du sende den **-placering** parameter som i følgende eksempel:

**. \\Setup.ps1-placering "Centrale USA"**

![Skærmbillede af Azure-logon-siden](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Scriptet åbnes **logonsiden** til Microsoft Azure. Angiv dine legitimationsoplysninger.

> [AZURE.NOTE] Hvis din konto har adgang til flere abonnementer, bliver du bedt om at angive det abonnementsnavn, du vil bruge i dette selvstudium.

Scriptet kan tage flere minutter at køre. Når den er færdig, skal output ligne følgende skærmbillede.

![Skærmbillede af output fra scriptet i vinduet Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Du kan også se et andet vindue, der ligner følgende skærmbillede. Dette program er at sende begivenheder til Azure begivenhed hubber, som er nødvendige for at køre selvstudiet. Så ikke stoppe programmet eller lukke vinduet, indtil du er færdig med selvstudiet.

![Skærmbillede af "Afsendelse begivenhed-hub data"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Du skal kunne se alle de ressourcer, der er oprettet i Azure portal nu. Gå til <https://manage.windowsazure.com>, og log på med legitimationsoplysningerne konto.

### <a name="azure-event-hubs"></a>Azure begivenhed Hubs

Klik på **SERVICE BUS** på venstre side af Azure portalen for at få vist begivenhed hubber, som scriptet oprettede i forrige afsnit.

![Service Bus](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Du får vist alle tilgængelige navneområder i dit abonnement. Klik på det, der starter med *tolldata* (tolldata4637388511 i vores eksempel). Klik på fanen **BEGIVENHED HUBS** .

![Begivenhed Hubs fane i portalen Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Du får vist to begivenhed hubs med navnet *posten* og *afslutte* oprettet i dette navneområde.

![Skærmbillede af "element" og "Afslut" begivenhed hubs Azure-portalen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Azure-objektbeholder til lagring

1. Klik på **lager** i venstre side af Azure portalen for at få vist objektbeholderen Azure-lager, der bruges i selvstudiet.

    ![Lagerplads menupunkt](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Klik på det, der starter med *tolldata* (tolldata4637388511 i vores eksempel). Klik på fanen **beholdere** for at få vist oprettede objektbeholderen.

    ![Under fanen beholdere i portalen Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Klik på objektbeholderen **tolldata** for at se de overførte JSON-fil, der indeholder biltype registreringsoplysninger.

    ![Skærmbillede af filen registration.json i beholderen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Azure SQL-Database

1. Klik på **SQL-DATABASER** på venstre side af Azure portalen for at få vist den SQL-database, der skal bruges i selvstudiet.

    ![SQL-databaser](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Klik på **tolldatadb**.

    ![Skærmbillede af den oprettede SQL-database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Kopiere servernavnet uden portnummeret (*servernavn*. database.windows.net, for eksempel).

## <a name="connect-to-the-database-from-visual-studio"></a>Oprette forbindelse til databasen fra Visual Studio

Brug Visual Studio til at få adgang til forespørgselsresultater i output databasen.

Oprette forbindelse til SQL-databasen (destinationen) fra Visual Studio:

1. Åbn Visual Studio, og klik derefter på **værktøjer** > **Opret forbindelse til databasen**.

2. Hvis du bliver spurgt, skal du klikke på **Microsoft SQL Server** som datakilde.

    ![Dialogboksen Skift datakilde](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. Indsæt det navn, du har kopieret i forrige afsnit fra Azure-portalen, i feltet **servernavn** (det vil sige, *servernavn*. database.windows.net).

4. Klik på **Brug SQL Server-godkendelse**.

5. Angiv **tolladmin** i feltet **brugernavn** og **123toll!** i feltet **adgangskode** .

6. Klik på **Vælg eller Angiv et navn på databasen**, og vælg **TollDataDB** som databasen.

    ![Tilføje dialogboksen forbindelse](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Klik på **OK**.

8. Åbn Server Explorer.

    ![Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Se fire tabeller i TollDataDB databasen.

    ![Tabeller i TollDataDB database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Begivenhed generator: TollApp eksempel-projekt

PowerShell-script begynder automatisk at sende hændelser ved hjælp af TollApp eksempel programmet. Du behøver ikke at udføre nogen ekstra trin.

Hvis du er interesseret i implementeringsdetaljer, kan du finde kildekode af programmet TollApp i GitHub [Eksempler/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Skærmbillede af eksempel på kode vises i Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-job

1. Åbn Stream Analytics i portalen Azure, og klik på **Ny** i det nederste venstre hjørne af siden for at oprette et nyt analytics-job.

    ![Knappen Ny](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Klik på **Hurtig Opret**. Vælg det samme område, hvor dine andre ressourcer er oprettet af scriptet.

3. Vælg **Opret ny LAGERPLADS konto** for at indstillingen **Internationale overvågning LAGERPLADS konto** og give det et entydigt navn. Azure Stream Analytics Brug denne konto til at gemme overvågning oplysninger for alle fremtidige sager.

4. Klik på **Opret STREAM ANALYTICS JOB** nederst på siden.

    ![Stream Analytics Job indstillingen Opret](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definere inputkilder

1. Klikke på den oprettede analytics sag i portalen.

    ![Skærmbillede af analytics jobbet i portalen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Klik på fanen **input** for at definere kildedataene.

    ![Fanen input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Klik på **Tilføj en INPUT**.

    ![Tilføj en Input-indstilling](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Klik på **DATASTREAM** på den første side.

    ![Indstillingen Data værdistrøm](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Klik på **BEGIVENHED HUB** på den anden side i guiden.

    ![Indstillingen begivenhed Hub](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Angiv **EntryStream** som **INPUT ALIAS**.

7. Klik på **Begivenhed Hub** rullepilen, og vælg det, der starter med "TollData" (for eksempel TollData9518658221).

8. Vælg **element** som begivenhed hub navn og **alle** som begivenhed hub politikkens navn.

    Indstillingerne for ser sådan ud:

    ![Indstillinger for begivenhed hub](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. På den næste side skal du vælge **JSON** for **BEGIVENHED SERIALISERINGSFORMATET** og **UTF8** for **kodning**.

    ![Serialisering indstillinger](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Klik på **OK** nederst på siden for at fuldføre guiden.

    ![Skærmbillede af EntryStream input i portalen Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Nu, hvor du har oprettet strømmen posten, skal du følge de samme trin for at oprette Afslut strømmen. På den tredje side i guiden, skal du sørge for at angive værdier som på følgende skærmbillede.

    ![Indstillinger for Afslut strømmen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Du har defineret to input streams:

    ![Defineret input streams i portalen Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Dernæst skal tilføje du reference datainput på blob-filen, som indeholder bil registreringsoplysninger.

11. Klik på **Tilføj INPUT**, og klik derefter på **REFERENCE DATA**.

    !["Tilføj et input" indstillinger med Reference Data, der er markeret](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Vælg kontoen lagerplads, der starter med **tolldata**på den næste side. Objektbeholdernavnet skal være **tolldata**, og navnet på blob under **Sti MØNSTER** skal være **registration.json**. Filnavnet er store og små bogstaver og skal være små bogstaver.

    ![Indstillinger for lagring af blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Vælg værdierne, som vist på følgende skærmbillede på den næste side, og klik derefter på **OK** for at fuldføre guiden.

    ![Markering af JSON til "lige serialiseringsformatet" og UTF8 for "Kodning"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Nu er al input defineret.

![Skærmbillede af de tre definerede input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definere output

1. Klik på fanen **OUTPUT** , og klik derefter på **Tilføj en OUTPUT**.

    ![Fanen Output og "Tilføj output" indstillingen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Klik på **SQL-Database**.

3. Vælg navnet på serveren, der blev brugt i afsnittet "Oprette forbindelse til databasen fra Visual Studio" i artiklen. Databasenavnet er **TollDataDB**.

4. Angiv **tolladmin** i feltet **brugernavn** **123toll!** i feltet **adgangskode** , og **TollDataRefJoin** i feltet **tabel** .

    ![Indstillinger for SQL-Database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Azure Stream analytics-forespørgsel

Fanen **forespørgsel** indeholder en SQL-forespørgsel, der transformerer indgående data.

![En forespørgsel, der er føjet til fanen forespørgsel](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Dette selvstudium forsøger at besvare flere business spørgsmål, der er relateret til opkald data og konstruktioner Stream Analytics-forespørgsler, der kan bruges i Azure Stream Analytics for at give et relevante svar.

Inden du starter første Stream Analytics tingene, Lad os gå på opdagelse nogle scenarier og forespørgselssyntaksen.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduktion til Azure Stream Analytics forespørgselssprog
-----------------------------------------------------

Lad os sige, at du har brug for til at tælle antallet af køretøjer, angiver en kun standen. Da dette er en kontinuerlig strøm af hændelser, skal du definere en "tidsperiode." Lad os redigere spørgsmål for at være "hvor mange køretøjer Skriv en kun standen alle tre minutter?". Dette kaldes ofte tumbling antallet.

Lad os se på den Azure Stream Analytics-forespørgsel, finder du svar på spørgsmålet:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Som du kan se, bruger Azure Stream Analytics et forespørgselssprog, der minder om SQL og tilføjer et par filtypenavne for at angive tid beslægtede aspekter af forespørgslen.

Læs mere om [Tidsstyring](https://msdn.microsoft.com/library/azure/mt582045.aspx) og [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstruktioner, der bruges i forespørgslen fra MSDN for flere oplysninger.

## <a name="testing-azure-stream-analytics-queries"></a>Test Azure Stream Analytics forespørgsler

Nu hvor du har skrevet din første Azure Stream Analytics-forespørgsel, er det tid at teste den ved hjælp af eksempeldatafilerne, der er placeret i mappen TollApp i følgende sti:

**.. \\TollApp\\TollApp\\Data**

Denne mappe indeholder følgende filer:

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Spørgsmål 1: Antallet af køretøjer at angive en kun stand

1. Åbn Azure-portalen, og gå til oprettede Azure Stream Analytics tingene. Klik på fanen **forespørgsel** , og Indsæt forespørgsel fra den forrige sektion.

    ![Forespørgsel, der er indsat i fanen forespørgsel](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Hvis du vil validere denne forespørgsel mod eksempeldata, skal du klikke på knappen **Test** . Gå til Entry.json, en fil med eksempeldata fra **EntryTime** begivenhed strømmen i den dialogboks, der åbnes.

    ![Skærmbillede af filen Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Kontrollere, at forespørgslens output er som forventet:

    ![Resultaterne af en test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Spørgsmål 2: Rapport Samlet tid til hver bil til passerer gennem kun standen

Den gennemsnitlige tid, der kræves i en bil til passerer gennem kun hjælper med at vurdere effektiviteten for processen og kundeoplevelsen.

For at finde den samlede tid, vil du deltage i EntryTime strømmen med ExitTime strømmen. Du kan deltage i streams i kolonnerne TollId og LicencePlate. Operatoren **deltage i** , skal du angive tidsmæssig spillerum, der beskriver tidsforskellen mellem de joinforbundne hændelser acceptable. Du kan bruge funktionen **DATEDIFF** til at angive, at hændelser, der skal være ikke mere end 15 minutter fra hinanden. Du kan også anvende funktionen **DATEDIFF** at afslutte og indtastning gange til at beregne den faktiske tid, som bruger en bil i kun stationen. Bemærk forskellen mellem brug af **DATEDIFF** , når den bruges i en **SELECT** -sætning i stedet for en **deltage i** betingelse.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Opdater forespørgsel under fanen **forespørgsel** i tingene for at teste denne forespørgsel:

    ![Opdaterede forespørgsel i fanen forespørgsel](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Klik på **Test** , og Angiv input eksempelfiler til EntryTime og ExitTime.

    ![Skærmbillede af markerede input filer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Markér afkrydsningsfeltet for at teste forespørgslen og få vist output:

    ![Output fra en test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Spørgsmål 3: Rapportere alle erhvervskøretøjer med udløbet registrering

Azure Stream Analytics kan bruge statisk snapshot af dataene til at deltage med tidsmæssig datastreams. For at vise denne egenskab, du brug det følgende eksempel spørgsmål.

Hvis en kommerciel biltype er registreret med kun virksomheden, kan det igennem kun standen uden stoppes til inspektion. Du skal bruge kommerciel biltype registrering opslagstabel til at identificere alle erhvervskøretøjer, der er udløbet registreringer.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Hvis du vil afprøve en forespørgsel ved hjælp af referencedata, skal du definere en inputkilde reference data, som du allerede har gjort.

1. Indsæt forespørgslen fanen **forespørgsel** for at teste denne forespørgsel, skal du klikke på **Test**, og Angiv de to inputkilder:

    ![Skærmbillede af markerede input filer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Få vist forespørgslens output:

    ![Skærmbillede af forespørgslens output](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Starte Stream Analytics-job


Nu er det tid at fuldføre konfigurationen og starte jobbet. Gem forespørgslen fra spørgsmål 3, hvilket giver output, der svarer til skemaet i tabellen **TollDataRefJoin** output.

Gå til jobbet **DASHBOARD**, og klik på **START**.

![Skærmbillede af knappen Start i dashboardet job](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

I den dialogboks, der åbnes, kan du ændre klokkeslættet for **START OUTPUT** til **brugerdefineret tid**. Ændre timen til en time inden det aktuelle klokkeslæt. Denne ændring sikrer, at alle begivenheder fra begivenhed hub behandles, da du i gang for at generere hændelserne i starten af selvstudiet. Nu skal du markere afkrydsningsfeltet for at starte jobbet.

![Valg af brugerdefineret tid](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Starte jobbet kan tage et par minutter. Du kan se status på siden på øverste niveau for Stream analyser.

![Skærmbillede af status for jobbet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Kontrollere resultatet i Visual Studio

1. Åbne Visual Studio Server Explorer, og højreklik på tabellen **TollDataRefJoin** .
2. Klik på **Vis tabeldata** for at få vist output fra dit arbejde.

    ![Markering af "Vis tabeldata" i Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Skalere ud Azure Stream Analytics job

Azure Stream Analytics er udviklet til elastically skala, så den kan håndtere en stor mængde data. Azure Stream Analytics-forespørgsel kan bruge en **PARTITION BY** -delsætning til at fortælle systemet, som dette trin skaleres ud. **PartitionId** er en speciel kolonne, der tilføjer systemet så de passer til ID partition input (begivenhed hub).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Stoppe det aktuelle job, opdatere forespørgslen i fanen **forespørgsel** , og Åbn fanen **SKALA** .

    **STREAMING enheder** definere mængden Beregn power, der kan modtage jobbet.

2. Flyt skyderen til 6.

    ![Skærmbillede af valg af 6 streaming enheder](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Gå til fanen **output** og ændre navnet på SQL-tabellen til **TollDataTumblingCountPartitioned**.

Hvis du starter jobbet nu kan Azure Stream Analytics distribuere arbejde på tværs af flere Beregn ressourcer og opnå bedre overførselshastighed. Vær opmærksom på, at programmet TollApp også sender hændelser, der er opdelt ved TollId.

## <a name="monitor"></a>Skærm

Under fanen **skærm** indeholder statistik om det, der kørende job.

![Skærmbillede af statistik om at køre job](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Du kan få adgang til **Handlingen logfiler** fra fanen **DASHBOARD** .

![Indstillingen "Handlingen logfiler"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Skærmbillede af handlingen logger, hvor du kan se status for job](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

For at få vist yderligere oplysninger om en bestemt hændelse, klik på hændelsen, og klik derefter på knappen **Detaljer** .

![Skærmbillede af detaljer om en markeret hændelse](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Konklusion

Dette selvstudium introduceret til Azure Stream Analytics-tjenesten. Den se, hvordan du konfigurerer input og output til Stream Analytics jobbet. Ved hjælp af scenariet kun Data forklaring selvstudiet almindelige typer problemer, der opstår i område af data i bevægelse, og hvordan de kan løses med enkle synes godt om SQL-forespørgsler i Azure Stream Analytics. Selvstudiet beskrevet SQL lokalnummer konstruktioner til at arbejde med tidsmæssig data. Det viste Sådan deltager du i datastreams, hvordan du kan forbedre datastrømmen med statisk referencedata, og hvordan du skalere ud af en forespørgsel for at opnå højere overførselshastighed.

Selvom dette selvstudium indeholder en god introduktion, er det er ikke fuldført på nogen måde. Du kan finde flere forespørgsel mønstre med SAQL sprog på [forespørgsel eksempler på almindelige Stream Analytics brugsmønstre](stream-analytics-stream-analytics-query-patterns.md).
Se [onlinedokumentation](https://azure.microsoft.com/documentation/services/stream-analytics/) mere at vide om Azure Stream analyser.

## <a name="clean-up-your-azure-account"></a>Rydde op i din Azure-konto

1. Stoppe kørslen Stream Analytics i portalen Azure.

    Scriptet Setup.ps1 opretter to begivenhed hubs og en SQL-database. Følgende anvisninger hjælper du oprydning ressourcer i slutningen af selvstudiet.

2. Skriv **i en PowerShell-vinduet.\\ Cleanup.ps1** til at starte det script, der sletter ressourcer, der bruges i selvstudiet.

    > [AZURE.NOTE] Ressourcer, der er identificeret med navnet. Kontrollér, at du nøje gennemgå hver vare før du bekræfter fjernelse.

    ![Skærmbillede af oprydningsprocessen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
