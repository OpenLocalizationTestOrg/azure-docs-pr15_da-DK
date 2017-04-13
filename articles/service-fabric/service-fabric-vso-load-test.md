<properties
    pageTitle="Indlæs test dit program ved hjælp af Visual Studio Team Services | Microsoft Azure"
    description="Lær at understrege test programmerne Azure Service-strukturen ved hjælp af Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Indlæs test dit program ved hjælp af Visual Studio Team Services

I denne artikel viser, hvordan du bruger Microsoft Visual Studio Indlæs test funktioner til at understrege test et program. Anvender en Azure Service-strukturen med høj sikkerhed service backend og en uden status service web front-end. Programmet eksempel bruges her er en fly placering simulator. Du angiver en fly-ID, afgang klokkeslæt og destination. Programmets back-end behandler anmodningerne, og front end vises på et kort fly, der opfylder kriterierne.

I følgende diagram vises det Service-strukturen-program, du vil teste.

![Diagram over programmet eksempel fly placering][0]

## <a name="prerequisites"></a>Forudsætninger
Før du går i gang, skal du gøre følgende:

- Få en Visual Studio Team Services-konto. Du kan hente et gratis i [Visual Studio Team Services](https://www.visualstudio.com).
- Hent og Installer Visual Studio 2013 eller Visual Studio-2015. I denne artikel bruges Visual Studio 2015 Enterprise edition, men Visual Studio 2013 og andre udgaver skal fungerer på samme måde.
- Installere dit program til et midlertidigt miljø. Se, [hvordan du installerer programmerne til en ekstern klynge ved hjælp af Visual Studio](service-fabric-publish-app-remote-cluster.md) vide mere om dette.
- Forstå dit program brugsmønster. Disse oplysninger bruges til at simulere Indlæs mønster.
- Forstå formålet til din Indlæs test. Dette hjælper dig med at forstå og analysere testresultater Indlæs.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Oprette og køre Web ydeevne og Indlæs Test projektet

### <a name="create-a-web-performance-and-load-test-project"></a>Oprette et Web ydeevne og Indlæs Test projekt

1. Åbne Visual Studio-2015. Vælg **filer** > **Ny** > **projekt** på menulinjen for at åbne dialogboksen **Nyt projekt** .

2. Udvid noden **Visual C#** , og vælg **Test** > **Web ydeevne og Indlæs Test project**. Navngive projektet, og vælg knappen **OK** .

    ![Skærmbillede af dialogboksen nyt projekt][1]

    Du bør se et nyt Web ydeevne og Indlæs Test projekt i Solution Explorer.

    ![Skærmbillede af Solution Explorer viser det nye projekt][2]

### <a name="record-a-web-performance-test"></a>Optage en web performance-test

1. Åbn .webtest projektet.

2. Vælg ikonet **Tilføj optagelse** for at starte en session med optagelse i din browser.

    ![Skærmbillede af ikonet Tilføj optagelse i en browser][3]

    ![Skærmbillede af knappen post i en browser][4]

3. Gå til programmet Service struktur. Panelet optagelsen skal vise webanmodninger.

    ![Skærmbillede af anmodninger om web i panelet optagelse][5]

4. Udføre en række handlinger, som du forventer, at brugerne skal udføre. Disse handlinger anvendes som et mønster til at generere afkrydsningsfeltet Indlæs.

5. Når du er færdig, skal du vælge knappen **Stop** for at stoppe optagelsen.

    ![Skærmbillede af knappen Stop][6]

    .Webtest projektet i Visual Studio skal har hentet en række forespørgsler. Dynamiske parametre erstattes automatisk. På dette tidspunkt, kan du slette eventuelle ekstra, gentagne afhængighed-anmodninger, der ikke er en del af scenariet test.

6. Gemme projektet, og vælg derefter kommandoen **Kør Test** til at køre test af web lokalt og sikre at alting virker korrekt.

    ![Skærmbillede af kommandoen Kør Test][7]

### <a name="parameterize-the-web-performance-test"></a>Standardelementet web performance-test

Du kan standardelementet test af web ved at konvertere den til en kodet web performance-test og derefter redigere koden. Som et alternativ kan du binde test af web til en dataliste, så testen gentager listen over dataene. Se [Generer og køre en kodede web performance-test](https://msdn.microsoft.com/library/ms182552.aspx) for oplysninger om, hvordan du konverterer en web performance-test til en kodede test. Se [tilføje en datakilde til en web ydeevne test](https://msdn.microsoft.com/library/ms243142.aspx) for at få oplysninger om, hvordan du binder data til en web performance-test.

I dette eksempel skal vi konvertere test af web til en kodet test, så du kan erstatte fly-ID med et genererede GUID og tilføje flere anmodninger om at sende fly, flyver til forskellige steder.

### <a name="create-a-load-test-project"></a>Oprette et projekt til test af belastning

Et Indlæs testprojekt består af en eller flere scenarier, der er beskrevet ved web performance-test og enhedstest, sammen med andre angivne Indlæs Testindstillinger. Følgende trin viser, hvordan at oprette et Indlæs testprojekt:

1. Vælg **Add**på genvejsmenuen for projektet Web ydeevne og Indlæs Test > **Indlæs Test**. Vælg knappen **Næste** for at konfigurere indstillingerne for test i guiden **Belastning teste** .

2. Vælg, om du vil en konstant bruger belastning eller indlæse en trin, som starter med nogle få brugere og øges brugerne over tid i sektionen **Indlæse mønster** .

    Hvis du har en god vurdering af mængden bruger Indlæs og vil se, hvordan det aktuelle system udfører, kan du vælge **Konstant indlæse**. Hvis dit mål er at få mere at vide om systemet udfører konsekvent under forskellige indlæse, vælge **Trin Indlæs**.

3. Vælg knappen **Tilføj** i sektionen **Teste blanding** , og vælg derefter en test, som du vil medtage i Indlæs test. Du kan bruge kolonnen **fordeling** til at angive procentdelen af samlede test køre for hver test.

4. Angiv Indlæs test varighed i sektionen **Køre indstillinger** .

    >[AZURE.NOTE] **Teste gentagelser** indstilling er tilgængelig, kun, når du kører en Indlæs test lokalt ved hjælp af Visual Studio.

5. Angiv den placering, hvor Indlæs test anmodninger genereres i sektionen **placering** af **Køre indstillinger**. Guiden bede dig om at logge på dit Team Services-konto. Log på, og vælg derefter en geografisk placering. Når du er færdig, skal du vælge knappen **Udfør** .

6. Når Indlæs test er oprettet, åbne .loadtest projektet, og vælg den aktuelle køre indstilling, som **Kører indstillinger** > **køre Settings1 [aktive]**. Dette åbner køre indstillingerne i vinduet **Egenskaber** .

7. I afsnittet **resultater** i vinduet **Indstillinger for køre** egenskaber skal indstillingen **Tidsindstilling detaljer lagerplads** har **ingen** som er standardværdien. Ændre denne værdi til **Alle individuelle detaljer** for at få flere oplysninger om afkrydsningsfeltet Indlæs testresultater. Se [Indlæse test](https://www.visualstudio.com/load-testing.aspx) kan finde flere oplysninger om, hvordan du opretter forbindelse til Visual Studio Team Services og køre en Indlæs test.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Køre Indlæs test ved hjælp af Visual Studio Team Services

Vælge kommandoen **Kør belastning teste** at starte testen køre.

![Skærmbillede af kommandoen Kør Indlæs Test][8]

## <a name="view-and-analyze-the-load-test-results"></a>Få vist og analysere testresultater indlæsning

Teste skrider frem som afkrydsningsfeltet Indlæs, ydeevne oplysningerne er afbildes i diagrammet. Du bør se noget lignende følgende graf.

![Skærmbillede af ydeevnen graph til Indlæs testresultater][9]

1. Vælg linket **hente rapport** tæt på toppen af siden. Når rapporten er downloadet, skal du vælge knappen **Vis rapport** .

    Under fanen **diagram** kan du se grafer til forskellige tællere i ydeevne. Overordnede testresultaterne vises under fanen **Oversigt** . Fanen **tabeller** viser det samlede antal sendte og mislykkede Indlæs test.

2. Vælg tal hyperlinks på **Test** > **mislykket** og **fejl** > **antal** kolonner for at se flere oplysninger om fejlen.

    Fanen **Detaljer** viser virtuelle test scenarie oplysninger om brugere og mislykkede anmodninger om. Disse data kan være nyttigt, hvis Indlæs testen indeholder flere scenarier.

Få vist [Analyse af indlæse teste resultater i visningen grafer i indlæse teste Analyzer](https://www.visualstudio.com/load-testing.aspx) kan finde flere oplysninger om visning af belastning testresultater.

## <a name="automate-your-load-test"></a>Automatisere din Indlæs test

Visual Studio Team Services indlæse Test leverer API'er for at hjælpe dig med at administrere Indlæs test og analysere resultaterne i et Team Services-konto. Du kan få flere oplysninger i [Skyen Indlæs test Rest API'er](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) .

## <a name="next-steps"></a>Næste trin
- [Overvågning og diagnosticering tjenester i en lokal computer udvikling opsætning](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
