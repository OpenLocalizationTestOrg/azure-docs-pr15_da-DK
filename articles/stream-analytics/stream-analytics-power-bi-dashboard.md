<properties
    pageTitle="Dashboard i Power BI på Stream Analytics | Microsoft Azure"
    description="Brug et realtid streaming Power BI-dashboard til at samle business intelligence og analysere store mængder data fra en Stream Analytics-job."
    keywords="Analytics dashboardet realtid dashboard"
    services="stream-analytics"
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

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Streame analyser og Power BI: et realtid analytics dashboard til streaming data

Azure Stream Analytics kan du benytte en af de foranstillede business intelligence-værktøjer, Microsoft Power BI. Lær at bruge Azure Stream Analytics til at analysere store mængder, streaming data og få indsigt i et realtid Power BI analytics dashboard.

Bruge [Microsoft Power BI](https://powerbi.com/) til at oprette et live dashboard hurtigt. [Se en video, som illustrerer dette scenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

I denne artikel kan få mere at vide hvordan oprette dine egne brugerdefinerede business intelligence-værktøjer ved hjælp af Power BI som output til din Azure Stream Analytics-job og bruge et realtid dashboard.

## <a name="prerequisites"></a>Forudsætninger

* Microsoft Azure-konto
* Et input til Stream Analytics jobbet til forbruge streaming data fra. Stream Analytics accepterer input fra Azure begivenhed hubber eller Azure Blob-lager.  
* Arbejds- eller skolekonto konto til Power BI

## <a name="create-azure-stream-analytics-job"></a>Oprette Azure Stream Analytics-job

Klik på **Ny, datatjenester, Stream Analytics, Hurtig oprettelse**fra [Azure klassisk Portal](https://manage.windowsazure.com).

Angiv følgende værdier, klik derefter på **Opret Stream Analytics sag**:

* **Jobbet navn** - Angiv et navn til jobbet. For eksempel, **DeviceTemperatures**.
* **Område** - Vælg det område, hvor du vil jobbet findes. Overvej at placere jobbet og begivenhed hub i den samme område for at sikre optimal ydeevne og undgå at skulle betale omkostningerne for overførsel af data mellem områder.
* **Lagerplads konto** – Vælg den konto, lagerplads, du vil bruge til at lagre overvågningsdata for alle Stream Analytics-job i dette område, der kører. Du har mulighed for at vælge en eksisterende lagerplads-konto eller oprette en ny.

Klik på **Stream Analytics** i ruden til venstre med listen over værdistrøm Analytics-job.

![graphic1][graphic1]

> [AZURE.TIP] Vises den nye sag med statussen **Ikke startet**. Bemærk, at knappen **Start** i bunden af siden er deaktiveret. Dette er forventede funktionsmåde, som du skal konfigurere tingene input, output, forespørgsel, og så videre før du kan starte jobbet.

## <a name="specify-job-input"></a>Angive job input

I dette selvstudium, vi antager, du bruger begivenhed Hub som input med JSON serialisering og UTF-8-kodning.

* Klik på navnet på jobbet.
* Klik på **input** fra toppen af siden, og klik derefter på **Tilføj Input**. Dialogboksen, der åbnes fører dig gennem en række trin til konfiguration af dit input.
*   Vælg **datastream**, og klik derefter på knappen til højre.
*   Vælg **Begivenhed-Hub**, og klik derefter på knappen til højre.
*   Skriv eller Vælg følgende værdier på den tredje side:
  * **Indtast Alias** - Angiv et fuldt navn for denne sag, der er indtastet. Bemærk, at du vil bruge dette navn i forespørgslen senere.
  * **Begivenhed Hub** - Hvis hubben begivenhed, du har oprettet er i samme abonnement som Stream Analytics jobbet, Vælg det navneområde, begivenhed hub er i.
*   Hvis begivenheden-hub er i et andet abonnement, Vælg **Brug begivenhed Hub fra et andet abonnement** , og angiv oplysninger om **Tjenesten Bus Namespace**, **Begivenhed Hub navn**, **Begivenhed Hub politikkens navn**, **Begivenhed Hub politiknøgle**og **Begivenhed Hub Partition Tæl**manuelt.

> [AZURE.NOTE]  Dette eksempel bruger standardantallet af partitioner, som er 16.

* **Navn på denne begivenhed Hub** - Vælg navnet på den Azure begivenhed Hub, du har.
* **Navn på begivenhed Hub politik** - Vælg begivenhed Hub politikken for den begivenhed-Hub, du bruger. Sørg for, at denne politik har administrere tilladelser.
*   **Begivenhed Hub Consumer gruppe** – du kan lade tomme eller angive en forbruger-gruppe, du har på din begivenhed Hub. Bemærk, at hver consumer gruppe af en begivenhed Hub kan have kun 5 læsere ad gangen. Beslutte, gruppen højre consumer til dit arbejde i overensstemmelse hermed. Hvis feltet er tomt, anvendes consumer standardgruppen.

*   Klik på knappen til højre.
*   Angiv følgende værdier:
  * **Begivenhed serialiseringsfunktion Format** - JSON
  * **Kodning** - UTF8
*   Klik på knappen Kontrollér, at føje denne kilde og Kontrollér, at Stream Analytics kan oprette forbindelse til begivenhed-Hub.

## <a name="add-power-bi-output"></a>Tilføje Power BI output

1.  Klik på **Output** fra toppen af siden, og klik derefter på **Tilføj Output**. Du vil se Power BI er angivet som en outputindstilling.

    ![graphic2][graphic2]  

2.  Vælg **Power BI** , og klik derefter på den højre knap.
3.  Du får vist et skærmbillede i stil med følgende:

    ![graphic3][graphic3]  

4.  I dette trin skal du angive en arbejds- eller skolekonto konto Stream Analytics job output til. Hvis du allerede har Power BI-konto, Vælg **Godkend nu**. Hvis ikke, skal du vælge **Tilmeld dig nu**. [Her er en god blog gennemgang af oplysninger om Power BI-Tilmeld dig](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Næste får du vist et skærmbillede i stil med følgende:

    ![graphic4][graphic4]  

Angiv værdier som nedenfor:

* **Output Alias** – du kan anbringe en hvilken som helst output alias, der er nemt at referere til. Dette output alias er særligt nyttige, hvis du beslutter at have flere output til dit arbejde. Det er tilfældet, skal du referere til denne output i forespørgslen. For eksempel vi bruge værdien output alias = "OutPbi".
* **Navn på datasæt** - Angiv et navn til datasættet, du vil dit Power BI output skal have. Lad os bruge eksempelvis "pbidemo".
*   **Tabelnavn** - giver et tabelnavn under datasættet af dit Power BI-output. Antag, at vi kalder "pbidemo". Power BI output fra Stream Analytics job muligvis i øjeblikket kun én tabel i et datasæt.
*   **Arbejdsområde** – Vælg et arbejdsområde i din Power BI-lejer som datasættet oprettes under.

>   [AZURE.NOTE] Du bør ikke eksplicit oprette dette datasæt og tabel i Power BI-kontoen. De oprettes automatisk når du starter Stream Analytics tingene og jobbet starter Pumpende output til Power BI. Hvis din sag forespørgsel ikke returnerer nogen resultater, oprettes datasæt og tabel ikke.

*   Klik på **OK**, **Test forbindelse** , og nu du eksporterer serverens konfiguration er fuldført.

>   [AZURE.WARNING] Også være opmærksom på, at hvis Power BI allerede har en dataset og en tabel med det samme navn som den, du har angivet i dette Stream Analytics-job skal de eksisterende data vil blive overskrevet.


## <a name="write-query"></a>Skrive forespørgsel

Gå til fanen **forespørgsel** i dit arbejde. Skriv din forespørgsel, som output, du vil på dit Power BI. Det kan for eksempel være noget som følgende SQL-forespørgslen:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Start dit arbejde. Bekræfte, at din begivenhed hub modtager begivenheder og forespørgslen genererer de forventede resultater. Hvis din forespørgsel producerer 0 rækker, oprettes Power BI-datasæt og tabeller ikke automatisk.

## <a name="create-the-dashboard-in-power-bi"></a>Oprette dashboard i Power BI

Gå til [Powerbi.com](https://powerbi.com) og login med din arbejds- eller skolekonto. Hvis forespørgslen Stream Analytics job viser resultater, kan du se er allerede har oprettet dit datasæt:

![graphic5][graphic5]

Gå til indstillingen Dashboards til oprettelse af dashboardet, og Opret et nyt Dashboard.

![graphic6][graphic6]

I dette eksempel, vi vil etiket det "Demo Dashboard".

Klik nu på de datasæt, der er oprettet af Stream Analytics tingene (pbidemo i eksemplet aktuelle). Du føres til en side til at oprette et diagram oven på dette dataset. Følgende er men et eksempel på de rapporter, kan du oprette:

Vælg Σ temp og tilpasse tidspunktet for felter. De går automatisk til værdi og akse til diagrammet:

![graphic7][graphic7]

Med dette får du automatisk et diagram som nedenfor:

![graphic8][graphic8]

I sektionen værdi, klik på på rullelisten ned til temp og vælge **Gennemsnitlig** temperatur og i diagrammet, klikke på **visualisering** og vælge **kurvediagram**:

![graphic9][graphic9]

Nu får du et kurvediagram af gennemsnit over tid.  Ved hjælp af indstillingen Fastgør som nedenfor, kan du fastgøre dette til dit dashboard, som du tidligere har oprettet:

![graphic10][graphic10]

Nu når du får vist dashboard med denne fastgjorte rapport, får du vist rapporten ved at opdatere i realtid. Prøv at ændre dataene i dine begivenheder – samling temp eller noget, og du får vist realtid effekten af at, afspejles i dit dashboard.

Bemærk, at dette selvstudium se, hvordan du opretter, men én type diagram for et datasæt. Power BI kan hjælpe dig med at oprette andre kunde business intelligence-værktøjer til din organisation. Se videoen [Introduktion til Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) til et andet eksempel på et Power BI-dashboard.

Gennemse den [Power BI sektion](stream-analytics-define-outputs.md#power-bi) af [forstå Stream Analytics skriver](stream-analytics-define-outputs.md "om Stream Analytics skriver")for yderligere oplysninger om konfiguration af et Power BI-output og til at bruge Power BI-grupper. En anden nyttige ressource mere at vide om at oprette Dashboards med Power BI er [Dashboards i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Begrænsninger og bedste praksis

Power BI anvender både på dokumentsammenfald og overførselshastighed begrænsninger, som beskrevet her: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI priser")

På grund af dem, der er lander Power BI selve mest naturligt sager, hvor Azure Stream Analytics driver en Indlæs reduktion af vigtige data.
Vi anbefaler, at bruge TumblingWindow eller HoppingWindow for at sikre, at dataene opslagsnål ville være højst 1 push/andet og, at din forespørgsel lander i overførselshastighed kravene – du kan bruge følgende ligning til at beregne værdien for at give dit vindue i sekunder:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Som eksempel – hvis du har 1.000 enheder, der sender data hver andet, du er på den Power BI Pro SKU, der understøtter 1.000.000 rækker/time, og du vil have vist gennemsnitlige data hver enhed i Power BI kan du gøre højst en opslagsnål hver 4 sekunder per enhed (som vist nedenfor):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Hvilket betyder, at vi vil ændre den oprindelige forespørgsel til at:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Opdater PowerBI view

Et almindelige spørgsmål er "Hvorfor ikke dashboardet automatisk opdatering i PowerBI?".

Hvis du vil arkivere dette, i PowerBI benytter Q & A og stille et spørgsmål som "Maksimumværdi ved temp hvor tidsstempel i dag er" og Fastgør dette felt til dashboard.

### <a name="renew-authorization"></a>Forny godkendelse

Du skal godkende kontoen Power BI igen, hvis adgangskoden er ændret siden tingene blev oprettet eller senest godkendt. Hvis Multi-Factor Authentication (MFA) er konfigureret på din Azure Active Directory (AAD) lejer, skal du også forny Power BI godkendelse hver 2 uger. Et symptom på dette problem er ingen job output og "Godkend brugerfejl" i loggene handling:

![graphic12][graphic12]

Hvis et job forsøger at starte, mens Tokenet er udløbet, opstår der en fejl på samme måde, og start af job mislykkes. Fejlen ser omtrent sådan nedenfor:

![PowerBI valideringsfejl](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Stop igangværende tingene for at løse dette problem, og gå til din Power BI-output.  Klik på linket "Forny godkendelse", og genstart tingene fra den sidste ikke længere tid at undgå tab af data.

![PowerBI validering fornyelse](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Der vises en grøn besked i området godkendelse, når tilladelsen opdateres med Power BI:

![PowerBI validering fornyelse](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
