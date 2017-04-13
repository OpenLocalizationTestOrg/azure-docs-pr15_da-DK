<properties
    pageTitle="Kopiér data fra Blob-lager til SQL-Database | Microsoft Azure"
    description="Dette selvstudium viser, hvordan du bruger kopi aktivitet i en Azure Data Factory pipeline til at kopiere data fra Blob-lager til SQL-database."
    Keywords="BLOB sql, blob-lager, kopiere data"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Kopiér data fra Blob-lager til SQL-Database ved hjælp af Data Factory 
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kopiere guiden](data-factory-copy-data-wizard-tutorial.md)
- [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure ressourcestyring skabelon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


I dette selvstudium, kan du oprette en data fabrik med en pipeline til at kopiere data fra Blob-lager til SQL-database.

Kopiér aktiviteten udfører på flytning af data i Azure Data Factory. Den drives af en globalt tilgængelig tjeneste, som kan kopiere data mellem forskellige data butikker i et sikkert, pålideligt og SVG måde. Se [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel for at få mere at vide om den kopi aktivitet.  

> [AZURE.NOTE] Et detaljeret overblik over Data Factory-tjenesten, findes i artiklen [Introduktion til Azure Data Factory](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Forudsætninger for selvstudiet
Inden du starter selvstudiet, skal du have følgende:

- **Azure-abonnement**.  Hvis du ikke har et abonnement, kan du oprette en gratis prøveversion konto på blot et par minutter. Artiklen [Gratis prøveversion](http://azure.microsoft.com/pricing/free-trial/) få mere at vide.
- **Azure-lager-konto**. Du kan bruge blob-lager som en **datakilde** datalager i dette selvstudium. Hvis du ikke har en konto til Azure-lager, artiklen [Opret en konto lagerplads](../storage/storage-create-storage-account.md#create-a-storage-account) for trin til at oprette en.
- **Azure SQL-Database**. Du kan bruge en Azure SQL-database som en **destination** datalager i dette selvstudium. Hvis du ikke har en Azure SQL-database, som du kan bruge i selvstudiet, se, [hvordan du kan oprette og konfigurere en Azure SQL-Database](../sql-database/sql-database-get-started.md) til at oprette en.
- **SQL Server 2012-2014 eller Visual Studio-2013**. Du bruger SQL Server Management Studio eller Visual Studio til at oprette en eksempeldatabasen og til at få vist resultatdata i databasen.  

## <a name="collect-blob-storage-account-name-and-key"></a>Indsamle blob storage kontonavn og nøgle 
Du har brug for det kontonavn og kontonøgle af kontoen Azure-lager til at udføre dette selvstudium. Bemærk **kontonavn** og **kontonøgle** til kontoen Azure-lager.

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klikke på **flere tjenester** i menuen til venstre, og vælg **Lagerplads konti**.

    ![Gennemse - lagerplads konti](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. Vælg den **Azure lagerplads konto** , du vil bruge i dette selvstudium i bladet **Lagerplads konti** .
4. Vælg **hurtigtaster** link under **Indstillinger**.
5.  Klik på knappen **Kopiér** (billede) ud for **kontonavn lager** tekstboks og Gem og Indsæt det et sted, hvor (for eksempel: i en tekstfil).
6. Gentag det foregående trin til at kopiere eller note ned **nøgle1**.
    
    ![Lagerplads adgangsnøgle](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Luk alle blade ved at klikke på **X**.

## <a name="collect-sql-server-database-user-names"></a>Samle SQL server, database, brugernavne
Du skal bruge navne i Azure SQL server, database og bruger for at udføre dette selvstudium. Bemærk ned navnene på **servernavn**, **database**og **bruger** til Azure SQL-database.

1. Klik på **flere tjenester** til venstre i **Azure-portalen**, og vælg **SQL-databaser**.
2. Vælg den **database** , du vil bruge i dette selvstudium i **SQL-databaser blade**. Bemærk **Databasenavn**ned.  
3. Klik på **Egenskaber** under **Indstillinger**i bladet **SQL-database** .
4. Bemærk ned værdierne for **SERVERNAVNET** og **SERVER administrator logon**.
5. Luk alle blade ved at klikke på **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Tillad Azure services til at få adgang til SQL server 
Sørg for **, at **Tillad adgang til Azure services** indstilling slået til for din Azure SQL server så Data Factory-tjenesten kan få adgang til din Azure SQL-server** . For at bekræfte, og Aktivér denne indstilling skal du gøre følgende:

1. Klik på **flere tjenester** hub til venstre, og klik på **SQL-servere**.
2. Vælg serveren, og klik på **Firewall** under **Indstillinger**. 
4. Klik **på** for at **Tillad adgang til Azure tjenester**i bladet **Firewall-indstillinger** .
5. Luk alle blade ved at klikke på **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Forberede Blob-lager og SQL-Database 
Nu forberede Azure blob-lager og Azure SQL-database i dette selvstudium ved at benytte følgende fremgangsmåde:  

1. Start Notesblok, indsætte følgende tekst og gemme den som **emp.txt** til **C:\ADFGetStarted** mappe på harddisken.

        John, Doe
        Jane, Doe

2. Bruge funktioner såsom [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) til at oprette objektbeholderen **adftutorial** og overføre filen **emp.txt** til objektbeholderen.

    ![Azure-lager Explorer. Kopiér data fra Blob-lager til SQL-database](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Brug følgende SQL-script til at oprette tabellen **emp** i Azure SQL-Database.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Hvis du har SQL Server 2012-2014 installeret på din computer:** skal du følge vejledningen fra [trin 2: oprette forbindelse til SQL-Database på administrere Azure SQL-databasen ved hjælp af SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) artikel for at oprette forbindelse til din Azure SQL-server og køre scriptet SQL. I denne artikel bruger [Klassisk Azure-portalen](http://manage.windowsazure.com), ikke [nye Azure portal](https://portal.azure.com), til at konfigurere firewall til en Azure SQL-server.

    Hvis kunden ikke har tilladelse til at få adgang til Azure SQL-serveren, skal du konfigurere firewall til din Azure SQL server for at tillade adgang fra din computer (IP-adresse). Se, hvordan du konfigurerer firewallen for din Azure SQL server- [i denne artikel](../sql-database/sql-database-configure-firewall-settings.md) .

Du har fuldført forudsætningerne. Du kan oprette en data fabrik ved hjælp af en af følgende måder. Klik på en af fanerne øverst eller linkene nedenfor til at udføre selvstudiet.     

- [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Kopiere guiden](data-factory-copy-data-wizard-tutorial.md)
