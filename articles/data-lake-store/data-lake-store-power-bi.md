<properties
   pageTitle="Analysere data i sø datalager ved hjælp af Power BI | Microsoft Azure"
   description="Bruge Power BI til at analysere data, der er gemt i Azure sø datalager"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analysere data i sø datalager ved hjælp af Power BI

I denne artikel lærer du, hvordan du bruger Power BI Desktop til at analysere og visualisere data, der er gemt i Azure sø datalager.

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure sø datalager konto**. Følg vejledningen på [Introduktion til Azure Data sø Store ved hjælp af portalen Azure](data-lake-store-get-started-portal.md). Denne artikel forudsætter, at du allerede har oprettet en sø datalager-konto, kaldet **mybidatalakestore**, og en eksempel-datafil (**Drivers.txt**), der er overført til den. Denne eksempelfil kan downloades fra [Azure Data sø ciffer lager](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Power BI Desktop**. Du kan hente det fra [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Oprette en rapport i Power BI Desktop

1. Start Power BI Desktop på din computer.

2. Klik på **Hent Data**fra båndet **Hjem** , og klik derefter på mere. I dialogboksen **Hent Data** , skal du klikke på **Azure**, skal du klikke på **Azure sø datalager**og derefter klikke på **Opret forbindelse**.

    ![Oprette forbindelse til Data sø Store] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Oprette forbindelse til Data sø Store")

3. Hvis du får vist en dialogboks om den forbindelse, der i en fase, kan du vælge for at fortsætte.

4. Angive URL-adresse til kontoen sø datalager i dialogboksen **Microsoft Azure sø datalager** , og klik derefter på **OK**.

    ![URL-adressen til sø datalager] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL-adressen til sø datalager")

5. Klik på **Log på** at logge på sø datalager konto i dialogboksen næste. Du bliver omdirigeret til din organisations logonside. Følg vejledningen for at logge på kontoen.

    ![Log på sø datalager] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Log på sø datalager")

6. Når du har logget på, skal du klikke på **Opret forbindelse**.

    ![Oprette forbindelse til Data sø Store] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Oprette forbindelse til Data sø Store")

7. Den næste dialogboks viser den fil, du har overført til kontoen sø datalager. Bekræfte oplysninger, og klik derefter på **Indlæs**.

    ![Indlæs data fra sø datalager] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Indlæs data fra sø datalager")

8. Når dataene er blevet indlæst i Power BI, får du vist følgende felter i under fanen **felter** .

    ![Importeret felter] (./media/data-lake-store-power-bi/imported-fields.png "Importeret felter")

    Hvis du vil visualisere og analysere dataene, foretrækker vi data skal være tilgængelig i følgende felter

    ![Ønsket felter] (./media/data-lake-store-power-bi/desired-fields.png "Ønsket felter")

    I de næste trin opdaterer vi forespørgslen for at konvertere de importerede data i det ønskede format.

9. Klik på **Rediger forespørgsler**fra fanen **Hjem** .

    ![Redigere forespørgsler] (./media/data-lake-store-power-bi/edit-queries.png "Redigere forespørgsler")

10. I Forespørgselseditor, under kolonnen **indhold** , skal du klikke på **binær**.

    ![Redigere forespørgsler] (./media/data-lake-store-power-bi/convert-query1.png "Redigere forespørgsler")

11. Du får vist en filikonet, der repræsenterer den **Drivers.txt** -fil, du har overført. Højreklik på filen, og klik på **CSV**.  

    ![Redigere forespørgsler] (./media/data-lake-store-power-bi/convert-query2.png "Redigere forespørgsler")

12. Du bør se output, som vist nedenfor. Dine data er nu tilgængelig i et format, som du kan bruge til at oprette visualiseringer.

    ![Redigere forespørgsler] (./media/data-lake-store-power-bi/convert-query3.png "Redigere forespørgsler")

13. Klik på **Luk og Anvend**fra båndet **Hjem** , og klik derefter på **Luk og anvende**.

    ![Redigere forespørgsler] (./media/data-lake-store-power-bi/load-edited-query.png "Redigere forespørgsler")

14. Når forespørgslen er opdateret, vises de nye felter, der er tilgængelige for visualisering under fanen **felter** .

    ![Opdateret felter] (./media/data-lake-store-power-bi/updated-query-fields.png "Opdateret felter")

15. Lad os oprette et cirkeldiagram med mulighed for at repræsentere drivere på hver enkelt by for et bestemt land. Gør du ved at foretage følgende valg.

    1. Fanen visualiseringer, skal du klikke på symbolet for et cirkeldiagram.

        ![Opret cirkeldiagram] (./media/data-lake-store-power-bi/create-pie-chart.png "Opret cirkeldiagram")

    2. De kolonner, som vi skal bruge, er **kolonne 4** (navnet på by) og **kolonne 7** (navnet på landet). Træk disse kolonner fra fanen **felter** til **visualiseringer** fane, som vist nedenfor.

        ![Oprette visualiseringer] (./media/data-lake-store-power-bi/create-visualizations.png "Oprette visualiseringer")

    3. Cirkeldiagrammet skal ligne som vist nedenfor.

        ![Cirkeldiagram] (./media/data-lake-store-power-bi/pie-chart.png "Oprette visualiseringer")

16. Ved at vælge et bestemt land på siden niveau filtrene, kan du nu se antallet af drivere i hver enkelt by i det valgte land. For eksempel under fanen **visualiseringer** under **siden niveau filtrerer**, vælge **Brasilien**.

    ![Vælg et land] (./media/data-lake-store-power-bi/select-country.png "Vælg et land")

17. Cirkeldiagrammet opdateres automatisk for at få vist driverne i byer af Brasilien.

    ![Drivere i et land] (./media/data-lake-store-power-bi/driver-per-country.png "Drivere hvert land")

18. Klik på **Gem** for at gemme Visualiseringen som en Power BI Desktop-fil fra menuen **filer** .

## <a name="publish-report-to-power-bi-service"></a>Udgive rapport til Power BI-tjenesten

Når du har oprettet visualiseringer i Power BI Desktop, kan du dele den med andre ved at publicere den til Power BI-tjenesten. Flere oplysninger om, hvordan du gør dette, under [Udgiv fra Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Se også

* [Analysere data i datalager sø ved hjælp af Data sø Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
