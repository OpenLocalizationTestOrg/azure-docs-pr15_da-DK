<properties 
   pageTitle="Få mere at vide Data sø analyser og U-SQL ved hjælp af Azure Portal interaktive selvstudier | Azure" 
   description="Hurtig start med læ Data sø analyser og U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Brug Azure Data sø Analytics interaktive selvstudier

Azure-portalen indeholder en interaktiv Introduktion for dig at komme i gang med Data sø analyser. I denne artikel viser, hvordan du gennemfører selvstudium til at analysere websted logfiler.


>[AZURE.NOTE]Hvis du vil gennemgå den samme selvstudium ved hjælp af Visual Studio, skal du se [analysér websted logfiler, der bruger Data sø analyser](data-lake-analytics-analyze-weblogs.md).
>Mere interaktive selvstudier der skal føjes til portalen.


Du kan finde andre selvstudier, i:

- [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Komme i gang med Data sø analyser ved hjælp af .NET SDK](data-lake-analytics-get-started-net-sdk.md)
- [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Forudsætninger**

Inden du starter selvstudiet, skal du have følgende:

- **A Data sø Analytics-konto**.  Se [Introduktion til Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md).

##<a name="create-data-lake-analytics-account"></a>Oprette Data sø Analytics-konto 

Du skal have en Data sø Analytics-konto, før du kan køre et job.

Hver Data sø Analytics-konto har en [Azure datalager sø](../data-lake-store/data-lake-store-overview.md) konto afhængighed.  Denne konto kaldes sø datalager standardkontoen.  Du kan oprette kontoen sø datalager, på forhånd, eller når du opretter kontoen Data sø analyser. I dette selvstudium opretter du kontoen sø datalager med Analytics-konto

**Oprette en Data sø Analytics-konto**

1. Log [Azure-portalen](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Klik på **Microsoft Azure** i øverste venstre hjørne til at åbne StartBoard.
3. Klik på feltet **Marketplace** .  
3. Skriv **Azure Data sø Analytics** i søgefeltet på bladet **alt** , og tryk på **ENTER**. Du skal se **Azure Data sø Analytics** på listen.
4. Klik på **Azure Data sø Analytics** på listen.
5. Klik på **Opret** i bunden af bladet.
6. Skriv eller Vælg følgende:

    ![Azure Data sø Analytics portalen blade](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Navn**: navngive Analytics-kontoen.
    - **Sø datalager**: hver Data sø Analytics-konto har en afhængige sø datalager-konto. Data sø Analytics-kontoen og afhængige sø datalager kontoen skal være placeret i den samme Azure datacenter. Følg instruktionerne til at oprette en ny sø datalager-konto, eller Vælg en eksisterende.
    - **Abonnement**: Vælg Azure abonnementet bruges til Analytics-kontoen.
    - **Ressourcegruppe**. Vælg en eksisterende Azure ressourcegruppe eller Opret en ny. Programmer består normalt af mange komponenter, for eksempel en WebApp, database, databaseserver, lager og 3 part services. Azure ressource Manager (ARM) gør det muligt at arbejde med ressourcer i dit program som en gruppe, kaldes en Azure ressourcegruppe. Du kan installere, opdatere, overvåge eller slette alle ressourcerne for dit program i en enkelt, koordineret handling. Du bruger en skabelon til installation og denne skabelon kan arbejde for forskellige miljøer som test, test- og. Du kan få overblik over fakturering for din organisation ved at få vist de akkumulerede omkostninger for hele gruppen. Du kan finde yderligere oplysninger finder [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md). 
    - **Placering**. Vælg en Azure datacenter for Data sø Analytics-kontoen. 
7. Vælg **Fastgør til Startboard**. Dette er påkrævet for at følge dette selvstudium.
8. Klik på **Opret**. Det tager dig til portalen StartBoard. Et nyt felt føjes til startsiden med det navn, der viser "Implementerer Azure Data sø Analytics". Det tager et øjeblik på at oprette en Data sø Analytics-konto. Når kontoen er oprettet, åbnes på portalen på kontoen på en ny blade.

    ![Azure Data sø Analytics portalen blade](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##<a name="run-website-log-analysis-interactive-tutorial"></a>Køre websted Log analyse interaktive selvstudium

**Sådan åbnes websted Log Analytics interaktive selvstudiet**

1. Klik på **Microsoft Azure** fra menuen til venstre til at åbne StartBoard fra portalen.
2. Klik på feltet, der er knyttet til kontoen Data sø analyser.
3. Klik på **Gennemse interaktive selvstudier** fra værktøjslinjen **Essentials** .

    ![Data sø Analytics interaktive selvstudier](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Hvis du får vist en orange advarsel sige "eksempler ikke konfigurere, klik på..." skal du klikke på **Kopiér eksempeldata** for at kopiere eksempeldataene til sø datalager standardkontoen. Interaktive selvstudiet skal bruge data til at køre.
5. Klik på **Websted Log Analytics**fra bladet **Interaktive selvstudier** . På portalen åbnes selvstudiet i en ny portalen blade.
5. Klik på **1 Introduktion** , og følg derefter vejledningen

##<a name="see-also"></a>Se også

- [Oversigt over Microsoft Azure Data sø Analytics](data-lake-analytics-overview.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Analysere websted logfiler, der bruger Azure Data sø Analytics](data-lake-analytics-analyze-weblogs.md)
