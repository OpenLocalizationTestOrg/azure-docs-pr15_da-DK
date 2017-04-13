<properties
   pageTitle="Fordelt og distribueres Tabelindstillinger stort omfang parallelle behandling (MPP) systemer for SQL Data Warehouse og Parallel Data Warehouse | Microsoft Azure"
   description="Få mere at vide, hvordan data distribueres til stort omfang parallelle behandling (MPP) og indstillinger til at distribuere tabeller i Azure SQL Data Warehouse og Parallel Data Warehouse."
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
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Distribuerede data og fordelt tabeller til stort omfang parallelle behandling (MPP)

Få mere at vide, hvordan brugerdata er distribueret i Azure SQL Data Warehouse og Parallel Data Warehouse, som er Microsofts stort omfang parallelle behandling (MPP) systemer. Designe dit datawarehouse bruge distribuerede data effektivt hjælper dig med at opnå forespørgslen behandling af fordelene ved MPP-arkitektur. Et par database designvalg kan have stor indflydelse på forbedring af Forespørgselsydeevnen.  

>[AZURE.NOTE] Azure SQL Data Warehouse og Parallel Data Warehouse Brug samme designet med stort omfang parallelle behandling (MPP), men de har nogle forskelle på grund af den underliggende platform. SQL Data Warehouse er en Platform som en tjeneste (PaaS), der kører på Azure. Parallel Data Warehouse kører på Analytics Platform System (APS) som er en lokal enhed, der kører på Windows Server.

## <a name="what-is-distributed-data"></a>Hvad er distribueret data?

I SQL Data Warehouse og Parallel Data Warehouse refererer distribuerede data til brugerdata, der er gemt på flere placeringer på tværs af MPP-system. Hver af disse placeringer fungerer som en uafhængig lager og behandling af opgaver, der kører forespørgsler på del af dataene. Distribuerede data er vigtige for at køre forespørgsler parallelt at opnå høj forespørgselsydelse.

Hvis du vil distribuere data, tildeler datawarehouse hver række i en brugertabel til én fordelt placering.  Du kan distribuere tabeller med en hash-fordeling eller en round robin-metode. Metoden fordeling er angivet i sætningen CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Hash-distribueret tabeller
  
I følgende diagram vises, hvordan en fuld (ikke-distribueret tabel) gemmes som en hash-distribueret tabel. En deterministisk funktion tildeler hver række skal tilhøre én fordeling. I tabeldefinition, er en af kolonnerne angivet som kolonnen fordeling. Hash-funktionen bruger værdien i kolonnen fordeling skal tildeles en fordeling hver række.

Der er overvejelser til valg af en distribution kolonne, som selvstændighed, data skævhed og typerne af forespørgsler, der kører på systemet.
  
![Fordelt tabel] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Fordelt tabel")  
  
-   Hver række tilhører en fordeling.  
  
-   En deterministisk hashalgoritme tildeler hver række til en fordeling.  
  
-   Antallet af rækker i hver fordeling varierer som det fremgår af de forskellige størrelser af tabeller.

## <a name="round-robin-distributed-tables"></a>Round robin-fordelt tabeller

En round robin-fordelt tabel distribuerer rækkerne ved at tildele en fordeling hver række i en fortløbende måde. Det er hurtigt at indlæse data til en round robin-tabel, men forespørgselsydelse muligvis langsommere.  Deltage i en round robin-tabel som regel kræver reshuffling rækker for at aktivere forespørgslen for at frembringe et præcist resultat, hvilket tager tid.

## <a name="distributed-storage-locations-are-called-distributions"></a>Fordelt lagerplads placeringer kaldes salgsdistributioner

Hver distribueret sted kaldes en fordeling. Når en forespørgsel køres parallelt, udfører hver fordeling en SQL-forespørgsel på del af dataene. SQL Data Warehouse bruger SQL-Database til at køre forespørgslen. Parallel Data Warehouse bruger SQL Server til at køre forespørgslen. Dette delt arkitektur design er grundlæggende at nå skala fra parallel databehandling.

### <a name="can-i-view-the-distributions"></a>Kan jeg få vist fordelingerne?

Hver af de har en fordeling-ID og er synlige i systemvisninger, der vedrører SQL Data Warehouse og Parallel Data Warehouse. Du kan bruge fordeling ID til at foretage fejlfinding af forespørgselsydelse og andre problemer. Du kan finde en liste over systemvisningerne, [MPP systemvisning](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Forskellen mellem en fordeling og en beregningsnode

En fordeling er den grundlæggende enhed for at gemme distribuerede data og behandling af parallelle forespørgsler. Fordelingerne er inddelt i Beregn noder. Hver beregningsnode registrerer en eller flere salgsdistributioner.  

-   Platform analysesystem bruger Beregn noder som en central del af funktionerne hardware arkitektur og skala ud. Det bruges altid otte fordelingerne per beregningsnode, som vist i diagrammet for hash distribueret tabeller. Antallet af knuder, Beregn, og derfor antallet af salgsdistributioner, bestemmes af antallet af Beregn knuder du køber til maskinen. Hvis du køber otte Beregn noder, får du eksempelvis 64 salgsdistributioner (8 Beregn noder x 8 salgsdistributioner/node). 

-   SQL Data Warehouse har et fast antal 60 salgsdistributioner og en fleksibel antallet af knuder Beregn. Beregn knuderne implementeres med Azure computing og lager ressourcer. Antallet af knuder, Beregn kan ændre afhængigt af back end-tjenesten arbejdsbelastningen og databehandling kapaciteten (DWUs) du angiver for et datalager. Når antallet af knuder, Beregn ændres, ændres også antallet af salgsdistributioner per beregningsnode. 

### <a name="can-i-view-the-compute-nodes"></a>Kan jeg få vist noderne Beregn?

Hver beregningsnode har en node-ID'ET og er synlige i systemvisninger, der vedrører SQL Data Warehouse og Parallel Data Warehouse.  Du kan se noden Beregn ved at søge efter kolonnen node_id i systemvisninger, hvis navne begynder med sys.pdw_nodes. Du kan finde en liste over systemvisningerne, [MPP systemvisning](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replikerede tabeller for Parallel datawarehouse 
  
Gælder for: Parallel datawarehouse

Ud over at bruge fordelt tabeller indeholder Parallel Data Warehouse mulighed for at gentage tabeller. En *replikeres tabel* er en tabel, der er gemt i sin helhed på hver beregningsnode. Replikering af en tabel, fjerner er nødvendigheden at overføre sin tabelrækker mellem Beregn noder før ved hjælp af tabel i en joinforbindelse eller sammenlægning. Replikerede tabeller er kun muligt med små tabeller på grund af den ekstra lagerplads, der er påkrævet for at gemme tabellen fuld på hver beregningsnode.  
  
I det følgende diagram viser en replikerede tabel, der er gemt på hver beregningsnode. Tabellen replikerede er gemt på tværs af alle diske, der er tildelt til noden Beregn. Denne disk strategi er implementeret ved hjælp af SQL Server filgrupper.  
  
![Replikeret tabel] (media/sql-data-warehouse-distributed-data/replicated-table.png "Replikeret tabel") 
  
## <a name="next-steps"></a>Næste trin
  
Hvis du vil bruge fordelt tabeller effektivt, se [Distributing tabeller i SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)  
  



  
