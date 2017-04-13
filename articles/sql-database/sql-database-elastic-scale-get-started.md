<properties 
    pageTitle="Komme i gang med elastiske Databaseværktøjer" 
    description="Grundlæggende gennemgang af elastiske database værktøjer funktion i Azure SQL-Database, herunder nemt for at køre eksempel app." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Komme i gang med elastiske Databaseværktøjer

Dette dokument introducerer dig til udvikler oplevelsen ved at køre appen eksempel. Opretter et enkelt delt program og udforsker vigtigste funktioner i elastiske Databaseværktøjer. Eksemplet demonstrerer funktioner på [elastiske database klientbibliotek](sql-database-elastic-database-client-library.md)

For at installere biblioteket skal du gå til [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Bemærk, at biblioteket er installeret med eksempel app beskrevet nedenfor.

## <a name="prerequisites"></a>Forudsætninger

1. Visual Studio 2012 eller nyere med C# er påkrævet. Hente en gratis version på [Visual Studio-overførsler](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2,7 eller nyere. For at få den nyeste version skal du se [Installere NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Downloade og køre appen eksempel

Den **elastiske Database med Azure SQL-Introduktion** Northwind illustrerer de vigtigste aspekter af udvikling oplevelsen for delt programmer, der bruger SQL Azure elastiske Databaseværktøjer. Det fokuserer på vigtige Brug sager for [shard tilknytte management](sql-database-elastic-scale-shard-map-management.md), [afhængige distribution af data](sql-database-elastic-scale-data-dependent-routing.md) og [forespørgsler med flere shard](sql-database-elastic-scale-multishard-querying.md). Hvis du vil downloade og køre eksemplet, skal du følge disse trin: 

1. Åbn Visual Studio, og vælg **Filer -> ny Project ->**.
2. I dialogboksen, skal du klikke på **Online**.

    ![Nyt projekt > Online][2]
3. Klik derefter på **Visual C#** under **eksempler**.

    ![Klik på Visual C#][3]
4. Skriv **elastiske db** til at søge efter stikprøvernes i søgefeltet. Titlen **Elastiske DB værktøjer til Azure SQL - Introduktion** vises.

    ![Søgefeltet][1]
 
5. Markere eksemplet, vælge et navn og en placering til det nye projekt, og tryk på **OK** for at oprette projektet.
6. Åbn **app.config** -filen i en løsning for eksempelprojektet, og følg vejledningen i filen for at tilføje din Azure SQL database-servernavnet og logonoplysningerne (brugernavn og adgangskode).
7. Oprette og køre programmet. Når du bliver spurgt, skal du tillade Visual Studio til at gendanne NuGet pakkerne løsningens. Dette vil hente den nyeste version af biblioteket elastiske database klienten fra NuGet.
8. Lege med de forskellige indstillinger for mere at vide om funktionerne klient bibliotek. Bemærk de trin, der tager programmet i konsollen output og Velkommen til at udforske koden i baggrunden.

    ![opgavefremdrift][4]

Tillykke – du har oprettet og køre din første delt program ved hjælp af elastiske Databaseværktøjer på Azure SQL-Database. Tage et hurtigt blik på de shards, stikprøvernes oprettet ved at oprette forbindelse med Visual Studio eller SQL Server Management Studio til Azure DB serveren. Du vil se nye eksempel shard databaser og en shard kort manager-database, som eksemplet har oprettet.

> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Vigtige dele af kodeeksemplet

1. **Administrere Shards og Shard kort**: koden illustrerer, hvordan du arbejder med shards, områder, og tilknytninger i filen **ShardMapManagerSample.cs**. Du kan finde flere oplysninger om dette emne her: [Shard kort administration](http://go.microsoft.com/?linkid=9862595).  
2. **Afhængige distribution af data**: Routing af transaktioner til højre shard vises i **DataDependentRoutingSample.cs**. Yderligere oplysninger finder du [Afhængige distribution af Data](http://go.microsoft.com/?linkid=9862596). 
3. **Forespørgsler over flere Shards**: forespørgsler på tværs af shards illustreret i filen **MultiShardQuerySample.cs**. Få mere at vide ved at se [Flere Shard forespørgsler](http://go.microsoft.com/?linkid=9862597).
4. **Tilføje tomme shards**: den gentagen tilføjelse af nye tomme shards er udført af koden i filen **AddNewShardsSample.cs**. Oplysninger om dette emne findes her: [Shard kort administration](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Andre elastiske skala handlinger

1. **Opdele en eksisterende shard**: muligheden for at opdele shards leveres via **opdelt Flet værktøj**. Du kan finde flere oplysninger om dette værktøj her: [opdelt Flet værktøjet oversigt](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Fletning af eksisterende shards**: Shard fletter udføres også ved hjælp af **delt Flet værktøj**. Du kan finde flere oplysninger, der refererer til: [opdelt Flet værktøjet oversigt](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Omkostninger

De elastiske Databaseværktøjer er gratis. Elastiske Databaseværktøjer ikke angive yderligere gebyrer oven på omkostninger for dit Azure brugen. 

For eksempel opretter eksempelprogrammet nye databaser. Omkostninger, afhænger af den Azure SQL DB database udgave, du vælger og Azure brugen af dit program.

Se [SQL Database priser oplysninger](https://azure.microsoft.com/pricing/details/sql-database/)til prisoplysninger.

## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om de elastiske Databaseværktøjer, i:

* [Elastiske database værktøjer dokumentationsoversigt](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Kodeeksempler: 
    -    [Elastiske DB med Azure SQL - Introduktion](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Elastiske DB med Azure SQL - integration med enhed Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Shard Elasticitet på Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [elastiske skala meddelelse](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Kanal 9: [elastiske skala oversigt Video](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Diskussionsforum: [Azure SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Måle ydeevne: [tællere i ydeevne for shard kort manager](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
