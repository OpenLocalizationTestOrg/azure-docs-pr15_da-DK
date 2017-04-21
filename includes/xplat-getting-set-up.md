<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>Brug af Azure CLI

Følgende trin hjælpe dig med at bruge Azure CLI nemt med den nyeste version og stort abonnementet. Hvis du vil installere Azure CLI og forbinde den til din konto, skal du se [Azure kommandolinjen (Azure CLI)](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Trin 1: Opdater Azure CLI version

Hvis du vil bruge Azure CLI for vigtigt kommandoer med service management tilstand, bør du have en nyere version, hvis det er muligt. Du kan kontrollere din version, Skriv `azure --version`. Du bør se nogenlunde således:

    $ azure --version
    0.8.17 (node: 0.10.25)

Hvis du vil opdatere din version af Azure CLI, skal du se [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Trin 2: Angive Azure-konto og abonnement

Når du har oprettet din Azure CLI med den konto, du vil bruge, kan du muligvis har mere end ét abonnement. Hvis du gør, skal du gennemse de abonnementer, der er tilgængelige for din konto ved at skrive `azure account list`, og vælg derefter det abonnement, du vil bruge ved at skrive `azure account set <subscription id or name> true` hvor _abonnement-id eller navn_ er abonnement-id eller på abonnementets navn, som du gerne vil arbejde med i den aktuelle session. Du skal se ud som følger:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Hvis du ikke allerede har en Azure-konto, men du har et abonnement på MSDN-abonnement, kan du få gratis Azure kredit ved at aktivere dine [her MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – eller du kan bruge den gratis konto. Enten fungerer til Azure-adgang.
