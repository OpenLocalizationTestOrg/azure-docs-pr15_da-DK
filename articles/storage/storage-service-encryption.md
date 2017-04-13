<properties
    pageTitle="Azure-lager Service kryptering efter Data på resten | Microsoft Azure"
    description="Bruge funktionen Azure lagerplads Service kryptering til at kryptere Azure Blob-lager i tjenesten side, når du skal gemme dataene, og dekryptere den, når du henter dataene."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure-lager Service kryptering efter Data på resten

Azure lagerplads Service kryptering (SSE) efter Data på resten hjælper dig med at beskytte og beskytte dine data, så det opfylder dine organisatoriske sikkerhed og overholdelse af angivne standarder forpligtelser. Med denne funktion Azure-lager automatisk krypterer dataene før vedvarer til lager og dekrypterer før hentning. Kryptering, dekryptering og key management er helt gennemsigtig til brugere.

De følgende afsnit indeholder detaljerede vejledning i at bruge funktionerne lagerplads Service kryptering samt understøttede scenarier og bruger oplever.

## <a name="overview"></a>Oversigt

Azure-lager indeholder et omfattende sæt af sikkerhedsfunktioner hvorved sammen udviklere til at oprette sikker programmer. Data kan være fastgjort undervejs mellem et program og Azure ved hjælp af [Kryptering på klientsiden](storage-client-side-encryption.md), HTTPs eller små og mellemstore virksomheder 3.0. Lagerplads Service kryptering indeholder kryptering på resten, håndtering af kryptering, dekryptering og key management på en helt gennemsigtig måde. Alle data, der er krypteret med 256-bit [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en af de største Bloker ciphers tilgængelige.

SSE fungerer ved at kryptere dataene, når den er skrevet til Azure-lager og kan bruges til Bloker blob, siden BLOB og tilføje BLOB. Det fungerer for følgende:

-   Generelle formål lagerplads kontiene og Blob-lager
-   Standard storage og Premium storage 
-   Alle redundans udjævner (LRS, ZRS, GRS, RA-GRS)
-   Azure ressourcestyring lagerplads konti (men ikke klassisk) 
-   Alle områder

Log på [Azure portal](https://azure.portal.com) for at aktivere eller deaktivere lagerplads Service kryptering for en lagerplads-konto, og vælg en lagerplads konto. Se efter sektionen Blob Service, som vist i dette skærmbillede bladet indstillinger, og klik på kryptering.

![Portalen skærmbillede med krypteringsindstilling](./media/storage-service-encryption/image1.png)

Når du klikker på krypteringsindstillingen, kan du aktivere eller deaktivere lagerplads Service kryptering.

![Portalen skærmbillede med kryptering egenskaber](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Kryptering scenarier

Lagerplads Service kryptering kan aktiveres på en konto lagerpladsen. Den understøtter følgende kundescenarier:

-   Kryptering af Bloker BLOB føjer BLOB og siden BLOB.

-   Kryptering af arkiverede virtuelle harddiske og skabeloner, der er hentet ind til Azure fra det lokale.

-   Kryptering af underliggende OS og datadisce for IaaS FOS oprettet ved hjælp af din virtuelle harddiske.

SSE har følgende begrænsninger:

-   Kryptering af klassisk lagerplads konti understøttes ikke.

-   Kryptering af klassisk lagerplads konti overflyttet til Ressourcestyring lagerplads konti understøttes ikke.

-   Eksisterende Data - SSE krypterer kun nyoprettede data, når krypteringen er aktiveret. Hvis for eksempel du opretter en ny ressourcestyring lagerplads konto, men ikke slå kryptering, og derefter du overfører BLOB eller arkiverede virtuelle harddiske med det pågældende lagerplads firma og derefter aktivere SSE, krypteres disse BLOB ikke, medmindre de omskrives eller kopieres.

-   Marketplace Support - Aktivér kryptering af FOS oprettet fra Marketplace med [Azure-portalen](https://portal.azure.com), PowerShell og Azure CLI. Den grundlæggende afbildning Virtuelle forbliver ikke-krypteret; dog krypteres en hvilken som helst skriver færdig, når VM har spundet op.

-   Tabel, køer, og filer dataene krypteres ikke.

##<a name="getting-started"></a>Kom godt i gang

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Trin 1: [Opret en ny konto lagerplads](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Trin 2: Aktivere kryptering.

Du kan aktivere kryptering ved hjælp af [Azure-portalen](https://portal.azure.com).

> [AZURE.NOTE] Hvis du vil aktivere eller deaktivere lagerplads Service kryptering på en lagerplads konto fra et program, kan du bruge [Azure lagerplads ressource udbyder REST-API](https://msdn.microsoft.com/library/azure/mt163683.aspx), [Lagerplads ressource udbyder klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md)eller [Azure CLI](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Trin 3: Kopiere data til lagerplads konto

Hvis du aktiverer SSE på en lagerplads konto og derefter skrive BLOB til kontoen lagerplads, krypteres BLOB. En hvilken som helst blob, der allerede findes i den pågældende lagerplads konto vil ikke blive krypteret, indtil de omskrives. Du kan kopiere data fra én lagerplads konto til en med SSE krypteret, eller endda aktiverer SSE og kopiere BLOB fra én objektbeholder til en anden til sikker på, at tidligere data er krypteret. Du kan bruge en af følgende værktøjer til at udføre dette.

#### <a name="using-azcopy"></a>Brug af AzCopy

AzCopy er et Windows kommandolinjen værktøj til kopiering af data til og fra Microsoft Azure Blob, filer og tabel-lager, med enkle kommandoer med optimal ydeevne. Du kan bruge til at kopiere din BLOB fra den ene lagerplads konto til en anden, der indeholder SSE aktiveret. 

Hvis du vil have mere for at vide, skal du gå til [at overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Brug af lagerplads klient-biblioteker

Du kan kopiere blob-data til og fra blob-lager eller mellem lagerplads konti ved hjælp af vores bredt udvalg af lagerplads klientbiblioteker, herunder .NET, C++, Java, Android, Node.js, PHP, Python og fonetisk.

Hvis du vil have mere for at vide, skal du gå til vores [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Ved hjælp af en lagerplads Stifinder

Du kan bruge en lagerplads Stifinder til at oprette lagerplads konti, overføre og hente data, få vist indholdet af BLOB og navigere i mapper. Du kan bruge en af følgende for at overføre BLOB til kontoen lagerplads med kryptering aktiveret. Med nogle lagerplads stifindere, kan du også kopiere data fra eksisterende blob-lager til en anden objektbeholder i kontoen lagerplads eller en ny lagerplads-konto, der indeholder SSE aktiveret.

For at få mere for at vide, skal du gå til [Azure lagerplads stifindere](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Trin 4: Forespørgsel status for de krypterede data

Blevet er installeret en opdateret version af lagerplads klient-biblioteker, der gør det muligt at forespørge tilstanden for et objekt til at bestemme, hvis det er krypteret eller ej. Eksempler, føjes til dette dokument inden for kort tid.

I mellemtiden kan du ringe [Få egenskaber for konto](https://msdn.microsoft.com/library/azure/mt163553.aspx) for at bekræfte, at kontoen lagerplads har kryptering aktiveret eller få vist egenskaberne for kontoen lagerplads på portalen Azure.

##<a name="encryption-and-decryption-workflow"></a>Kryptering og dekryptering arbejdsproces

Her er en kort beskrivelse af arbejdsprocessen for kryptering, der er relateret:

-   Kunden aktiverer kryptering på kontoen lagerplads.

-   Når kunden skriver nye data (PLACERE Blob, PLACERE Bloker, PLACERE siden osv.) til Blob-lager hver Skriv er krypteret med 256-bit [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en af de største Bloker ciphers tilgængelige.

-   Når kunden har brug at få adgang til data (få Blob osv.), er data automatisk dekrypteres før vende tilbage til brugeren.

-   Hvis kryptering er deaktiveret, ny skriver ikke længere er krypteret og eksisterende krypterede data forbliver krypterede, indtil omskrives af brugeren. Mens kryptering er aktiveret, krypteres skriver til Blob-lager. Tilstanden for data ændres ikke med den bruger, hvis du skifter mellem aktivering/deaktivering af kryptering for kontoen lagerplads.

-   Alle krypteringsnøgler er gemt, krypteres og administreres af Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Ofte stillede spørgsmål om lagerplads Service kryptering efter Data på resten

**Sp: Jeg har en eksisterende konto klassisk lagerplads. Kan jeg aktivere SSE på den?**

SV: Nej, SSE understøttes kun på ressourcestyring lagerplads konti.

**Sp: Hvordan kan jeg kryptere data i min klassisk lagerplads konto?**

SV: Du kan oprette en ny ressourcestyring lagerplads konto og kopiere dataene ved hjælp af [AzCopy](storage-use-azcopy.md) fra din eksisterende klassisk lagerplads-konto til din nyoprettede ressourcestyring lagerplads konto. 

En anden mulighed er at overføre kontoen klassisk lagerplads til en ressource Administrer lagerplads konto. Få mere at vide under [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Ressourcestyring](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**Sp: Jeg har en eksisterende ressourcestyring lagerplads konto. Kan jeg aktivere SSE på den?**

SV: Ja, men kun nyligt skrevet BLOB krypteres. Den ikke gå tilbage og kryptere data, som allerede findes. 

**Sp: Jeg vil gerne kryptere de aktuelle data i en eksisterende ressourcestyring lagerplads konto?**

SV: Du kan aktivere SSE når som helst på en ressourcestyring lagerplads konto. BLOB, som fandtes allerede krypteres dog ikke. Hvis du vil kryptere disse blob, kan du kopiere dem til et andet navn eller en anden objektbeholder og derefter fjerne ikke-krypteret versioner.

**Sp: Jeg bruger Premium lagerplads; kan jeg bruge SSE?**

SV: Ja, understøttes SSE på både Standard lager og Premium lagerplads.

**Sp: Hvis jeg oprette en ny firmapost lager og aktivere SSE og derefter oprette en ny VM ved hjælp af lagerplads kontoen, betyder det min VM er krypteret?**

SV: Ja. En hvilken som helst diske, der er oprettet, der bruger den nye konto lagerplads krypteres, som de er oprettet, efter SSE er aktiveret. Hvis VM blev oprettet ved hjælp af Azure markedsplads, forbliver den Virtuelle grundlæggende afbildning ikke-krypteret; dog krypteres en hvilken som helst skriver færdig, når VM har spundet op.

**Sp: kan jeg oprette nye lagerplads konti med SSE aktiveret ved hjælp af Azure PowerShell og Azure CLI?**

SV: Ja.

**Sp: hvordan meget mere koster Azure-lager, hvis SSE er aktiveret?**

SV: der er gratis.

**Sp: hvem administrerer tasterne kryptering?**

SV: taster administreres af Microsoft.

**Sp: kan jeg bruge mit eget kryptering taster?**

SV: Vi arbejder på at give mulighed for kunder til at hente egen krypterede.

**Sp: kan jeg tilbagekalde adgang til tasterne kryptering?**

SV: ikke på nuværende tidspunkt. tasterne er fuldt administreres af Microsoft.

**Sp: er SSE som standard aktiveret, når jeg opretter en ny konto lagerplads?**

SV: SSE er ikke aktiveret som standard. Du kan bruge portalen Azure til at aktivere den. Du kan også automatisk aktivere denne funktion ved hjælp af lagerplads ressource udbyder REST-API.

**Sp: hvordan er det adskiller sig fra Azure Drive kryptering?**

SV: Denne funktion bruges til at kryptere data i Azure Blob-lager. Azure Disk kryptering bruges til at kryptere OS og Data diske i IaaS FOS. Besøg vores [Lagerplads Security Guide](storage-security-guide.md)kan finde flere oplysninger.

**Sp: Hvad nu, hvis jeg Aktiver SSE, og derefter gå i og Azure Disk kryptering på disk?**

SV: Dette fungerer problemfrit. Dataene krypteres ved begge metoder.

**Sp: min lagerplads konto er konfigureret til at replikeres geografisk give. Hvis jeg aktiverer SSE, min overflødige kopi krypteres også?**

SV: Ja, alle kopier af kontoen lagerplads er krypteret, og alle redundans indstillinger – lokalt overflødige lagerplads (LRS), Zone overflødigt lagerplads (ZRS), geografisk overflødigt lagerplads (GRS) og læseadgang geografisk overflødigt lagerplads (RA-GRS) – understøttes.

**Sp: Jeg kan ikke aktivere kryptering på min konto lagerplads.**

SV: er det en ressourcestyring lagerplads konto? Klassisk lagerplads konti understøttes ikke. 

**Sp: er SSE kun tilladt i bestemte områder?**

SV: SSE er tilgængelig i alle områder. 

**Sp: hvordan kontakter jeg nogen Hvis jeg har problemer med at eller vil give feedback?**

SV: Kontakt [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) til problemer med at lagerplads Service kryptering.

##<a name="next-steps"></a>Næste trin

Azure-lager indeholder et omfattende sæt af sikkerhedsfunktioner hvorved sammen udviklere til at oprette sikker programmer. Få mere at vide ved at besøge [Lagerplads Security Guide](storage-security-guide.md).
