<properties 
    pageTitle="Importere og eksportere data i Azure Redis Cache | Microsoft Azure" 
    description="Lær, hvordan du importere og eksportere data til og fra blob-lager med din premium Azure Redis Cache forekomster" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importere og eksportere data i Azure Redis Cache

Importér/Eksportér er en Azure Redis Cache data management handling, hvor du kan importere data til Azure Redis Cache eller eksportere data fra Azure Redis Cache ved at importere og eksportere et Redis Cache Database (RDB) snapshot fra en premium cache til en side blob på en Azure-lager-konto. Importér/Eksportér gør det muligt at overføre mellem forskellige Azure Redis Cache forekomster eller udfylde cachen med data, før brug.

I denne artikel giver en vejledning til import og eksport af data med Azure Redis Cache og svar på ofte stillede spørgsmål.

>[AZURE.IMPORTANT] Importér/Eksportér i Vis udskrift og er kun tilgængelig for [premium niveau](cache-premium-tier-intro.md) cache.

## <a name="import"></a>Importér

Importér kan bruges til at hente Redis kompatible RDB filer fra en hvilken som helst Redis server, der kører i skyen eller miljøet, herunder Redis kører på Linux, Windows eller enhver skyen udbyder som Amazon webtjenester og andre. Import af data er en nem måde at oprette en cache med udfyldt på forhånd data. Azure Redis Cache indlæser RDB filer fra Azure-lager i hukommelsen under importen og derefter indsætter tasterne i cachen.

>[AZURE.NOTE] Inden du starter importhandlingen, sikre, at din Redis Database (RDB) eller flere filer er overført til siden BLOB i Azure-lager, i samme område og abonnement som din Azure Redis Cache forekomst. Yderligere oplysninger finder du se [Introduktion til Azure Blob-lager](../storage/storage-dotnet-how-to-use-blobs.md). Hvis du har eksporteret RDB filen ved hjælp af funktionen [Azure Redis Cache eksportere](#export) , der allerede er gemt i en side blob RDB filen, og er klar til at importere.

1. Importere en eller flere eksporterede cache blob, [Gå til din cache](cache-configure.md#configure-redis-cache-settings) på portalen Azure, og klik på **importdata** fra bladet **Indstillinger** af din cache forekomst.

    ![Importdata][cache-import-data]

2. Klik på **Vælg Blob(s)** , og Vælg kontoen lagerplads, der indeholder dataene, der skal importeres.

    ![Vælg lagerplads konto][cache-import-choose-storage-account]

3. Klik på objektbeholderen, der indeholder dataene, der skal importeres.

    ![Vælg objektbeholder][cache-import-choose-container]

4. Vælg en eller flere BLOB importere ved at klikke på området til venstre for navnet på blob, og klik derefter på **Vælg**.

    ![Vælg BLOB][cache-import-choose-blobs]

5. Klik på **Importér** for at starte importen.

    >[AZURE.IMPORTANT] Cachen er ikke tilgængelig for cache klienter under importen, og slettes eventuelle eksisterende data i cachen.

    ![Importér][cache-import-blobs]

    Du kan overvåge forløbet af importhandlingen, ved at følge i meddelelser fra Azure-portalen eller ved at få vist hændelserne i [overvågningsloggen logge](cache-configure.md#support-amp-troubleshooting-settings).

    ![Status for import][cache-import-data-import-complete] 


## <a name="export"></a>Eksportere

Eksportér giver dig mulighed at eksportere de data, der er gemt i Azure Redis Cache for Redis kompatible RDB fil(er). Du kan bruge denne funktion til at flytte data fra én Azure Redis Cache-forekomst, til en anden eller til en anden Redis server. Under eksporten, der oprettes en midlertidig fil på den VM, der er vært Azure Redis Cache server-forekomst, og filen er overført til kontoen udpegede lagerplads. Når eksporten er fuldført med enten en status for et gunstigt udfald eller fejl, slettes den midlertidige fil.

1. Du kan eksportere indholdet af cachen til lager, [Gå til din cache](cache-configure.md#configure-redis-cache-settings) på portalen Azure, og klik på **Eksporter data** fra bladet **Indstillinger** af din cache forekomst.

    ![Vælg objektbeholder til lagring][cache-export-data-choose-storage-container]

2. Klik på **Vælg objektbeholder til lagring** , og vælg den ønskede lagerplads konto. Kontoen lagerplads skal være i samme abonnement og område som din cache.

    >[AZURE.IMPORTANT] Importér/Eksportér fungerer med siden blob, der understøttes af både klassisk og ARM lagerplads konti, men der ikke understøttes af [Blob storage konti](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) på nuværende tidspunkt.

    ![Lagerplads konto][cache-export-data-choose-account]

3. Vælg den ønskede blob beholder, og klik på **Vælg**. Hvis du vil bruge nye en objektbeholder, skal du klikke på **Tilføj objektbeholder** for at føje den først og derefter vælge den på listen.

    ![Vælg objektbeholder til lagring][cache-export-data-container]

4. Skriv et **Blob navnepræfiks** , og klik på **Eksporter** for at starte eksporten. Præfikset blob navn bruges til at præfiks navnene på de filer, der genereres af denne eksporten.

    ![Eksportere][cache-export-data]

    Du kan overvåge forløbet af eksporten, ved at følge i meddelelser fra Azure-portalen eller ved at få vist hændelserne i [overvågningsloggen logge](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Cachelagre forbliver tilgængelig til brug under eksporten.


## <a name="importexport-faq"></a>Importér/Eksportér ofte stillede spørgsmål

Dette afsnit indeholder ofte stillede spørgsmål om funktionen Importér/Eksportér.

-   [Hvilke priser niveauer kan bruge Importér/Eksportér?](#what-pricing-tiers-can-use-importexport)
-   [Kan jeg importere data fra en hvilken som helst Redis server?](#can-i-import-data-from-any-redis-server)
-   [Min cache er tilgængelige under handlingen Importér/Eksportér?](#will-my-cache-be-available-during-an-importexport-operation)
-   [Kan jeg bruge Importér/Eksportér med Redis klynge?](#can-i-use-importexport-with-redis-cluster)
-   [Hvordan fungerer Importér/Eksportér med en brugerdefineret databaser?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Hvordan adskiller sig fra Redis brugerdata Importér/Eksportér?](#how-is-importexport-different-from-redis-persistence)
-   [Kan jeg automatisere Importér/Eksportér ved hjælp af PowerShell, CLI eller andre management klienter?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [Jeg har modtaget en timeoutfejl under Mine importere eller eksportere dataene. Hvad betyder det?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [Jeg fik en fejl, når du eksporterer dataene til Azure Blob-lager. Hvad skete der?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>Hvilke priser niveauer kan bruge Importér/Eksportér?

Importér/Eksportér findes kun i premium priser niveau.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan jeg importere data fra en hvilken som helst Redis server?

Ja, ud over at importere data, der er eksporteret fra Azure Redis Cache forekomster, du kan importere RDB filer fra en hvilken som helst Redis server, der kører i skyen eller miljø, som Linux, Windows, eller udbydere som Amazon webtjenester i skyen. Overføre filen RDB fra den ønskede Redis server til en side blob på en Azure-lager konto for at gøre dette, og derefter importere dem til din premium Azure Redis Cache forekomst. Du vil muligvis eksportere dataene fra din fremstilling cache og importere den til en cache, der bruges som en del af et midlertidigt miljø til test eller overførsel. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Min cache er tilgængelige under handlingen Importér/Eksportér?

-   **Eksportere** - cache er stadig tilgængelige, og du kan fortsætte med at bruge din cache under eksporten.
-   **Importere** - cachelagre bliver utilgængelige, når en importhandlingen starter, og bliver tilgængelige til brug, når importen er fuldført.


### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan jeg bruge Importér/Eksportér med Redis klynge?

Ja, og du kan importere/eksportere mellem en grupperet cache og en ikke-grupperede cache. Siden Redis klynge [understøtter kun database 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), kan ikke importeres data i databaser end 0. Når grupperet cache dataene er importeret, er de pågældende taster fordeles mellem shards af klyngen. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hvordan fungerer Importér/Eksportér med en brugerdefineret databaser?

Nogle priser niveauer har forskellige [begrænsninger for databaser](cache-configure.md#databases), så der er nogle overvejelser, når du importerer, hvis du har konfigureret en brugerdefineret værdi for den `databases` indstilling under oprettelse af cache.

-   Når du importerer til et priser trin med en lavere `databases` grænse end det niveau, hvorfra du eksporterede:
    -   Hvis du bruger standardantallet af `databases` , er 16 for alle priser niveauer, ingen data går tabt.
    -   Hvis du bruger et brugerdefineret antal `databases` , der falder inden for grænserne for det niveau, du importerer, ingen data går tabt.
    -   Hvis de eksporterede data indeholdt data i en database, der overskrider grænserne af nye niveauet, importeres data fra disse højere databaser ikke.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hvordan adskiller sig fra Redis brugerdata Importér/Eksportér?

Azure Redis Cache brugerdata gør det muligt at fastholdes data gemt i Redis til Azure-lager. Når brugerdata er konfigureret, fortsætter Azure Redis Cache et øjebliksbillede af Redis cachen i Redis binært format på disken ud fra en konfigurerbar sikkerhedskopiering frekvens. Hvis der opstår en katastrofal hændelse, der deaktiverer både primær og replika cachen, genoprettes cachedata automatisk ved hjælp af den seneste snapshot. Se, [hvordan du konfigurerer data brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md)kan finde flere oplysninger.

Importér / Eksportér gør det muligt at overføre data til eller eksportere fra Azure Redis Cache. Den ikke konfigurere sikkerhedskopiering og gendannelse ved hjælp af Redis brugerdata.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan jeg automatisere Importér/Eksportér ved hjælp af PowerShell, CLI eller andre management klienter?

Ja, til PowerShell finder instruktioner [til at importere en Redis cache](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) og [eksportere en Redis cache](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Jeg har modtaget en timeoutfejl under Mine importere eller eksportere dataene. Hvad betyder det?

Hvis du stadig på bladet **importdata** eller **eksportere data** til mere end 15 minutter, før du starter handlingen, modtager du en fejl, der ligner følgende.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

For at løse dette problem, starte importen eller eksport før 15 minutter er udløbet.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Jeg fik en fejl, når du eksporterer dataene til Azure Blob-lager. Hvad skete der?

Importér/Eksportér fungerer kun med RDB filer, der er gemt som siden BLOB. Andre blob-datatyper understøttes ikke på nuværende tidspunkt, herunder blob storage konti med varmt og smarte niveauer.


## <a name="next-steps"></a>Næste trin
Lær at bruge flere premium-funktioner i cachen.

-   [Introduktion til Azure Redis Cache Premium niveau](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








