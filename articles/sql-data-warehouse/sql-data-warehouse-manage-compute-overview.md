<properties
   pageTitle="Administrere Beregn power i Azure SQL Data Warehouse (oversigt) | Microsoft Azure"
   description="Ydeevnen Skaler ud-funktioner i Azure SQL Data Warehouse. Skalere ud ved at justere DWUs eller stoppe og fortsætte Beregn ressourcer for at gemme omkostninger."
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
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Administrere Beregn power i Azure SQL Data Warehouse (oversigt)

> [AZURE.SELECTOR]
- [Oversigt](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTEN](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

Arkitekturen i SQL Data Warehouse adskiller lager og Beregn, så hver skalere uafhængigt af hinanden. Som et resultat, kan du skalere ud af ydeevne, mens du sparer omkostninger ved at betale kun for ydeevnen, når du har brug for. 

Denne oversigt beskrives følgende ydeevne skala op-funktionerne i SQL Data Warehouse og giver anbefalinger om, hvordan og hvornår du skal bruge dem. 

- Skala beregne power ved at justere [data lager enheder (DWUs)][]
- Afbryd midlertidigt eller genoptage Beregn ressourcer

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Skala ydeevne

Du kan hurtigt skalere ydeevne ud eller tilbage ved at øge eller mindske Beregn ressourcer af CPU, hukommelse og i/o-båndbredde i SQL Data Warehouse. Hvis du vil skalere ydeevne, skal du gøre blot tilpasse antallet af [data lager enheder (DWUs)][] , som SQL Data Warehouse allokerer til databasen. SQL Data Warehouse hurtigt gør ændringen og håndterer alle de underliggende ændringer til hardware og software.

Er blevet fjernet dagene, hvor du skal undersøge hvilken type af processorer, hvor meget hukommelse eller hvilken type lagerplads du vil have høj ydeevne i dit datawarehouse. Når du indsætter Data Warehouse i skyen, skal du ikke længere håndtere lavt niveau hardwareproblemer. I stedet SQL Data Warehouse beder dig om dette spørgsmål: hvor hurtigt vil du analysere dine data? 

### <a name="how-do-i-scale-performance"></a>Hvordan jeg tilpasse ydeevnen?

For at øge eller mindske din Beregn power elastically, skal du blot ændre indstillingen [data lager enheder (DWUs)][] til din database. Ydeevnen, øger lineært, når du tilføjer flere DWU.  På højere DWU niveau skal du tilføje mere end 100 DWUs for at Bemærk forbedre betydeligt i ydeevne. For at hjælpe dig med at markere sigende Spring i DWUs, tilbyder vi de DWU niveauer, får de bedste resultater.
 
Hvis du vil justere DWUs, kan du bruge en af følgende individuelle metoder.

- [Skala beregne power med Azure-portalen][]
- [Skala beregne power med PowerShell][]
- [Skala Beregn power med REST API'er][]
- [Skala beregne power med TSQL][]

### <a name="how-many-dwus-should-i-use"></a>Hvor mange DWUs skal jeg bruge?
 
Ydeevne i SQL Data Warehouse skaleres lineært, og ændrer fra én Beregn skala til en anden (sig fra 100 DWUs til 2000 DWUs) sker der i sekunder. Det giver dig mulighed for at eksperimentere med forskellige DWU indstillinger, indtil du har fundet det pågældende scenarie passer bedst muligt.

At forstå, hvad din ideel DWU værdi er, prøv skalering op og ned, og nogle forespørgsler, der kører efter indlæsning af dine data. Da skalering er hurtig, kan du prøve et antal forskellige niveauer af ydeevne i en time eller mindre. Huske på, at SQL Data Warehouse er udviklet til at behandle store mængder data og se dens sande kapacitet til skalering, især på de større satser vi tilbyder, du skal bruge et stort datasæt som nærmer sig eller overstiger 1 TB.

Anbefalinger for at finde den bedste DWU til din arbejdsbyrde:

1. Begynd for et datawarehouse er under udvikling, ved at vælge et lille antal DWUs.  Et godt udgangspunkt er DW400 eller DW200.
2. Overvåge dit programmets ydeevne, grundlag af antallet af DWUs markeret sammenlignet med den ydeevne, du ser.
3. Finde ud af, hvor meget hurtigere eller langsommere ydeevne skal være for dig at nå til optimal ydeevneniveau for dine krav ved hvis lineære skala.
4. Øge eller mindske antallet af DWUs i forhold til hvordan meget hurtigere eller langsommere, du vil arbejdsbelastningen til at udføre. Tjenesten svare hurtigt og justere Beregn ressourcer for at imødekomme de nye DWU krav.
5. Fortsætte med at foretage justeringer, indtil du kommer til en optimal ydeevneniveau for virksomhedens behov.

### <a name="when-should-i-scale-dwus"></a>Hvornår skal jeg skalere DWUs?

Når du har brug for hurtigere resultater, øge din DWUs og betale for større ydeevne.  Når du har brug for mindre beregne power, Formindsk din DWUs og betaler kun for de ønskede oplysninger. 

Anbefalinger for, hvornår skalere DWUs:

1. Hvis dit program har en varierende arbejdsbyrde skala DWU niveauer op eller ned for at imødekomme spidser og lave punkter. Eksempelvis hvis arbejdsbelastningen spidser normalt i slutningen af måneden, Planlæg at tilføje flere DWUs under disse spidsbelastning, og klik derefter skalere, når spidsbelastning perioden er.
2. Før du udfører en handling af tunge data indlæsning eller transformation, skalere op DWUs, så dine data findes hurtigere.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pause Beregn

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

For at afbryde en database, du brug en af disse individuelle metoder.

- [Pause Beregn med Azure-portalen][]
- [Pause Beregn med PowerShell][]
- [Pause Beregn med REST API'er][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV Beregn

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

For at fortsætte en database, du brug en af disse individuelle metoder.

- [CV Beregn med Azure-portalen][]
- [CV Beregn med PowerShell][]
- [CV Beregn med REST API'er][]

## <a name="permissions"></a>Tilladelser

Skalering databasen kræver de tilladelser, der er beskrevet i [JUSTER DATABASE][].  Afbryd midlertidigt og CV kræver tilladelsen [SQL DB bidragyder][] , specifikt Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Næste trin
Se følgende artikler for at hjælpe dig med at forstå nogle ekstra KPI'er begreber:

- [Arbejdsbelastningen og på dokumentsammenfald][]
- [Oversigt over design tabel][]
- [Tabel fordeling][]
- [Tabel indeksering][]
- [Tabel partitionering][]
- [Tabel statistik][]
- [Bedste fremgangsmåder][]

<!--Image reference-->

<!--Article references-->
[data warehouse enheder (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Skala beregne power med Azure-portalen]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Skala beregne power med PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Skala Beregn power med REST API'er]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Skala beregne power med TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause Beregn med Azure-portalen]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause Beregn med PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause Beregn med REST API'er]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[CV Beregn med Azure-portalen]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[CV Beregn med PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[CV Beregn med REST API'er]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Arbejdsbelastningen og på dokumentsammenfald]: ./sql-data-warehouse-develop-concurrency.md
[Oversigt over design tabel]: ./sql-data-warehouse-tables-overview.md
[Tabel fordeling]: ./sql-data-warehouse-tables-distribute.md
[Tabel indeksering]: ./sql-data-warehouse-tables-index.md
[Tabel partitionering]: ./sql-data-warehouse-tables-partition.md
[Tabel statistik]: ./sql-data-warehouse-tables-statistics.md
[Bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB bidragyder]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ÆNDRE DATABASEN]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
