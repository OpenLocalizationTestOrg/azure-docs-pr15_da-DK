<properties
    pageTitle="Hvordan du kan oprette, administrere eller slette en lagerplads konto i portalen Azure | Microsoft Azure"
    description="Oprette en ny firmapost lagerplads, administrere din konto adgangstaster eller slette en lagerplads konto i portalen Azure. Få mere at vide om standard og premium lagerplads konti."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Om Azure lagerplads konti

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Oversigt

En Azure lagerplads konto indeholder et entydigt navneområde for at gemme og få adgang til din dataobjekter Azure-lager. Alle objekter på en lagerplads konto faktureres sammen som en gruppe. Dataene i din konto er kun tilgængelig for dig, kontoejeren som standard.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Lagerplads konto fakturering

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Når du opretter en Azure virtuelt, der en lagerplads konto oprettes for dig automatisk i dialogboksen installation placering Hvis du ikke allerede har en lagerplads konto i den nye placering. Så det ikke er nødvendigt at følge nedenstående trin for at oprette en lagerplads konto for virtuelt disken. Kontonavn lager baseres på navnet på den virtuelle computer. Se [dokumentationen til virtuelle Azure-computere](https://azure.microsoft.com/documentation/services/virtual-machines/) kan finde flere oplysninger.

## <a name="storage-account-endpoints"></a>Lagerplads konto slutpunkter

Hvert objekt, du gemmer i Azure-lager har en entydig URL-adresse. Kontonavn lager formularer underdomæne til denne adresse. Kombination af underdomæne og domæne navn, som er specifikke for hver tjeneste, udgør et *slutpunkt* for kontoen lagerplads.

Eksempelvis hvis kontoen lagerplads har navnet *mystorageaccount*, derefter standard slutpunkterne for kontoen lagerplads er:

- BLOB service: http://*mystorageaccount*. blob.core.windows.net

- Tabellen service: http://*mystorageaccount*. table.core.windows.net

- Kø service: http://*mystorageaccount*. queue.core.windows.net

- Arkivere service: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] En Blob storage konto Fremviser kun Blob tjenesteslutpunkt.

URL-adressen for at få adgang til et objekt i en lagerplads konto er oprettet ved at tilføje objektets placering i kontoen lagerplads til slutpunktet. For eksempel en blob adresse kan have dette format: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Du kan også konfigurere et brugerdefineret domænenavn til brug med kontoen lagerplads. Se [konfigurere et brugerdefineret domæne navnet på din Blob Storage slutpunkt](storage-custom-domain-name.md) til detaljer for klassisk lagerplads konti. Denne funktion er ikke føjet til [Azure portal](https://portal.azure.com) endnu for ressourcestyring lagerplads konti, men kan du konfigurere det med PowerShell. Du kan finde yderligere oplysninger finder cmdlet'en [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) til.  

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

1. Log på [Azure-portalen](https://portal.azure.com).

2. Vælg **Ny**i menuen Hub -> **Data + lagerplads** -> **lagerplads konto**.

3. Angiv et navn til kontoen lagerplads. Du kan finde oplysninger om, hvordan kontonavn lager skal bruges til at løse dine objekter i Azure-lager i [lagerplads konto slutpunkter](#storage-account-endpoints) .

    > [AZURE.NOTE] Lagerplads kontonavne skal være mellem 3 og 24 tegn og kan indeholde tal og små bogstaver.
    >  
    > Dit kontonavn lager skal være entydig i Azure. Portalen Azure indikerer, hvis kontonavn lager, du vælger er allerede i brug.

4. Angiv installation, der skal bruges: **Ressourcestyring** eller **Klassisk**. **Ressourcestyring** er det anbefalede implementeringsmodel. Se Få mere at vide [om ressourcestyring installation og klassisk installation](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] BLOB storage konti kan kun oprettes med implementeringsmodel ressourcestyring.

5. Vælg typen konto, lagerplads: **generelle formål** eller **Blob-lager**. **Generelle formål** er standard.

    Hvis **generelle formål** er valgt, skal du angive det ydeevne niveau: **Standard** eller **Premium**. Standard er **Standard**. Flere oplysninger om standard og imponerende lagerplads konti under [Introduktion til Microsoft Azure-lager](storage-introduction.md) og [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](storage-premium-storage.md).

    Hvis **Blob-lager** er valgt, skal du angive access-niveauet: **varmt** eller **smarte**. Standard er **varmt**. Se [Azure Blob-lager: Smart og varmt lag](storage-blob-storage-tiers.md) få mere at vide.

6. Vælg indstillingen gentagelse for kontoen lagerplads: **LRS**, **GRS**, **RA-GRS**eller **ZRS**. Standard er **RA-GRS**. Du kan finde flere oplysninger om Azure-lager Replikeringsindstillinger [Azure-lager gentagelse](storage-redundancy.md).

7. Vælg det abonnement, hvor du vil oprette den nye konto lagerplads.

8. Angiv en ny ressourcegruppe, eller Vælg en eksisterende ressourcegruppe. Du kan finde flere oplysninger om ressourcegrupper, [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md).

9. Vælg kontoen lagerplads geografiske placering. Du kan finde flere oplysninger om hvilke tjenester der er tilgængelige i hvilket område i [Azure områder](https://azure.microsoft.com/regions/#services) .

10. Klik på **Opret** for at oprette kontoen lagerplads.

## <a name="manage-your-storage-account"></a>Administrere din lagerplads konto

### <a name="change-your-account-configuration"></a>Ændre din konfiguration af konto

Du kan ændre dens konfiguration, eksempelvis ændring af indstillingen gentagelse, der bruges til kontoen, når du opretter kontoen lagerplads, eller ændre access niveau for en Blob storage konto. [Azure-portalen](https://portal.azure.com), gå til kontoen lagerplads, skal du klikke på **alle indstillinger** og klik derefter på **konfiguration** for at få vist og/eller ændre konfigurationen af konto.

> [AZURE.NOTE] Afhængigt af det ydeevne niveau, du har valgt, når du opretter kontoen lagerplads, nogle Replikeringsindstillinger muligvis ikke tilgængelig.

Ændring af indstillingen gentagelse ændrer dine priser. Få mere at vide, se [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage/) side.

For Blob storage konti kan ændre access-niveau betale gebyrer, før ændringen ud over at ændre din priser. Se [Blob storage konti – priser og fakturering](storage-blob-storage-tiers.md#pricing-and-billing) for at få flere oplysninger.

### <a name="manage-your-storage-access-keys"></a>Administrere din lagerplads access-taster

Når du opretter en konto med lagerplads, genererer Azure to 512-bit lagerplads access nøgler, som bruges til godkendelse, når der opnås adgang til kontoen lagerplads. Ved at oprette to lagerplads access-taster, Azure giver dig mulighed at genoprette de pågældende taster med ingen afbrudt lagerplads eller access i den pågældende tjeneste.

> [AZURE.NOTE] Vi anbefaler, at du undlader at dele din lagerplads hurtigtaster med andre. Hvis du vil tillade adgang til lagerplads ressourcer uden at give din access-taster, kan du bruge en *delt adgang signatur*. En delt adgang signatur giver adgang til en ressource på din konto for et interval, som du definerer og med de tilladelser, du angiver. Du kan få flere oplysninger i [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Få vist og kopiere lagerplads access-taster

Gå til kontoen lagerplads på [Azure-portalen](https://portal.azure.com), klik på **Indstillinger for alle** , og klik derefter på **Access-taster** til at få vist, kopiere og genoprette hurtigtaster din konto. Bladet **Hurtigtaster** indeholder også forudkonfigurerede forbindelsesstrenge ved hjælp af din primære og sekundære nøgler, som du kan kopiere Hvis du vil bruge i dine programmer.

#### <a name="regenerate-storage-access-keys"></a>Genoprette lagerplads access-taster

Vi anbefaler, at du ændrer hurtigtasterne til kontoen lagerplads med jævne mellemrum for at beskytte dine lagerplads forbindelser. To access-taster er tildelt, så du kan bevare forbindelser til kontoen lagerplads ved hjælp af en access-tasten, mens du genoprette andre hurtigtast.

> [AZURE.WARNING] Genoprette dine access-taster kan påvirke tjenester i Azure samt dine egne programmer, der er afhængige af kontoen lagerplads. Alle klienter, der bruger tasten access til at få adgang til kontoen lagerplads skal opdateres, hvis du vil bruge den nye nøgle.

**Media services** – Hvis du har media services, der er afhængige af din lagerplads konto, du skal igen synkronisere hurtigtasterne med din tjeneste medier efter at du genoprette tasterne.

**Programmer** – Hvis du har webprogrammer eller skytjenester, der bruger kontoen lagerplads, mister du forbindelserne Hvis du genoprette taster, medmindre du vender dine nøgler.

**Lagerplads stifindere** – Hvis du bruger en hvilken som helst [lagerplads explorer programmer](storage-explorers.md), skal du sandsynligvis opdatere tasten lagerplads, der bruges af disse programmer.

Her er en proces for rotation af din lagerplads adgangstaster:

1. Opdater forbindelse strengene i din programkode til at referere til tasten sekundær adgang til kontoen, lagerplads.

2. Genoprette nøglen primære adgang til kontoen lagerplads. Klik på **Genoprette nøgle1**på bladet **Access-taster** , og klik derefter på **Ja** for at bekræfte, at du vil oprette en ny nøgle.

3. Opdater forbindelse strengene i din kode til at referere til den nye primære access-nøgle.

4. Genoprette nøglen sekundær access på samme måde.

## <a name="delete-a-storage-account"></a>Slette en lagerplads konto

Hvis du vil fjerne en lagerplads-konto, som du ikke længere bruger, Naviger til kontoen lagerplads på [Azure portal](https://portal.azure.com), og klik på **Slet**. Slette en lagerplads konto sletter hele kontoen, herunder alle data i kontoen.

> [AZURE.WARNING] Det er ikke muligt at gendanne en slettet lagerplads konto eller hente det indhold, som den indeholdt før sletning. Sørg for at sikkerhedskopiere noget, du vil gemme, før du sletter kontoen. Dette også gælder for alle ressourcer i kontoen, når du sletter en blob, tabel, kø eller filer, slettes den permanent.

Hvis du vil slette en lagerplads-konto, som er knyttet til en Azure virtuelt, skal du kontrollere, at et virtuelt diske er blevet slettet. Hvis du ikke først sletter virtuelt disken, derefter vises når du forsøger at slette kontoen lagerplads en fejlmeddelelse i stil til:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Hvis kontoen lagerplads bruger implementeringsmodel klassisk, kan du fjerne virtuelt disken ved at følge disse trin i [Azure portal](https://manage.windowsazure.com):

1. Gå til [Klassisk Azure portal](https://manage.windowsazure.com).
2. Gå til fanen virtuelle computere.
3. Klik på fanen diske.
4. Vælg harddisken data, og klik derefter på Slet Disk.
5. Hvis du vil slette diskbilleder, gå til fanen billeder og slette nogen billeder, der er gemt på kontoen.

Yderligere oplysninger finder du i [Azure virtuelt dokumentation](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Næste trin

- [Azure Blob-lager: Fantastiske og varmt niveauer](storage-blob-storage-tiers.md)
- [Azure lagerplads gentagelse](storage-redundancy.md)
- [Konfigurere Azure lagerplads forbindelsesstrenge](storage-configure-connection-string.md)
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
- Gå til [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/).
