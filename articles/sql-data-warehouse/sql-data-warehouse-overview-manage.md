<properties
   pageTitle="Administrer databaser i Azure SQL Data Warehouse | Microsoft Azure"
   description="Oversigt over administration af SQL Data Warehouse databaser. Indeholder værktøjer til administration, DWUs og skalering af ydeevne, fejlfinding i forbindelse med forespørgselsydelse, om oprettelse af god sikkerhedspolitikker og gendanne en database fra databeskadigelse eller fra en internationale afbrydelse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Administrer databaser i Azure SQL Data Warehouse

SQL Data Warehouse automatisere mange aspekter af at administrere dine databaser. For eksempel for at tilpasse ydeevnen vil du kun justere og købe det rette niveau af Beregn ressourcer og derefter lade SQL Data Warehouse gøre al arbejdet med skalering af og skalering tilbage. 

Du vil uden tvivl til at overvåge arbejdsbelastningen for at identificere dine krav til ydeevne samt fejlfinding i forbindelse med længerevarende forespørgsler. Du skal også til at udføre et par sikkerhedsopgaver for at administrere tilladelser for brugere og -logon.

Denne oversigt dækker disse aspekter af administration af SQL Data Warehouse.

- Værktøjer til administration
- Skala Beregn
- Afbryd midlertidigt og CV
- Ydeevnen bedste fremgangsmåder
- Overvågning af forespørgsel
- Sikkerhed
- Sikkerhedskopiering og gendannelse

## <a name="management-tools"></a>Værktøjer til administration

Du kan bruge en række værktøjer til at administrere databaser i SQL Data Warehouse. Når du administrerer databaser, vil du udvikle værktøjet Indstillinger for hver type opgave, du skal udføre.

### <a name="azure-portal"></a>Azure-portalen
[Azure-portalen][] er en webbaseret portal, hvor du kan oprette, opdatere, og Slet databaser og overvåge databaseressourcer. Dette værktøj er velegnet er, hvis du er lige gået i gang med Azure, administration af et lille antal data warehouse databaser, eller har brug at gøre noget hurtigt.

For at komme i gang med Azure portalen skal du se [oprette et SQL Data Warehouse (Azure portal)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools i Visual Studio
[SQL Server Data Tools][] (SSDT) i Visual Studio giver dig mulighed at oprette forbindelse til, administrere og udvikle dine databaser. Hvis du er en programmet udvikler kendskab til Visual Studio eller andre integreret udviklingsmiljøer (IDEs), kan du prøve at bruge SSDT i Visual Studio.

SSDT omfatter SQL Server Object Explorer som gør det muligt at visualisere, oprette forbindelse og udføre scripts mod SQL Data Warehouse databaser. Du kan hurtigt oprette forbindelse til SQL Data Warehouse, du kan blot klikke på knappen **Åbn i Visual Studio** i kommandolinjen når du åbner databasen detaljerne i portalen Azure klassisk.  

For at komme i gang med SSDT i Visual Studio skal du se [Forespørgslen Azure SQL Data Warehouse med Visual Studio][].

### <a name="command-line-tools"></a>Kommandolinjen værktøjer
Kommandolinjen redskaber er velegnet til at automatisere din arbejdsmængder.  PowerShell og sqlcmd er to gode måder til at automatisere processerne.  Vi anbefaler disse værktøjer til administration af et stort antal logiske servere og implementere ressourceændringer i et produktionsmiljø, som de nødvendige opgaver kan scripts og derefter automatisk.

### <a name="dynamic-management-views"></a>Administration af dynamiske visninger 

DMVs er brød og smørret til administration af SQL Data Warehouse. Næsten alle oplysninger, der overflader i portalen er afhængig af DMVs. For at se en liste over SQL Data Warehouse DMVs skal du se [SQL Data Warehouse systemvisninger][].

For at komme i gang skal du se [forbinde og forespørgsel med sqlcmd][], og [Opret en database (PowerShell)][].

## <a name="scale-compute"></a>Skala Beregn

Du kan hurtigt skalere ydeevne ud eller tilbage ved at øge eller mindske Beregn ressourcer af CPU, hukommelse og i/o-båndbredde i SQL Data Warehouse. Hvis du vil skalere ydeevne, skal du gøre blot justere antallet af data warehouse enheder (DWUs), som SQL Data Warehouse allokerer til databasen. SQL Data Warehouse hurtigt gør ændringen og håndterer alle de underliggende ændringer til hardware og software.

Få mere at vide om skalering DWUs under [skala ydeevne][].

##  <a name="pause-and-resume"></a>Afbryd midlertidigt og CV

Hvis du vil gemme omkostninger, kan du stoppe og fortsætte Beregn ressourcer efter behov. Eksempelvis hvis du ikke bruger databasen under NAT. og på weekender, kan du pause disse tidspunkter og genoptage om dagen. Du kan ikke betale for DWUs mens databasen er afbrudt midlertidigt.

Se [Pause beregne][]og [CV beregne][]kan finde flere oplysninger.

## <a name="performance-best-practices"></a>Ydeevnen bedste fremgangsmåder

Når du går i gang med en ny teknologi, opdager tip og tricks, der fungerer bedst lige fra starten kan du spare meget tid.  Du kan finde bedste fremgangsmåder i hele mange af vores emner.

I [SQL Data Warehouse bedste fremgangsmåder][]for at se mange en oversigt over de vigtigste overvejelser, når udvikling af din arbejdsbyrde.

## <a name="query-monitoring"></a>Overvågning af forespørgsel

Nogle gange en forespørgsel kører for lang tid, men du ikke sikker på, hvilket der er det pågældende element. SQL Data Warehouse har dynamiske management visninger (DMVs), som du kan bruge til at finde ud af, hvilken forespørgsel tager for lang. 

Længerevarende forespørgsler finder du [overvåge arbejdsbelastningen ved hjælp af DMVs][].

## <a name="security"></a>Sikkerhed

Hvis du vil bevare et sikkert system, skal du være på beskeden og beskytter mod alle typer uautoriseret adgang. Et sikkerhedssystem, skal du kontrollere firewallregler er på plads, så kun autoriserede IP-adresser kan oprette forbindelse. Det skal have stort godkendelse af legitimationsoplysninger. Når en bruger har forbindelse til databasen, skal brugeren kun har tilladelser til at udføre en minimale antal handlinger. For at sikre data, kan du bruge kryptering. Det er også vigtigt at have overvågning og registrering, så du kan gå begivenheder, hvis der er en hvilken som helst mistænkelig aktivitet.

Få at vide om administration af sikkerhed, afsnit til [Oversigt over sikkerhed][].

## <a name="backup-and-restore"></a>Sikkerhedskopiering og gendannelse

Har du pålidelig backps af dine data er en vigtig del af enhver anden fremstilling database. SQL Data Warehouse holder dine data sikre ved automatisk sikkerhedskopiering af dine aktive databaser med jævne mellemrum. Disse sikkerhedskopier gør det muligt at gendanne fra scenarier, hvor du har beskadiget dine data eller ved et uheld sluppet din data eller database.  For data tidsplanen for sikkerhedskopiering se opbevaringspolitik og hvordan du kan gendanne en database, [gendanne fra øjebliksbillede][].

## <a name="next-steps"></a>Næste trin
Brug af godt databasedesign principper vil gøre det nemmere at administrere dine databaser i SQL Data Warehouse. Du vil vide mere, i afsnit til [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oprette et SQL datawarehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Oprette en database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Forespørgsel Azure SQL datawarehouse med Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Oprette forbindelse og forespørgsler med sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md
[Overvåge arbejdsbelastningen ved hjælp af DMVs]: sql-data-warehouse-manage-monitor.md
[Pause Beregn]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Gendanne fra øjebliksbillede]: sql-data-warehouse-restore-database-overview.md
[CV Beregn]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Skala ydeevne]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Oversigt over sikkerhed]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse bedste fremgangsmåder]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse systemvisninger]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure-portalen]: http://portal.azure.com/
