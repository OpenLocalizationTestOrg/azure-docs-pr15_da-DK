<properties 
   pageTitle="Komme i gang med sø datalager | Azure" 
   description="Bruge portal til at oprette en sø datalager konto og udføre grundlæggende handlinger i sø datalager" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introduktion til Azure Data sø Store ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lær at bruge Azure-portalen til at oprette en Azure datalager sø konto og udføre grundlæggende handlinger, sådan som oprette mapper, upload og download-datafiler, skal du slette din konto, osv. Du kan finde flere oplysninger om sø datalager, [Oversigt over Azure sø datalager](data-lake-store-overview.md).

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Du lære hurtigt med videoer?

Se de følgende videoer for at komme i gang med sø datalager.

* [Oprette en sø datalager-konto](https://mix.office.com/watch/1k1cycy4l4gen)
* [Administrere data i datalager sø Brug Stifinder Data](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Oprette en Azure datalager sø-konto

1. Log den nye [Azure-portalen](https://portal.azure.com).

2. Klik på **Ny**, skal du klikke på **Data + lagerplads**, og klik derefter **Azure Data sø Store**. Læs oplysningerne i bladet **Azure sø datalager** , og klik derefter på **Opret** i det nederste venstre hjørne af bladet.

3. I bladet **Ny datalager sø** indeholder værdierne, som vist i skærmbilledet nedenfor:

    ![Opret en ny Azure datalager sø-konto] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Opret en ny Azure Data sø-konto")

    - **Abonnement**. Vælg det abonnement, under hvilket du vil oprette en ny sø datalager-konto.
    - **Ressourcegruppe**. Vælg en eksisterende ressourcegruppe, eller klik på **Opret en ressourcegruppe** for at oprette en. En ressourcegruppe er en objektbeholder, der indeholder relaterede ressourcer for et program. Du kan finde flere oplysninger, se, hvordan [Azure ressourcegrupper](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Placering**: Vælg en placering, hvor du vil oprette kontoen sø datalager.

4. Vælg **Fastgør til Startboard** , hvis du vil kontoen sø datalager at kunne åbnes fra Startboard.

5. Klik på **Opret**. Hvis du vælger at fastgøre kontoen til startboard, kommer du tilbage til startboard, og du kan se statussen for din sø datalager konto klargøring. Når kontoen sø datalager er klargjort, vises bladet konto.

6. Udvide det **grundlæggende om** ned for at se oplysningerne om kontoen sø datalager, som ressourcen gruppere det er en del af, placering, osv. Klik på ikonet **Hurtig Start** for at få vist links til andre ressourcer, der er relateret til sø datalager.

    ![Din Azure sø datalager konto] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Din Azure Data sø konto")

## <a name="createfolder"></a>Oprette mapper i Azure datalager sø konto

Du kan oprette mapper under kontoen sø datalager til at administrere og gemme data.

1. Åbn den sø datalager-konto, du lige har oprettet. Klik på **Gennemse**i venstre rude, klik på **Sø datalager**, og klik på kontonavnet under hvilket du vil oprette mapper fra bladet sø datalager. Hvis du fastgjort kontoen til startboard, skal du klikke på konto-feltet.

2. Klik på **Data Explorer**i din sø datalager konto blade.

    ![Opret mapper i sø datalager konto] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Opret mapper i sø datalager konto")

3. I din sø datalager konto blade, klik på **Ny mappe**, Skriv et navn til den nye mappe og klik derefter på **OK**.
    
    ![Opret mapper i sø datalager konto] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Opret mapper i sø datalager konto")
    
    Den nyoprettede mappe vises i bladet **Data Explorer** . Du kan oprette indlejrede mapper op til alle niveauer.

    ![Opret mapper i Data sø konto] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Opret mapper i Data sø konto")


## <a name="uploaddata"></a>Overføre data til Azure sø datalager konto

En Azure datalager sø konto direkte på rodniveau eller til en mappe, du har oprettet i kontoen, kan du overføre dine data. Følg trinnene for at overføre en fil til en undermappe fra bladet **Data Explorer** i skærmbilledet nedenfor. I dette skærmbillede er filen overført til en undermappe, der vises i brødkrummer (markeret i en rød boks).

Hvis du leder efter nogle eksempeldata for at overføre, kan du få mappen **Ambulance Data** fra [Azure Data sø ciffer lager](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Overføre data] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Overføre data")


## <a name="properties"></a>Egenskaber og handlinger, der er tilgængelige på de lagrede data

Klik på den nyligt tilføjede fil for at åbne bladet **Egenskaber** . De egenskaber, der er knyttet til filen og de handlinger, du kan udføre på filen er tilgængelige i denne blade. Du kan også kopiere den fulde sti til filen i kontoen Azure sø datalager, fremhævet i det røde felt i skærmbilledet nedenfor.

![Egenskaber på dataene] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaber på dataene")

* Klik på **eksempel** for at få vist et eksempel på filen direkte fra browseren. Du kan angive formatet for eksempel samt. Klik på **Vis udskrift**, skal du klikke på **Format** i bladet **Filgennemgang** og i bladet **Preview filformat** angive indstillinger som antallet af rækker for at få vist, kodning bruge afgrænser brug, osv.

  ![Preview filformat] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Preview filformat")

* Klik på **Hent** for at hente filen til din computer.

* Klik på **Omdøb fil** for at omdøbe filen.

* Klik på **Slet filer** for at slette filen.


## <a name="secure-your-data"></a>Sikre dine data

Du kan sikre de data, der er gemt i Azure datalager sø-kontoen ved hjælp af Azure Active Directory og adgangskontrol (ACLs). Flere oplysninger om, hvordan du gør dette, under [sikring af data i Azure sø datalager](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Slet Azure sø datalager konto

Hvis du vil slette en Azure sø datalager konto fra din sø datalager blade, skal du klikke på **Slet**. For at bekræfte handlingen, bliver du bedt om at angive navnet på den konto, du vil slette. Skriv navnet på kontoen, og klik derefter på **Slet**.

![Slet Data sø konto] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Slet Data sø konto")


## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Access diagnosticeringslogfiler for sø datalager](data-lake-store-diagnostic-logs.md)
