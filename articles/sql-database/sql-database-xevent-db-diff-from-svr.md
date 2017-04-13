<properties
    pageTitle="Udvidet begivenheder i SQL-Database | Microsoft Azure"
    description="Beskriver udvidede begivenheder (XEvents) i Azure SQL-Database, og hvordan begivenhed sessioner er lidt anderledes end begivenhed sessioner i Microsoft SQL Server."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""
    tags=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="genemi"/>


# <a name="extended-events-in-sql-database"></a>Udvidede begivenheder i SQL-Database

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Dette emne forklares det, hvordan implementeringen af udvidede begivenheder i Azure SQL-Database er en smule anderledes sammenlignet med udvidede begivenheder i Microsoft SQL Server.


- SQL-Database version 12 erfaringer funktionen udvidede hændelser i andet, halvdelen af kalender 2015.
- SQL Server har haft udvidede begivenheder siden 2008.
- Funktionen række udvidede hændelser på SQL-Database er et robust undersæt af funktionerne i SQL Server.


*XEvents* er et uformelt kaldenavne, der kan bruges til 'udvidede begivenheder' i blogge og andre uformelt placeringer.


> [AZURE.NOTE] Funktionen udvidede begivenhed session er aktiveret i Azure SQL-Database på niveauet for udskrift og oktober 2015. Generelle tilgængelighed (GA) dato er endnu ikke angivet.
>
> Siden Azure [Service-opdateringer](https://azure.microsoft.com/updates/?service=sql-database) indeholder indlæg, når der er foretaget GA meddelelser.


Yderligere oplysninger om udvidede begivenheder til Azure SQL-Database og Microsoft SQL Server, kan hentes her:

- [Hurtig Start: Udvidede begivenheder i SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Udvidede begivenheder](http://msdn.microsoft.com/library/bb630282.aspx)


## <a name="prerequisites"></a>Forudsætninger


Dette emne antages det, du allerede har kendskab til:


- [Azure SQL Database-tjenesten](https://azure.microsoft.com/services/sql-database/).


- [Udvidet begivenheder](http://msdn.microsoft.com/library/bb630282.aspx) i Microsoft SQL Server.
 - Størstedelen af vores dokumentation om udvidede begivenheder gælder for både SQL Server og SQL-Database.


Forudgående visning af følgende elementer er nyttigt, når du vælger den begivenhedsfil som [destination](#AzureXEventsTargets):


- [Azure-lagringstjeneste](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Bruge Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md) - indeholder omfattende oplysninger om PowerShell og tjenesten Azure-lager.


## <a name="code-samples"></a>Kodeeksempler


Relaterede emner indeholder to kodeeksempler:


- [Ring til bufferen target kode for udvidede begivenheder i SQL-Database](sql-database-xevent-code-ring-buffer.md)
 - Kort simpelt Transact-SQL-script.
 - Vi fremhæve i kode eksempel emnet, når du er færdig med en Ring bufferen destination, bør du slipper sine ressourcer ved at udføre en alter slip `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` sætning. Senere, kan du føje en anden forekomst af Ring bufferen ved `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Begivenhed fil target kode for udvidede begivenheder i SQL-Database](sql-database-xevent-code-event-file.md)
 - Fase 1 er PowerShell til at oprette en beholder Azure-lager.
 - Fase 2 er Transact-SQL, der bruger objektbeholderen Azure-lager.


## <a name="transact-sql-differences"></a>Transact-SQL forskelle


- Når du udfører kommandoen [Opret BEGIVENHED SESSION](http://msdn.microsoft.com/library/bb677289.aspx) på SQL Server, kan du bruge delsætningen **På SERVEREN** . Men SQL-Database, du bruger delsætningen **På DATABASE** i stedet.


- Delsætningen **ON DATABASE** gælder også for [Ændre HÆNDELSEN SESSION](http://msdn.microsoft.com/library/bb630368.aspx) , og [Slip BEGIVENHED SESSION](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL-kommandoer.


- Den bedste fremgangsmåde er at medtage begivenhed session mulighed for **STARTUP_STATE = ON** i **Oprette BEGIVENHED SESSION** eller **ændre BEGIVENHED SESSION** -sætninger.
 - Værdien **= ON** understøtter en automatisk genstart efter en omstrukturering af logiske databasen på grund af en failover.


## <a name="new-catalog-views"></a>Nye katalog visninger


Funktionen udvidede hændelser understøttes af flere [katalog visninger](http://msdn.microsoft.com/library/ms174365.aspx). Katalog visninger fortælle dig, om *metadata eller definitioner* af brugeroprettede begivenhed sessioner i den aktuelle database. Visningerne, der returnerer ikke kan finde oplysninger om forekomster af aktive begivenhed sessioner.


| Navnet på<br/>få vist katalog | Beskrivelse |
| :-- | :-- |
| **sys.database_event_session_actions** | Returnerer en række for hver handling på hver enkelt hændelse af en begivenhed session. |
| **sys.database_event_session_events** | Returnerer en række for hver enkelt hændelse i en begivenhed session. |
| **sys.database_event_session_fields** | Returnerer en række for hver tilpasse-kan kolonne, der eksplicit er angivet under begivenheder og mål. |
| **sys.database_event_session_targets** | Returnerer en række for hver begivenhed destination for en begivenhed session. |
| **sys.database_event_sessions** | Returnerer en række for hver begivenhed session i SQL-Database databasen. |


I Microsoft SQL Server, lignende katalog visninger har navne, der omfatter *.server\_ * i stedet for *.database\_*. Navn mønstret er som **sys.server_event_%**.


## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Administration af nye dynamiske visninger [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx)


Azure SQL-Database indeholder [dynamiske management visninger (DMVs)](http://msdn.microsoft.com/library/bb677293.aspx) , der understøtter udvidede begivenheder. DMVs fortæller dig om *aktive* begivenhed sessioner.


| Navnet på DMV | Beskrivelse |
| :-- | :-- |
| **sys.dm_xe_database_session_event_actions** | Returnerer oplysninger om begivenhed session handlinger. |
| **sys.dm_xe_database_session_events** | Returnerer oplysninger om session begivenheder. |
| **sys.dm_xe_database_session_object_columns** | Viser konfiguration værdierne for objekter, der er bundet til en session. |
| **sys.dm_xe_database_session_targets** | Returnerer oplysninger om session destinationer. |
| **sys.dm_xe_database_sessions** | Returnerer en række for hver begivenhed session, der er fastsat til den aktuelle database. |


I Microsoft SQL Server, lignende katalog visninger er navngivne uden den * \_database* del af navnet, f.eks.:


- **sys.dm_xe_sessions**, i stedet for navn<br/>**sys.dm_xe_database_sessions**.


### <a name="dmvs-common-to-both"></a>DMVs fælles for begge


Udvidede hændelser der er flere DMVs, der er fælles for både Azure SQL-Database og Microsoft SQL Server:


- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Finde de tilgængelige udvidede begivenheder, handlinger og mål


Du kan køre en simpel SQL **Vælg** for at få vist en liste over de tilgængelige hændelser, handlinger og mål.


```
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Mål for dine SQL-Database begivenhed sessioner


Her er mål, der kan hente resultater fra din begivenhed sessioner på SQL-Database:


- [Ring til bufferen destinationsadresse](http://msdn.microsoft.com/library/ff878182.aspx) - indeholder kortvarigt begivenhed data i hukommelsen.
- [Begivenhed tæller destinationsadresse](http://msdn.microsoft.com/library/ff878025.aspx) - tæller alle hændelser, der forekommer i en udvidet begivenheder session.
- [Begivenhedsfil destinationsadresse](http://msdn.microsoft.com/library/ff878115.aspx) - skriver fuldført buffere til en objektbeholder med Azure-lager.


[Begivenhed sporing for Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API er ikke tilgængelig for udvidede begivenheder i SQL-Database.


## <a name="restrictions"></a>Begrænsninger


Der er et par sikkerhedsrelaterede forskelle befitting cloud-miljø i SQL-Database:


- Udvidede hændelser er baseret på enkelt lejer isolationsniveauet modellen. En begivenhed session i én database kan ikke få adgang til data eller begivenheder fra en anden database.

- Du kan ikke overføre en sætning, **Oprette BEGIVENHED SESSION** i konteksten af **master** databasen.


## <a name="permission-model"></a>Tilladelsesmodel


Du skal have tilladelsen **kontrol** på databasen til at overføre en sætning, **Oprette BEGIVENHED SESSION** . Databaseejer (dbo) har tilladelsen **kontrol** .


### <a name="storage-container-authorizations"></a>Lagerplads objektbeholder tilladelser


SAS tokenet du generere til din Azure-lager objektbeholder skal angive **rwl** for tilladelser. Værdien **rwl** indeholder følgende tilladelser:


- Læs
- Skrive
- Liste


## <a name="performance-considerations"></a>Overvejelser


Der findes scenarier, hvor intensivt udvidede begivenheder kan samle flere aktive hukommelse, end der er i orden for det samlede system. Derfor Azure SQL-Database systemet dynamisk angiver og justerer begrænsninger på mængden aktive hukommelse, der kan være akkumuleret ved en begivenhed session. Mange faktorer går den dynamiske beregning.


Hvis du modtager en fejlmeddelelse om, at en maksimal hukommelse blev træder i kraft, er der nogle korrigerende handlinger, du kan udføre:


- Køre færre samtidige begivenhed sessioner.


- Reducere mængden af hukommelse, du angiver på gennem din **oprette** og **ændre** -sætningerne for begivenhed sessioner på **Maks\_hukommelse** delsætningen.


### <a name="network-latency"></a>Netværksventetid


**Begivenhedsfil** destinationen vil opleve netværksventetid eller fejl, mens du gøre data til Azure-lager BLOB. Andre begivenheder i SQL-Database kan forsinkes, mens de venter på netværkskommunikation til at fuldføre. Denne forsinkelse kan reducere arbejdsbelastningen.

- Undgå at angive indstillingen **EVENT_RETENTION_MODE** til **NO_EVENT_LOSS** i begivenheden session definitioner for at reducere risikoen denne ydeevne.


## <a name="related-links"></a>Relaterede links


- [Brug af Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md).
- [Cmdletter til Azure-lager](http://msdn.microsoft.com/library/dn806401.aspx)


- [Bruge Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md) - indeholder omfattende oplysninger om PowerShell og tjenesten Azure-lager.
- [Sådan bruger du Blob-lager fra .NET](../storage/storage-dotnet-how-to-use-blobs.md)


- [OPRETTE LEGITIMATIONSOPLYSNINGER (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [OPRETTE BEGIVENHED SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Jonathan Kehayias blogindlæg om udvidede begivenheder i Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Andre emner i kode eksempel til udvidede hændelser er tilgængelige på linkene nedenfor. Du skal dog regelmæssigt se et eksempel for at se, om prøven er beregnet til Microsoft SQL Server kontra Azure SQL-Database. Derefter kan du bestemme, om mindre ændringer er nødvendige for at køre eksemplet.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
