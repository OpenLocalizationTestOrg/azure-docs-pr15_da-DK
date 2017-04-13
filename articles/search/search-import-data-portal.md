<properties
    pageTitle="Importere data til Azure søgning ved hjælp af indeks i portalen Azure | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Brug af guiden Azure Søg Importér Data i portalen Azure til gennemsøgning data fra Azure Blob-lager, tabel stroage, SQL-Database og SQL Server på Azure FOS."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importere data til Azure søgning ved hjælp af portalen

Portalen Azure indeholder en version af guiden **Importdata** på dashboardet til Azure Søg til at indlæse data i et indeks. 

  ![Importere Data på kommandolinjen][1]

Internt guiden konfigurerer og starter et *indekseringsprogram*kan automatisere flere trin over indeksering processen: 

- Oprette forbindelse til en ekstern datakilde i det aktuelle Azure abonnement
- Generer en indeks-skema, der er baseret på kilde-datastruktur
- Oprette dokumenter baseret på et rækkesæt, der er hentet fra datakilden
- Overføre dokumenter til indekset i search-tjenesten

Du kan afprøve arbejdsprocessen ved hjælp af eksempeldata i DocumentDB. Gå til [komme i gang med Azure søgning i portalen Azure](search-get-started-portal.md) til instruktioner.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Datakilder, der understøttes af guiden Importér Data

Guiden importdata understøtter følgende datakilder: 

- Azure SQL-Database
- SQL Server relationelle data på en Azure VM
- Azure DocumentDB
- Azure Blob-lager (i foreløbige versioner)
- Azure-tabellager (i foreløbige versioner)

En flad datasæt er en påkrævet input. Du kan kun importere fra en enkelt tabel, databasevisning eller tilsvarende datastruktur. Før du kører guiden skal du oprette denne datastruktur.

Bemærk, at nogle af indeks er stadig i preview, hvilket betyder, at indekseringsprogram definitionen understøttes af prøveversionen af API. Se [Oversigt over indekseringsprogram](search-indexer-overview.md) kan finde flere oplysninger og links.

## <a name="connect-to-your-data"></a>Oprette forbindelse til dine data

1. Log på [Portalen Azure](https://portal.azure.com) og åbne dashboard. Du kan klikke på **søgetjenester** på linjen gå til at vise de eksisterende tjenester i det aktuelle abonnement. 

2. Klik på **Importdata** på kommandolinjen til slide Åbn bladet importdata.  

3. Klik på **Opret forbindelse til dine data** for at angive en datadefinitionsforespørgsel i kilde, der bruges af et indekseringsprogram. Handel-abonnement-datakilder, kan guiden som regel finde og læse forbindelsesoplysningerne, minimering overordnede konfigurationskrav.

| | |
|--------|------------|
|**Eksisterende datakilde** | Hvis du allerede har indeks, der er defineret i search-tjenesten, kan du vælge en eksisterende datakilde definition af data til en anden import.|
|**Azure SQL-Database** | Navnet på tjenesten, legitimationsoplysninger for databasebruger med læsetilladelse og et databasenavn kan angives på siden eller via en ADO.NET-forbindelsesstrengen. Vælg indstillingen for en forbindelsesstreng til at få vist eller tilpasse egenskaber. <br/><br/>Den tabel eller visning, der indeholder rækkesættet skal angives på siden. Denne indstilling vises, når forbindelsen er oprettet, giver en rulleliste, så du kan foretage et valg.|
|**SQL Server på Azure VM** | Angiv et fuldt kvalificeret service navn, bruger-ID og din adgangskode og database som en forbindelsesstreng. Hvis du vil bruge denne datakilde, skal du have tidligere installeret et certifikat i det lokale lager, der krypterer forbindelsen. <br/><br/>Den tabel eller visning, der indeholder rækkesættet skal angives på siden. Denne indstilling vises, når forbindelsen er oprettet, giver en rulleliste, så du kan foretage et valg.
|**DocumentDB** |Krav omfatter den konto, database og af websteder. Alle dokumenter i gruppen af websteder skal indgå i indekset. Du kan definere en forespørgsel til at udjævne eller filtrere rækkesættet eller til at registrere ændrede dokumenter til efterfølgende dataopdateringer.|
|**Azure Blob-lager** | Krav omfatter lagerplads kontoen og en objektbeholder. Du kan også hvis blob navne følger et virtuelt Navnekonventionen for gruppering formål, kan du angive den virtuelle mappe del af navnet som en mappe i objektbeholderen. Du kan finde flere oplysninger i [Indeksering Blob-lager (preview)](search-howto-indexing-azure-blob-storage.md) . |
|**Azure-Tabellager** | Krav omfatter kontoen lager og navnet på en tabel. Du kan også angive en forespørgsel for at hente et undersæt af tabellerne. Du kan få flere oplysninger i [Indeksering Table Storage (preview)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Tilpasse target indeks

Et indledende indeks er typisk udledes fra datasættet. Tilføje, redigere eller slette felter for at fuldføre skemaet. Desuden kan angive attributter på niveauet felt til at bestemme de efterfølgende Søg funktionsmåder.

1. Angiv navn og en **nøgle** bruges til at entydigt identificerer hvert dokument i **Tilpas target indeks**. Tasten skal være en streng. Hvis feltværdier indeholde mellemrum eller stiplet Sørg for at angive avancerede indstillinger i **importere dine data** til at skjule validering Kontrollér, om disse tegn.

2. Gennemse og revidere de resterende felter. Feltnavn og type er typisk udfyldt for dig. Du kan ændre datatypen.

3. Angiv indeksattributter for hvert felt:

 - Størrelse returnerer feltet i søgeresultaterne.
 - Filtrerbare giver mulighed for feltet, der skal refereres til i filterudtryk.
 - Sorterbar kan feltet skal bruges i en sortering.
 - Facetable gør det muligt for feltet for facetteret navigation.
 - Søgbar gør det muligt for søgning i hele teksten.
  
4. Klik på fanen **Analyzer** , hvis du vil angive et sprog analyzer på niveauet for felt. Kun sprog programmer til analyse kan angives på nuværende tidspunkt. Ved hjælp af en brugerdefineret analyzer eller en ikke-sprog analyzer som nøgleord, mønster og så videre kræver kode.

 - Klik på **søgbar** for at udpege søgning i hele teksten på feltet, og aktivere rullelisten Analyzer.
 - Vælg den ønskede analyzer. Du kan finde oplysninger i [oprette et indeks for dokumenter på flere sprog](search-language-support.md) .

5. Klik på **Suggester** for at aktivere til Autoudfyldning forespørgselsforslag på valgte felter.


## <a name="import-your-data"></a>Importere dine data

1. **Importere dine data**, Angiv et navn til indekseringstjenesten. Tilbagekalde, produktet af guiden importdata er et indekseringsprogram. Senere, hvis du vil se eller redigere den, skal du vælge den fra portalen i stedet for ved at køre guiden. 

2. Angiv tidsplan, der er baseret på den regionale tidszone, hvor tjenesten er klargjort.

3. Angive avancerede indstillinger til at angive tærskler på om indeksering kan fortsætte med Hvis et dokument bliver afbrudt. Desuden kan du angive, om **nøgle** -felter kan indeholde mellemrum og skråstreger.  

## <a name="edit-an-existing-indexer"></a>Redigere et eksisterende indekseringsprogram

Dobbeltklik på feltet indekseringsprogram til slide en liste over alle indeks, der er oprettet for dit abonnement i dashboardet til tjenesten. Dobbeltklik på en af indeks til at køre, redigere eller slette den. Du kan erstatte indekset med en anden eksisterende, ændre datakilden og angive indstillinger for fejl tærskler under indeksering.

## <a name="edit-an-existing-index"></a>Redigere et eksisterende indeks

I Azure Søg kræver strukturel opdateringer til et indeks en Genopbyg, indeks, der består af at slette indekset, genoprette indekset og genindlæser data. Strukturel opdateringer indeholder ændre datatypen og omdøbe eller slette et felt.

Ændringer, der ikke kræver en Genopbyg omfatter tilføjelse af et nyt felt, ændre vurdering profiler, ændre suggesters eller ændring af sprog programmer til analyse. Du kan finde flere oplysninger i [Opdater indeks](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Næste trin

Gennemgå disse links for at få flere oplysninger om indeks:

- [Indeksering Azure SQL-Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indeksering DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indeksering Blob-lager (preview)](search-howto-indexing-azure-blob-storage.md)
- [Indeksering Table Storage (preview)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

