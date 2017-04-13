<properties
    pageTitle="Oprette en Azure batchen konto | Microsoft Azure"
    description="Lær, hvordan du opretter en Azure batchen konto i Azure portalen for at køre store parallelle arbejdsmængder i skyen"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Oprette en Azure batchen konto ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](batch-account-create-portal.md)
- [Batchen Management .NET](batch-management-dotnet.md)

Lær, hvordan du opretter en Azure batchen konto i [Azure portal][azure_portal], og hvor du kan finde vigtige egenskaber for konto som hurtigtasterne og konto URL-adresser. Vi diskuterer også batchen priser og sammenkæde en Azure-lager-konto til kontoen batchen, så du kan bruge [-programpakker](batch-application-packages.md) og [fastholdes sag og output](batch-task-output.md).

## <a name="create-a-batch-account"></a>Oprette en Batch-konto

1. Log på [Azure portal][azure_portal].

2. Klik på **nyt** > **beregne** > **batchen tjeneste**.

    ![Batchen på markedet][marketplace_portal]

3. Bladet **Ny batchen konto** vises. Få vist elementer *en* gennem *e* under beskrivelser af hvert blade element.

    ![Oprette en Batch-konto][account_portal]

    en. **Kontonavn**: et entydigt navn til kontoen batchen. Dette navn skal være entydig i området Azure kontoen oprettes (se *placering* nedenfor). Det kan indeholde kun små bogstaver, tal, og skal være på 3-24 tegn.

    b. **Abonnement**: et abonnement, til at oprette kontoen batchen. Hvis du har kun ét abonnement, markeres den som standard.

    c. **Ressourcegruppe**: en eksisterende ressource gruppere til din nye batchen-konto, eller du kan også oprette en ny.

    d. **Placering**: en Azure område, hvor du kan oprette kontoen batchen. Kun de områder, der understøttes af dit abonnement og ressourcegruppe vises som indstillinger.

    e. **Lagerplads konto** (valgfrit): en **generelle formål** lagerplads konto, du knytte (link) til den nye Batch-konto. Du kan se [sammenkædede Azure-lager konto](#linked-azure-storage-account) under få mere at vide.

4. Klik på **Opret** for at oprette kontoen.

  På portalen angiver, at det er **installere** kontoen, og når er afsluttet, vises en meddelelse om **installationer lykkedes** i *meddelelser*.

## <a name="view-batch-account-properties"></a>Få vist egenskaber for batchen konto

Når kontoen er blevet oprettet, kan du åbne **batchen konto blade** for at få adgang til dens indstillinger og egenskaber. Du kan få adgang til alle kontoindstillinger og egenskaber ved hjælp af menuen til venstre for bladet batchen konto.

![Batchen konto blade Azure-portalen][account_blade]

* **Batchen konto URL-adresse**: programmer, du opretter i [batchen udvikling API'er](batch-technical-overview.md#batch-development-apis) har brug for en konto URL-adresse til at administrere ressourcer og køre job i kontoen. En URL-adresse til batchen-konto har følgende format:

    `https://<account_name>.<region>.batch.azure.com`

![Batchen konto URL-adresse i portalen][account_url]

* **Access-taster**: skal også hurtigtast dine programmer, når du arbejder med ressourcer i kontoen batchen. For at få vist eller genoprette kontoen batchen hurtigtaster skal angive `keys` i **venstre menu søgefeltet på bladet batchen konto,** og vælg derefter **taster**.

    ![Batchen konto taster Azure-portalen][account_keys]

## <a name="pricing"></a>Priser

Batchen konti tilbydes kun i ét "gratis lag," hvilket betyder, at du ikke betale for kontoen batchen sig selv. Du betaler for de underliggende Azure Beregn ressourcer, som dine batchen løsninger forbruge og for de ressourcer, der bruges af andre tjenester, når din arbejdsbelastninger, som kører. For eksempel du betaler for noderne Beregn i dine grupper, og for dataene, du har gemt i Azure-lager som input eller output for dine opgaver. På samme måde, hvis du bruger funktionen [programpakker](batch-application-packages.md) i batchen, du betaler for de Azure-lager ressourcer, der bruges til at gemme dine programpakker. Se [batchen priser] [ batch_pricing] kan finde flere oplysninger.

## <a name="linked-azure-storage-account"></a>Sammenkædet firma i Azure-lager

Som nævnt tidligere, kan du sammenkæde en **generelle formål** lagerplads konto til kontoen batchen (valgfrit). Funktionen [programpakker](batch-application-packages.md) i batchen bruger blob-lager i en sammenkædet generelle formål lagerplads-konto, ligesom biblioteket [Batchen fil konventioner .NET](batch-task-output.md) . Disse valgfrie funktioner hjælper dig med at implementere de programmer, køres batchopgaverne og bevare dataene de landbrugsprodukter.

Batch aktuelt understøtter *kun* kontotypen **generelle formål** lagerplads som beskrevet i trin 5, [Opret en konto med lagerplads](../storage/storage-create-storage-account.md#create-a-storage-account), i [om Azure lagerplads konti](../storage/storage-create-storage-account.md). Når du sammenkæder en Azure-lager-konto til din batchen konto, være sikker på, at linket *kun* en **Hovedformålet** lagerplads konto.

![Oprette en "Generelle formål" lagerplads konto][storage_account]

Vi anbefaler, at du opretter en lagerplads konto med udelt adgang ved kontoen batchen.

>[AZURE.WARNING] Være forsigtig, når genoprette hurtigtasterne for en sammenkædet lagerplads-konto. Genoprette kun én lagerplads kontonøgle og klikke på **Synkroniser taster** i bladet sammenkædede lagerplads konto. Vente fem minutter, så de pågældende taster rettighederne blive anvendt på Beregn knuderne i dine grupper, og derefter genoprette og synkronisere anden nøglen, hvis det er nødvendigt. Hvis du genoprette begge taster samtidig, din Beregn noder vil ikke kunne synkronisere enten nøgle, og de vil miste adgang til kontoen lagerplads.

  ![Genoprette lagerplads konto taster][4]

## <a name="batch-service-quotas-and-limits"></a>Batchen service kvoter og -begrænsninger

Vær opmærksom på, som i abonnementet Azure og andre Azure tjenester visse [kvoter og begrænsninger for](batch-quota-limit.md) anvendelse på batchen konti. Aktuelle kvoter for en batchen konto vises i portalen i kontoen **Egenskaber**.

![Batchen konto kvoter Azure-portalen][quotas]

Husk disse kvoter på, når du designe og skalering af din batchen arbejdsmængder. Eksempelvis hvis din puljen ikke at kontakte mål antallet af Beregn knuder du har angivet, du muligvis har nået core kvote grænsen for kontoen batchen.

Bemærk også, ikke er du begrænset til en enkelt batchen konto for abonnementet Azure. Du kan køre flere batchen arbejdsbelastninger i en enkelt Batch-konto, eller du kan distribuere din ressourcernes arbejdsmængde batchen konti i det samme abonnement, men i forskellige Azure områder.

Mange af disse kvoter forbedres blot med en gratis produkt supportanmodning blev sendt i portalen Azure. Se [kvoter og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md) få at vide om anmoder om kvote øges.

## <a name="other-batch-account-management-options"></a>Andre batchen kontoindstillinger management

Ud over ved hjælp af portalen Azure, kan du også oprette og administrere batchen konti med følgende:

* [Batchen PowerShell-cmdletter](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [Batchen Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Næste trin

* Få vist [Azure batchen funktionsoversigt](batch-api-basics.md) mere at vide om batchen service begreber og funktioner. I artiklen beskrives de primære Batch ressourcer som grupper, Beregn noder, job og opgaver, og giver et overblik over tjenestens funktioner, der aktiverer udførelse af store Beregn arbejdsbyrde.

* Lær grundlæggende udvikling af en Batch-aktiverede program ved hjælp af [batchen .NET klientbibliotek](batch-dotnet-get-started.md). [Indledende artikel](batch-dotnet-get-started.md) fører dig gennem et arbejde-program, der bruger tjenesten batchen til at udføre en arbejdsbyrde for flere Beregn noder og omfatter bruge Azure-lager til arbejdsbelastningen fil midlertidige og hentning.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Genoprette lagerplads konto taster"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
