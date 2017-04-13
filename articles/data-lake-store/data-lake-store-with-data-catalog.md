<properties
   pageTitle="Registrere data fra sø datalager i Azure datakatalog | Microsoft Azure"
   description="Registrere data fra sø datalager i Azure datakatalog"
   services="data-lake-store,data-catalog" 
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrere data fra sø datalager i Azure datakatalog

I denne artikel lærer du, hvordan du integrerer Azure sø datalager med Azure datakatalog til at ændre dataene kan findes i en organisation ved at integrere det med datakatalog. Du kan finde flere oplysninger om katalogiseringsprocessen data, [Azure datakatalog](../data-catalog/data-catalog-what-is-data-catalog.md). For at forstå scenarier, hvor du kan bruge datakatalog, skal du se [Azure datakatalog almindelige scenarier](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Aktivere abonnementet Azure** Data sø Store Public Preview. Se [vejledningen](data-lake-store-get-started-portal.md#signup).

- **Azure sø datalager konto**. Følg vejledningen på [Introduktion til Azure Data sø Store ved hjælp af portalen Azure](data-lake-store-get-started-portal.md). Dette selvstudium Lad os oprette en sø datalager konto med navnet **datacatalogstore**. 

    Når du har oprettet kontoen, skal du overføre et sæt med eksempeldata til den. Dette selvstudium Lad os overføre alle .csv-filer under mappen **AmbulanceData** i [Azure Data sø ciffer lager](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Du kan bruge forskellige klienter, som [Azure Storage Explorer](http://storageexplorer.com/)til at overføre data til en blob objektbeholder.

- **Azure Data for en katalogside**. Din organisation skal allerede være en Azure datakatalog, der er oprettet for din organisation. Kun ét katalog er tilladt for hver organisation.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registrer sø datalager som en kilde til datakatalog

>[AZURE.VIDEO adcwithadl] 

1. Gå til `https://azure.microsoft.com/services/data-catalog`, og klik på **Introduktion**.

2. Log på portalen Azure datakatalog, og klik på **Publicer data**.

    ![Registrere en datakilde] (./media/data-lake-store-with-data-catalog/register-data-source.png "Registrere en datakilde")

3. Klik på **Start program**på den næste side. Dette vil hente manifestfilen til programmet på computeren. Dobbeltklik på Manifestfilen for at starte programmet.

4. Klik på **Log på**, og Angiv dine legitimationsoplysninger på siden Velkommen.

    ![Velkomstsiden] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Velkomstsiden")

5. Vælg **Azure Data sø**på siden Vælg en datakilde, og klik derefter på **Næste**.

    ![Vælg datakilde] (./media/data-lake-store-with-data-catalog/select-source.png "Vælg datakilde")

6. På den næste side skal du angive kontonavnet sø datalager, du vil registrere i datakatalog. Lad andre indstillinger som standard, og klik derefter på **Opret forbindelse**.

    ![Opret forbindelse til datakilde] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Opret forbindelse til datakilde")

7. Den næste side kan opdeles i de følgende segmenter.

    en. Feltet **Server hierarki** repræsenterer mappestrukturen sø datalager konto. **$Root** repræsenterer sø datalager konto roden, og **AmbulanceData** repræsenterer den mappe, der er oprettet i roden af kontoen sø datalager.

    b. Feltet **tilgængelige objekter** viser de filer og mapper under mappen **AmbulanceData** .

    c. **Objekter, der skal være registreret boks** viser en liste over de filer og mapper, du vil registrere i Azure datakatalog.

    ![Vis datastruktur] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Vis datastruktur")

8. I dette selvstudium skal du registrere alle filerne i kataloget. Klik på knappen (![flytte objekter](./media/data-lake-store-with-data-catalog/move-objects.png "flytte objekter")) for at flytte alle filerne til boksen **objekter skal registreres** i dette tilfælde skal. 

    Det er en recommened metode til at tilføje nogle metadata, som du senere kan bruge til hurtigt at finde dataene, fordi dataene, der skal registreres i en hele organisationen datakatalog. For eksempel kan du tilføje en e-mail-adresse til dataejeren af (for eksempel en person overførsel af dataene) eller tilføje et mærke for at identificere dataene. Skærmbilledet nedenfor viser et mærke, vi føjes til dataene.

    ![Vis datastruktur] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Vis datastruktur")

    Klik på **Registrer**.

8. Følgende skærmbillede angiver, at dataene er registreret i Datakataloget.

    ![Registreringen er fuldført] (./media/data-lake-store-with-data-catalog/registration-complete.png "Vis datastruktur")

9. Klik på **Vis Portal** for at gå tilbage til portalen datakatalog og bekræfte, at du kan nu få adgang til de registrerede oplysninger fra portalen. Hvis du vil søge dataene, kan du bruge det mærke, du har brugt under registrering dataene.

    ![Søgedata i kataloget] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Søgedata i kataloget")

10. Du kan nu udføre handlinger som tilføjelse af anmærkninger og dokumentation til dataene. Se linkene nedenfor kan finde flere oplysninger.
    * [Anmærke datakilder i datakatalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentdatakilder i datakatalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Se også

* [Anmærke datakilder i datakatalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentdatakilder i datakatalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrere sø datalager med andre Azure tjenester](data-lake-store-integrate-with-other-services.md)
