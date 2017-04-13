<properties
   pageTitle="Distribuerede transaktioner på tværs af skyen databaser"
   description="Oversigt over elastiske databasetransaktioner med Azure SQL-Database"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Distribuerede transaktioner på tværs af skyen databaser

Elastiske databasetransaktioner til Azure SQL-Database (SQL DB) gør det muligt at køre transaktioner, der strækker sig over flere databaser i SQL DB. Elastiske databasetransaktioner til SQL DB er tilgængelige for .NET-programmer ved hjælp af ADO .NET og integrere med den velkendte programming oplevelse ved hjælp af [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) klasser. Biblioteket, kan du se [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalt kræves et scenarie med sådanne normalt kører Microsoft koordinater MSDTC (Distributed Transaction). Da MSDTC ikke er tilgængelig for Platform som en-tjenesteprogrammet i Azure, har muligheden for at koordinere distribuerede transaktioner nu er direkte integreret i SQL DB. Programmer kan oprette forbindelse til en SQL-Database til Start distribuerede transaktioner, og en af databaserne kan transparent koordinere den distribuerede transaktion, som vist i følgende figur. 

  ![Distribuerede transaktioner med Azure SQL-Database ved hjælp af elastiske databasetransaktioner ][1]

## <a name="common-scenarios"></a>Almindelige scenarier

Elastiske databasetransaktioner til SQL DB kan programmer til at foretage atomisk ændringer i data, der er gemt på flere forskellige SQL-databaser. Eksempel fokuserer på klientsiden udvikling oplevelser i C# og .NET. En serversiden oplevelse ved hjælp af T-SQL er planlagt for et senere tidspunkt.  
Elastiske databasetransaktioner rettet mod følgende scenarier:

* Flere databaseprogrammer i Azure: med dette scenarie skal data er lodret opdelt på tværs af flere databaser i SQL DB så forskellige typer data, der er placeret på forskellige databaser. Nogle handlinger kræver ændringer af data, som er bevaret i to eller flere databaser. Programmet bruger elastiske databasetransaktioner til at koordinere ændringerne på tværs af databaser og sikre atomicitet.

* Delt databaseprogrammer i Azure: med dette scenarie skal data niveau bruger [elastiske Database klient biblioteket](sql-database-elastic-database-client-library.md) eller selvsigneret sharding til vandret partition data på tværs af mange databaser i SQL DB. En fremtrædende use case er det ikke nødvendigt at udføre atomisk ændringer til et delt med flere lejer-program, når ændringer dække lejere. Tænk for eksempel på en overførsel fra én lejer til en anden begge er placeret på forskellige databaser. En anden sag er detaljerede sharding at imødekomme kapacitetsbehov for en stor lejer, der også typisk angiver, at nogle atomisk handlinger skal strækker sig over flere databaser, der bruges til samme lejer. En tredje sag er atomisk opdateringer til at referere til data, der replikeres på tværs af databaser. Atomiske, overført, handlinger langs disse linjer kan nu koordineret på tværs af flere databaser med eksemplet.
Elastiske databasetransaktioner bruge totrinsudførelse for at sikre transaktion atomicitet på tværs af databaser. Er det passer til transaktioner, der involverer mindre end 100 databaser på et tidspunkt inden for en enkelt transaktion. Disse begrænsninger håndhæves ikke, men en forvente ydeevne og succes satser for elastiske databasetransaktioner til at forsinke når overstiger disse begrænsninger.


## <a name="installation-and-migration"></a>Installation og migrering

Funktionerne til elastiske databasetransaktioner i SQL DB er tilgængelige via opdateringer til .NET-biblioteker System.Data.dll og System.Transactions.dll. DLL-filerne sikre pågældende totrinsudførelse bruges, hvor det er nødvendigt for at sikre atomicitet. For at starte udviklingsprogrammer ved hjælp af elastiske databasetransaktioner skal du installere [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en nyere version. Når du kører på en tidligere version af .NET framework, mislykkes transaktioner rykke til en distribueret transaktion og en undtagelse op.

Efter installationen, kan du bruge den distribuerede transaktion API'er i System.Transactions med forbindelser til SQL DB. Hvis du har eksisterende MSDTC programmer, der bruger disse API'er, skal du blot genopbygge dine eksisterende programmer til .NET 4.6 Når du har installeret 4.6.1 Framework. Hvis dine projekter tilpasser .NET 4.6, de bruger automatisk de opdaterede DLL-filer fra den nye version af Framework og distribueret transaktion API-kald sammen med forbindelser til SQL DB lykkes nu.

Husk, at elastiske databasetransaktioner ikke kræver installation af MSDTC. I stedet administreres elastiske databasetransaktioner direkte efter og i SQL DB. Dette forenkler betydeligt skyen scenarier, da en installation af MSDTC ikke er nødvendigt at bruge distribuerede transaktioner med SQL DB. Del 4 forklarer mere detaljeret, hvordan du kan installere elastiske databasetransaktioner og kræves .NET framework sammen med dine skyen programmer til Azure.

## <a name="development-experience"></a>Udvikling oplevelse

### <a name="multi-database-applications"></a>Flere databaseprogrammer

Følgende eksempelkode bruger den velkendte programming oplevelse med .NET System.Transactions. Klassen TransactionScope opretter en omgivende transaktion i .NET. (En "omgivende transaktion" er en, der findes i den aktuelle tråd). Alle forbindelser, der er åbnet i TransactionScope deltage i posteringen. Hvis forskellige databaser deltager, sættes posteringen automatisk til en distribueret transaktion. Resultatet af transaktionen styres ved angivelse af område til at fuldføre for at angive en bekræftelse.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Delt databaseprogrammer
 
Elastiske databasetransaktioner til SQL DB understøtter også koordinere distribuerede transaktioner, hvor du bruge metoden OpenConnectionForKey i biblioteket elastiske database-klienten til at åbne forbindelser til en skaleret ud data niveau. Overvej tilfælde, hvor du har brug for at sikre transaktions konsistens for ændringer på tværs af flere forskellige sharding nøgleværdier. Forbindelser til de shards, der er vært for de forskellige sharding nøgleværdier er formidlet ved hjælp af OpenConnectionForKey. I den generelle tilfælde, kan der være forbindelserne til forskellige shards, så sikre transaktions garantier kræver en distribueret transaktion. Følgende kodeeksempel på viser denne metode. Det antages, at en variabel, kaldet shardmap bruges til at repræsentere et shard kort fra biblioteket elastiske database klient:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>.NET installation til Azure Cloud Services

Azure leverer flere tilbud til host .NET-programmer. En sammenligning af de forskellige tilbud er tilgængelig i [Azure App Service, Cloud Services og virtuelle maskiner sammenligning](../app-service-web/choose-web-site-cloud-service-vm.md). Hvis gæst OS af tilbuddet er mindre end .NET 4.6.1, der kræves til elastiske transaktioner, skal du opgradere gæst OS til 4.6.1. 

Til Azure App Services understøttes opgraderinger til gæst OS ikke i øjeblikket. Til Azure virtuelle maskiner nøjes med at logge ind på VM og kør installationsprogrammet til seneste .NET framework. I Azure Cloud Services skal du medtage installationen af en nyere version af .NET til din installation Start opgaver. Begreber og trin er beskrevet i [Installere .NET på en skybaseret tjeneste rolle](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Bemærk, at installationsprogrammet til .NET 4.6.1 kan kræver mere midlertidige lagerplads under processen selvstart af et på Azure skytjenester end installationsprogrammet til .NET 4.6. For at sikre en vellykket installation, du har brug at øge midlertidige lagerplads på din Azure skybaseret tjeneste i filen ServiceDefinition.csdef i sektionen LocalResources og miljøindstillinger på Start opgaven, som vist i følgende eksempel:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transaktioner på tværs af flere servere

Elastiske databasetransaktioner understøttes på tværs af forskellige logiske servere i Azure SQL-Database. Når transaktioner logiske server grænser, skal deltagende servere først indtastes i en fælles kommunikation relation. Når kommunikation relationen, der er oprettet, kan enhver database i en af de to servere deltage i elastiske transaktioner med databaser fra anden serveren. Med transaktioner, der strækker sig over mere end to logiske servere, skal en kommunikation relation være i stedet for en hvilken som helst par logiske servere.

Brug følgende PowerShell-cmdlet'er til at administrere relationer i tværs server-kommunikation til elastiske databasetransaktioner:

* **Ny AzureRmSqlServerCommunicationLink**: Brug denne cmdlet til at oprette en ny kommunikation relation mellem to logiske servere i Azure SQL DB. Relationen er symmetriske, hvilket betyder, at begge servere kan starte transaktioner med anden serveren.
* **Get-AzureRmSqlServerCommunicationLink**: Brug denne cmdlet til at hente eksisterende kommunikationsrelationer og deres egenskaber.
* **Fjern AzureRmSqlServerCommunicationLink**: Brug denne cmdlet til at fjerne en eksisterende kommunikation relation. 


## <a name="monitoring-transaction-status"></a>Overvågning transaktionsstatus

Bruge dynamisk Management visninger (DMVs) i SQL DB til at overvåge status og fremdrift for dine igangværende elastiske databasetransaktioner. Alle DMVs, der er relateret til transaktioner er relevante for distribuerede transaktioner i SQL DB. Du kan finde den tilsvarende liste over DMVs, der her: [transaktion relaterede dynamiske Management visninger og funktioner (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Disse DMVs er særligt nyttige:

* **sys.dm\_tran\_aktive\_transaktioner**: Viser en liste over aktive transaktioner og deres status. Kolonnen UOW (enhed for arbejde) kan identificere de forskellige underordnede transaktioner, der hører til den samme distribuerede transaktion. Alle transaktioner i den samme distribuerede transaktion udføre den samme UOW værdi. Se [DMV dokumentation](https://msdn.microsoft.com/library/ms174302.aspx) for at få mere at vide.
* **sys.dm\_tran\_database\_transaktioner**: giver ekstra oplysninger om transaktioner, som placeringen af posteringen log. Se [DMV dokumentation](https://msdn.microsoft.com/library/ms186957.aspx) for at få mere at vide.
* **sys.dm\_tran\_låse**: indeholder oplysninger om de låse, der er i øjeblikket i besiddelse af igangværende transaktioner. Se [DMV dokumentation](https://msdn.microsoft.com/library/ms190345.aspx) for at få mere at vide.

## <a name="limitations"></a>Begrænsninger 

Aktuelt gælder følgende begrænsninger for elastiske databasetransaktioner i SQL DB:

* Kun transaktioner på tværs af databaser i SQL DB understøttes. Andre [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) ressource udbydere og databaser uden for SQL DB ikke kan deltage i elastiske databasetransaktioner. Det betyder, at elastiske databasetransaktioner ikke strækker sig over lokalt SQL Server og Azure SQL-databaser. Fortsætte med at bruge MSDTC til distribuerede transaktioner lokalt. 
* Kun klient-koordineret transaktioner fra et program i .NET understøttes. Serversiden understøttelse af T-SQL som BEGYNDE DISTRIBUTED TRANSACTION er planlagt, men endnu ikke tilgængelig. 
* Kun databaser på Azure SQL DB version 12 understøttes.
* Transaktioner på tværs af WCF-tjenester understøttes ikke. Eksempelvis har du en WCF-tjenesten afskrivningsmetode, som udfører en transaktion. Sætte opkaldet i et transaktion omfang mislykkes som en [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Yderligere ressourcer

Endnu ikke er ved hjælp af elastiske databasefunktioner til Azure-programmer? Se vores [Dokumentationsoversigt](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Spørgsmål, skal du Henvend dig til os på [SQL-Database forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) og anmode om funktioner, skal du føje dem til [SQL-Database feedback-forummet](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



