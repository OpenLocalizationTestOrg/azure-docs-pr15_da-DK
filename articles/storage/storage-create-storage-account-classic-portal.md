<properties
    pageTitle="Hvordan du kan oprette, administrere eller slette en lagerplads konto i portalen Azure klassisk | Microsoft Azure"
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

En Azure lagerplads konto giver dig adgang til Azure Blob, kø, tabel og fil tjenesterne på Azure-lager. Kontoen lagerplads indeholder det entydige navneområde til dataobjekter din Azure-lager. Dataene i din konto er kun tilgængelig for dig, kontoejeren som standard.

Der findes to typer lagring konti:

- En almindelig lagerplads kontoen omfatter Blob, tabel, kø og fil lagerplads.
- En premium lagerplads konto understøtter i øjeblikket kun Azure virtuelt diske. Se [Premium lagerplads: High-performance lagerplads til Azure virtuelt arbejdsbelastninger](storage-premium-storage.md) for et detaljeret overblik over Premium lagerplads.

## <a name="storage-account-billing"></a>Lagerplads konto fakturering

Du er faktureret til Azure lagerforbrug baseret på kontoen lagerplads. Lagerplads omkostninger er baseret på fire faktorer: lagerkapacitet, gentagelse farveskema, lagerplads transaktioner og data udgangspunkt.

- Lagerkapacitet refererer til hvor meget af din lagerplads konto tildeling du er bruger til at gemme data. Omkostningerne for blot gemmer dine data, bestemmes af hvor meget data du gemmer, og hvordan den replikeres.
- Replikering bestemmer, hvor mange kopier af dine data vedligeholdes på én gang, og i hvilke steder.
- Transaktioner refererer til alle læse og skrive handlinger til Azure-lager.
- Data udgangspunkt refererer til data, der overføres af et Azure område. Når dataene i din lagerplads konto åbnes med et program, der ikke kører i det samme region, om dette program er en skybaseret tjeneste eller en anden type af programmet, er du betale for data udgangspunkt. (For Azure-tjenester, kan du tage trin til at gruppere dine data og tjenester i de samme datacentre at mindske eller fjerne data udgangspunkt gebyrer.)  

Siden [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage) indeholder detaljerede prisoplysninger for lagerkapacitet, gentagelse og transaktioner. Siden [Data overførsler priser detaljer](https://azure.microsoft.com/pricing/details/data-transfers/) indeholder detaljerede prisoplysninger for data udgangspunkt.

Se Få mere at vide om lagring konto kapacitet og ydeevne destinationer [Azure lagerplads skalerbarhed og ydeevne destinationer](storage-scalability-targets.md).

> [AZURE.NOTE] Når du opretter en Azure virtuelt, der en lagerplads konto oprettes for dig automatisk i dialogboksen installation placering Hvis du ikke allerede har en lagerplads konto i den nye placering. Så det ikke er nødvendigt at følge nedenstående trin for at oprette en lagerplads konto for virtuelt disken. Kontonavn lager baseres på navnet på den virtuelle computer. Se [dokumentationen til virtuelle Azure-computere](https://azure.microsoft.com/documentation/services/virtual-machines/) kan finde flere oplysninger.

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

1. Log på [Azure klassisk Portal](https://manage.windowsazure.com).

2. Klik på **Ny** på proceslinjen nederst på siden. Vælg **Data Services** | **lagerplads**, og klik derefter på **Hurtig oprettelse**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Angiv et navn til kontoen lagerplads i **URL-adresse**.

    > [AZURE.NOTE] Lagerplads kontonavne skal være mellem 3 og 24 tegn og kan indeholde tal og små bogstaver.
    >  
    > Dit kontonavn lager skal være entydig i Azure. Portalen Azure klassisk indikerer, hvis der allerede er optaget kontonavn lager, du vælger.

    Se [lagerplads konto slutpunkter](#storage-account-endpoints) under få mere at vide om, hvordan kontonavn lager skal bruges til at løse dine objekter i Azure-lager.

4. Vælg en placering til kontoen lagerplads, der er tæt du eller dine kunder i **Gruppen placering/forbindelse**. Hvis data i kontoen lagerplads kan åbnes fra en anden Azure tjeneste, såsom en Azure virtuel computer eller en skybaseret tjeneste, kan du vælge en forbindelse gruppe på listen til at gruppere kontoen lagerplads i den samme datacenter med andre Azure tjenester, du bruger til at forbedre ydeevnen og reducere omkostningerne.

    Bemærk, skal du vælge en forbindelse gruppe, når der oprettes kontoen lagerplads. Du kan ikke flytte en eksisterende konto til en forbindelse gruppe. Du kan finde flere oplysninger om forbindelsen mellem processorer grupper, [tjenesten samtidig placering med en forbindelse gruppe](#service-co-location-with-an-affinity-group) nedenfor.

    >[AZURE.IMPORTANT] Hvis du vil finde ud af, hvilke placeringer er tilgængelige for dit abonnement, kan du ringe handlingen [liste over alle ressource-udbydere](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Liste over udbydere fra PowerShell skal du ringe [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). Bruge metoden [liste](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) i klassen ProviderOperationsExtensions fra .NET.
    >
    >Desuden kan se [Azure områder](https://azure.microsoft.com/regions/#services) kan finde flere oplysninger om hvilke tjenester der er tilgængelige i hvilket område.


5. Hvis du har mere end ét abonnement på Azure, vises feltet **abonnement** . Angiv det Azure abonnement, du vil bruge kontoen lagerplads med i- **abonnement**.

6. Vælg det ønskede niveau af replikering for kontoen lagerplads i **Gentagelse**. Indstillingen anbefalede replikering er geografisk overflødige gentagelse, som indeholder signaltabet til dine data. Du kan finde flere oplysninger om Azure-lager Replikeringsindstillinger [Azure-lager gentagelse](storage-redundancy.md).

6. Klik på **Opret lagerplads konto**.

    Det kan tage et par minutter til at oprette kontoen lagerplads. Hvis du vil kontrollere status, kan du overvåge beskeder i bunden af portalen Azure klassisk. Når kontoen lagerplads er blevet oprettet, din nye konto lagerplads har status som **Online** og er klar til brug.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Lagerplads konto slutpunkter

Hvert objekt, du gemmer i Azure-lager har en entydig URL-adresse. Kontonavn lager formularer underdomæne til denne adresse. Kombination af underdomæne og domæne navn, som er specifikke for hver tjeneste, udgør et *slutpunkt* for kontoen lagerplads.

Eksempelvis hvis kontoen lagerplads har navnet *mystorageaccount*, derefter standard slutpunkterne for kontoen lagerplads er:

- BLOB service: http://*mystorageaccount*. blob.core.windows.net

- Tabellen service: http://*mystorageaccount*. table.core.windows.net

- Kø service: http://*mystorageaccount*. queue.core.windows.net

- Arkivere service: http://*mystorageaccount*. file.core.windows.net

Du kan se slutpunkterne for kontoen lagerplads på dashboardet til lager i [Azure klassisk Portal](https://manage.windowsazure.com) , når kontoen er blevet oprettet.

URL-adressen for at få adgang til et objekt i en lagerplads konto er oprettet ved at tilføje objektets placering i kontoen lagerplads til slutpunktet. For eksempel en blob adresse muligvis dette format: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Du kan også konfigurere et brugerdefineret domænenavn til brug med kontoen lagerplads. Få mere at vide, skal du se [konfigurere et brugerdefineret domænenavn til din blob storage slutpunkt](storage-custom-domain-name.md) .

### <a name="service-co-location-with-an-affinity-group"></a>Tjenesten samtidig placering med en forbindelse-gruppe

En *forbindelse gruppe* er et geografiske gruppering af Azure tjenester og FOS med kontoen Azure-lager. En forbindelse gruppe kan forbedre ydeevnen for tjenesten ved at finde computer arbejdsbelastninger, i den samme datacenter eller i nærheden af brugeren målgruppe. Desuden påløbet ingen fakturering gebyrer for udgangspunkt, når data i en lagerplads konto åbnes fra en anden tjeneste, der er en del af gruppen samme forbindelse.

> [AZURE.NOTE]  Åbn området <b>Indstillinger</b> i [Klassisk Azure-portalen](https://manage.windowsazure.com), klik på <b>forbindelse grupper</b>, og klik derefter på <b>Tilføj en forbindelse gruppe</b> eller knappen <b>Tilføj</b> for at oprette en forbindelse gruppe skal. Du kan også oprette og administrere grupper forbindelse ved hjælp af Azure Service Management API. Du kan få flere oplysninger i <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">handlinger på forbindelsen mellem processorer grupper</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Få vist, kopiere og genoprette lagerplads access-taster

Når du opretter en konto med lagerplads, genererer Azure to 512-bit lagerplads adgangstaster, som bruges til godkendelse, når der opnås adgang til kontoen lagerplads. Ved at oprette to lagerplads access-taster, Azure giver dig mulighed at genoprette de pågældende taster med ingen afbrudt lagerplads eller access i den pågældende tjeneste.

> [AZURE.NOTE] Vi anbefaler, at du undlader at dele din lagerplads hurtigtaster med andre. Hvis du vil tillade adgang til lagerplads ressourcer uden at give din access-taster, kan du bruge en *delt adgang signatur*. En delt adgang signatur giver adgang til en ressource på din konto for et interval, som du definerer og med de tilladelser, du angiver. Du kan få flere oplysninger i [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

Bruge **Administrer nøgler** på dashboardet eller siden **lagerplads** til at få vist, kopiere og genoprette hurtigtasterne lagerplads, der bruges til at få adgang til Blob, tabel og kø tjenester i [Azure klassisk Portal](https://manage.windowsazure.com).

### <a name="copy-a-storage-access-key"></a>Kopiere en lagerplads adgangsnøgle  

Du kan bruge **Administrer nøgler** til at kopiere en lagerplads hurtigtast til brug i en forbindelsesstreng. Forbindelsesstrengen kræver kontonavn lager og en nøgle til brug i godkendelse. Du kan finde oplysninger om at konfigurere forbindelsesstrenge for at få adgang til Azure lagerplads tjenester [Konfigurere Azure lagerplads forbindelsesstrenge](storage-configure-connection-string.md).

1. Klikke på **lagerplads**på [Azure klassisk Portal](https://manage.windowsazure.com), og klik derefter på navnet på lagerplads konto til at åbne dashboard.

2. Klik på **Administrer taster**.

    **Administrere hurtigtaster** åbnes.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Hvis du vil kopiere en lagerplads hurtigtast, Markér den vigtige tekst. Højreklik, og klik på **Kopiér**.

### <a name="regenerate-storage-access-keys"></a>Genoprette lagerplads access-taster
Vi anbefaler, at du ændrer hurtigtasterne til kontoen lagerplads med jævne mellemrum for at beskytte dine lagerplads forbindelser. To access-taster er tildelt, så du kan bevare forbindelser til kontoen lagerplads ved hjælp af en access-tasten, mens du genoprette andre hurtigtast.

> [AZURE.WARNING] Genoprette dine access-taster kan påvirke tjenester i Azure samt dine egne programmer, der er afhængige af kontoen lagerplads. Alle klienter, der bruger tasten access til at få adgang til kontoen lagerplads skal opdateres, hvis du vil bruge den nye nøgle.

**Media services** – Hvis du har media services, der er afhængige af din lagerplads konto, du skal igen synkronisere hurtigtasterne med din tjeneste medier efter at du genoprette tasterne.

**Programmer** – Hvis du har webprogrammer eller skytjenester, der bruger kontoen lagerplads, mister du forbindelserne Hvis du genoprette taster, medmindre du vender dine nøgler. 

**Lagerplads stifindere** - Hvis du bruger en hvilken som helst [lagerplads explorer programmer](storage-explorers.md), skal du sandsynligvis opdatere tasten lagerplads, der bruges af disse programmer.

Her er processen for at rotere lagerplads access-taster:

1. Opdater forbindelse strengene i din programkode til at referere til tasten sekundær adgang til kontoen, lagerplads.

2. Genoprette nøglen primære adgang til kontoen lagerplads. Klik på **Administrer nøgler**i [Azure klassisk Portal](https://manage.windowsazure.com)fra dashboardet eller siden **Konfigurer** . **Klik på under nøglen primære access** , og klik derefter på **Ja** for at bekræfte, at du vil oprette en ny nøgle.

3. Opdater forbindelse strengene i din kode til at referere til den nye primære access-nøgle.

4. Genoprette nøglen sekundær adgang.

## <a name="delete-a-storage-account"></a>Slette en lagerplads konto

Hvis du vil fjerne en lagerplads-konto, som du ikke længere bruger, skal du bruge **slette** på dashboardet eller siden **Konfigurer** . **Slette** sletter hele lagerplads kontoen, herunder alle blob, tabeller og køer i kontoen.

> [AZURE.WARNING] Det er ikke muligt at gendanne en slettet lagerplads konto eller hente det indhold, som den indeholdt før sletning. Sørg for at sikkerhedskopiere noget, du vil gemme, før du sletter kontoen. Dette også gælder for alle ressourcer i kontoen, når du sletter en blob, tabel, kø eller filer, slettes den permanent.
>
> Hvis kontoen lagerplads indeholder Virtuelle filer til en Azure virtuelt, skal derefter du slette alle billeder og diske, der bruger filerne Virtuelle, før du kan slette kontoen lagerplads. Først skal stoppe den virtuelle maskine, hvis det kører, og derefter slette den. Hvis du vil slette diske, gå til fanen **diske** og Slet eventuelle diske der. Hvis du vil slette billeder, gå til fanen **billeder** og slette nogen billeder, der er gemt på kontoen.

1. Klik på **lager**i [Azure klassisk Portal](https://manage.windowsazure.com).

2. Klik et vilkårligt sted i lagerplads kontopost undtagen navnet, og klik derefter på **Slet**.

     - Eller -

    Klik på navnet på kontoen lagerplads til at åbne dashboard, og klik derefter på **Slet**.

3. Klik på **Ja** for at bekræfte, at du vil slette kontoen lagerplads.

## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om Azure-lager, se [dokumentationen til Azure-lager](https://azure.microsoft.com/documentation/services/storage/).
- Gå til [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/).
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
