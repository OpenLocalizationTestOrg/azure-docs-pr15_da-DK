<properties
    pageTitle="Planlægge opgraderingen til SQL-Database version 12 | Microsoft Azure"
    description="I denne artikel beskrives de forberedelser og begrænsninger, der er involveret i forbindelse med opgradering til version version 12 af Azure SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planlægge og forberede dig på at opgradere til SQL-Database version 12


Dette emne beskrives planlægning og forberedelser, du skal udføre for at opgradere din Azure SQL-databaser fra version V11 til version 12.


En ny [Azure Portal](https://portal.azure.com/) er tilgængelig til at understøtte opgraderingen til version 12.


I følgende tabel vises andre emner i Hjælp til version 12.


| Titel og link | Beskrivelse af indhold |
| :--- | :--- |
| [Hvad er nyt i SQL-Database version 12](sql-database-v12-whats-new.md) | I denne artikel beskrives oplysninger om hvordan version 12 viser tættere Azure SQL-Database til fuld fungerer ensartet med Microsoft SQL Server. |
| [Oprette en database i SQL-Database version 12](sql-database-get-started.md) | I denne artikel beskrives, hvordan du kan oprette en ny Azure SQL-database på version version 12. Det beskrives forskellige indstillinger ud over en tom database. |


## <a name="plan-ahead"></a>Planlægge


Følgende underafsnit beskriver læring og beslutningsprocesser, du skal løse, før du gør en handlinger mod opgradering Azure SQL-database til version 12.

### <a name="version-clarification"></a>Version præcisering


Dette dokument omhandler opgraderingen af Microsoft Azure SQL-Database fra version V11 til version 12. Mere formelt version tallene er tæt på følgende to værdier, som har rapporteret Transact-SQL-sætningen **Vælg @@version; ** :


- 12.0.2000.8 *(eller en nyere bit version 12)*
- 11.0.9228.18 *(V11)*
 - V11 blev også kaldes også SAWA v2.

### <a name="service-tier-planning"></a>Nyt niveau planlægning


Starter med version 12, understøtter Azure SQL-Database kun de service lag med navnet Basic, som Standard og Premium. Webtjenesten Web og Business understøttes ikke på version 12. Derfor, hvis du planlægger at opgradere din Azure SQL-database, der aktuelt er på internettet eller Business-webtjenesten, skal du beslutte, hvad den nye webtjenesten skal være.


Du kan finde detaljerede oplysninger om de grundlæggende, Standard og Premium service lag, i:

- [SQL-Database service niveauer](sql-database-service-tiers.md)
- [Opgradere SQL Database Web/Business databaser til ny tjeneste niveauer](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Gennemse konfigurationen geografisk gentagelse


Hvis din Azure SQL-database er konfigureret til geografisk gentagelse, skal du dokument dens aktuelle konfiguration og stop geografisk-gentagelse, før du starter forberedelse handlinger. Når opgraderingen er fuldført, skal du konfigurere din database til geografisk gentagelse.


Strategien er at lade kilden intakt og teste på en kopi af databasen.


## <a name="preparation-actions"></a>Forberedelse handlinger


Når du har fuldført din planlægning, kan du udføre følgende handling, der er beskrevet i de følgende underafsnit til forberedelse af den sidste opgradering fase.


Detaljerede beskrivelser af den sidste opgradering fase er tilgængelige i Hjælp-emner, der er knyttet til øverst i dette Hjælp-emne.


### <a name="service-tier-actions"></a>Tjeneste niveau handlinger


Tjenesten Web og Business priser niveau understøttes ikke på version 12.


Hvis din V11 Azure SQL-database er en Web- eller Business-database, indeholder opgraderingsprocessen skifte din database til en understøttet niveau. Opgraderingen anbefaler et trin, der passer til arbejdsbelastningen oversigten over din database. Dog kan du vælge alle understøttede lag, du kan lide.


Du kan reducere de nødvendige under opgraderingen trin ved at skifte databasen V11 væk fra Web-og-Business niveauet, før du starter opgraderingen. Du kan gøre dette ved hjælp af den nye [Azure-portalen](https://portal.azure.com/).


Hvis du er sikker på, hvilken webtjenesten til at skifte til, kan S2 niveauet af Standard-niveauet være en fornuftig første valg. Alle mindre lag har færre ressourcer end internettet og Business niveauet havde.


### <a name="suspend-geo-replication-during-upgrade"></a>Suspender geografisk gentagelse under opgraderingen


Opgraderingen til version 12 kan ikke køre, hvis geografisk replikering er aktiv på din database. Først skal du konfigurere din database for at stoppe med at bruge geografisk gentagelse.


Du kan konfigurere din database for at bruge igen geografisk gentagelse, når opgraderingen er fuldført.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Åbn porte på en Azure VM til klientforbindelser


Hvis klientprogrammet opretter forbindelse til SQL-Database version 12, mens din klient kører på en Azure VM (virtual machine), skal du åbne følgende port intervaller på VM:

- 11000 11999
- 14000 14999


Klik [her](sql-database-develop-direct-route-ports-adonet-v12.md) for at få mere at vide om portene version 12 SQL-Database. Portene, der er nødvendige for forbedret ydeevne i SQL-Database version 12.


##<a id="limitations"></a>Begrænsninger under og efter opgradering til version 12


### <a name="portals-for-v12"></a>Portaler for version 12


Der er tre portaler til Azure, og hver har forskellige muligheder vedrørende version 12 SQL-Database.


- [http://Portal.Azure.com/](https://portal.azure.com/)<br/>Denne Azure Portal er ny og er stadig på preview status. Denne portal er endnu ikke på hele generelle tilgængelighed (GA). Denne portal:
 - Kan administrere din version 12-server og database.
 - Databasen V11 kan opgradere til version 12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Denne Azure klassisk Portal kan være udfases. Denne portal:
 - Kan administrere din version 12-server og database.
 - Du kan *ikke* opgradere din V11 databasen til version 12.


- (http://*yourservername*. database.windows.net)<br/>Azure SQL Database klassisk portalen:
 - Kan*ikke* administrere version 12-servere.


Vi anbefaler, at oprette forbindelse til din Azure SQL-databaser med Visual Studio 2015 (VS2015). VS2015 kan bruges til opgaver som følgende:


- Du kører en sætning i Transact-SQL.
- Til at designe et skema.
- At udvikle en database, enten online eller offline.


Eller i stedet gratis kan du hente [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/), som er en komplet version af VS2015.


Du kan klikke **åben i Visual Studio** for at starte VS2015 på din computer for forbindelse til din Azure SQL-Database i ældre Azure klassisk portalen, på siden database.


Du kan bruge SQL Server Management Studio (SSMS) 2014 med [CU6](http://support.microsoft.com/kb/3031047/) til et andet alternativ til at oprette forbindelse til Azure SQL-Database. Få mere at vide er på dette blogindlæg:<br/>[Klient værktøjer opdateringer til Azure SQL-Database](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Begrænsning *under* opgraderingen til version 12


V11 databasen forbliver tilgængelig for dataadgang under opgraderingen til version 12. Der findes endnu et par begrænsninger, du skal overveje.


| Begrænsning | Beskrivelse |
| :--- | :--- |
| Varigheden af opgraderingen | Varigheden af opgraderingen afhænger af den størrelse, edition og antallet af databaser i feltet server. Opgraderingsprocessen kan køre for timer til dage for servere især til servere, der har databaser:<br/><br/>*Større end 50 GB eller<br/> * På en ikke-premium webtjenesten<br/><br/>Oprettelse af nye databaser på serveren under opgraderingen kan også øge opgradering varigheden. |
| Ingen geografisk-gentagelse | Geografisk replikering understøttes ikke på en version 12-server, der er i øjeblikket er involveret i en opgradering fra V11. |
| Databasen er kortvarigt ikke tilgængelig i sidste fase af opgraderingen til version 12 | De databaser, der hører til serverens V11 forbliver tilgængelige under opgraderingen. Forbindelsen til serveren og databaser er dog kortvarigt ikke tilgængelig i den endelige fase, når parameteren over begynder fra V11 til klar version 12.<br/><br/>Skift over periode kan variere fra 40 sekunder til 5 minutter. For de fleste servere forventes skifte over til at udføre inden for 90 sekunder. Skifte over tid øges for servere, der har et stort antal databaser, eller når databaserne har tunge Skriv arbejdsmængder. |


### <a name="limitation-after-upgrade-to-v12"></a>Begrænsning, *efter at* opgradere til version 12


| Begrænsning | Beskrivelse |
| :--- | :--- |
| Kan ikke gå tilbage til V11 | Efter en opgradering lokal, kan ikke resultatet gendannes eller annulleret. |
| Web- eller Business niveau | Når de opgradering starter, server til den nye version 12-database kan ikke længere kan genkende eller accepterer webtjenesten Web eller Business. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Eksportere og importere *efter* opgradering til version 12


Du kan eksportere eller importere en version 12-database ved hjælp af [Azure-portalen](https://portal.azure.com/). Eller du kan eksportere eller importere ved hjælp af en af følgende værktøjer:


- SQL Server Management Studio (SSMS)
- Visual Studio 2015
- Data lag Application Framework (DacFx)


Men hvis du vil bruge værktøjerne, skal du først have eller installere de seneste opdateringer for at sikre, at de understøtter de nye version 12-funktioner:


- [Kumulativ opdatering 6 til SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Februar 2015 opdatering til SQL Server-Database værktøjer i Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Februar 2015 Data lag Application Framework (DacFx) til Azure SQL-Database version 12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] De foregående links værktøj er blevet opdateret på eller efter 2 marts 2015. Vi anbefaler, at du bruger disse nyere opdateringer af disse værktøjer.


#### <a name="automated-export"></a>Automatiseret Eksportér


Automatiseret Eksportér stadig være tilgængeligt som eksempel.  Ingen klient værktøjet opdateringer er nødvendige, når du bruger automatiserede eksportere.  Hvis du vælger at få den resulterende fil og importere til en lokal server, er de værktøj opdateringer, der er anført ovenfor bruges til at importere korrekt.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Gendanne til version 12 af en slettet V11-database

I følgende scenario forklares det, at der kan gendannes et slettet V11 Azure SQL-database på en version 12 Azure SQL-databaseserver.

1. Antag, at du har en V11 Azure SQL-databaseserver. <br/> På serveren, har du en database forældede Web-og-Business service lag.
2. Du har slettet databasen.
3. Du kan opgradere serveren til version 12.
4. Derefter kan du gendanne databasen på serveren. <br/> Databasen er derved opgraderet til version 12 på niveauet S0 af Standard service-niveauet.
5. Du kan skifte databasen til en tjeneste til niveau, hvis S0 ikke er din præference.


### <a name="powershell-cmdlets"></a>PowerShell-cmdlet'er


PowerShell-cmdlet'er er tilgængelige til at starte, stoppe eller overvåge en opgradering til version 12 Azure SQL-Database fra V11 eller en anden version pre-12-version.

- [Opgradering til version 12 SQL-Database ved hjælp af PowerShell](sql-database-upgrade-server-powershell.md)

Referencedokumentation om disse PowerShell-cmdletter, i:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop-cmdlet betyder, at annullere, ikke midlertidigt. Der findes ingen måde at genoptage en opgradering, end starte igen fra begyndelsen. Stop-cmdlet rydder op og Frigør alle relevante ressourcer.


## <a name="failure-resolution"></a>Fejl ved opløsning


Hvis opgraderingen af en ulige eller anden grund ikke lykkes, forbliver databasen V11 aktiv og tilgængelig som normalt.


## <a name="related-links"></a>Relaterede links


- Microsoft Azure [Preview funktioner](https://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
